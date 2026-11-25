-- =====================================================================
--  DELTA MOBILE v2  —  Aim / Move / ESP / GFX / SQUID
-- =====================================================================

local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local Lighting         = game:GetService("Lighting")
local CoreGui          = game:GetService("CoreGui")
local HttpService      = game:GetService("HttpService")

local LP     = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- evita rodar duas vezes (descarrega a instância anterior)
local genv = (getgenv and getgenv()) or _G
if genv.DeltaMobileUnload then pcall(genv.DeltaMobileUnload) end

-- ── OPÇÕES ─────────────────────────────────────────────────────────
local OPTIONS = {
    LoadEmotes      = true,  -- false = não carrega script remoto de terceiros
    EmotesUrl       = "https://raw.githubusercontent.com/7yd7/Hub/refs/heads/Branch/GUIS/Emotes.lua",
    BoxAspect       = 0.6,   -- largura/altura da caixa ESP
    SkeletonMaxDist = 300,   -- distância máx. do esqueleto (economiza FPS)
    StickDist       = 1,     -- distância fixa atrás do inimigo (studs)
    ExtraTeamNames  = {},    -- nomes extras de time pra jogos diferentes, ex: { "Lado", "Grupo" }
    ConfigFile      = "DeltaMobileConfig.json",
}

-- ── IDs DE IMAGEM ──────────────────────────────────────────────────
local ASSETS = {
    FabIcon   = "6421296789",      -- ícone do botão flutuante
    PanelBg   = "9838762149",      -- fundo do painel
    LockIcon  = "117007154108938", -- ícone do Lock Shift (ligado e desligado)
    Crosshair = "95549072871456",  -- mira do Lock Shift
}

-- ── LOCK SHIFT (ajustes) ───────────────────────────────────────────
local SHIFTLOCK = {
    BtnSize      = 44,                         -- tamanho do botão (px)
    CrossSize    = 22,                         -- tamanho da mira (px)
    BtnPos       = UDim2.new(0.5, 200, 1, -64),-- embaixo, bem pro lado direito e um pouco acima
    OffsetX      = 1.75,                       -- câmera no ombro (mesmo valor do shift lock do PC)
    CrossOffsetX = 0,                          -- mira em px pro lado (0 = centro, igual ao PC)
    BlendTime    = 0.35,                       -- tempo (s) pra travar/destravar suave
    TurnSpeed    = 18,                         -- personagem virando pra câmera (maior = mais rápido)
}

-- ── TEMA ───────────────────────────────────────────────────────────
local ACCENT    = Color3.fromRGB(108, 92, 231)
local ACCENT2   = Color3.fromRGB(72, 160, 255)
local BG_MAIN   = Color3.fromRGB(16, 16, 22)
local BG_TAB    = Color3.fromRGB(24, 24, 32)
local BG_WIDGET = Color3.fromRGB(28, 28, 37)
local BAR_BG    = Color3.fromRGB(40, 40, 50)
local LINE      = Color3.fromRGB(52, 52, 66)
local TXT_MAIN  = Color3.fromRGB(225, 225, 235)
local TXT_SUB   = Color3.fromRGB(165, 165, 180)
local OFF_COL   = Color3.fromRGB(55, 55, 65)
local ICON_DIM  = Color3.fromRGB(150, 150, 165)
local C_GREEN   = Color3.fromRGB(0, 255, 100)
local C_RED     = Color3.fromRGB(255, 95, 95)
local C_WHITE   = Color3.fromRGB(255, 255, 255)
local C_GRAY    = Color3.fromRGB(200, 200, 200)

-- ── UTIL ───────────────────────────────────────────────────────────
local conns, guis, warned = {}, {}, {}

local function Connect(sig, fn)
    local c = sig:Connect(fn)
    conns[#conns + 1] = c
    return c
end

local function New(class, props)
    local o = Instance.new(class)
    local parent
    for k, v in pairs(props) do
        if k == "Parent" then parent = v else o[k] = v end
    end
    if parent then o.Parent = parent end
    return o
end

local function Corner(parent, px)
    return New("UICorner", { CornerRadius = px and UDim.new(0, px) or UDim.new(1, 0), Parent = parent })
end

local function Stroke(parent, color, thick, transp)
    return New("UIStroke", {
        Color = color, Thickness = thick or 1, Transparency = transp or 0,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border, Parent = parent,
    })
end

local function SetVis(o, v)
    if o.Visible ~= v then o.Visible = v end
end

local function Tween(obj, props, instant, t)
    if instant then
        for k, v in pairs(props) do obj[k] = v end
    else
        TweenService:Create(obj, TweenInfo.new(t or 0.18, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), props):Play()
    end
end

-- aplica o ID; IDs da loja costumam ser de Decal, então tenta converter pro ID da imagem real
local function SetImage(obj, id)
    obj.Image = "rbxassetid://" .. id
    task.spawn(function()
        pcall(function()
            local o = game:GetObjects("rbxassetid://" .. id)[1]
            if not o then return end
            if (o:IsA("Decal") or o:IsA("Texture")) and o.Texture ~= "" and obj.Parent then
                obj.Image = o.Texture
            end
            o:Destroy()
        end)
    end)
end

local function Safe(tag, fn, ...)
    local ok, err = pcall(fn, ...)
    if not ok and not warned[tag] then
        warned[tag] = true
        warn("[Delta Mobile] erro em " .. tag .. ": " .. tostring(err))
    end
end

local function WarnOnce(tag, msg)
    if warned[tag] then return end
    warned[tag] = true
    warn("[Delta Mobile] " .. msg)
end

local function MakeGui(name, order)
    local g = New("ScreenGui", {
        Name = name, ResetOnSpawn = false, IgnoreGuiInset = true,
        ZIndexBehavior = Enum.ZIndexBehavior.Sibling, DisplayOrder = order,
    })
    pcall(function() g.ScreenInsets = Enum.ScreenInsets.None end) -- tela cheia, sem offset de notch
    if not (pcall(function() g.Parent = CoreGui end) and g.Parent) then
        if not (pcall(function() g.Parent = gethui() end) and g.Parent) then
            g.Parent = LP:WaitForChild("PlayerGui")
        end
    end
    guis[#guis + 1] = g
    return g
end

-- ── CONFIG ─────────────────────────────────────────────────────────
local Config = {
    Aimbot    = { Enabled = false, SmoothLevel = 7, FOV = 150, MaxDistance = 300, ShowFOV = false, WallCheck = true, TeamCheck = true },
    Movement  = { Speed = 16, SpeedEnabled = false, Fly = false, FlySpeed = 60, Noclip = false },
    Visual    = { Enabled = false, Box = false, Name = false, Dist = false, HP = false, Skeleton = false, Tracer = false, Highlight = false, MaxDistance = 500 },
    Graphics  = { LowPoly = false, Cartoon = false },
    ShiftLock = { Enabled = false, Locked = false }, -- salvo: não precisa ligar toda vez
    Squid     = { Stick = false, AutoAttack = false, EnemyOnly = true },
    UI        = { FabXScale = 0, FabXOffset = 14, FabYScale = 0.5, FabYOffset = -17 }, -- posição do botão flutuante
}

local saveQueued = false
local function Save() -- debounce: não grava a cada tick do slider
    if not writefile or saveQueued then return end
    saveQueued = true
    task.delay(0.6, function()
        saveQueued = false
        pcall(function()
            writefile(OPTIONS.ConfigFile, HttpService:JSONEncode({
                Aimbot = Config.Aimbot, Movement = Config.Movement, Visual = Config.Visual,
                ShiftLock = Config.ShiftLock, UI = Config.UI,
            }))
        end)
    end)
end

local function Load()
    if isfile and readfile then
        pcall(function()
            if not isfile(OPTIONS.ConfigFile) then return end
            local data = HttpService:JSONDecode(readfile(OPTIONS.ConfigFile))
            for section, values in pairs(data) do
                local target = Config[section]
                if target and section ~= "Graphics" and type(values) == "table" then
                    for key, val in pairs(values) do
                        if target[key] ~= nil and type(val) == type(target[key]) then
                            target[key] = val
                        end
                    end
                end
            end
        end)
    end
    Config.Movement.Fly = false -- Fly nunca inicia ligado
end
Load()

-- ── CACHE DE PARTES DOS PLAYERS ────────────────────────────────────
local BONES_R15 = {
    {"Head","UpperTorso"},{"UpperTorso","LowerTorso"},
    {"UpperTorso","LeftUpperArm"},{"LeftUpperArm","LeftLowerArm"},{"LeftLowerArm","LeftHand"},
    {"UpperTorso","RightUpperArm"},{"RightUpperArm","RightLowerArm"},{"RightLowerArm","RightHand"},
    {"LowerTorso","LeftUpperLeg"},{"LeftUpperLeg","LeftLowerLeg"},{"LeftLowerLeg","LeftFoot"},
    {"LowerTorso","RightUpperLeg"},{"RightUpperLeg","RightLowerLeg"},{"RightLowerLeg","RightFoot"},
}
local BONES_R6 = {
    {"Head","Torso"},{"Torso","Left Arm"},{"Torso","Right Arm"},{"Torso","Left Leg"},{"Torso","Right Leg"},
}

local PartCache = {}

local function GetParts(plr)
    local char = plr.Character
    if not char then PartCache[plr] = nil; return nil end
    local c = PartCache[plr]
    if c and c.Char == char and c.Root.Parent and c.Head.Parent and c.Hum.Parent then return c end
    local root = char:FindFirstChild("HumanoidRootPart")
    local head = char:FindFirstChild("Head")
    local hum  = char:FindFirstChildOfClass("Humanoid")
    if not (root and head and hum) then PartCache[plr] = nil; return nil end
    c = { Char = char, Root = root, Head = head, Hum = hum, R15 = (hum.RigType == Enum.HumanoidRigType.R15) }
    PartCache[plr] = c
    return c
end

local function GetBones(c)
    local now = os.clock()
    if c.Bones and now < c.BonesExp then return c.Bones end
    local list = {}
    for _, pair in ipairs(c.R15 and BONES_R15 or BONES_R6) do
        local a, b = c.Char:FindFirstChild(pair[1]), c.Char:FindFirstChild(pair[2])
        if a and b then list[#list + 1] = { a, b } end
    end
    c.Bones, c.BonesExp = list, now + 1.5
    return list
end

-- ── TIMES: DETECÇÃO UNIVERSAL ──────────────────────────────────────
-- Cada jogo guarda o time num lugar diferente. Procura, nesta ordem:
--   1) Team do Roblox  2) atributos/valores do player  3) leaderstats
--   4) atributos/valores do personagem
-- Só vale campo com nome de time (team, equipe, faction, squad, alliance, clan,
-- time, side, lado + ExtraTeamNames). A fonte achada pra VOCÊ é usada pra ler
-- o time de todos os outros, então a comparação é sempre "maçã com maçã".
local TEAM_WORDS = { "team", "equipe", "faction", "squad", "alliance", "clan" } -- nome CONTÉM
local TEAM_EXACT = { time = true, side = true, lado = true }                     -- nome IGUAL
local TEAM_SKIP  = { "kill", "score", "point", "win", "death", "coin", "cash", "money", "rank", "level", "xp", "count", "size", "invite", "chat", "voice", "damage", "assist" }
local TEAM_EXTRA = {}
for _, n in ipairs(OPTIONS.ExtraTeamNames) do TEAM_EXTRA[tostring(n):lower()] = true end

local function TeamNameRank(name) -- menor = mais confiável; nil = não é campo de time
    local n = name:lower()
    if TEAM_EXTRA[n] then return 0 end
    if n == "team" then return 1 end
    for _, s in ipairs(TEAM_SKIP) do
        if n:find(s, 1, true) then return nil end
    end
    for _, w in ipairs(TEAM_WORDS) do
        if n:find(w, 1, true) then return 2 end
    end
    if TEAM_EXACT[n] then return 3 end
    return nil
end

local function NormKey(v) -- qualquer tipo de valor vira uma chave comparável
    local t = typeof(v)
    if t == "string" then
        v = v:lower():match("^%s*(.-)%s*$")
        return v ~= "" and v or nil
    elseif t == "number" or t == "boolean" then
        return tostring(v)
    elseif t == "BrickColor" then
        return v.Name
    elseif t == "Color3" then
        return v:ToHex()
    elseif t == "Instance" then
        return v.Name
    end
    return nil
end

local function TeamCandidates(plr) -- todos os campos com cara de time desse player
    local out = {}
    local function addAttrs(inst, prefix, order)
        if not inst then return end
        for name, val in pairs(inst:GetAttributes()) do
            local r = TeamNameRank(name)
            if r and NormKey(val) then out[#out + 1] = { src = prefix .. name, rank = r, order = order } end
        end
    end
    local function addValues(inst, prefix, order)
        if not inst then return end
        for _, ch in ipairs(inst:GetChildren()) do
            if ch:IsA("ValueBase") then
                local r = TeamNameRank(ch.Name)
                if r and NormKey(ch.Value) then out[#out + 1] = { src = prefix .. ch.Name, rank = r, order = order } end
            end
        end
    end
    local char = plr.Character
    addAttrs(plr, "pa:", 1)
    addValues(plr, "pv:", 2)
    addValues(plr:FindFirstChild("leaderstats"), "ls:", 3)
    addAttrs(char, "ca:", 4)
    addValues(char, "cv:", 5)
    return out
end

local function FindTeamSource(plr)
    if plr.Team ~= nil then return "team" end
    local list = TeamCandidates(plr)
    if #list == 0 then return nil end
    table.sort(list, function(a, b)
        if a.rank ~= b.rank then return a.rank < b.rank end
        if a.order ~= b.order then return a.order < b.order end
        return a.src < b.src
    end)
    return list[1].src
end

local function ReadTeam(plr, src) -- lê o time do player usando uma fonte específica
    if src == "team" then
        local t = plr.Team
        return t and NormKey(t.Name) or nil
    end
    local kind, name = src:match("^(%a+):(.+)$")
    local char = plr.Character
    if kind == "pa" then return NormKey(plr:GetAttribute(name)) end
    if kind == "ca" then return char and NormKey(char:GetAttribute(name)) or nil end
    local holder = (kind == "pv" and plr) or (kind == "ls" and plr:FindFirstChild("leaderstats")) or (kind == "cv" and char) or nil
    local v = holder and holder:FindFirstChild(name)
    if v and v:IsA("ValueBase") then return NormKey(v.Value) end
    return nil
end

local myTeam = { src = nil, key = nil, exp = 0, char = nil }
local function RefreshMyTeam() -- a busca pesada roda no máx. 2x por segundo
    local now = os.clock()
    local char = LP.Character
    if now < myTeam.exp and myTeam.char == char then return end
    myTeam.exp, myTeam.char = now + 0.5, char
    local src = FindTeamSource(LP)
    myTeam.src, myTeam.key = src, src and ReadTeam(LP, src) or nil
end

local function IsEnemyTeam(plr) -- true = time rival (ou time não detectado)
    if plr == LP then return false end
    RefreshMyTeam()
    local mine = myTeam.key
    if not mine then
        WarnOnce("noteam", "Nenhum time detectado pra você: todos os jogadores serão tratados como inimigos.")
        return true
    end
    return ReadTeam(plr, myTeam.src) ~= mine
end

-- ── NOMES NATIVOS (evita nome duplicado com o ESP) ─────────────────
local NativeSaved = setmetatable({}, { __mode = "k" })

local function WantHideNative()
    return Config.Visual.Enabled and Config.Visual.Name
end

local function HideNative(hum, hide)
    if not hum then return end
    if hide then
        if NativeSaved[hum] == nil then NativeSaved[hum] = hum.DisplayDistanceType end
        hum.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None
    elseif NativeSaved[hum] ~= nil then
        hum.DisplayDistanceType = NativeSaved[hum]
        NativeSaved[hum] = nil
    end
end

local function ApplyNativeNames()
    local hide = WantHideNative()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LP and plr.Character then
            HideNative(plr.Character:FindFirstChildOfClass("Humanoid"), hide)
        end
    end
end

-- ── FLY ────────────────────────────────────────────────────────────
local flySavedAutoRot = nil
local flyActive, flyVelocity, flyOrientation = false, nil, nil
local flyHumanoid, flyRoot, flyControls = nil, nil, nil
local FlyToggle -- preenchido quando a GUI é criada

task.spawn(function() -- assíncrono + timeout: nunca trava o script
    pcall(function()
        local ps = LP:WaitForChild("PlayerScripts", 10)
        local module = ps and ps:WaitForChild("PlayerModule", 10)
        if module then flyControls = require(module):GetControls() end
    end)
end)

local function StopFly()
    local was = flyActive
    flyActive = false
    if flyVelocity then flyVelocity:Destroy(); flyVelocity = nil end
    if flyOrientation then flyOrientation:Destroy(); flyOrientation = nil end
    if was and flyHumanoid and flyHumanoid.Parent and flyHumanoid.Health > 0 then
        flyHumanoid.AutoRotate = (flySavedAutoRot ~= false)
        flyHumanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
    end
    Config.Movement.Fly = false
    if FlyToggle then FlyToggle.Set(false) end -- sincroniza o toggle da GUI
end

local function StartFly()
    if flyActive then return true end
    local char = LP.Character
    local hum  = char and char:FindFirstChildOfClass("Humanoid")
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not hum or not root or hum.Health <= 0 then return false end
    flyHumanoid, flyRoot = hum, root
    flySavedAutoRot = hum.AutoRotate
    flyActive = true
    hum.AutoRotate = false
    hum:ChangeState(Enum.HumanoidStateType.Physics)
    flyVelocity = New("BodyVelocity", { Name = "DeltaFlyBV", MaxForce = Vector3.new(1e6, 1e6, 1e6), P = 1e4, Velocity = Vector3.zero, Parent = root })
    flyOrientation = New("BodyGyro", { Name = "DeltaFlyBG", MaxTorque = Vector3.new(1e6, 1e6, 1e6), P = 1e4, D = 500, CFrame = root.CFrame, Parent = root })
    return true
end

local function GetMoveVector()
    if flyControls then
        local ok, res = pcall(flyControls.GetMoveVector, flyControls)
        if ok and typeof(res) == "Vector3" then return res end
    end
    -- fallback: converte MoveDirection para o espaço da câmera
    local rel = Camera.CFrame:VectorToObjectSpace(flyHumanoid.MoveDirection)
    return Vector3.new(rel.X, 0, rel.Z)
end

local function FlyStep()
    local hum, root = flyHumanoid, flyRoot
    if not hum or not hum.Parent or hum.Health <= 0 or not root or not root.Parent
        or not flyVelocity or not flyVelocity.Parent or not flyOrientation then
        StopFly(); return
    end
    local cf = Camera.CFrame
    local mv = GetMoveVector()
    local dir = cf.RightVector * mv.X - cf.LookVector * mv.Z
    if dir.Magnitude > 1 then dir = dir.Unit end
    flyVelocity.Velocity = dir * Config.Movement.FlySpeed
    flyOrientation.CFrame = CFrame.lookAt(root.Position, root.Position + cf.LookVector, cf.UpVector)
end

-- ── SPEED / NOCLIP ─────────────────────────────────────────────────
local speedHum, speedOrig = nil, nil

local function RestoreSpeed()
    if speedHum and speedHum.Parent and speedOrig then speedHum.WalkSpeed = speedOrig end
    speedHum, speedOrig = nil, nil
end

local function SpeedStep()
    local char = LP.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    if hum ~= speedHum then speedHum = hum; speedOrig = hum.WalkSpeed end
    if hum.WalkSpeed ~= Config.Movement.Speed then hum.WalkSpeed = Config.Movement.Speed end
end

local noclipChar, noclipParts, noclipRefresh, noclipChanged = nil, {}, 0, {}

local function RestoreNoclip()
    for p in pairs(noclipChanged) do
        if p.Parent then p.CanCollide = true end
    end
    noclipChanged = {}
end

local function NoclipStep() -- roda no Stepped (antes da física)
    if not Config.Movement.Noclip then return end
    local char = LP.Character
    if not char then return end
    local now = os.clock()
    if char ~= noclipChar or now > noclipRefresh then
        noclipChar, noclipRefresh, noclipParts = char, now + 2, {}
        for _, p in ipairs(char:GetDescendants()) do
            if p:IsA("BasePart") then noclipParts[#noclipParts + 1] = p end
        end
    end
    for i = 1, #noclipParts do
        local p = noclipParts[i]
        if p.CanCollide then p.CanCollide = false; noclipChanged[p] = true end
    end
end

-- ── GRÁFICOS ───────────────────────────────────────────────────────
local gfx = { light = nil, effects = {}, skies = {}, clouds = nil, quality = nil, cartoon = {}, cartoonConn = nil }

local function ApplyLighting()
    local g = Config.Graphics
    if gfx.light then -- volta à base antes de reaplicar o perfil
        for k, v in pairs(gfx.light) do pcall(function() Lighting[k] = v end) end
    end
    if not (g.LowPoly or g.Cartoon) then gfx.light = nil; return end
    if not gfx.light then
        gfx.light = {
            GlobalShadows = Lighting.GlobalShadows, Brightness = Lighting.Brightness, Ambient = Lighting.Ambient,
            FogStart = Lighting.FogStart, FogEnd = Lighting.FogEnd, FogColor = Lighting.FogColor,
        }
    end
    if g.LowPoly then
        Lighting.GlobalShadows = false; Lighting.Brightness = 2
        Lighting.Ambient = Color3.fromRGB(180, 180, 180)
        Lighting.FogStart = 100; Lighting.FogEnd = 300; Lighting.FogColor = Color3.fromRGB(180, 180, 190)
    else
        Lighting.Brightness = 2.5; Lighting.Ambient = Color3.fromRGB(180, 180, 180)
    end
end

local function SetLowPoly(v)
    Config.Graphics.LowPoly = v
    if v then
        for _, obj in ipairs(Lighting:GetChildren()) do
            if obj:IsA("PostEffect") and obj.Enabled then
                gfx.effects[#gfx.effects + 1] = obj; obj.Enabled = false
            elseif obj:IsA("Sky") then
                gfx.skies[#gfx.skies + 1] = obj; obj.Parent = nil
            end
        end
        local clouds = workspace.Terrain:FindFirstChildOfClass("Clouds")
        if clouds and clouds.Enabled then gfx.clouds = clouds; clouds.Enabled = false end
        pcall(function()
            local r = settings().Rendering
            gfx.quality = gfx.quality or r.QualityLevel
            r.QualityLevel = Enum.QualityLevel.Level01
        end)
    else
        for _, e in ipairs(gfx.effects) do if e.Parent then e.Enabled = true end end
        gfx.effects = {}
        for _, s in ipairs(gfx.skies) do s.Parent = Lighting end
        gfx.skies = {}
        if gfx.clouds then gfx.clouds.Enabled = true; gfx.clouds = nil end
        if gfx.quality then
            pcall(function() settings().Rendering.QualityLevel = gfx.quality end)
            gfx.quality = nil
        end
    end
    ApplyLighting()
end

local function IsCharacterPart(obj) -- não mexe no rosto dos players/NPCs
    local m = obj:FindFirstAncestorOfClass("Model")
    while m do
        if m:FindFirstChildOfClass("Humanoid") then return true end
        m = m:FindFirstAncestorOfClass("Model")
    end
    return false
end

local function CartoonHide(obj)
    if obj:IsA("Decal") and not gfx.cartoon[obj] and not IsCharacterPart(obj) then
        gfx.cartoon[obj] = obj.Transparency
        obj.Transparency = 1
    end
end

local function SetCartoon(v)
    Config.Graphics.Cartoon = v
    if v then
        gfx.cartoonConn = Connect(workspace.DescendantAdded, CartoonHide)
        task.spawn(function() -- em lotes: não congela em mapas grandes
            local n = 0
            for _, o in ipairs(workspace:GetDescendants()) do
                if not Config.Graphics.Cartoon then break end
                CartoonHide(o)
                n += 1
                if n % 400 == 0 then task.wait() end
            end
        end)
    else
        if gfx.cartoonConn then gfx.cartoonConn:Disconnect(); gfx.cartoonConn = nil end
        for obj, t in pairs(gfx.cartoon) do
            pcall(function() if obj.Parent then obj.Transparency = t end end)
        end
        gfx.cartoon = {}
    end
    ApplyLighting()
end

-- ── LOCK SHIFT (mobile) ────────────────────────────────────────────
-- Igual ao do PC: o personagem olha pra onde a câmera aponta, a câmera vai pro
-- ombro (1.75 studs), não atravessa parede e a mira fica no centro da tela.
-- Tudo entra/sai com ease suave. Toggle + estado travado são salvos no config.
local SL = Config.ShiftLock
local SLGui = MakeGui("DeltaShiftLock", 60)

local SLCross = New("ImageLabel", {
    Name = "Mira", AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.new(0.5, SHIFTLOCK.CrossOffsetX, 0.5, 0),
    Size = UDim2.fromOffset(SHIFTLOCK.CrossSize, SHIFTLOCK.CrossSize), BackgroundTransparency = 1,
    ImageTransparency = 1, Visible = false, Parent = SLGui,
})
SetImage(SLCross, ASSETS.Crosshair)

local SLBtn = New("TextButton", {
    Name = "BotaoLockShift", AnchorPoint = Vector2.new(0.5, 1), Position = SHIFTLOCK.BtnPos,
    Size = UDim2.fromOffset(SHIFTLOCK.BtnSize, SHIFTLOCK.BtnSize), BackgroundColor3 = BG_MAIN,
    BackgroundTransparency = 0.15, Text = "", AutoButtonColor = false, Visible = false, Parent = SLGui,
})
Corner(SLBtn)
local SLStroke = Stroke(SLBtn, OFF_COL, 1.5)
local SLIcon = New("ImageLabel", {
    AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromScale(0.62, 0.62),
    BackgroundTransparency = 1, Parent = SLBtn,
})
SetImage(SLIcon, ASSETS.LockIcon)

local slLocked, slHum, slSavedRot = false, nil, nil
local slP, slFP = 0, 1           -- progresso da trava (0..1) / fator de 1ª pessoa
local slDelta, slCam = nil, nil  -- último deslocamento aplicado na câmera
local slRay = RaycastParams.new()
slRay.FilterType = Enum.RaycastFilterType.Exclude
slRay.IgnoreWater = true
pcall(function() slRay.RespectCanCollide = true end)
local slRayChar

-- mesmo ícone nos dois estados: ligado = colorido com borda de destaque, desligado = apagado
local function PaintLockBtn(instant)
    local on = slLocked
    Tween(SLBtn,    { BackgroundColor3 = on and ACCENT or BG_MAIN }, instant)
    Tween(SLStroke, { Color = on and ACCENT2 or OFF_COL }, instant)
    Tween(SLIcon,   { ImageColor3 = on and C_WHITE or ICON_DIM, ImageTransparency = on and 0 or 0.25 }, instant)
end

local function RestoreShiftLock()
    local hum = slHum
    if hum and hum.Parent and slSavedRot ~= nil then
        hum.AutoRotate = slSavedRot
        -- se o Fly estiver ligado, ele não pode "herdar" o AutoRotate falso do lock
        if flyActive and flySavedAutoRot == false then flySavedAutoRot = slSavedRot end
    end
    slHum, slSavedRot = nil, nil
end

local function ApplyLock(v, instant) -- só estado de execução, não mexe na config
    slLocked = v and true or false
    PaintLockBtn(instant)
    if not slLocked then RestoreShiftLock() end
end

local function SetShiftLock(on, instant)
    SL.Enabled = on and true or false
    SLBtn.Visible = SL.Enabled
    ApplyLock(SL.Enabled and SL.Locked, instant)
end

SLBtn.Activated:Connect(function()
    if not SL.Enabled then return end
    SL.Locked = not SL.Locked
    ApplyLock(SL.Locked)
    Save()
end)

-- Roda ANTES da câmera padrão: tira o deslocamento do frame anterior, assim a
-- câmera do jogo sempre trabalha numa posição limpa (nada acumula).
local function UndoShiftCam()
    local cam = slCam
    if slDelta and cam and cam.Parent then cam.CFrame = cam.CFrame - slDelta end
    slDelta, slCam = nil, nil
end

local function ShiftLockStep(dt)
    if slP == 0 and not slLocked then return end
    dt = math.min(dt, 0.1)

    -- progresso com ease in/out: câmera, mira e personagem entram juntos, suave
    local k = dt / SHIFTLOCK.BlendTime
    slP = slLocked and math.min(slP + k, 1) or math.max(slP - k, 0)
    local e = slP * slP * (3 - 2 * slP)

    local tr = 1 - e
    if SLCross.ImageTransparency ~= tr then SLCross.ImageTransparency = tr end
    SetVis(SLCross, e > 0.02)

    -- câmera no ombro (em 1ª pessoa não desloca)
    local cf = Camera.CFrame
    local fpGoal = ((Camera.Focus.Position - cf.Position).Magnitude < 1) and 0 or 1
    slFP += (fpGoal - slFP) * (1 - math.exp(-dt * 12))
    local off = SHIFTLOCK.OffsetX * e * slFP
    if off > 0.01 then
        local want = cf.RightVector * off
        local char = LP.Character
        if char then -- não deixa a câmera entrar na parede
            if slRayChar ~= char then slRayChar = char; slRay.FilterDescendantsInstances = { char } end
            local hit = workspace:Raycast(cf.Position, want, slRay)
            if hit then want = want.Unit * math.max(hit.Distance - 0.5, 0) end
        end
        Camera.CFrame = cf + want
        slDelta, slCam = want, Camera
    end

    if not slLocked then return end

    local char = LP.Character
    local hum  = char and char:FindFirstChildOfClass("Humanoid")
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not hum or not root or hum.Health <= 0 then return end

    if hum ~= slHum then slHum, slSavedRot = hum, nil end -- novo personagem (respawn)

    -- Fly / Grudar / sentado controlam a rotação sozinhos: não interfere
    if flyActive or Config.Squid.Stick or hum.Sit then return end

    if slSavedRot == nil then slSavedRot = hum.AutoRotate end
    if hum.AutoRotate then hum.AutoRotate = false end

    -- personagem vira suave pra onde a câmera aponta (mais lento no começo, firme depois)
    local look = Camera.CFrame.LookVector
    local flat = Vector3.new(look.X, 0, look.Z)
    if flat.Magnitude < 1e-3 then return end
    local rp = root.Position
    local goal = CFrame.lookAt(rp, rp + flat)
    local speed = SHIFTLOCK.TurnSpeed * (0.5 + 0.5 * e)
    root.CFrame = root.CFrame:Lerp(goal, 1 - math.exp(-dt * speed))
end

SetShiftLock(SL.Enabled, true) -- restaura o estado salvo

-- ── ESP: MOTOR DE DESENHO ──────────────────────────────────────────
local ESPGui  = MakeGui("DeltaESP", 50)
local ESPRoot = New("Frame", { Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, BorderSizePixel = 0, Parent = ESPGui })

local function NewRect(color, z)
    return New("Frame", { BackgroundColor3 = color, BorderSizePixel = 0, Visible = false, ZIndex = z or 5, Parent = ESPRoot })
end

local function NewLine(color, thick)
    local f = NewRect(color, 5)
    f.AnchorPoint = Vector2.new(0.5, 0.5)
    return { F = f, T = thick }
end

-- Rotation gira no CENTRO do Frame. Por isso a linha é posicionada
-- no PONTO MÉDIO com AnchorPoint 0.5.
local function SetLine(l, ax, ay, bx, by)
    local dx, dy = bx - ax, by - ay
    local len = math.sqrt(dx * dx + dy * dy)
    local f = l.F
    if len < 0.5 then SetVis(f, false); return end
    f.Size = UDim2.fromOffset(len, l.T)
    f.Position = UDim2.fromOffset((ax + bx) / 2, (ay + by) / 2)
    f.Rotation = math.deg(math.atan2(dy, dx))
    SetVis(f, true)
end

local FOVCircle = New("Frame", { AnchorPoint = Vector2.new(0.5, 0.5), BackgroundTransparency = 1, BorderSizePixel = 0, Visible = false, ZIndex = 5, Parent = ESPRoot })
Corner(FOVCircle)
New("UIStroke", { Color = Color3.fromRGB(255, 50, 50), Thickness = 1.5, Parent = FOVCircle })

local ESPStorage, HighlightStorage = {}, {}

local function GetESP(plr)
    local d = ESPStorage[plr]
    if d then return d end
    d = { Shown = false, Col = C_GREEN }
    d.Box = New("Frame", { BackgroundTransparency = 1, BorderSizePixel = 0, Visible = false, ZIndex = 5, Parent = ESPRoot })
    d.BoxStroke = New("UIStroke", { Color = C_GREEN, Thickness = 1.5, LineJoinMode = Enum.LineJoinMode.Miter, Parent = d.Box })
    d.NameTxt = New("TextLabel", {
        AnchorPoint = Vector2.new(0.5, 1), BackgroundTransparency = 1, Size = UDim2.fromOffset(200, 16),
        Font = Enum.Font.GothamBold, TextSize = 13, TextColor3 = C_WHITE, TextStrokeTransparency = 0,
        Text = plr.DisplayName, Visible = false, ZIndex = 6, Parent = ESPRoot,
    })
    d.DistTxt = New("TextLabel", {
        AnchorPoint = Vector2.new(0.5, 0), BackgroundTransparency = 1, Size = UDim2.fromOffset(120, 14),
        Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = C_GRAY, TextStrokeTransparency = 0.3,
        Visible = false, ZIndex = 6, Parent = ESPRoot,
    })
    d.HPBg = NewRect(Color3.fromRGB(20, 20, 20), 5)
    d.HPBg.BackgroundTransparency = 0.3
    d.HPFill = NewRect(Color3.fromRGB(50, 220, 80), 6)
    d.HPFill.AnchorPoint = Vector2.new(0, 1)
    -- Tracer e Bones são criados sob demanda (d.Tracer / d.Bones)
    ESPStorage[plr] = d
    return d
end

local function HideESP(d)
    if not d.Shown then return end
    d.Shown = false
    d.Box.Visible = false; d.NameTxt.Visible = false; d.DistTxt.Visible = false
    d.HPBg.Visible = false; d.HPFill.Visible = false
    if d.Tracer then d.Tracer.F.Visible = false end
    if d.Bones then for i = 1, #d.Bones do d.Bones[i].F.Visible = false end end
end

-- verde = visível / vermelho = atrás de parede (caixa, tracer e esqueleto)
local function PaintESP(d, col)
    d.Col = col
    d.BoxStroke.Color = col
    if d.Tracer then d.Tracer.F.BackgroundColor3 = col end
    if d.Bones then for i = 1, #d.Bones do d.Bones[i].F.BackgroundColor3 = col end end
end

-- visibilidade: raio da câmera até a cabeça ou o corpo. Personagens não contam como parede.
local espRay = RaycastParams.new()
espRay.FilterType = Enum.RaycastFilterType.Exclude
espRay.IgnoreWater = true
pcall(function() espRay.RespectCanCollide = true end)
local espFilterT = 0
local visCache = setmetatable({}, { __mode = "k" })

local function IsVisible(plr, c, camPos, now)
    local e = visCache[plr]
    if not e then e = { t = -1, v = true }; visCache[plr] = e end
    if now - e.t >= 0.08 then -- reavalia ~12x por segundo (economiza raycast)
        e.t = now
        e.v = not workspace:Raycast(camPos, c.Head.Position - camPos, espRay)
            or not workspace:Raycast(camPos, c.Root.Position - camPos, espRay)
    end
    return e.v
end

local function CleanPlayer(plr)
    local d = ESPStorage[plr]
    if d then
        d.Box:Destroy(); d.NameTxt:Destroy(); d.DistTxt:Destroy(); d.HPBg:Destroy(); d.HPFill:Destroy()
        if d.Tracer then d.Tracer.F:Destroy() end
        if d.Bones then for _, l in ipairs(d.Bones) do l.F:Destroy() end end
        ESPStorage[plr] = nil
    end
    local hl = HighlightStorage[plr]
    if hl then pcall(function() hl:Destroy() end); HighlightStorage[plr] = nil end
    PartCache[plr] = nil
    visCache[plr] = nil
end

local espCleared = true -- true = tudo do ESP (inclusive Highlight) já está escondido
local function ClearESP()
    for _, d in pairs(ESPStorage) do HideESP(d) end
    for _, hl in pairs(HighlightStorage) do if hl.Enabled then hl.Enabled = false end end
end

local function HookPlayer(plr)
    if plr == LP then return end
    Connect(plr.CharacterAdded, function(char)
        local hum = char:WaitForChild("Humanoid", 5)
        if hum then HideNative(hum, WantHideNative()) end
    end)
end
for _, p in ipairs(Players:GetPlayers()) do HookPlayer(p) end
Connect(Players.PlayerAdded, HookPlayer)
Connect(Players.PlayerRemoving, CleanPlayer)
ApplyNativeNames()

local projCache = {}
local function Proj(part)
    local v = projCache[part]
    if not v then v = Camera:WorldToViewportPoint(part.Position); projCache[part] = v end
    return v
end

local function UpdateESP(plrs, sx, sy, guiW, guiH)
    local V = Config.Visual
    if not V.Enabled then -- ESP desligado: esconde tudo (Highlight incluso) uma vez e não gasta mais nada
        if not espCleared then ClearESP(); espCleared = true end
        return
    end
    espCleared = false

    local now = os.clock()
    local camPos = Camera.CFrame.Position
    if now - espFilterT >= 0.5 then -- lista de personagens ignorados pelo raio de visibilidade
        espFilterT = now
        local list = {}
        for _, p in ipairs(plrs) do
            if p.Character then list[#list + 1] = p.Character end
        end
        espRay.FilterDescendantsInstances = list
    end

    local myChar = LP.Character
    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
    local origin = myRoot and myRoot.Position or camPos
    local maxDist = V.MaxDistance

    for _, plr in ipairs(plrs) do
        if plr == LP then continue end
        local d, hl, c = ESPStorage[plr], HighlightStorage[plr], GetParts(plr)

        -- só aparece quem está vivo E é do time rival
        if not c or c.Hum.Health <= 0 or not IsEnemyTeam(plr) then
            if d then HideESP(d) end
            if hl and hl.Enabled then hl.Enabled = false end
            continue
        end

        local root, head, hum = c.Root, c.Head, c.Hum
        local dist = (root.Position - origin).Magnitude
        local inRange = dist <= maxDist
        local vis = inRange and IsVisible(plr, c, camPos, now)
        local col = vis and C_GREEN or C_RED

        -- Highlight (o Roblox renderiza no máx. ~31 ao mesmo tempo)
        if V.Highlight and inRange then
            if not hl or not hl.Parent then
                hl = New("Highlight", {
                    FillTransparency = 0.72, FillColor = col, OutlineColor = col,
                    DepthMode = Enum.HighlightDepthMode.AlwaysOnTop, Adornee = c.Char, Parent = CoreGui,
                })
                HighlightStorage[plr] = hl
            else
                if hl.Adornee ~= c.Char then hl.Adornee = c.Char end
                if not hl.Enabled then hl.Enabled = true end
                if hl.FillColor ~= col then hl.FillColor = col; hl.OutlineColor = col end
            end
        elseif hl and hl.Enabled then
            hl.Enabled = false
        end

        if not inRange then
            if d then HideESP(d) end
            continue
        end

        -- Caixa: só 2 projeções (topo da cabeça e pés) + proporção fixa
        local rp = root.Position
        local footY = rp.Y - root.Size.Y / 2 - (c.R15 and hum.HipHeight or 2)
        local tp = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, head.Size.Y / 2 + 0.15, 0))
        local bp = Camera:WorldToViewportPoint(Vector3.new(rp.X, footY, rp.Z))
        if tp.Z <= 0 or bp.Z <= 0 then
            if d then HideESP(d) end
            continue
        end

        local topY  = math.floor(tp.Y * sy + 0.5)
        local botY  = math.floor(bp.Y * sy + 0.5)
        local h     = math.max(botY - topY, 2)
        local w     = math.max(math.floor(h * OPTIONS.BoxAspect + 0.5), 2)
        local midX  = math.floor(((tp.X + bp.X) / 2) * sx + 0.5)
        local left  = midX - math.floor(w / 2)
        local right = left + w

        if right < 0 or left > guiW or botY < 0 or topY > guiH then
            if d then HideESP(d) end
            continue
        end

        d = d or GetESP(plr)
        d.Shown = true
        if d.Col ~= col then PaintESP(d, col) end

        if V.Box then
            d.Box.Position = UDim2.fromOffset(left, topY)
            d.Box.Size = UDim2.fromOffset(w, h)
            SetVis(d.Box, true)
        else SetVis(d.Box, false) end

        if V.Name then
            d.NameTxt.Position = UDim2.fromOffset(midX, topY - 3)
            SetVis(d.NameTxt, true)
        else SetVis(d.NameTxt, false) end

        if V.Dist then
            local di = math.floor(dist)
            if d.LastDist ~= di then d.LastDist = di; d.DistTxt.Text = di .. "m" end
            d.DistTxt.Position = UDim2.fromOffset(midX, botY + 3)
            SetVis(d.DistTxt, true)
        else SetVis(d.DistTxt, false) end

        if V.HP then
            local pct = math.clamp(hum.Health / math.max(hum.MaxHealth, 1), 0, 1)
            local bx = right + 4
            d.HPBg.Position = UDim2.fromOffset(bx - 1, topY - 1)
            d.HPBg.Size = UDim2.fromOffset(5, h + 2)
            d.HPFill.Position = UDim2.fromOffset(bx, botY)
            d.HPFill.Size = UDim2.fromOffset(3, math.max(math.floor(h * pct + 0.5), 1))
            d.HPFill.BackgroundColor3 = Color3.fromHSV(pct * 0.33, 0.9, 0.95)
            SetVis(d.HPBg, true); SetVis(d.HPFill, true)
        else SetVis(d.HPBg, false); SetVis(d.HPFill, false) end

        if V.Tracer then
            d.Tracer = d.Tracer or NewLine(d.Col, 1.5)
            SetLine(d.Tracer, guiW / 2, guiH, midX, botY)
        elseif d.Tracer then
            SetVis(d.Tracer.F, false)
        end

        local n = 0
        if V.Skeleton and dist <= OPTIONS.SkeletonMaxDist then
            table.clear(projCache)
            local list = d.Bones
            if not list then list = {}; d.Bones = list end
            local bones = GetBones(c)
            for i = 1, #bones do
                local a, b = Proj(bones[i][1]), Proj(bones[i][2])
                if a.Z > 0 and b.Z > 0 then
                    n += 1
                    local line = list[n]
                    if not line then line = NewLine(d.Col, 1); list[n] = line end
                    SetLine(line, a.X * sx, a.Y * sy, b.X * sx, b.Y * sy)
                end
            end
        end
        if d.Bones then
            for i = n + 1, #d.Bones do SetVis(d.Bones[i].F, false) end
        end
    end
end

-- ── AIMBOT ─────────────────────────────────────────────────────────
local rayParams = RaycastParams.new()
rayParams.FilterType = Enum.RaycastFilterType.Exclude
rayParams.IgnoreWater = true
pcall(function() rayParams.RespectCanCollide = true end)

local function GetTarget(plrs, vp)
    local myChar = LP.Character
    if not myChar then return nil end
    local A = Config.Aimbot
    local camPos = Camera.CFrame.Position
    local cx, cy = vp.X / 2, vp.Y / 2
    local best, bestDist = nil, A.FOV

    for _, plr in ipairs(plrs) do
        if plr == LP then continue end
        -- sem times (nil == nil) o TeamCheck ignoraria todo mundo
        if A.TeamCheck and LP.Team ~= nil and plr.Team == LP.Team then continue end
        local c = GetParts(plr)
        if not c or c.Hum.Health <= 0 then continue end

        local headPos = c.Head.Position
        if (headPos - camPos).Magnitude > A.MaxDistance then continue end
        local sp, onScreen = Camera:WorldToViewportPoint(headPos)
        if not onScreen then continue end

        local dx, dy = sp.X - cx, sp.Y - cy
        local screenDist = math.sqrt(dx * dx + dy * dy)
        if screenDist >= bestDist then continue end -- raycast só p/ quem pode ser o melhor

        if A.WallCheck then
            rayParams.FilterDescendantsInstances = { myChar, c.Char }
            if workspace:Raycast(camPos, headPos - camPos, rayParams) then continue end
        end
        bestDist, best = screenDist, c.Head
    end
    return best
end

local function AimStep(plrs, vp, dt)
    local head = GetTarget(plrs, vp)
    if not head then return end
    local lvl = Config.Aimbot.SmoothLevel
    local alpha = lvl <= 0 and 1 or ((1 - lvl / 10) * 0.48 + 0.02)
    alpha = 1 - (1 - alpha) ^ math.clamp(dt * 60, 0.1, 6) -- independente do FPS
    local cf = Camera.CFrame
    Camera.CFrame = cf:Lerp(CFrame.lookAt(cf.Position, head.Position), alpha)
end

-- ── GUI: MEDIDAS ───────────────────────────────────────────────────
local PANEL_W, PANEL_H = 260, 260
local FAB_SIZE         = 34
local HEADER_H, TAB_H  = 36, 26
local PAD              = 8
local ROW_H, SLIDER_H  = 30, 42
local TAB_Y            = HEADER_H + 6
local PAGE_Y           = TAB_Y + TAB_H + 6

local SG = MakeGui("DeltaMobile", 100) -- acima do ESP

-- ── GUI: BOTÃO FLUTUANTE (posição salva) ───────────────────────────
local FB = New("ImageButton", {
    Name = "Fab", Size = UDim2.fromOffset(FAB_SIZE, FAB_SIZE),
    Position = UDim2.new(Config.UI.FabXScale, Config.UI.FabXOffset, Config.UI.FabYScale, Config.UI.FabYOffset),
    BackgroundColor3 = BG_MAIN, Image = "", AutoButtonColor = true, ClipsDescendants = true, Parent = SG,
})
Corner(FB)
New("UIGradient", { Color = ColorSequence.new(ACCENT, ACCENT2), Rotation = 45, Parent = Stroke(FB, C_WHITE, 1.5) })
local FabIcon = New("ImageLabel", {
    AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromScale(0.7, 0.7),
    BackgroundTransparency = 1, Parent = FB,
})
SetImage(FabIcon, ASSETS.FabIcon)

-- mantém o botão flutuante dentro da tela (caso a posição salva fique fora)
local function ClampFab()
    local vp = Camera.ViewportSize
    if vp.X <= 0 or vp.Y <= 0 then return end
    local p = FB.Position
    local x = p.X.Scale * vp.X + p.X.Offset
    local y = p.Y.Scale * vp.Y + p.Y.Offset
    local nx = math.clamp(x, 0, math.max(vp.X - FAB_SIZE, 0))
    local ny = math.clamp(y, 0, math.max(vp.Y - FAB_SIZE, 0))
    if nx ~= x or ny ~= y then FB.Position = UDim2.fromOffset(nx, ny) end
end
ClampFab()

local function SaveFabPos()
    ClampFab()
    local p = FB.Position
    Config.UI.FabXScale, Config.UI.FabXOffset = p.X.Scale, p.X.Offset
    Config.UI.FabYScale, Config.UI.FabYOffset = p.Y.Scale, p.Y.Offset
    Save()
end

-- ── GUI: PAINEL ────────────────────────────────────────────────────
local Panel = New("Frame", {
    Name = "Panel", AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5),
    Size = UDim2.fromOffset(PANEL_W, PANEL_H), BackgroundColor3 = BG_MAIN, BorderSizePixel = 0,
    Visible = false, ClipsDescendants = true, Parent = SG,
})
Corner(Panel, 12)
Stroke(Panel, ACCENT, 1.2, 0.3)

-- fundo: a imagem aparece inteira, sem nada por cima
local PanelBg = New("ImageLabel", {
    Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, ImageTransparency = 0,
    ScaleType = Enum.ScaleType.Crop, Parent = Panel,
})
Corner(PanelBg, 12)
SetImage(PanelBg, ASSETS.PanelBg)

-- cabeçalho = única área que arrasta o painel
local Header = New("Frame", { Size = UDim2.new(1, 0, 0, HEADER_H), BackgroundTransparency = 1, Active = true, Parent = Panel })
Corner(New("Frame", {
    Size = UDim2.fromOffset(4, 16), Position = UDim2.fromOffset(0, (HEADER_H - 16) / 2),
    BackgroundColor3 = ACCENT, BorderSizePixel = 0, Parent = Header,
}))
New("TextLabel", {
    Size = UDim2.new(1, -56, 1, 0), Position = UDim2.fromOffset(14, 0), BackgroundTransparency = 1, RichText = true,
    Text = 'DELTA  <font size="10" color="rgb(165,165,180)">MOBILE v2</font>', TextColor3 = TXT_MAIN,
    Font = Enum.Font.GothamBlack, TextSize = 15, TextXAlignment = Enum.TextXAlignment.Left, Parent = Header,
})
local XBtn = New("TextButton", {
    Size = UDim2.fromOffset(24, 24), Position = UDim2.new(1, -PAD - 24, 0, (HEADER_H - 24) / 2),
    BackgroundColor3 = BG_WIDGET, Text = "✕", TextColor3 = TXT_SUB, Font = Enum.Font.GothamBold, TextSize = 12,
    Parent = Panel,
})
Corner(XBtn)
New("Frame", {
    Size = UDim2.new(1, -PAD * 2, 0, 1), Position = UDim2.fromOffset(PAD, HEADER_H),
    BackgroundColor3 = LINE, BackgroundTransparency = 0.4, BorderSizePixel = 0, Parent = Panel,
})

-- abas
local TabBar = New("Frame", {
    Size = UDim2.new(1, -PAD * 2, 0, TAB_H), Position = UDim2.fromOffset(PAD, TAB_Y), BackgroundTransparency = 1, Parent = Panel,
})
New("UIListLayout", { FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 4), Parent = TabBar })

local tabs = {}
local function SelectTab(i, instant)
    for j, t in ipairs(tabs) do
        local on = (j == i)
        t.page.Visible = on
        Tween(t.btn, { BackgroundColor3 = on and ACCENT or BG_TAB, TextColor3 = on and C_WHITE or TXT_SUB }, instant)
    end
end

local function AddTab(name)
    local btn = New("TextButton", {
        Size = UDim2.new(0.2, -4, 1, 0), BackgroundColor3 = BG_TAB, Text = name, TextColor3 = TXT_SUB,
        Font = Enum.Font.GothamBold, TextSize = 10, AutoButtonColor = false, Parent = TabBar,
    })
    Corner(btn, 7)
    local page = New("ScrollingFrame", {
        Size = UDim2.new(1, -PAD * 2, 1, -(PAGE_Y + PAD)), Position = UDim2.fromOffset(PAD, PAGE_Y),
        BackgroundTransparency = 1, BorderSizePixel = 0, ScrollBarThickness = 3, ScrollBarImageColor3 = ACCENT,
        CanvasSize = UDim2.new(), AutomaticCanvasSize = Enum.AutomaticSize.Y,
        ScrollingDirection = Enum.ScrollingDirection.Y, Visible = false, Parent = Panel,
    })
    New("UIListLayout", { Padding = UDim.new(0, 5), SortOrder = Enum.SortOrder.LayoutOrder, Parent = page })
    New("UIPadding", { PaddingRight = UDim.new(0, 6), PaddingBottom = UDim.new(0, 4), Parent = page })
    tabs[#tabs + 1] = { btn = btn, page = page }
    local idx = #tabs
    btn.Activated:Connect(function() SelectTab(idx) end)
    return page
end

local PAim   = AddTab("Aim")
local PMove  = AddTab("Move")
local PVis   = AddTab("ESP")
local PGfx   = AddTab("GFX")
local PSquid = AddTab("Squid")
SelectTab(1, true)

-- arraste centralizado (1 só handler global; respeita o dedo certo no multitouch)
local activeDrag
Connect(UserInputService.InputChanged, function(i)
    local a = activeDrag
    if not a then return end
    local t = a.input.UserInputType
    if (t == Enum.UserInputType.Touch and i == a.input)
        or (t == Enum.UserInputType.MouseButton1 and i.UserInputType == Enum.UserInputType.MouseMovement) then
        a.move(i.Position)
    end
end)
Connect(UserInputService.InputEnded, function(i)
    local a = activeDrag
    if not a then return end
    local t = a.input.UserInputType
    if (t == Enum.UserInputType.Touch and i == a.input)
        or (t == Enum.UserInputType.MouseButton1 and i.UserInputType == t) then
        activeDrag = nil
        if a.finish then a.finish() end
    end
end)

local function IsPointer(i)
    return i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1
end

local function MakeDraggable(handle, target, onFinish)
    local info = { moved = false }
    handle.InputBegan:Connect(function(i)
        if not IsPointer(i) then return end
        local startPos, startUI = i.Position, target.Position
        info.moved = false
        activeDrag = {
            input = i,
            move = function(pos)
                local dx, dy = pos.X - startPos.X, pos.Y - startPos.Y
                if math.abs(dx) + math.abs(dy) > 6 then info.moved = true end
                if info.moved then
                    target.Position = UDim2.new(startUI.X.Scale, startUI.X.Offset + dx, startUI.Y.Scale, startUI.Y.Offset + dy)
                end
            end,
            finish = function()
                if info.moved and onFinish then onFinish() end
            end,
        }
    end)
    return info
end

-- ── GUI: WIDGETS ───────────────────────────────────────────────────
local pageOrder = setmetatable({}, { __mode = "k" })
local function NextOrder(page)
    local n = (pageOrder[page] or 0) + 1
    pageOrder[page] = n
    return n
end

local function Row(page, h)
    local f = New("Frame", {
        Size = UDim2.new(1, 0, 0, h), BackgroundColor3 = BG_WIDGET, BorderSizePixel = 0,
        LayoutOrder = NextOrder(page), Parent = page,
    })
    Corner(f, 8)
    Stroke(f, LINE, 1, 0.5)
    return f
end

local function Section(page, text)
    local f = New("Frame", { Size = UDim2.new(1, 0, 0, 20), BackgroundTransparency = 1, LayoutOrder = NextOrder(page), Parent = page })
    New("TextLabel", {
        Size = UDim2.new(1, 0, 1, -3), Position = UDim2.fromOffset(2, 0), BackgroundTransparency = 1, Text = text,
        TextColor3 = ACCENT2, Font = Enum.Font.GothamBold, TextSize = 10,
        TextXAlignment = Enum.TextXAlignment.Left, TextYAlignment = Enum.TextYAlignment.Bottom, Parent = f,
    })
    New("Frame", {
        Size = UDim2.new(1, 0, 0, 1), Position = UDim2.new(0, 0, 1, -1), BackgroundColor3 = LINE,
        BackgroundTransparency = 0.4, BorderSizePixel = 0, Parent = f,
    })
    return f
end

local function Toggle(page, text, default, cb)
    local state = default and true or false
    local f = Row(page, ROW_H)
    New("TextLabel", {
        Size = UDim2.new(1, -64, 1, 0), Position = UDim2.fromOffset(12, 0), BackgroundTransparency = 1,
        Text = text, TextColor3 = TXT_MAIN, Font = Enum.Font.Gotham, TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Left, TextTruncate = Enum.TextTruncate.AtEnd, Parent = f,
    })
    local ON_POS, OFF_POS = UDim2.new(0, 28, 0.5, 0), UDim2.new(0, 10, 0.5, 0)
    local track = New("Frame", {
        Size = UDim2.fromOffset(38, 20), Position = UDim2.new(1, -48, 0.5, -10),
        BackgroundColor3 = state and ACCENT or OFF_COL, BorderSizePixel = 0, Parent = f,
    })
    Corner(track)
    local knob = New("Frame", {
        AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(14, 14), Position = state and ON_POS or OFF_POS,
        BackgroundColor3 = C_WHITE, BorderSizePixel = 0, Parent = track,
    })
    Corner(knob)
    local btn = New("TextButton", { Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", Parent = f })

    local function paint(s)
        Tween(track, { BackgroundColor3 = s and ACCENT or OFF_COL }, false, 0.15)
        Tween(knob, { Position = s and ON_POS or OFF_POS }, false, 0.15)
    end
    btn.Activated:Connect(function()
        state = not state
        paint(state)
        cb(state)
        Save()
    end)
    return { Set = function(s) state = s and true or false; paint(state) end }
end

local function Slider(page, text, mn, mx, def, cb)
    local value = math.clamp(def, mn, mx)
    local f = Row(page, SLIDER_H)
    f.Active = true
    New("TextLabel", {
        Size = UDim2.new(1, -78, 0, 16), Position = UDim2.fromOffset(12, 5), BackgroundTransparency = 1,
        Text = text, TextColor3 = TXT_MAIN, Font = Enum.Font.Gotham, TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Left, TextTruncate = Enum.TextTruncate.AtEnd, Parent = f,
    })
    local val = New("TextLabel", {
        Size = UDim2.fromOffset(60, 16), Position = UDim2.new(1, -72, 0, 5), BackgroundTransparency = 1,
        Text = tostring(value), TextColor3 = ACCENT2, Font = Enum.Font.GothamBold, TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Right, Parent = f,
    })
    local bar = New("Frame", {
        Size = UDim2.new(1, -24, 0, 6), Position = UDim2.new(0, 12, 1, -16),
        BackgroundColor3 = BAR_BG, BorderSizePixel = 0, Parent = f,
    })
    Corner(bar)
    local fill = New("Frame", { BackgroundColor3 = ACCENT, BorderSizePixel = 0, Parent = bar })
    Corner(fill)
    local knob = New("Frame", {
        AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(12, 12), BackgroundColor3 = C_WHITE,
        BorderSizePixel = 0, ZIndex = 2, Parent = bar,
    })
    Corner(knob)

    local function paint(v)
        local rel = (v - mn) / (mx - mn)
        fill.Size = UDim2.fromScale(rel, 1)
        knob.Position = UDim2.fromScale(rel, 0.5)
        val.Text = tostring(v)
    end
    paint(value)

    local function update(x)
        local rel = math.clamp((x - bar.AbsolutePosition.X) / math.max(bar.AbsoluteSize.X, 1), 0, 1)
        local v = math.floor(mn + (mx - mn) * rel + 0.5)
        if v == value then return end
        value = v
        paint(v)
        cb(v)
        Save()
    end
    -- a área inteira do widget é "pegável" (barra fina é pequena no dedo)
    f.InputBegan:Connect(function(i)
        if not IsPointer(i) then return end
        page.ScrollingEnabled = false
        activeDrag = {
            input = i,
            move = function(pos) update(pos.X) end,
            finish = function() page.ScrollingEnabled = true end,
        }
        update(i.Position.X)
    end)
end

local function Button(page, text, cb, danger)
    local b = New("TextButton", {
        Size = UDim2.new(1, 0, 0, ROW_H), BackgroundColor3 = BG_WIDGET, BorderSizePixel = 0, Text = text,
        TextColor3 = danger and C_RED or TXT_MAIN, Font = Enum.Font.GothamBold, TextSize = 12,
        AutoButtonColor = true, LayoutOrder = NextOrder(page), Parent = page,
    })
    Corner(b, 8)
    Stroke(b, danger and C_RED or LINE, 1, danger and 0.6 or 0.5)
    b.Activated:Connect(cb)
    return b
end

local A, M, V = Config.Aimbot, Config.Movement, Config.Visual
local Q = Config.Squid
local Unload -- definido no fim

-- aviso rápido na tela (o celular não mostra o console)
local toastLabel, toastGen = nil, 0
local function Toast(text)
    if not toastLabel then
        toastLabel = New("TextLabel", {
            AnchorPoint = Vector2.new(0.5, 1), Position = UDim2.new(0.5, 0, 1, -90),
            Size = UDim2.fromOffset(320, 34), BackgroundColor3 = BG_MAIN, BackgroundTransparency = 0.1,
            Text = "", TextColor3 = TXT_MAIN, Font = Enum.Font.GothamBold, TextSize = 12,
            TextWrapped = true, Visible = false, ZIndex = 20, Parent = SG,
        })
        Corner(toastLabel, 8)
        Stroke(toastLabel, ACCENT, 1)
    end
    toastGen += 1
    local my = toastGen
    toastLabel.Text = text
    toastLabel.Visible = true
    task.delay(2.2, function()
        if toastGen == my and toastLabel then toastLabel.Visible = false end
    end)
end

-- ── SQUID GAME X: LÓGICA ───────────────────────────────────────────
local function MyParts()
    local char = LP.Character
    local hum  = char and char:FindFirstChildOfClass("Humanoid")
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if hum and root and hum.Health > 0 then return char, hum, root end
    return nil
end

local function TeleportTo(cf)
    local _, _, root = MyParts()
    if not root then return false end
    root.CFrame = cf
    root.AssemblyLinearVelocity = Vector3.zero
    root.AssemblyAngularVelocity = Vector3.zero
    return true
end

local function Has(str, keys)
    str = str:lower()
    for i = 1, #keys do
        if str:find(keys[i], 1, true) then return true end
    end
    return false
end

-- ── Luz Vermelha / Teleportes ──────────────────────────────────────
-- { padrão, palavra inteira?, prioridade (menor = melhor) }
local FINISH_KEYS = {
    { "finish", false, 1 }, { "chegada", false, 1 }, { "goal", false, 2 },
    { "endzone", false, 2 }, { "end_zone", false, 2 }, { "endline", false, 2 },
    { "winner", false, 3 }, { "win", true, 3 }, { "fim", true, 3 }, { "end", true, 4 },
}

local function FinishPriority(name)
    name = name:lower()
    local best
    for _, k in ipairs(FINISH_KEYS) do
        local hit
        if k[2] then hit = name:find("%f[%a]" .. k[1] .. "%f[%A]") else hit = name:find(k[1], 1, true) end
        if hit and (not best or k[3] < best) then best = k[3] end
    end
    return best
end

local function FindFinish()
    local _, _, myRoot = MyParts()
    local origin = myRoot and myRoot.Position or Camera.CFrame.Position
    local best, bestPrio, bestDist
    for _, o in ipairs(workspace:GetDescendants()) do
        if o:IsA("BasePart") then
            local prio = FinishPriority(o.Name)
            if not prio and o.Parent and o.Parent ~= workspace then
                local pp = FinishPriority(o.Parent.Name)
                if pp then prio = pp + 0.5 end
            end
            if prio and not IsCharacterPart(o) then
                local d = (o.Position - origin).Magnitude
                if d > 15 and (not best or prio < bestPrio or (prio == bestPrio and d < bestDist)) then
                    best, bestPrio, bestDist = o, prio, d
                end
            end
        end
    end
    return best
end

local function TeleportFinish()
    local part = FindFinish()
    if not part then
        Toast("Chegada não encontrada. Use 'Salvar posição' ou o Debug.")
        return
    end
    if TeleportTo(CFrame.new(part.Position + Vector3.new(0, part.Size.Y / 2 + 4, 0))) then
        Toast("Teleportado para a chegada")
    end
end

local savedCF
local function SavePos()
    local _, _, root = MyParts()
    if not root then return end
    savedCF = root.CFrame
    Toast("Posição salva")
end

local function TeleportSaved()
    if not savedCF then Toast("Nenhuma posição salva"); return end
    if TeleportTo(savedCF + Vector3.new(0, 2, 0)) then Toast("Teleportado para a posição salva") end
end

-- ── Combate: grudar atrás do inimigo / auto atacar ─────────────────
-- mesma detecção de time do ESP; o toggle "Só time inimigo" liga/desliga o filtro
local function IsEnemy(plr)
    if plr == LP then return false end
    if not Q.EnemyOnly then return true end
    return IsEnemyTeam(plr)
end

local sqTarget

local function ValidTarget(plr)
    if not plr or plr.Parent ~= Players or not IsEnemy(plr) then return false end
    local c = GetParts(plr)
    if not c or c.Hum.Health <= 0 then return false end
    if c.Char:FindFirstChildOfClass("ForceField") then return false end
    return true
end

local function SortedEnemies()
    local _, _, myRoot = MyParts()
    if not myRoot then return {} end
    local list = {}
    for _, plr in ipairs(Players:GetPlayers()) do
        if ValidTarget(plr) then
            local c = GetParts(plr)
            list[#list + 1] = { plr = plr, d = (c.Root.Position - myRoot.Position).Magnitude }
        end
    end
    table.sort(list, function(a, b) return a.d < b.d end)
    return list
end

local function PickTarget()
    local list = SortedEnemies()
    return list[1] and list[1].plr or nil
end

local function CycleTarget()
    local list = SortedEnemies()
    if #list == 0 then Toast("Nenhum inimigo encontrado"); return end
    local idx = 0
    for i, e in ipairs(list) do
        if e.plr == sqTarget then idx = i; break end
    end
    sqTarget = list[(idx % #list) + 1].plr
    Toast("Alvo: " .. sqTarget.DisplayName)
end

local function BehindCF(c) -- posição nas costas do alvo (distância fixa), olhando para ele
    local tr = c.Root
    local pos = (tr.CFrame * CFrame.new(0, 0, OPTIONS.StickDist)).Position
    return CFrame.lookAt(pos, Vector3.new(tr.Position.X, pos.Y, tr.Position.Z))
end

local function TeleportBehindNearest()
    local plr = PickTarget()
    if not plr then Toast("Nenhum inimigo encontrado"); return end
    sqTarget = plr
    local c = GetParts(plr)
    if c and TeleportTo(BehindCF(c)) then Toast("TP atrás de " .. plr.DisplayName) end
end

local function StickStep()
    local _, _, myRoot = MyParts()
    if not myRoot then return end
    if not ValidTarget(sqTarget) then
        sqTarget = PickTarget()
        if not sqTarget then return end
    end
    local c = GetParts(sqTarget)
    if not c then sqTarget = nil; return end
    myRoot.CFrame = BehindCF(c)
    myRoot.AssemblyLinearVelocity = Vector3.zero
end

local WEAPON_KEYS = { "knife", "faca", "blade", "sword", "espada", "dagger", "punhal", "shiv", "punch", "soco", "fist", "push", "empurr", "bat" }
local lastAtk = 0
local VIM
pcall(function() VIM = game:GetService("VirtualInputManager") end)

local function AttackStep()
    local now = os.clock()
    if now - lastAtk < 0.2 then return end
    local char, hum, myRoot = MyParts()
    if not myRoot then return end

    local near = false -- só ataca se houver inimigo por perto
    for _, plr in ipairs(Players:GetPlayers()) do
        if ValidTarget(plr) then
            local c = GetParts(plr)
            if c and (c.Root.Position - myRoot.Position).Magnitude <= 12 then
                near = true
                break
            end
        end
    end
    if not near then return end
    lastAtk = now

    local tool = char:FindFirstChildOfClass("Tool")
    if not tool then
        local bp = LP:FindFirstChildOfClass("Backpack")
        if bp then
            for _, t in ipairs(bp:GetChildren()) do
                if t:IsA("Tool") and Has(t.Name, WEAPON_KEYS) then
                    pcall(function() hum:EquipTool(t) end)
                    tool = t
                    break
                end
            end
        end
    end

    if tool then
        if tool.Parent == char then pcall(function() tool:Activate() end) end
    elseif VIM then -- sem arma na mão/mochila: tenta clique no centro da tela (soco/empurrão)
        local vp = Camera.ViewportSize
        pcall(function()
            VIM:SendMouseButtonEvent(vp.X / 2, vp.Y / 2, 0, true, game, 0)
            VIM:SendMouseButtonEvent(vp.X / 2, vp.Y / 2, 0, false, game, 0)
        end)
    end
end

-- ── Diagnóstico (imprime no console do Delta) ──────────────────────
local function DebugDump()
    print("========== [Delta Mobile] DEBUG ==========")
    RefreshMyTeam()
    print(("-- Meu time: fonte=%s | chave=%s"):format(tostring(myTeam.src), tostring(myTeam.key)))
    local cands = TeamCandidates(LP)
    for _, cnd in ipairs(cands) do print("  campo de time achado: " .. cnd.src) end
    if LP.Team == nil and #cands == 0 then
        print("  (nenhum campo de time achado; veja abaixo e use OPTIONS.ExtraTeamNames com o nome certo)")
    end
    for k, v in pairs(LP:GetAttributes()) do print("  atributo do player: " .. k .. " = " .. tostring(v)) end
    for _, ch in ipairs(LP:GetChildren()) do
        if ch:IsA("ValueBase") then print("  valor do player: " .. ch.Name .. " = " .. tostring(ch.Value)) end
    end
    local myChar = LP.Character
    if myChar then
        for k, v in pairs(myChar:GetAttributes()) do print("  atributo do personagem: " .. k .. " = " .. tostring(v)) end
    end
    print("-- Players (inimigo = time diferente do meu)")
    for _, plr in ipairs(Players:GetPlayers()) do
        print(("  %s | time=%s | inimigo=%s"):format(
            plr.Name, tostring(myTeam.src and ReadTeam(plr, myTeam.src)), tostring(IsEnemyTeam(plr))))
    end

    local _, _, myRoot = MyParts()
    local origin = myRoot and myRoot.Position or Camera.CFrame.Position
    local list = {}
    for _, o in ipairs(workspace:GetDescendants()) do
        if o:IsA("BasePart") and not IsCharacterPart(o) then
            local p = FinishPriority(o.Name)
            if p then list[#list + 1] = { o = o, d = (o.Position - origin).Magnitude, p = p } end
        end
    end
    table.sort(list, function(a, b) return a.d < b.d end)
    print("-- Candidatos de chegada (mais perto primeiro, até 8): " .. #list .. " no total")
    for i = 1, math.min(#list, 8) do
        print(("  %s | prio=%d | %d studs"):format(list[i].o:GetFullName(), list[i].p, list[i].d))
    end
    print("================================================")
    Toast("Debug impresso no console do Delta")
end

-- ── GUI: CONTEÚDO DAS ABAS ─────────────────────────────────────────
-- AIM
Section(PAim, "AIMBOT")
Toggle(PAim, "Aimbot", A.Enabled, function(s) A.Enabled = s end)
Slider(PAim, "Suavidade", 0, 10, A.SmoothLevel, function(n) A.SmoothLevel = n end)
Slider(PAim, "FOV Aim", 40, 400, A.FOV, function(n) A.FOV = n end)
Slider(PAim, "Dist Aim (m)", 50, 1000, A.MaxDistance, function(n) A.MaxDistance = n end)
Section(PAim, "FILTROS")
Toggle(PAim, "Mostrar FOV", A.ShowFOV, function(s) A.ShowFOV = s end)
Toggle(PAim, "Wall Check", A.WallCheck, function(s) A.WallCheck = s end)
Toggle(PAim, "Team Check", A.TeamCheck, function(s) A.TeamCheck = s end)

-- MOVE
Section(PMove, "VELOCIDADE")
Toggle(PMove, "Speed Hack", M.SpeedEnabled, function(s)
    M.SpeedEnabled = s
    if not s then RestoreSpeed() end
end)
Slider(PMove, "Velocidade", 16, 120, M.Speed, function(n) M.Speed = n end)
Section(PMove, "VOO E COLISÃO")
FlyToggle = Toggle(PMove, "Fly", false, function(s)
    M.Fly = s
    if s then
        if not StartFly() then M.Fly = false; FlyToggle.Set(false) end
    else
        StopFly()
    end
end)
Slider(PMove, "Fly Speed", 20, 200, M.FlySpeed, function(n) M.FlySpeed = n end)
Toggle(PMove, "Noclip", M.Noclip, function(s)
    M.Noclip = s
    if not s then RestoreNoclip() end
end)

-- ESP
Section(PVis, "GERAL")
Toggle(PVis, "ESP Enabled", V.Enabled, function(s) V.Enabled = s; ApplyNativeNames() end)
Section(PVis, "ELEMENTOS")
Toggle(PVis, "ESP Box", V.Box, function(s) V.Box = s end)
Toggle(PVis, "ESP Nome", V.Name, function(s) V.Name = s; ApplyNativeNames() end)
Toggle(PVis, "ESP Distância", V.Dist, function(s) V.Dist = s end)
Toggle(PVis, "ESP HP", V.HP, function(s) V.HP = s end)
Toggle(PVis, "ESP Skeleton", V.Skeleton, function(s) V.Skeleton = s end)
Toggle(PVis, "ESP Tracer", V.Tracer, function(s) V.Tracer = s end)
Toggle(PVis, "Highlight", V.Highlight, function(s) V.Highlight = s end)
Section(PVis, "ALCANCE")
Slider(PVis, "Dist Máx (m)", 50, 1000, V.MaxDistance, function(n) V.MaxDistance = n end)

-- GFX
Section(PGfx, "DESEMPENHO")
Toggle(PGfx, "Low Poly (FPS)", false, SetLowPoly)
Toggle(PGfx, "Cartoon (sem textura)", false, SetCartoon)
Section(PGfx, "CÂMERA")
Toggle(PGfx, "Lock Shift (mobile)", SL.Enabled, SetShiftLock)
Section(PGfx, "SCRIPT")
Button(PGfx, "Descarregar Script", function() Unload() end, true)

-- SQUID
Section(PSquid, "LUZ VERMELHA / TELEPORTE")
Button(PSquid, "TP Chegada (auto)", TeleportFinish)
Button(PSquid, "Salvar posição atual", SavePos)
Button(PSquid, "TP posição salva", TeleportSaved)
Section(PSquid, "COMBATE (TIME INIMIGO)")
Toggle(PSquid, "Grudar no inimigo", false, function(s)
    Q.Stick = s
    if not s then sqTarget = nil end
end)
Toggle(PSquid, "Auto Atacar", false, function(s) Q.AutoAttack = s end)
Toggle(PSquid, "Só time inimigo", Q.EnemyOnly, function(s) Q.EnemyOnly = s; sqTarget = nil end)
Button(PSquid, "TP atrás do inimigo (1x)", TeleportBehindNearest)
Button(PSquid, "Trocar alvo", CycleTarget)
Section(PSquid, "DIAGNÓSTICO")
Button(PSquid, "Debug (imprime no console)", DebugDump)

-- abrir / fechar
local panelOpen = false
local function TogglePanel()
    panelOpen = not panelOpen
    if panelOpen then
        Panel.Visible = true
        Panel.Size = UDim2.fromOffset(10, 10)
        TweenService:Create(Panel, TweenInfo.new(0.22, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
            { Size = UDim2.fromOffset(PANEL_W, PANEL_H) }):Play()
    else
        local tw = TweenService:Create(Panel, TweenInfo.new(0.16, Enum.EasingStyle.Quad, Enum.EasingDirection.In),
            { Size = UDim2.fromOffset(10, 10) })
        tw.Completed:Connect(function() if not panelOpen then Panel.Visible = false end end)
        tw:Play()
    end
end

local FBDrag = MakeDraggable(FB, FB, SaveFabPos) -- salva a posição ao soltar
MakeDraggable(Header, Panel)
FB.Activated:Connect(function() if not FBDrag.moved then TogglePanel() end end)
XBtn.Activated:Connect(function() if panelOpen then TogglePanel() end end)

-- ── LOOP PRINCIPAL ─────────────────────────────────────────────────
-- Roda DEPOIS da câmera padrão do Roblox (Camera + 1): sem tremor/atraso
-- do ESP e sem briga com o aimbot. O Lock Shift vem primeiro, assim o aim
-- e o ESP já enxergam a câmera deslocada.
local function Step(dt)
    local cam = workspace.CurrentCamera
    if not cam then return end
    Camera = cam
    local vp = cam.ViewportSize
    if vp.X <= 0 or vp.Y <= 0 then return end

    Safe("ShiftLock", ShiftLockStep, dt)

    local ref = ESPRoot.AbsoluteSize
    local sx, sy = ref.X / vp.X, ref.Y / vp.Y
    if sx <= 0 or sy <= 0 then sx, sy = 1, 1 end
    local guiW, guiH = vp.X * sx, vp.Y * sy

    local showFOV = A.Enabled and A.ShowFOV
    if showFOV then
        local r = A.FOV * (sx + sy) / 2
        FOVCircle.Position = UDim2.fromOffset(guiW / 2, guiH / 2)
        FOVCircle.Size = UDim2.fromOffset(r * 2, r * 2)
    end
    SetVis(FOVCircle, showFOV)

    local plrs = (A.Enabled or V.Enabled) and Players:GetPlayers() or nil -- só cria a lista se alguém usar
    if A.Enabled then Safe("Aimbot", AimStep, plrs, vp, dt) end
    if flyActive then Safe("Fly", FlyStep) end
    if M.SpeedEnabled then Safe("Speed", SpeedStep) end
    if Q.Stick then Safe("Stick", StickStep) end
    if Q.AutoAttack then Safe("Attack", AttackStep) end
    Safe("ESP", UpdateESP, plrs, sx, sy, guiW, guiH)
end

pcall(function() RunService:UnbindFromRenderStep("DeltaMobileShiftPre") end)
pcall(function() RunService:UnbindFromRenderStep("DeltaMobileStep") end)
RunService:BindToRenderStep("DeltaMobileShiftPre", Enum.RenderPriority.Camera.Value - 1, UndoShiftCam)
RunService:BindToRenderStep("DeltaMobileStep", Enum.RenderPriority.Camera.Value + 1, Step)
Connect(RunService.Stepped, function() Safe("Noclip", NoclipStep) end)
Connect(LP.CharacterAdded, function() StopFly() end)

-- ── DESCARREGAR ────────────────────────────────────────────────────
Unload = function()
    pcall(function() RunService:UnbindFromRenderStep("DeltaMobileShiftPre") end)
    pcall(function() RunService:UnbindFromRenderStep("DeltaMobileStep") end)
    pcall(UndoShiftCam)
    for _, c in ipairs(conns) do pcall(function() c:Disconnect() end) end
    conns = {}
    pcall(StopFly)
    pcall(RestoreSpeed)
    pcall(RestoreNoclip)
    pcall(SetCartoon, false)
    pcall(SetLowPoly, false)
    pcall(ApplyLock, false, true) -- só desfaz o efeito; o estado salvo do Lock Shift continua
    Q.Stick, Q.AutoAttack = false, false
    for hum, t in pairs(NativeSaved) do pcall(function() hum.DisplayDistanceType = t end) end
    for _, hl in pairs(HighlightStorage) do pcall(function() hl:Destroy() end) end
    for _, g in ipairs(guis) do pcall(function() g:Destroy() end) end
    if genv.DeltaMobileUnload == Unload then genv.DeltaMobileUnload = nil end
end
genv.DeltaMobileUnload = Unload

-- ── SCRIPT REMOTO (por último, não bloqueia o carregamento) ────────
if OPTIONS.LoadEmotes then
    task.spawn(function()
        local ok, err = pcall(function() loadstring(game:HttpGet(OPTIONS.EmotesUrl))() end)
        if not ok then warn("[Delta Mobile] Emotes falhou: " .. tostring(err)) end
    end)
end

print("[Delta Mobile v2] Carregado!")
