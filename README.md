local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
                            
local Window = WindUI:CreateWindow({
    Title = "SCARLET HUB",
    Folder = "ScarletHub",
    Size = UDim2.fromOffset(600, 500),
    Transparent = true,
    Theme = "Dark",
    Resizable = false,
    SideBarWidth = 200,
    HideSearchBar = true,
    ScrollBarEnabled = false,
    OpenButton = {
        Title = "S",
        Enabled = true,
        OnlyMobile = true,
        Draggable = true,
        CornerRadius = UDim.new(0.2, 0),
    },
    User = {
        Enabled = true,
        Anonymous = false
    }
})

-- ================================ FUNÇÕES GLOBAIS ================================

local function Notify(message, type)
    WindUI:Notify({
        Title = "SCARLET HUB",
        Content = message,
        Duration = 3,
        Icon = "info"
    })
    print("[" .. type:upper() .. "] " .. message)
end

-- ================================ SERVIÇOS ================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- ================================ FUNÇÕES AUXILIARES ================================

local function GetChar()
    return player.Character
end

local function GetHRP(char)
    char = char or GetChar()
    return char and char:FindFirstChild("HumanoidRootPart")
end

local function GetHumanoid(char)
    char = char or GetChar()
    return char and char:FindFirstChild("Humanoid")
end

-- ================================ VARIÁVEIS GLOBAIS ================================

local AimbotEnabled = false
local LockCamEnabled = false
local ShowFPSEnabled = false
local FPSUnlocked = false
local backpackActive = false
local headsitActive = false
local spectateActive = false
local infJumpEnabled = false
local flingOPActive = false
local LockUIActive = false
local AutoFarmEnabled = false
local HideNameEnabled = false
local SpeedToggleEnabled = false
local FOVCamToggleEnabled = false
local SpinbotEnabled = false
local headlessActive = false
local korbloxActive = false
local flying = false
local ESPHighlightEnabled = false
local ESPMM2HighlightEnabled = false
local IgnoreWalls = false

local SelectedAimPart = "Head"
local AimbotFOV = 90
local AimbotSmoothness = 5
local SpeedValue = 16
local FovCamValue = 70
local FlySpeed = 50
local lastPositionsCount = 0

local lastPositions = {}
local FOVCircle = nil
local FOVCircleFrame = nil
local selectedPlayer = nil
local posicaoOriginal = nil
local savedPosition = nil
local lastPositionTP = nil
local originalHead = nil
local originalLeftLeg = nil
local bodyVelocity = nil
local bodyGyro = nil
local playerDropdown = nil
local selectedAnimationPack = nil
local NoClipToggle = nil
local AutoFarmToggleElement = nil

local LockUI
local MiraUI

local killAssassinActive = false
local killSheriffActive = false
local killAssassinConnection = nil
local killSheriffConnection = nil
local killAssassinPos = nil
local killSheriffPos = nil
local flingAllActive = false
local flingAllQueue = {}
local flingAllCurrentTarget = nil
local flingAllConnection = nil
local flingAllCheckConnection = nil

-- ================================ GERENCIADOR DE EVENTOS ================================

local EventManager = {
    events = {}
}

function EventManager:Add(name, connection)
    if self.events[name] then
        self.events[name]:Disconnect()
    end
    self.events[name] = connection
end

-- ================================ GERENCIADOR DE LOOPS ================================

local LoopManager = {
    tasks = {},
    conn = nil
}

function LoopManager:Add(name, callback)
    if self.tasks[name] then return end
    self.tasks[name] = callback
    if not self.conn then
        self.conn = RunService.Heartbeat:Connect(function(dt)
            if next(self.tasks) then
                for taskName, func in pairs(self.tasks) do
                    pcall(func, dt)
                end
            end
        end)
    end
end

function LoopManager:Remove(name)
    self.tasks[name] = nil
    if not next(self.tasks) and self.conn then
        self.conn:Disconnect()
        self.conn = nil
    end
end

-- ================================ CACHE DE MOEDAS ================================

local coinCache = {}
local coinCacheDirty = true
local lastCoinCacheUpdate = 0

local function atualizarCacheMoedas()
    if not coinCacheDirty then return end
    if tick() - lastCoinCacheUpdate < 2 then return end 
    lastCoinCacheUpdate = tick()
    
    coinCache = {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Transparency < 0.8 then
            local nome = obj.Name:lower()
            if nome:find("coin") or nome:find("gold") or nome:find("money") then
                table.insert(coinCache, obj)
            end
        end
    end
    coinCacheDirty = false
end

workspace.DescendantAdded:Connect(function()
    coinCacheDirty = true
end)

workspace.DescendantRemoving:Connect(function()
    coinCacheDirty = true
end)

-- ================================ CACHE DE PLAYERS ================================

local playerCache = {}
local playerCacheDirty = true

local function atualizarPlayerCache()
    if not playerCacheDirty then return end
    playerCache = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= player then
            table.insert(playerCache, plr)
        end
    end
    playerCacheDirty = false
end

Players.PlayerAdded:Connect(function()
    playerCacheDirty = true
end)

Players.PlayerRemoving:Connect(function()
    playerCacheDirty = true
end)

-- ================================ CACHE DE PAPEL ================================

local function getPapel(jogador)
    local char = jogador.Character
    if not char then 
        return "Inocente"
    end
    
    if char:FindFirstChild("Knife") or char:FindFirstChild("FadeKnife") then 
        return "Assassino"
    end    
    
    if char:FindFirstChild("Gun") or char:FindFirstChild("Pistol") or char:FindFirstChild("Revolver") then 
        return "Xerife"
    end
    
    if jogador.Backpack then
        if jogador.Backpack:FindFirstChild("Knife") or jogador.Backpack:FindFirstChild("FadeKnife") then 
            return "Assassino"
        end        
        
        if jogador.Backpack:FindFirstChild("Gun") or jogador.Backpack:FindFirstChild("Pistol") or jogador.Backpack:FindFirstChild("Revolver") then 
            return "Xerife"
        end
    end
    return "Inocente"
end

-- ================================ ANIMAÇÕES ================================

local ANIMATION_PACKS = {
    Sneaky = { Idle1 = "1132473842", Idle2 = "1132477671", Walk = "1132510133", Run = "1132494274", Jump = "1132489853", Fall = "1132461372", Climb = "1132469004", Swim = "1132513516", SwimIdle = "1132516639" },
    Patrol = { Idle1 = "1149612882", Idle2 = "1150842221", Walk = "1151231493", Run = "1150967949", Jump = "1148811837", Fall = "1148863382", Climb = "1148811837", Swim = "1151204998", SwimIdle = "1151220343" },
    Ghost = { Idle1 = "616006778", Idle2 = "616008087", Walk = "616013216", Run = "616010382", Jump = "616008936", Fall = "616005863", Climb = "616003713", Swim = "616011509", SwimIdle = "616012453" },
    AdidasSports = { Idle1 = "18537371272", Idle2 = "18537376492", Walk = "18537392113", Run = "18537384940", Jump = "18537380791", Fall = "18537367238", Climb = "18537363391", Swim = "18537397079", SwimIdle = "18537402320" },
    Levitation = { Idle1 = "616006778", Idle2 = "616008087", Walk = "616013216", Run = "616010382", Jump = "616008936", Fall = "616005863", Climb = "616003713", Swim = "616011509", SwimIdle = "616012453" },
    Werewolf = { Idle1 = "1083195517", Idle2 = "1083214717", Walk = "1083178339", Run = "1083216690", Jump = "1083218792", Fall = "1083189019", Climb = "1083182000", Swim = "1083222527", SwimIdle = "1083225406" },
    Vampire = { Idle1 = "1083445855", Idle2 = "1083450166", Walk = "1083473930", Run = "1083462077", Jump = "1083455352", Fall = "1083443587", Climb = "1083439238", Swim = "1083477583", SwimIdle = "1083480623" },
    Pirate = { Idle1 = "750781874", Idle2 = "750782770", Walk = "750785693", Run = "750783738", Jump = "750782230", Fall = "750780242", Climb = "750779899", Swim = "750784579", SwimIdle = "750785176" },
    Zombie = { Idle1 = "616158929", Idle2 = "616160636", Walk = "616168032", Run = "616163682", Jump = "616161997", Fall = "616157476", Climb = "616156119", Swim = "616165109", SwimIdle = "616166655" },
    Astronaut = { Idle1 = "891621366", Idle2 = "891633237", Walk = "891667138", Run = "891636393", Jump = "891627522", Fall = "891617961", Climb = "891609353", Swim = "891663592", SwimIdle = "891662115" },
    Ninja = { Idle1 = "656117400", Idle2 = "656118341", Walk = "656121766", Run = "656118852", Jump = "656117878", Fall = "656115606", Climb = "656114359", Swim = "656119721", SwimIdle = "656121397" },
    Mage = { Idle1 = "707742142", Idle2 = "707855907", Walk = "707897309", Run = "707861613", Jump = "707853694", Fall = "707829716", Climb = "707826056", Swim = "707876443", SwimIdle = "707894699" },
    Elder = { Idle1 = "845397899", Idle2 = "845400520", Walk = "845403856", Run = "845386501", Jump = "845398858", Fall = "845396048", Climb = "845392038", Swim = "845401742", SwimIdle = "845403127" },
    Toy = { Idle1 = "782841498", Idle2 = "782845736", Walk = "782843345", Run = "782842708", Jump = "782847020", Fall = "782846423", Climb = "782843869", Swim = "782844582", SwimIdle = "782845186" },
    Knight = { Idle1 = "657595757", Idle2 = "657568135", Walk = "657552124", Run = "657564596", Jump = "658409194", Fall = "657600338", Climb = "658360781", Swim = "657560551", SwimIdle = "657557095" },
    Cartoony = { Idle1 = "742637544", Idle2 = "742638445", Walk = "742640026", Run = "742638842", Jump = "742637942", Fall = "742637151", Climb = "742636889", Swim = "742639220", SwimIdle = "742639812" },
    SuperHero = { Idle1 = "616111295", Idle2 = "616113536", Walk = "616122287", Run = "616117076", Jump = "616115533", Fall = "616114533", Climb = "616104706", Swim = "616119360", SwimIdle = "616120861" },
    Bubbly = { Idle1 = "910004836", Idle2 = "910009958", Walk = "910034870", Run = "910025107", Jump = "910016857", Fall = "910001910", Climb = "910001910", Swim = "910028158", SwimIdle = "910030921" },
    Robot = { Idle1 = "616088211", Idle2 = "616089559", Walk = "616095330", Run = "616091570", Jump = "616090535", Fall = "616087089", Climb = "616086039", Swim = "616092998", SwimIdle = "616094091" },
    Stylish = { Idle1 = "616136790", Idle2 = "616138447", Walk = "616146177", Run = "616140816", Jump = "616139451", Fall = "616134815", Climb = "616133594", Swim = "616143378", SwimIdle = "616144772" },
    Confident = { Idle1 = "1069977950", Idle2 = "1069987858", Walk = "1070017263", Run = "1070001516", Jump = "1069984524", Fall = "1069973677", Climb = "1069946257", Swim = "1070009914", SwimIdle = "1070012133" },
    Popstar = { Idle1 = "1212900985", Idle2 = "1212900995", Walk = "1212980348", Run = "1212980338", Jump = "1212954642", Fall = "1212900995", Climb = "1213044953", Swim = "1213028291", SwimIdle = "1213028407" },
    Princess = { Idle1 = "941003647", Idle2 = "941013098", Walk = "941028902", Run = "941015281", Jump = "941008832", Fall = "941000007", Climb = "940996062", Swim = "941018893", SwimIdle = "941025398" }
}

local famousEmotes = {
    {name = "Minhoca", id = "76212616246340"},
    {name = "Peixe", id = "96081038851490"},
    {name = "Dança Shin Chan", id = "118870795136468"},
    {name = "Footwork Funk Brasileiro 2", id = "140219184038687"},
    {name = "Dança da Aranha", id = "76993911030490"},
    {name = "Homem Aranha", id = "88841065236214"},
    {name = "Invisível", id = "126995783634131"},
    {name = "Sentar", id = "86052354840008"},
    {name = "67", id = "98895028985842"},
    {name = "Passinho do Jamal", id = "83796130837213"},
    {name = "Rocker Festa", id = "97500383729078"},
    {name = "Kareen", id = "131703052643885"},
    {name = "Popular", id = "94292758607678"},
    {name = "Falso Morto MM2", id = "110770090804022"},
    {name = "Cagar", id = "121561448415799"},
    {name = "Dança Hype", id = "129200076968240"},
    {name = "Cachorro", id = "72736816865417"},
    {name = "Abraçar", id = "89634365084537"},
    {name = "Escorregador", id = "98374707218668"},
    {name = "Rebolada", id = "86256296565849"}
}    

local currentEmoteTrack = nil
local emoteSpeed = 1

local function aplicarPack(pack)
    if not pack then return end
    
    local char = player.Character or player.CharacterAdded:Wait()
    local animate = char:WaitForChild("Animate")
    
    local function set(folder, id)
        if not folder then return end
        for _, v in pairs(folder:GetChildren()) do
            if v:IsA("Animation") then
                v.AnimationId = "rbxassetid://" .. id
            end
        end
    end
    
    set(animate:FindFirstChild("idle"), pack.Idle1)
    set(animate:FindFirstChild("walk"), pack.Walk)
    set(animate:FindFirstChild("run"), pack.Run)
    set(animate:FindFirstChild("jump"), pack.Jump)
    set(animate:FindFirstChild("fall"), pack.Fall)
    set(animate:FindFirstChild("climb"), pack.Climb)
    set(animate:FindFirstChild("swim"), pack.Swim)
    
    char:WaitForChild("Humanoid"):ChangeState(Enum.HumanoidStateType.Running)
end

local function playEmote(id, speed)
    local char = player.Character or player.CharacterAdded:Wait()
    local humanoid = char:FindFirstChild("Humanoid")
    if not humanoid then return end
    
    local animator = humanoid:FindFirstChild("Animator")
    if not animator then
        animator = Instance.new("Animator")
        animator.Parent = humanoid
    end
    
    if currentEmoteTrack then
        currentEmoteTrack:Stop()
        currentEmoteTrack = nil
    end
    
    local success = pcall(function()
        humanoid:PlayEmote(tostring(id))
    end)
    
    if success then
        print(" Emote oficial: " .. id)
        return
    end
    
    local anim = Instance.new("Animation")
    anim.AnimationId = "rbxassetid://" .. tostring(id)
    
    local track = animator:LoadAnimation(anim)
    if track then
        track.Priority = Enum.AnimationPriority.Action4
        track.Looped = true
        track:Play()
        
        if speed and speed ~= 1 then
            track:AdjustSpeed(speed)
        end
        
        currentEmoteTrack = track
        print(" Animation carregada: " .. id)
    else
        warn(" Falhou: " .. id)
        Notify("Emote não encontrado!", "error")
    end
end

local function stopEmote()
    if currentEmoteTrack then
        currentEmoteTrack:Stop()
        currentEmoteTrack = nil
    end
end

-- ================================ ESP HIGHLIGHT ================================

local ESPHighlights = {}
local lastESPUpdate = 0

local function limparESPHighlight()
    for player, data in pairs(ESPHighlights) do
        pcall(function()
            if data.Highlight then data.Highlight:Destroy() end
            if data.Billboard then data.Billboard:Destroy() end
        end)
    end
    ESPHighlights = {}
end

local function atualizarESPHighlight()
    if not (ESPHighlightEnabled or ESPMM2HighlightEnabled) then
        if next(ESPHighlights) then limparESPHighlight() end
        return
    end
    
    if tick() - lastESPUpdate < 1 then return end
    lastESPUpdate = tick()
    
    local myChar = GetChar()
    if not myChar then return end
    local myHRP = GetHRP(myChar)
    if not myHRP then return end
    local myPos = myHRP.Position
    
    local MAX_DISTANCE = 250
    
    for _, plr in pairs(Players:GetPlayers()) do
        if plr == player then continue end
        
        local char = plr.Character
        if not char then
            if ESPHighlights[plr] then
                pcall(function()
                    if ESPHighlights[plr].Highlight then ESPHighlights[plr].Highlight:Destroy() end
                    if ESPHighlights[plr].Billboard then ESPHighlights[plr].Billboard:Destroy() end
                end)
                ESPHighlights[plr] = nil
            end
            continue
        end
        
        local humanoid = GetHumanoid(char)
        if not humanoid or humanoid.Health <= 0 then
            if ESPHighlights[plr] then
                pcall(function()
                    if ESPHighlights[plr].Highlight then ESPHighlights[plr].Highlight:Destroy() end
                    if ESPHighlights[plr].Billboard then ESPHighlights[plr].Billboard:Destroy() end
                end)
                ESPHighlights[plr] = nil
            end
            continue
        end
        
        local root = GetHRP(char)
        if not root then continue end
        
        local distancia = (root.Position - myPos).Magnitude
        
        if distancia > MAX_DISTANCE then
            if ESPHighlights[plr] then
                pcall(function()
                    if ESPHighlights[plr].Highlight then ESPHighlights[plr].Highlight:Destroy() end
                    if ESPHighlights[plr].Billboard then ESPHighlights[plr].Billboard:Destroy() end
                end)
                ESPHighlights[plr] = nil
            end
            continue
        end
        
        if ESPHighlights[plr] and ESPHighlights[plr].Highlight then
            if ESPHighlights[plr].Highlight.Parent ~= char then
                pcall(function()
                    ESPHighlights[plr].Highlight:Destroy()
                end)
                ESPHighlights[plr] = nil
            end
        end
        
        local cor = Color3.fromRGB(255, 255, 255)
        if ESPMM2HighlightEnabled then
            local papel = getPapel(plr)
            if papel == "Assassino" then
                cor = Color3.fromRGB(255, 0, 0)
            elseif papel == "Xerife" then
                cor = Color3.fromRGB(0, 100, 255)
            else
                cor = Color3.fromRGB(0, 255, 0)
            end
        end
        
        if not ESPHighlights[plr] then
            if char and char.Parent then
                local success, highlight = pcall(function()
                    local h = Instance.new("Highlight")
                    h.Parent = char
                    h.FillTransparency = 1
                    h.OutlineTransparency = 0.2
                    if ESPMM2HighlightEnabled then
                        h.OutlineColor = cor
                    else
                        h.OutlineColor = Color3.fromRGB(0, 191, 255)
                    end
                    h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                    return h
                end)
                
                if not success or not highlight then
                    return
                end
                
                local billboard = Instance.new("BillboardGui")
                billboard.Name = "ESPBillboard"
                local head = char:FindFirstChild("Head")
                billboard.Parent = head or char
                billboard.Size = UDim2.new(0, 100, 0, 30)
                billboard.StudsOffset = Vector3.new(0, 2.5, 0)
                billboard.AlwaysOnTop = true
                
                local textLabel = Instance.new("TextLabel")
                textLabel.Parent = billboard
                textLabel.Size = UDim2.new(1, 0, 1, 0)
                textLabel.BackgroundTransparency = 1
                textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                textLabel.TextStrokeTransparency = 0.5
                textLabel.Font = Enum.Font.GothamBold
                textLabel.TextSize = 8
                textLabel.Text = plr.Name .. " [" .. math.floor(distancia) .. "m]"
                
                ESPHighlights[plr] = {Highlight = highlight, Billboard = billboard, TextLabel = textLabel}
            end
        else
            local data = ESPHighlights[plr]
            if data.Highlight then
                if ESPMM2HighlightEnabled then
                    data.Highlight.OutlineColor = cor
                else
                    data.Highlight.OutlineColor = Color3.fromRGB(0, 191, 255)
                end
            end
            if data.TextLabel then
                data.TextLabel.Text = plr.Name .. " [" .. math.floor(distancia) .. "m]"
            end
        end
    end
end

local function iniciarESPHighlight()
    LoopManager:Add("ESPUpdate", atualizarESPHighlight)
end

local function pararESPHighlight()
    LoopManager:Remove("ESPUpdate")
    limparESPHighlight()
end

-- ================================ OCULTAR NOME ================================

local HideNameEnabled = false

local function aplicarOcultarNome(char)
    if not char then return end
    
    local humanoid = GetHumanoid(char)
    if humanoid then
        if HideNameEnabled then
            humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None
        else
            humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.Viewer
        end
    end
end

local function aplicarEmTodos()
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= player then
            aplicarOcultarNome(plr.Character)
        end
    end
end

Players.PlayerAdded:Connect(function(plr)
    if plr == player then return end
    
    plr.CharacterAdded:Connect(function(char)
        task.wait(0.5)
        if HideNameEnabled then
            aplicarOcultarNome(char)
        end
    end)
    
    if plr.Character then
        task.wait(0.5)
        if HideNameEnabled then
            aplicarOcultarNome(plr.Character)
        end
    end
end)

for _, plr in pairs(Players:GetPlayers()) do
    if plr ~= player then
        if plr.Character then
            task.spawn(function()
                task.wait(0.5)
                aplicarOcultarNome(plr.Character)
            end)
        end
        
        plr.CharacterAdded:Connect(function(char)
            task.wait(0.5)
            if HideNameEnabled then
                aplicarOcultarNome(char)
            end
        end)
    end
end

-- ================================ NOCLIP ================================

local NoClipEnabled = false

local function aplicarNoClip()
    if not NoClipEnabled then return end
    
    local char = GetChar()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end

local function reverterNoClip()
    local char = GetChar()
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
    end
end

-- ================================ AUTOFARM ================================

local AutoFarmEnabled = false
local AutoFarmMoving = false
local AutoFarmTween = nil
local AutoFarmAtivouNoClip = false

local function pararAutoFarmMovimento()
    if AutoFarmTween and AutoFarmTween.PlaybackState == Enum.PlaybackState.Playing then
        AutoFarmTween:Cancel()
    end
    AutoFarmMoving = false
    AutoFarmTween = nil
    
    if AutoFarmAtivouNoClip then
        local char = GetChar()
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
        AutoFarmAtivouNoClip = false
    end
end

local function moverParaMoeda(moeda)
    local char = GetChar()
    if not char then return false end
    
    local hrp = GetHRP(char)
    if not hrp then return false end
    
    if not NoClipEnabled then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
        AutoFarmAtivouNoClip = true
    end
    
    local targetPos = moeda.Position + Vector3.new(0, 2, 0)
    local distancia = (targetPos - hrp.Position).Magnitude
    local velocidade = 45
    local duracao = distancia / velocidade
    
    if duracao < 0.25 then duracao = 0.25 end
    
    local tweenInfo = TweenInfo.new(duracao, Enum.EasingStyle.Linear, Enum.EasingDirection.Out)
    local tween = TweenService:Create(hrp, tweenInfo, {CFrame = CFrame.new(targetPos)})
    
    AutoFarmTween = tween
    AutoFarmMoving = true
    tween:Play()
    tween.Completed:Wait()
    task.wait(0.2)
    AutoFarmMoving = false
    
    return true
end

local function getNearestCoinNatural()
    local myHRP = GetHRP()
    if not myHRP then return nil end
    
    atualizarCacheMoedas()
    
    local nearestCoin = nil
    local nearestDist = 200
    
    for _, obj in pairs(coinCache) do
        if obj and obj.Parent then
            local dist = (obj.Position - myHRP.Position).Magnitude
            if dist < nearestDist then
                nearestDist = dist
                nearestCoin = obj
            end
        end
    end
    
    return nearestCoin
end

local lastFarmTime = 0

local function AutoFarmTaskNatural()
    if not AutoFarmEnabled then return end
    if AutoFarmMoving then return end
    if not player.Character then return end  
    
    if tick() - lastFarmTime < 1 then return end
    lastFarmTime = tick()
    
    local moeda = getNearestCoinNatural()
    if moeda then
        moverParaMoeda(moeda)
    end
end

local function AutoFarmToggle(Value)
    AutoFarmEnabled = Value
    
    if Value then
        if NoClipToggle then
            NoClipToggle:Lock()
        end
        
        if LoopManager.tasks["AutoFarm"] then
            LoopManager:Remove("AutoFarm")
        end
        LoopManager:Add("AutoFarm", AutoFarmTaskNatural)
    else
        if NoClipToggle then
            NoClipToggle:Unlock()
        end
        
        LoopManager:Remove("AutoFarm")
        pararAutoFarmMovimento()
    end
end

-- ================================ FUNÇÕES DE VISÃO ================================

local function CanSeePlayer(target)
    local myChar = GetChar()
    if not myChar or not myChar:FindFirstChild("Head") then return false end
    if not target.Character or not target.Character:FindFirstChild("Head") then return false end
    
    local origin = camera.CFrame.Position
    local targetPos = target.Character.Head.Position
    local direction = (targetPos - origin).Unit * 1000
    
    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {myChar}
    
    local result = workspace:Raycast(origin, direction, raycastParams)
    if result then
        return result.Instance:IsDescendantOf(target.Character)
    end
    
    return true
end

local function UpdateFOVCircle()
    if not AimbotEnabled then
        if FOVCircle then
            FOVCircle:Destroy()
            FOVCircle = nil
            FOVCircleFrame = nil
        end
        return
    end
    
    if not FOVCircle then
        local gui = Instance.new("ScreenGui")
        gui.Name = "FOVCircle"
        gui.Parent = player:FindFirstChild("PlayerGui") or player:WaitForChild("PlayerGui")
        gui.ResetOnSpawn = false
        gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        gui.DisplayOrder = 999
        gui.IgnoreGuiInset = true
        
        local circle = Instance.new("Frame")
        circle.Parent = gui
        circle.BackgroundTransparency = 1
        circle.BorderSizePixel = 0
        
        local stroke = Instance.new("UIStroke")
        stroke.Parent = circle
        stroke.Color = Color3.fromRGB(220, 50, 50)
        stroke.Thickness = 0.3
        stroke.Transparency = 0
        
        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(1, 0)
        corner.Parent = circle
        
        FOVCircle = gui
        FOVCircleFrame = circle
    end
    
    if FOVCircleFrame then
        FOVCircleFrame.Size = UDim2.new(0, AimbotFOV * 2, 0, AimbotFOV * 2)
        FOVCircleFrame.Position = UDim2.new(0.5, -AimbotFOV, 0.5, -AimbotFOV)
    end
end

local function GetClosestPlayerWithFOV(ignoreWalls)
    local closestPlayer = nil
    local bestScore = math.huge
    
    local myPos = GetChar() and GetChar():FindFirstChild("Head") and GetChar().Head.Position
    if not myPos then return nil end
    
    atualizarPlayerCache()
    
    for _, otherPlayer in pairs(playerCache) do
        local char = otherPlayer.Character
        if not char then continue end
        
        local aimPart
        if SelectedAimPart == "Head" then
            aimPart = char:FindFirstChild("Head")
        else
            aimPart = GetHRP(char)
        end
        if not aimPart then continue end
        
        local humanoid = GetHumanoid(char)
        if not humanoid or humanoid.Health <= 0 then continue end
        
        if ignoreWalls and not CanSeePlayer(otherPlayer) then continue end
        
        local targetPos = aimPart.Position
        
        if lastPositionsCount > 100 then
            local oldest = nil
            local oldestTime = math.huge
            for k, v in pairs(lastPositions) do
                if v.time < oldestTime then
                    oldestTime = v.time
                    oldest = k
                end
            end
            if oldest then
                lastPositions[oldest] = nil
                lastPositionsCount = lastPositionsCount - 1
            end
        end
        
        local velocity = Vector3.new(0, 0, 0)
        if lastPositions[otherPlayer] then
            local lastPos, lastTime = lastPositions[otherPlayer].pos, lastPositions[otherPlayer].time
            local dt = tick() - lastTime
            if dt > 0 then
                velocity = (targetPos - lastPos) / dt
            end
        end
        
        lastPositions[otherPlayer] = {pos = targetPos, time = tick()}
        lastPositionsCount = lastPositionsCount + 1
        
        local predictedPos = targetPos + (velocity * 0.2)
        local screenPoint = camera:WorldToViewportPoint(predictedPos)
        if screenPoint.Z <= 0 then continue end
        
        local centerDist = (Vector2.new(screenPoint.X, screenPoint.Y) - Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)).Magnitude
        if centerDist > AimbotFOV then continue end
        
        local realDist = (targetPos - myPos).Magnitude
        local score = (centerDist * 0.7) + (realDist * 0.3)
        
        if score < bestScore then
            bestScore = score
            closestPlayer = otherPlayer
        end
    end
    
    return closestPlayer
end

-- ================================ TAREFAS DOS LOOPS ================================

local lastAimbotTime = 0

local function AimbotTask()
    if not AimbotEnabled then return end
    if not camera or not player.Character then return end
    
    if tick() - lastAimbotTime < 0.05 then return end 
    lastAimbotTime = tick()
    
    if GetChar() and GetHRP() then
        local target = GetClosestPlayerWithFOV(IgnoreWalls)
        if target and target.Character then        
            local aimPart
            if SelectedAimPart == "Head" then
                aimPart = target.Character:FindFirstChild("Head")
            else
                aimPart = GetHRP(target.Character)
            end
            
            if aimPart then
                local targetPos = aimPart.Position
                if lastPositions[target] then
                    local lastPos, lastTime = lastPositions[target].pos, lastPositions[target].time
                    local dt = tick() - lastTime
                    if dt > 0 then
                        local velocity = (targetPos - lastPos) / dt
                        targetPos = targetPos + (velocity * 0.15)
                    end
                end
                
                local currentLook = camera.CFrame.LookVector
                local targetLook = (targetPos - camera.CFrame.Position).Unit
                local distancia = (targetPos - camera.CFrame.Position).Magnitude
                local smoothFactor = 1 / (AimbotSmoothness * (1 + distancia / 100))
                local smoothLook = currentLook:Lerp(targetLook, smoothFactor)
                
                camera.CFrame = CFrame.lookAt(camera.CFrame.Position, camera.CFrame.Position + smoothLook)
            end
        end
    end
end

local function aplicarSpeed()
    if not SpeedToggleEnabled then return end
    
    local char = GetChar()
    if char then
        local humanoid = GetHumanoid(char)
        if humanoid then
            humanoid.WalkSpeed = SpeedValue
        end
    end
end

local function reverterSpeed()
    local char = GetChar()
    if char then
        local humanoid = GetHumanoid(char)
        if humanoid then
            humanoid.WalkSpeed = 16
        end
    end
end

local function aplicarFov()
    if not FOVCamToggleEnabled then return end
    if camera then
        camera.FieldOfView = FovCamValue
    end
end

local function reverterFov()
    if camera then
        camera.FieldOfView = 70
    end
end

local function LockCamTask()
    if LockCamEnabled and LockUIActive then
        local character = GetChar()
        if character and GetHRP(character) then
            local rootPart = GetHRP(character)
            local humanoid = GetHumanoid(character)
            
            if humanoid then
                humanoid.CameraOffset = Vector3.new(1.5, 0, 0)
            end
            
            local cameraDirection = camera.CFrame.LookVector
            cameraDirection = Vector3.new(cameraDirection.X, 0, cameraDirection.Z).Unit
            
            if cameraDirection.Magnitude > 0 then
                rootPart.CFrame = CFrame.lookAt(rootPart.Position, rootPart.Position + cameraDirection)
            end
        end
    else
        local char = GetChar()
        if char then
            local humanoid = GetHumanoid(char)
            if humanoid then
                humanoid.CameraOffset = Vector3.new(0, 0, 0)
            end
        end
    end
end

-- ================================ UTILIDADES ================================

local function CriarFPSDisplay()
    local gui = Instance.new("ScreenGui")
    gui.Name = "FPSDisplay"
    gui.Parent = player:FindFirstChild("PlayerGui") or player:WaitForChild("PlayerGui")
    gui.ResetOnSpawn = false
    gui.DisplayOrder = 999
    gui.IgnoreGuiInset = true
    gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    gui.Enabled = false
    
    local frame = Instance.new("Frame")
    frame.Parent = gui
    frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    frame.BackgroundTransparency = 0.5
    frame.Size = UDim2.new(0, 70, 0, 25)
    frame.Position = UDim2.new(1, -80, 0, 8)
    frame.Active = false
    frame.Draggable = false
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = frame
    
    local fpsLabel = Instance.new("TextLabel")
    fpsLabel.Parent = frame
    fpsLabel.BackgroundTransparency = 1
    fpsLabel.Size = UDim2.new(1, 0, 1, 0)
    fpsLabel.Text = "FPS: 60"
    fpsLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    fpsLabel.TextScaled = true
    fpsLabel.Font = Enum.Font.GothamBold
    fpsLabel.TextStrokeTransparency = 0.5
    
    local frameCount = 0
    local lastTime = tick()
    
    RunService.RenderStepped:Connect(function()
        if not gui.Enabled then return end
        
        frameCount = frameCount + 1
        local currentTime = tick()
        if currentTime - lastTime >= 1 then
            local fps = frameCount
            frameCount = 0
            lastTime = currentTime
            
            if fps >= 50 then
                fpsLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
            elseif fps >= 30 then
                fpsLabel.TextColor3 = Color3.fromRGB(255, 200, 0)
            else
                fpsLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
            end
            
            fpsLabel.Text = "FPS: " .. fps
        end
    end)
    
    return gui
end

FPSUI = CriarFPSDisplay()

-- ================================ PULO INFINITO ================================

jumpConnection = UserInputService.JumpRequest:Connect(function()
    if infJumpEnabled and not UserInputService:GetFocusedTextBox() then
        local humanoid = GetChar() and GetChar():FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid:ChangeState("Jumping")
        end
    end
end)

-- ================================ HUD ================================

local function CreateShiftLockButton()
    if LockUI then LockUI:Destroy() end
    
    local gui = Instance.new("ScreenGui")
    gui.Name = "ShiftLockButton"
    gui.Parent = player:FindFirstChild("PlayerGui") or player:WaitForChild("PlayerGui")
    gui.ResetOnSpawn = false
    gui.DisplayOrder = 999
    gui.IgnoreGuiInset = true
    gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    local button = Instance.new("ImageButton")
    button.Parent = gui
    button.Name = "ShiftLockButton"
    button.BackgroundTransparency = 1
    button.Size = UDim2.new(0, 70, 0, 70)
    button.Position = UDim2.new(1, -200, 0.5, -5)
    button.Active = true
    button.Draggable = false
    button.BorderSizePixel = 0
    button.AutoButtonColor = false
    button.ZIndex = 2
    
    local function UpdateButtonState(active)
        if active then
            button.Image = "rbxassetid://139211296111194"
        else
            button.Image = "rbxassetid://16812589014"
        end
        if MiraUI then
            MiraUI.Enabled = active
        end
        LockUIActive = active
    end
    
    button.MouseButton1Click:Connect(function()
        local novoEstado = not LockUIActive
        UpdateButtonState(novoEstado)
    end)
    
    UpdateButtonState(false)
    LockUI = gui
end

local function CriarMira()
    if MiraUI then MiraUI:Destroy() end
    
    local gui = Instance.new("ScreenGui")
    gui.Name = "MiraCentral"
    gui.Parent = player:FindFirstChild("PlayerGui") or player:WaitForChild("PlayerGui")
    gui.ResetOnSpawn = false
    gui.DisplayOrder = 1000
    gui.IgnoreGuiInset = true
    gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    gui.Enabled = false
    
    local mira = Instance.new("ImageLabel")
    mira.Parent = gui
    mira.BackgroundTransparency = 1
    mira.Size = UDim2.new(0, 23, 0, 23)
    mira.Position = UDim2.new(0.5, -11, 0.5, -12)
    mira.Image = "rbxassetid://17404114716"
    
    MiraUI = gui
end

-- ================================ FLY ================================

local function startFly()
    local char = GetChar()
    if not char then
        Notify("Personagem não encontrado", "error")
        return
    end
    
    local humanoid = GetHumanoid(char)
    local hrp = GetHRP(char)
    
    if not humanoid or not hrp then
        Notify("Humanoid ou HRP não encontrado", "error")
        return
    end
    
    if bodyVelocity then pcall(function() bodyVelocity:Destroy() end) end
    if bodyGyro then pcall(function() bodyGyro:Destroy() end) end
    
    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Name = "FlyVelocity"
    bodyVelocity.MaxForce = Vector3.new(999999, 999999, 999999)
    bodyVelocity.Parent = hrp
    
    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.Name = "FlyGyro"
    bodyGyro.MaxTorque = Vector3.new(999999, 999999, 999999)
    bodyGyro.Parent = hrp
    
    humanoid.PlatformStand = true
    
    local cachedChar = nil
    local cachedHRP = nil
    local cachedHumanoid = nil
    
    LoopManager:Add("FlyUpdate", function()
        if not flying then return end
        if not camera or not player.Character then return end
    
        if not cachedChar or cachedChar ~= GetChar() then
            cachedChar = GetChar()
            cachedHRP = cachedChar and GetHRP(cachedChar)
            cachedHumanoid = cachedChar and GetHumanoid(cachedChar)
        end
        
        if not cachedChar or not cachedHumanoid or not cachedHRP then
            if flying then
                flying = false
                StopFly()
            end
            return
        end
        
        local camera = workspace.CurrentCamera
        local moveDir = cachedHumanoid.MoveDirection
        
        local velocity = moveDir * FlySpeed
        
        if moveDir.Magnitude > 0 then
            local lookY = camera.CFrame.LookVector.Y
            local forwardDot = moveDir:Dot(camera.CFrame.LookVector)
            local directionFix = (forwardDot >= 0) and 1 or -1
            velocity = velocity + Vector3.new(0, lookY * FlySpeed * directionFix, 0)
        end
        
        if bodyVelocity then
            bodyVelocity.Velocity = velocity
        end
        
        if bodyGyro then
            bodyGyro.CFrame = camera.CFrame
        end
    end)
end

local function StopFly()
    flying = false
    
    local char = GetChar()
    if char then
        local humanoid = GetHumanoid(char)
        if humanoid then
            humanoid.PlatformStand = false
        end
    end
    
    if bodyVelocity then
        pcall(function() bodyVelocity:Destroy() end)
        bodyVelocity = nil
    end
    
    if bodyGyro then
        pcall(function() bodyGyro:Destroy() end)
        bodyGyro = nil
    end
    
    LoopManager:Remove("FlyUpdate")
end

local function toggleFly(state)
    flying = state
    if state then
        startFly()
    else
        StopFly()
    end
end

-- ================================ SPINBOT ================================

local function SpinbotTask()
    if not SpinbotEnabled then return end
    if not player.Character then return end
    
    local char = GetChar()
    if not char then return end
    
    local hrp = GetHRP(char)
    if hrp then
        hrp.RotVelocity = Vector3.new(0, 200, 0)
    end
end

local function toggleSpinbot(state)
    SpinbotEnabled = state
    
    if state then
        LoopManager:Add("Spinbot", SpinbotTask)
    else
        LoopManager:Remove("Spinbot")
        
        local char = GetChar()
        if char then
            local hrp = GetHRP(char)
            if hrp then
                hrp.RotVelocity = Vector3.new(0, 0, 0)
            end
        end
    end
end

-- ================================ FUNÇÕES DE LISTA ================================

local function stopAll()
    if backpackActive then
        backpackActive = false
        LoopManager:Remove("Backpack")
    end
    
    if headsitActive then
        headsitActive = false
        LoopManager:Remove("Headsit")
    end
    
    if spectateActive then
        spectateActive = false
        local myChar = GetChar()
        if myChar and myChar:FindFirstChild("Humanoid") then
            camera.CameraSubject = myChar.Humanoid
            camera.CameraType = Enum.CameraType.Custom
        end
    end
    
    if flingOPActive then
        flingOPActive = false
        LoopManager:Remove("FlingOP")
        
        local meuHRP = GetHRP()
        if meuHRP then
            meuHRP.RotVelocity = Vector3.new(0, 0, 0)
            meuHRP.Velocity = Vector3.new(0, 0, 0)
            meuHRP.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            meuHRP.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
            
            if posicaoOriginal then
                meuHRP.CFrame = posicaoOriginal
                posicaoOriginal = nil
            end
        end
    end
    
    local char = GetChar()
    if char and char:FindFirstChild("Humanoid") then
        char.Humanoid.Sit = false
    end
end

-- ================================ FUNÇÕES DE HEAD/KORBLOX ================================

local function toggleHeadless(state)
    local char = GetChar()
    if not char then return end

    local head = char:FindFirstChild("Head")
    if not head then return end

    local hairKeywords = {"hair", "cabelo", "wig", "cabel", "hairstyle", "bangs", "ponytail", "braid", "haircut"}
    
    local function isHair(accessoryName)
        local lowerName = accessoryName:lower()
        for _, keyword in pairs(hairKeywords) do
            if lowerName:find(keyword) then
                return true
            end
        end
        return false
    end

    if state then        
        originalHead = head:Clone()
        originalHead.Parent = nil

        head.Transparency = 1
        head.CanCollide = false
        head.Massless = true

        local face = head:FindFirstChildOfClass("Decal")
        if face then
            face.Transparency = 1
        end

        for _, accessory in pairs(char:GetChildren()) do
            if accessory:IsA("Accessory") then
                local handle = accessory:FindFirstChild("Handle")
                local weld = handle and handle:FindFirstChildOfClass("Weld")
                
                if isHair(accessory.Name) then
                    if handle then
                        handle.Transparency = 0
                    end
                else
                    if weld and weld.Part1 == head then
                        if handle then
                            handle.Transparency = 1
                        end
                    end
                end
            end
        end
    else
        head.Transparency = 0
        head.CanCollide = true
        head.Massless = false

        local face = head:FindFirstChildOfClass("Decal")
        if face then
            face.Transparency = 0
        end

        for _, accessory in pairs(char:GetChildren()) do
            if accessory:IsA("Accessory") then
                local handle = accessory:FindFirstChild("Handle")
                if handle then
                    handle.Transparency = 0
                end
            end
        end

        if originalHead then
            originalHead:Destroy()
            originalHead = nil
        end
    end
end

local function toggleKorblox(state)
    local char = GetChar()
    if not char then return end

    local parts = {
        "LeftUpperLeg",
        "LeftLowerLeg",
        "LeftFoot"
    }

    if state then     
        originalLeftLeg = {}
        for _, partName in pairs(parts) do            
            local part = char:FindFirstChild(partName)
            if part then
                originalLeftLeg[partName] = part.Transparency
                part.Transparency = 1
                part.CanCollide = false
                part.Massless = true
            end
        end
    else
        for _, partName in pairs(parts) do            
            local part = char:FindFirstChild(partName)
            if part then               
                part.Transparency = 0
                part.CanCollide = true
                part.Massless = false
            end
        end
        originalLeftLeg = nil
    end
end

local function updateVisuals()
    toggleHeadless(headlessActive)
    toggleKorblox(korbloxActive)
end

-- ================================ EVENTOS CENTRALIZADOS ================================

EventManager:Add("CharacterAdded", player.CharacterAdded:Connect(function(char)
    if flying then
        startFly()
    end
    if headlessActive or korbloxActive then
        updateVisuals()
    end
    if selectedAnimationPack then
        aplicarPack(ANIMATION_PACKS[selectedAnimationPack])
    end
    if NoClipEnabled then
        aplicarNoClip()
    end
    if SpeedToggleEnabled then
        aplicarSpeed()
    end
    if FOVCamToggleEnabled then
        aplicarFov()
    end
end))

EventManager:Add("CharacterRemoving", player.CharacterRemoving:Connect(function()
    if bodyVelocity then
        pcall(function() bodyVelocity:Destroy() end)
        bodyVelocity = nil
    end
    if bodyGyro then
        pcall(function() bodyGyro:Destroy() end)
        bodyGyro = nil
    end
end))

-- ================================ CRIAÇÃO DAS TABS ================================

local AimbotTab = Window:Tab({Title = "Aimbot", Icon = "crosshair"})

AimbotTab:Toggle({
    Title = "Ativar Aimbot",
    Default = false,
    Callback = function(Value)
        AimbotEnabled = Value
        if AimbotEnabled then
            LoopManager:Add("Aimbot", AimbotTask)
        else
            LoopManager:Remove("Aimbot")
        end
        UpdateFOVCircle()
    end
})

AimbotTab:Dropdown({
    Title = "Target",
    Values = {"Head", "Torso"},
    Default = "Head",
    Callback = function(Value)
        SelectedAimPart = Value
    end
})

AimbotTab:Slider({
    Title = "Exibir Circulo do FOV",
    Step = 1,
    Value = {
        Min = 30,
        Max = 150,
        Default = 90,
    },
    Callback = function(Value)
        AimbotFOV = Value
        UpdateFOVCircle()
    end
})

AimbotTab:Slider({
    Title = "Smoothing",
    Step = 1,
    Value = {
        Min = 1,
        Max = 10,
        Default = 5,
    },
    Callback = function(Value)
        AimbotSmoothness = Value
    end
})

AimbotTab:Toggle({
    Title = "Ignore Walls",
    Default = false,
    Callback = function(Value)
        IgnoreWalls = Value
    end
})

-- ================================ ABA VISUAIS ================================

local VisualsTab = Window:Tab({Title = "Visuais", Icon = "eye"})

VisualsTab:Toggle({
    Title = "Enabled ESP",
    Default = false,
    Callback = function(Value)
        if Value then
            if ESPMM2HighlightEnabled then
                Notify("Desative o ESP MM2 primeiro", "error")
                return
            end
            ESPHighlightEnabled = true
            iniciarESPHighlight()  
        else
            ESPHighlightEnabled = false
            if not ESPMM2HighlightEnabled then
                pararESPHighlight()  
            end
        end
    end
})

VisualsTab:Toggle({
    Title = "Hide @User (Clean Names)",
    Default = false,
    Callback = function(Value)
        HideNameEnabled = Value
        aplicarEmTodos()
    end
})

VisualsTab:Toggle({
    Title = "Show FPS",
    Default = false,
    Callback = function(Value)
        ShowFPSEnabled = Value
        if FPSUI then
            FPSUI.Enabled = Value
        end
    end
})

VisualsTab:Section({Title = "Hub Cosmetics"})

VisualsTab:Toggle({
    Title = "Headless (Visual Only)",
    Default = false,
    Callback = function(Value)
        headlessActive = Value
        toggleHeadless(Value)
    end
})

VisualsTab:Toggle({
    Title = "Korblox (Visual Only)",
    Default = false,
    Callback = function(Value)
        korbloxActive = Value
        toggleKorblox(Value)
    end
})

-- ================================ ABA JOGADOR ================================

local JogadorTab = Window:Tab({Title = "Exploit", Icon = "box"})

JogadorTab:Section({Title = "Movement & Tools"})

JogadorTab:Toggle({
    Title = "Fly",
    Default = false,
    Callback = function(Value)
        toggleFly(Value)
    end
})

JogadorTab:Slider({
    Title = "Fly Speed",
    Step = 1,
    Value = {
        Min = 10,
        Max = 300,
        Default = 50,
    },
    Callback = function(Value)
        FlySpeed = Value
    end
})

JogadorTab:Toggle({
    Title = "Speed",
    Default = false,
    Callback = function(Value)
        SpeedToggleEnabled = Value
        if Value then
            aplicarSpeed()
        else
            reverterSpeed()
        end
    end
})

JogadorTab:Slider({
    Title = "Speed Walk",
    Step = 1,
    Value = {
        Min = 16,
        Max = 150,
        Default = 16,
    },
    Callback = function(Value)
        SpeedValue = Value
        if SpeedToggleEnabled then
            aplicarSpeed()
        end
    end
})

JogadorTab:Toggle({
    Title = "FOV Cam",
    Default = false,
    Callback = function(Value)
        FOVCamToggleEnabled = Value
        if Value then
            aplicarFov()
        else
            reverterFov()
        end
    end
})

JogadorTab:Slider({
    Title = "FOV Cam (Zoom)",
    Step = 1,
    Value = {
        Min = 1,
        Max = 120,
        Default = 70,
    },
    Callback = function(Value)
        FovCamValue = Value
        if FOVCamToggleEnabled then
            aplicarFov()
        end
    end
})

JogadorTab:Toggle({
    Title = "Infinite Jump",
    Default = false,
    Callback = function(Value)
        infJumpEnabled = Value
    end
})

NoClipToggle = JogadorTab:Toggle({
    Title = "NoClip",
    Default = false,
    Callback = function(Value)
        if AutoFarmEnabled then
            Notify("AutoFarm ativo! Desative o AutoFarm primeiro.", "error")
            NoClipToggle:Set(NoClipEnabled)
            return
        end
        
        NoClipEnabled = Value
        
        if Value then
            aplicarNoClip()
            if AutoFarmToggleElement then
                AutoFarmToggleElement:Lock()
            end
        else
            reverterNoClip()
            if AutoFarmToggleElement then
                AutoFarmToggleElement:Unlock()
            end
        end
    end
})

JogadorTab:Section({Title = "Tools & Utilities"})

JogadorTab:Toggle({
    Title = "Spinbot",
    Default = false,
    Callback = function(Value)
        toggleSpinbot(Value)
    end
})

JogadorTab:Toggle({
    Title = "Lock Cam",
    Default = false,
    Callback = function(Value)
        LockCamEnabled = Value
        if LockCamEnabled then
            CreateShiftLockButton()
            CriarMira()
            LoopManager:Add("LockCam", LockCamTask)
        else
            if LockUI then
                LockUI:Destroy()
                LockUI = nil
            end
            if MiraUI then
                MiraUI:Destroy()
                MiraUI = nil
            end
            LoopManager:Remove("LockCam")
            local char = GetChar()
            if char then
                local humanoid = GetHumanoid(char)
                if humanoid then
                    humanoid.CameraOffset = Vector3.new(0, 0, 0)
                end
            end
        end
    end
})

JogadorTab:Button({
    Title = "Click TP",
    Callback = function()
        local success, err = pcall(function()
            local plr = Players.LocalPlayer
            if not plr.Character then return end
            
            local tool = Instance.new("Tool")
            tool.Name = "TP Tool"
            tool.RequiresHandle = false
            tool.CanBeDropped = false
            tool.TextureId = "rbxassetid://13060727541"
            
            tool.Activated:Connect(function()
                local mouse = plr:GetMouse()
                local target = mouse.Target
                local hit = mouse.Hit
                
                if target and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local rayOrigin = hit.Position + Vector3.new(0, 5, 0)
                    local rayDirection = Vector3.new(0, -10, 0)
                    local raycastParams = RaycastParams.new()
                    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
                    raycastParams.FilterDescendantsInstances = {plr.Character}
                    
                    local rayResult = workspace:Raycast(rayOrigin, rayDirection, raycastParams)
                    
                    if rayResult and rayResult.Instance and rayResult.Instance.CanCollide then
                        plr.Character.HumanoidRootPart.CFrame = CFrame.new(rayResult.Position + Vector3.new(0, 3, 0))
                    elseif hit.Position.Y > 3 and hit.Position.Y < 1000 then
                        plr.Character.HumanoidRootPart.CFrame = CFrame.new(hit.Position + Vector3.new(0, 3, 0))
                    end
                end
            end)
            
            tool.Parent = plr.Backpack
        end)
        
        if not success then
            Notify("Erro ao criar TP Tool", "error")
        end
    end
})

JogadorTab:Button({
    Title = "Reiniciar",
    Callback = function()
        local char = GetChar()
        if not char then
            Notify("Personagem não encontrado", "error")
            return
        end
        
        local hrp = GetHRP(char)
        if not hrp then
            Notify("HumanoidRootPart não encontrado", "error")
            return
        end
        
        local posicaoAtual = hrp.CFrame
        local flyEstavaAtivo = flying
        
        if flyEstavaAtivo then
            toggleFly(false)
        end
        
        local humanoid = GetHumanoid(char)
        if humanoid then
            humanoid.Health = 0
        end
        
        local newChar = player.CharacterAdded:Wait()
        task.wait(0.1)
        
        local newHRP = GetHRP(newChar)
        if newHRP then
            newHRP.CFrame = posicaoAtual
        end
        
        if flyEstavaAtivo then
            task.wait(0.2)
            toggleFly(true)
        end
    end
})

-- ================================ ABA ONLINES ================================

local OnlinesTab = Window:Tab({Title = "Onlines", Icon = "users"})

OnlinesTab:Section({Title = "Jogadores"})

local lastUpdate = 0

local function getPlayerList()
    local list = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player then
            table.insert(list, p.Name)
        end
    end
    return list
end

local function updatePlayerDropdown()
    if tick() - lastUpdate < 1 then return end
    lastUpdate = tick()
    if playerDropdown and playerDropdown.Refresh then
        pcall(function()
            playerDropdown:Refresh(getPlayerList())
        end)
    end
end

playerDropdown = OnlinesTab:Dropdown({
    Title = "Selecionar Jogador",
    Values = getPlayerList(),
    Default = "Ninguem",
    Callback = function(name)
        for _, p in ipairs(Players:GetPlayers()) do
            if p.Name == name and p ~= player then
                selectedPlayer = p
                break
            end
        end
    end
})

Players.PlayerAdded:Connect(updatePlayerDropdown)
Players.PlayerRemoving:Connect(function(p)
    if selectedPlayer == p then
        selectedPlayer = nil
        stopAll()
    end
    updatePlayerDropdown()
end)

OnlinesTab:Section({Title = "Ações"})

OnlinesTab:Button({
    Title = "Spectate",
    Callback = function()
        if not selectedPlayer then
            Notify("Selecione um jogador primeiro", "error")
            return
        end
        stopAll()
        
        local char = selectedPlayer.Character
        if char and char:FindFirstChild("Humanoid") then
            camera.CameraSubject = char.Humanoid
            camera.CameraType = Enum.CameraType.Custom
            spectateActive = true
        else
            Notify("Jogador sem personagem", "error")
        end
    end
})

OnlinesTab:Button({
    Title = "Teleport",
    Callback = function()
        if not selectedPlayer then
            Notify("Selecione um jogador primeiro", "error")
            return
        end
        stopAll()
        
        local targetChar = selectedPlayer.Character
        local myChar = GetChar()
        
        if targetChar and targetChar:FindFirstChild("HumanoidRootPart") and myChar and myChar:FindFirstChild("HumanoidRootPart") then
            myChar.HumanoidRootPart.CFrame = targetChar.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
        else
            Notify("Jogador sem personagem", "error")
        end
    end
})

OnlinesTab:Button({
    Title = "Mochila",
    Callback = function()
        if not selectedPlayer then
            Notify("Selecione um jogador primeiro", "error")
            return
        end
        
        if backpackActive then
            stopAll()
            Notify("Backpack desativado", "info")
            return
        end
        
        stopAll()
        backpackActive = true
        
        LoopManager:Add("Backpack", function()
            if not backpackActive or not selectedPlayer or not selectedPlayer.Character then
                LoopManager:Remove("Backpack")
                return
            end
            
            local myChar = GetChar()
            if myChar and myChar:FindFirstChild("HumanoidRootPart") then
                local targetRoot = selectedPlayer.Character:FindFirstChild("HumanoidRootPart")
                if targetRoot then
                    myChar.HumanoidRootPart.CFrame = targetRoot.CFrame * CFrame.new(0, 0, 1.3) * CFrame.Angles(0, math.rad(180), 0)
                    if myChar.Humanoid:GetState() ~= Enum.HumanoidStateType.Seated then
                        myChar.Humanoid.Sit = true
                    end
                end
            end
        end)
    end
})

OnlinesTab:Button({
    Title = "Headsit",
    Callback = function()
        if not selectedPlayer then
            Notify("Selecione um jogador primeiro", "error")
            return
        end
        
        if headsitActive then
            stopAll()
            Notify("Headsit desativado", "info")
            return
        end
        
        stopAll()
        headsitActive = true
        
        LoopManager:Add("Headsit", function()
            if not headsitActive or not selectedPlayer or not selectedPlayer.Character then
                LoopManager:Remove("Headsit")
                return
            end
            
            local myChar = GetChar()
            if myChar and myChar:FindFirstChild("HumanoidRootPart") then
                local targetHead = selectedPlayer.Character:FindFirstChild("Head")
                local targetRoot = selectedPlayer.Character:FindFirstChild("HumanoidRootPart")
                
                if targetHead and targetRoot then
                    local headPos = targetHead.Position + Vector3.new(0, 2.5, 0)
                    local lookDirection = targetRoot.CFrame.LookVector
                    lookDirection = Vector3.new(lookDirection.X, 0, lookDirection.Z).Unit
                    
                    myChar.HumanoidRootPart.CFrame = CFrame.lookAt(headPos, headPos + lookDirection)
                    
                    if myChar.Humanoid:GetState() ~= Enum.HumanoidStateType.Seated then
                        myChar.Humanoid.Sit = true
                    end
                end
            end
        end)
    end
})

OnlinesTab:Button({
    Title = "Fling OP",
    Callback = function()
        if not selectedPlayer then
            Notify("Selecione um jogador primeiro", "error")
            return
        end
        
        if flingOPActive then
            stopAll()
            Notify("Fling OP desativado", "info")
            return
        end
        
        stopAll()
        
        local targetChar = selectedPlayer.Character
        local myChar = GetChar()
        
        if not targetChar or not myChar then
            Notify("Personagem não encontrado", "error")
            return
        end
        
        local targetHRP = GetHRP(targetChar)
        local myHRP = GetHRP(myChar)
        
        if not targetHRP or not myHRP then
            Notify("HumanoidRootPart não encontrado", "error")
            return
        end
        
        flingOPActive = true
        posicaoOriginal = myHRP.CFrame
        
        local direcaoZ = 1
        local contador = 0
        
        LoopManager:Add("FlingOP", function()
            if not flingOPActive then
                LoopManager:Remove("FlingOP")
                return
            end
            
            if not selectedPlayer or not selectedPlayer.Character then
                stopAll()
                return
            end
            
            local myHRP = GetHRP()
            local targetHRP = GetHRP(selectedPlayer.Character)
            
            if not myHRP or not targetHRP then
                return
            end
            
            contador = contador + 1
            if contador >= 10 then
                direcaoZ = direcaoZ * -1
                contador = 0
            end
            
            local forcaX = math.random(-500, 500)
            local forcaY = math.random(400, 900)
            local forcaZ = math.random(500, 800) * direcaoZ 
            
            myHRP.AssemblyLinearVelocity = Vector3.new(forcaX, forcaY, forcaZ)
            myHRP.AssemblyAngularVelocity = Vector3.new(
                math.random(-30, 30),
                math.random(-30, 30),
                math.random(-30, 30)
            )
            myHRP.RotVelocity = Vector3.new(
                math.random(8000, 12000),
                math.random(8000, 12000),
                math.random(8000, 12000)
            )
            
            local randomAngle = CFrame.Angles(
                math.rad(math.random(-360, 360)),
                math.rad(math.random(-360, 360)),
                math.rad(math.random(-360, 360))
            )
            
            local yOffset = math.random(-6, 6)
            
            myHRP.CFrame = targetHRP.CFrame *
                CFrame.new(
                    math.random(-1, 1),
                    yOffset,
                    math.random(-1, 1)
                ) * randomAngle
            
            targetHRP.AssemblyLinearVelocity = Vector3.new(
                math.random(-200, 200),
                math.random(300, 600),
                math.random(-200, 200)
            )
        end)
    end
})

OnlinesTab:Button({
    Title = "Stop",
    Callback = function()
        local temAcaoAtiva = backpackActive or headsitActive or spectateActive or flingOPActive        
        if not temAcaoAtiva then
            Notify("Nenhuma ação ativa na aba Onlines para parar!", "info")
            return
        end
        stopAll()
    end
})

-- ================================ ABA MURDER MYSTERY 2 ================================

local MM2Tab = Window:Tab({Title = "Murder Mystery 2", Icon = "axe"})

MM2Tab:Section({Title = "Visuais"})

MM2Tab:Toggle({
    Title = "ESP de Jogadores",
    Default = false,
    Callback = function(Value)
        if Value then
            if ESPHighlightEnabled then
                Notify("Desative o outro ESP primeiro", "error")
                return
            end
            ESPMM2HighlightEnabled = true
            iniciarESPHighlight()
        else
            ESPMM2HighlightEnabled = false
            if not ESPHighlightEnabled then
                pararESPHighlight()
            end
        end
    end
})

local function encontrarPorPapel(papelAlvo)
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= player then
            if getPapel(plr) == papelAlvo then
                return plr
            end
        end
    end
    return nil
end

MM2Tab:Section({Title = "Combat"})

MM2Tab:Button({
    Title = "Puxar Xerife",
    Callback = function()
        local xerife = encontrarPorPapel("Xerife")
        if xerife and xerife.Character then
            local targetHRP = xerife.Character:FindFirstChild("HumanoidRootPart")
            local myHRP = GetHRP()
            if targetHRP and myHRP then
                targetHRP.CFrame = myHRP.CFrame
            end
        else
            Notify("Xerife nao encontrado", "error")
        end
    end
})

MM2Tab:Button({
    Title = "Puxar Todos",
    Callback = function()
        local myHRP = GetHRP()
        if not myHRP then return end
        
        local myPos = myHRP.Position
        
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= player and plr.Character then
                local targetHRP = plr.Character:FindFirstChild("HumanoidRootPart")
                if targetHRP then
                    local distancia = (targetHRP.Position - myPos).Magnitude
                    if distancia <= 300 then
                        targetHRP.CFrame = myHRP.CFrame
                        task.wait(0.1)
                    end
                end
            end
        end
    end
})

local function pararJogarAssassino()
    if killAssassinActive then
        killAssassinActive = false
        if killAssassinConnection then
            killAssassinConnection:Disconnect()
            killAssassinConnection = nil
        end
        LoopManager:Remove("KillAssassinFling")
        local meuHRP = GetHRP()
        if meuHRP then
            meuHRP.RotVelocity = Vector3.new(0, 0, 0)
            meuHRP.Velocity = Vector3.new(0, 0, 0)
            meuHRP.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            meuHRP.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
            if killAssassinPos then
                meuHRP.CFrame = killAssassinPos
                killAssassinPos = nil
            end
        end
    end
end

local function pararJogarXerife()
    if killSheriffActive then
        killSheriffActive = false
        if killSheriffConnection then
            killSheriffConnection:Disconnect()
            killSheriffConnection = nil
        end
        LoopManager:Remove("KillSheriffFling")
        local meuHRP = GetHRP()
        if meuHRP then
            meuHRP.RotVelocity = Vector3.new(0, 0, 0)
            meuHRP.Velocity = Vector3.new(0, 0, 0)
            meuHRP.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            meuHRP.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
            if killSheriffPos then
                meuHRP.CFrame = killSheriffPos
                killSheriffPos = nil
            end
        end
    end
end

MM2Tab:Button({
    Title = "Jogar Assassino Longe",
    Callback = function()
        if killAssassinActive then
            pararJogarAssassino()
            return
        end
        
        local assassino = encontrarPorPapel("Assassino")
        if not assassino then
            Notify("Nenhum assassino encontrado", "error")
            return
        end
        
        local targetChar = assassino.Character
        local myChar = GetChar()
        
        if not targetChar or not myChar then
            Notify("Personagem não encontrado", "error")
            return
        end
        
        local targetHRP = GetHRP(targetChar)
        local myHRP = GetHRP(myChar)
        
        if not targetHRP or not myHRP then
            Notify("HumanoidRootPart não encontrado", "error")
            return
        end
        
        killAssassinActive = true
        killAssassinPos = myHRP.CFrame
        
        LoopManager:Add("KillAssassinFling", function()
            if not killAssassinActive then
                LoopManager:Remove("KillAssassinFling")
                return
            end
            
            local currentAssassino = encontrarPorPapel("Assassino")
            if not currentAssassino or not currentAssassino.Character then
                pararJogarAssassino()
                return
            end
            
            local targetChar = currentAssassino.Character
            local targetHRP = GetHRP(targetChar)
            local myHRP = GetHRP()
            
            if not targetHRP or not myHRP then
                pararJogarAssassino()
                return
            end
            
            local humanoid = GetHumanoid(targetChar)
            if not humanoid or humanoid.Health <= 0 then
                pararJogarAssassino()
                return
            end
            
            myHRP.AssemblyLinearVelocity = Vector3.new(
                math.random(-500, 500),
                math.random(400, 900),
                math.random(-500, 500)
            )
            
            myHRP.AssemblyAngularVelocity = Vector3.new(
                math.random(-30, 30),
                math.random(-30, 30),
                math.random(-30, 30)
            )
            
            myHRP.RotVelocity = Vector3.new(
                math.random(8000, 12000),
                math.random(8000, 12000),
                math.random(8000, 12000)
            )
            
            local randomAngle = CFrame.Angles(
                math.rad(math.random(-360, 360)),
                math.rad(math.random(-360, 360)),
                math.rad(math.random(-360, 360))
            )
            
            local yOffset = math.random(-6, 6)
            
            myHRP.CFrame = targetHRP.CFrame *
                CFrame.new(
                    math.random(-1, 1),
                    yOffset,
                    math.random(-1, 1)
                ) * randomAngle
            
            targetHRP.AssemblyLinearVelocity = Vector3.new(
                math.random(-200, 200),
                math.random(300, 600),
                math.random(-200, 200)
            )
        end)
    end
})

MM2Tab:Button({
    Title = "Jogar Xerife Longe",
    Callback = function()
        if killSheriffActive then
            pararJogarXerife()
            return
        end
        
        local xerife = encontrarPorPapel("Xerife")
        if not xerife then
            Notify("Nenhum xerife encontrado", "error")
            return
        end
        
        local targetChar = xerife.Character
        local myChar = GetChar()
        
        if not targetChar or not myChar then
            Notify("Personagem não encontrado", "error")
            return
        end
        
        local targetHRP = GetHRP(targetChar)
        local myHRP = GetHRP(myChar)
        
        if not targetHRP or not myHRP then
            Notify("HumanoidRootPart não encontrado", "error")
            return
        end
        
        killSheriffActive = true
        killSheriffPos = myHRP.CFrame
        
        LoopManager:Add("KillSheriffFling", function()
            if not killSheriffActive then
                LoopManager:Remove("KillSheriffFling")
                return
            end
            
            local currentXerife = encontrarPorPapel("Xerife")
            if not currentXerife or not currentXerife.Character then
                pararJogarXerife()
                return
            end
            
            local targetChar = currentXerife.Character
            local targetHRP = GetHRP(targetChar)
            local myHRP = GetHRP()
            
            if not targetHRP or not myHRP then
                pararJogarXerife()
                return
            end
            
            local humanoid = GetHumanoid(targetChar)
            if not humanoid or humanoid.Health <= 0 then
                pararJogarXerife()
                return
            end
            
            myHRP.AssemblyLinearVelocity = Vector3.new(
                math.random(-500, 500),
                math.random(400, 900),
                math.random(-500, 500)
            )
            
            myHRP.AssemblyAngularVelocity = Vector3.new(
                math.random(-30, 30),
                math.random(-30, 30),
                math.random(-30, 30)
            )
            
            myHRP.RotVelocity = Vector3.new(
                math.random(8000, 12000),
                math.random(8000, 12000),
                math.random(8000, 12000)
            )
            
            local randomAngle = CFrame.Angles(
                math.rad(math.random(-360, 360)),
                math.rad(math.random(-360, 360)),
                math.rad(math.random(-360, 360))
            )
            
            local yOffset = math.random(-6, 6)
            
            myHRP.CFrame = targetHRP.CFrame *
                CFrame.new(
                    math.random(-1, 1),
                    yOffset,
                    math.random(-1, 1)
                ) * randomAngle
            
            targetHRP.AssemblyLinearVelocity = Vector3.new(
                math.random(-200, 200),
                math.random(300, 600),
                math.random(-200, 200)
            )
        end)
    end
})

local function pararFlingAll()
    if flingAllActive then
        flingAllActive = false
        flingAllQueue = {}
        flingAllCurrentTarget = nil
        if flingAllConnection then
            pcall(function() flingAllConnection:Disconnect() end)
            flingAllConnection = nil
        end
        if flingAllCheckConnection then
            pcall(function() flingAllCheckConnection:Disconnect() end)
            flingAllCheckConnection = nil
        end
        LoopManager:Remove("FlingAll")
        Notify("Jogar todos longe desativado", "info")
    end
end

local function flingarJogador(alvo)
    if not alvo or not alvo.Character then
        processarProximoAlvo()
        return false
    end
    
    local targetChar = alvo.Character
    local myChar = GetChar()
    
    if not targetChar or not myChar then
        processarProximoAlvo()
        return false
    end
    
    local targetHRP = GetHRP(targetChar)
    local myHRP = GetHRP(myChar)
    
    if not targetHRP or not myHRP then
        processarProximoAlvo()
        return false
    end
    
    local humanoid = GetHumanoid(targetChar)
    if not humanoid or humanoid.Health <= 0 then
        processarProximoAlvo()
        return false
    end
    
    flingAllCurrentTarget = alvo
    
    local posicaoInicial = targetHRP.Position
    local flingTimeout = tick() + 5
    
    if flingAllLoopRunning then
        LoopManager:Remove("FlingAll")
        flingAllLoopRunning = false
    end
    
    LoopManager:Add("FlingAll", function()
        if not flingAllActive or not flingAllCurrentTarget then
            if flingAllLoopRunning then
                LoopManager:Remove("FlingAll")
                flingAllLoopRunning = false
            end
            return
        end
        
        if tick() > flingTimeout then
            processarProximoAlvo()
            return
        end
        
        local currentTargetChar = flingAllCurrentTarget.Character
        if not currentTargetChar then
            processarProximoAlvo()
            return
        end
        
        local targetHRP = GetHRP(currentTargetChar)
        local myHRP = GetHRP()
        
        if not targetHRP or not myHRP then
            return
        end
        
        local humanoid = GetHumanoid(currentTargetChar)
        if not humanoid or humanoid.Health <= 0 then
            processarProximoAlvo()
            return
        end
        
        local distanciaAtual = (targetHRP.Position - posicaoInicial).Magnitude
        if distanciaAtual > 100 then
            processarProximoAlvo()
            return
        end
        
        myHRP.AssemblyLinearVelocity = Vector3.new(
            math.random(-500, 500),
            math.random(400, 900),
            math.random(-500, 500)
        )
        
        myHRP.AssemblyAngularVelocity = Vector3.new(
            math.random(-30, 30),
            math.random(-30, 30),
            math.random(-30, 30)
        )
        
        myHRP.RotVelocity = Vector3.new(
            math.random(8000, 12000),
            math.random(8000, 12000),
            math.random(8000, 12000)
        )
            
        local randomAngle = CFrame.Angles(
            math.rad(math.random(-360, 360)),
            math.rad(math.random(-360, 360)),
            math.rad(math.random(-360, 360))
        )
        
        local yOffset = math.random(-6, 6)
        
        myHRP.CFrame = targetHRP.CFrame *
            CFrame.new(
                math.random(-1, 1),
                yOffset,
                math.random(-1, 1)
            ) * randomAngle
        
        targetHRP.AssemblyLinearVelocity = Vector3.new(
            math.random(-200, 200),
            math.random(300, 600),
            math.random(-200, 200)
        )
    end)
    flingAllLoopRunning = true
    
    local function onTargetDied()
        if flingAllActive then
            task.wait(0.5)
            processarProximoAlvo()
        end
    end
    
    if flingAllConnection then
        pcall(function() flingAllConnection:Disconnect() end)
    end
    flingAllConnection = humanoid.Died:Connect(onTargetDied)
    
    return true
end

local flingAllLoopRunning = false

local function processarProximoAlvo()
    if not flingAllActive then
        return
    end
    
    if flingAllLoopRunning then
        LoopManager:Remove("FlingAll")
        flingAllLoopRunning = false
    end
    
    if flingAllConnection then
        pcall(function() flingAllConnection:Disconnect() end)
        flingAllConnection = nil
    end
    if flingAllCheckConnection then
        pcall(function() flingAllCheckConnection:Disconnect() end)
        flingAllCheckConnection = nil
    end
    
    if #flingAllQueue == 0 then
        pararFlingAll()
        Notify("Todos os jogadores foram lançados!", "success")
        return
    end
    
    local proximo = table.remove(flingAllQueue, 1)
    flingarJogador(proximo)
end

MM2Tab:Button({
    Title = "Jogar Todos Longe",
    Callback = function()
        if flingAllActive then
            pararFlingAll()
            return
        end
        
        local myChar = GetChar()
        if not myChar then
            Notify("Personagem não encontrado", "error")
            return
        end
        
        local myHRP = GetHRP(myChar)
        if not myHRP then
            Notify("HumanoidRootPart não encontrado", "error")
            return
        end
        
        local myPos = myHRP.Position
        flingAllQueue = {}
        
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= player then
                local char = plr.Character
                if char then
                    local hrp = GetHRP(char)
                    if hrp then
                        local distancia = (hrp.Position - myPos).Magnitude
                        if distancia <= 300 then
                            table.insert(flingAllQueue, plr)
                        end
                    end
                end
            end
        end
        
        if #flingAllQueue == 0 then
            Notify("Nenhum jogador próximo encontrado", "error")
            return
        end
        
        flingAllActive = true
        
        processarProximoAlvo()
    end
})

local function monitorarAssassino()
    local assassino = encontrarPorPapel("Assassino")
    if assassino and assassino.Character then
        local humanoid = GetHumanoid(assassino.Character)
        if humanoid then
            if killAssassinConnection then
                killAssassinConnection:Disconnect()
            end
            killAssassinConnection = humanoid.Died:Connect(function()
                if killAssassinActive then
                    pararJogarAssassino()
                end
            end)
        end
    end
end

local function monitorarXerife()
    local xerife = encontrarPorPapel("Xerife")
    if xerife and xerife.Character then
        local humanoid = GetHumanoid(xerife.Character)
        if humanoid then
            if killSheriffConnection then
                killSheriffConnection:Disconnect()
            end
            killSheriffConnection = humanoid.Died:Connect(function()
                if killSheriffActive then
                    pararJogarXerife()
                end
            end)
        end
    end
end

local function atualizarMonitoramento()
    monitorarAssassino()
    monitorarXerife()
end

EventManager:Add("PlayerAdded", Players.PlayerAdded:Connect(function()
    atualizarMonitoramento()
end))

for _, plr in pairs(Players:GetPlayers()) do
    EventManager:Add("CharacterAdded_" .. plr.Name, plr.CharacterAdded:Connect(function()
        atualizarMonitoramento()
    end))
end

EventManager:Add("PlayerRemoving", Players.PlayerRemoving:Connect(function(p)
    if killAssassinActive and p == encontrarPorPapel("Assassino") then
        pararJogarAssassino()
    end
    if killSheriffActive and p == encontrarPorPapel("Xerife") then
        pararJogarXerife()
    end
end))

MM2Tab:Section({Title = "Utilidades"})

AutoFarmToggleElement = MM2Tab:Toggle({
    Title = "Auto Farm Moedas",
    Default = false,
    Callback = function(Value)
        if NoClipEnabled and Value then
            Notify("NoClip manual está ativo! Desative o NoClip primeiro.", "error")
            AutoFarmToggleElement:Set(false)
            return
        end
        AutoFarmToggle(Value)
    end
})

-- ================================ ABA GRÁFICOS ================================

local GraphicsTab = Window:Tab({Title = "Graphics", Icon = "sparkle"})

GraphicsTab:Section({Title = "Time Control"})

GraphicsTab:Slider({
    Title = "Clock Time",
    Step = 1,
    Value = {
        Min = 0,
        Max = 24,
        Default = 12,
    },
    Callback = function(Value)
        Lighting.ClockTime = Value
    end
})

GraphicsTab:Button({
    Title = "Morning (08:00)",
    Callback = function()
        Lighting.ClockTime = 8
    end
})

GraphicsTab:Button({
    Title = "Noon (14:00)",
    Callback = function()
        Lighting.ClockTime = 14
    end
})

GraphicsTab:Button({
    Title = "Night (00:00)",
    Callback = function()
        Lighting.ClockTime = 0
    end
})

GraphicsTab:Section({Title = "Performance"})

GraphicsTab:Toggle({
    Title = "Destravar FPS",
    Default = false,
    Callback = function(Value)
        FPSUnlocked = Value
        pcall(function()
            if Value then
                setfpscap(999)
            else
                setfpscap(60)
            end
        end)
    end
})

-- ================================ ABA ANIMAÇÕES ================================

local AnimationsTab = Window:Tab({Title = "Animations", Icon = "layers"})

AnimationsTab:Section({Title = "Emote Player"})

AnimationsTab:Dropdown({
    Title = "Emotes",
    Values = {
        "Minhoca", "Peixe", "Dança Shin Chan", "Footwork Funk Brasileiro 2", "Dança da Aranha",
        "Homem Aranha", "Invisível", "Sentar", "67", "Passinho do Jamal", "Rocker Festa", "Kareen",
        "Popular", "Falso Morto MM2", "Cagar", "Dança Hype", "Cachorro",
        "Abraçar", "Escorregador", "Rebolada"
    },
    Default = "Nenhum",
    Callback = function(Value)
        for _, emote in pairs(famousEmotes) do
            if emote.name == Value then
                playEmote(emote.id, emoteSpeed)
                break
            end
        end
    end
})

AnimationsTab:Button({
    Title = "Stop Animation",
    Callback = function()
        stopEmote()
    end
})

AnimationsTab:Slider({
    Title = "Emote Speed",
    Step = 1,
    Value = {
        Min = 1,
        Max = 5,
        Default = 1,
    },
    Callback = function(Value)
        emoteSpeed = Value
        if currentEmoteTrack then
            currentEmoteTrack:AdjustSpeed(emoteSpeed)
        end
    end
})

AnimationsTab:Section({Title = "Full Packs"})

AnimationsTab:Dropdown({
    Title = "Animation Packs",
    Values = {
        "Sneaky", "Patrol", "Ghost", "AdidasSports",
        "Levitation", "Werewolf", "Vampire", "Pirate", "Zombie",
        "Astronaut", "Ninja", "Mage", "Elder", "Toy",
        "Knight", "Cartoony", "SuperHero", "Bubbly", "Robot",
        "Stylish", "Confident", "Popstar", "Princess"
    },
    Default = "Nenhum",
    Callback = function(Value)
        if Value == "Nenhum" then
            selectedAnimationPack = nil
            return
        end
        selectedAnimationPack = Value
        aplicarPack(ANIMATION_PACKS[Value])
    end
})

-- ================================ ABA TELEPORT ================================

local TeleportTab = Window:Tab({Title = "Teleport", Icon = "map-pin"})

TeleportTab:Section({Title = "Principais"})

TeleportTab:Button({
    Title = "Save Pos",
    Callback = function()
        local char = GetChar()
        if not char then
            Notify("Personagem não encontrado", "error")
            return
        end
        
        local hrp = GetHRP(char)
        if not hrp then
            Notify("HumanoidRootPart não encontrado", "error")
            return
        end
        
        savedPosition = hrp.CFrame
        lastPositionTP = hrp.CFrame
        Notify("Posição salva!", "success")
    end
})

TeleportTab:Button({
    Title = "Load Pos",
    Callback = function()
        if not savedPosition then
            Notify("Nenhuma posição salva!", "error")
            return
        end
        
        local char = GetChar()
        if not char then
            Notify("Personagem não encontrado", "error")
            return
        end
        
        local hrp = GetHRP(char)
        if not hrp then
            Notify("HumanoidRootPart não encontrado", "error")
            return
        end
        
        lastPositionTP = hrp.CFrame
        hrp.CFrame = savedPosition
        Notify("Teleportado!", "success")
    end
})

TeleportTab:Button({
    Title = "Return (Last Pos)",
    Callback = function()
        if not lastPositionTP then
            Notify("Nenhuma posição anterior!", "error")
            return
        end
        
        local char = GetChar()
        if not char then
            Notify("Personagem não encontrado", "error")
            return
        end
        
        local hrp = GetHRP(char)
        if not hrp then
            Notify("HumanoidRootPart não encontrado", "error")
            return
        end
        
        hrp.CFrame = lastPositionTP
        Notify("Retornado!", "success")
    end
})
