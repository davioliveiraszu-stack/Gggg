-- ╔══════════════════════════════════════════╗
-- ║         NYX PANEL  |  Delta Mobile       ║
-- ║   ESP · Combat · Movement · Animations   ║
-- ╚══════════════════════════════════════════╝
-- Runtime: Roblox LuaU / Delta Mobile executor
-- FIXED: FloatBtn agora é TextButton (tem MouseButton1Click)

local Players    = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS        = game:GetService("UserInputService")
local TweenSvc   = game:GetService("TweenService")
local CoreGui    = game:GetService("CoreGui")
local HttpSvc    = game:GetService("HttpService")

local LP  = Players.LocalPlayer
local Cam = workspace.CurrentCamera

pcall(function() CoreGui:FindFirstChild("NyxUI"):Destroy() end)

local function NewDrawing(dtype)
    if not rawget(_G, "Drawing") and not Drawing then
        return setmetatable({Visible=false}, {
            __index    = function(t, k) return t[k] end,
            __newindex = function(t, k, v) rawset(t, k, v) end,
        })
    end
    return Drawing.new(dtype)
end

local function safeWritefile(path, data)
    if writefile then pcall(writefile, path, data) end
end
local function safeReadfile(path)
    if isfile and readfile and isfile(path) then
        local ok, res = pcall(readfile, path)
        if ok then return res end
    end
    return nil
end

local CFG = {
    BoxESP        = false, BoxColor    = Color3.fromRGB(255,50,50),
    SkelESP       = false, SkelColor   = Color3.fromRGB(50,255,50),
    LineESP       = false, LineColor   = Color3.fromRGB(50,150,255),
    HLEnabled     = false,
    HL_Fill       = Color3.fromRGB(255,255,255),
    HL_Outline    = Color3.fromRGB(255,50,50),
    HL_FillAlpha  = 0.7,
    TeamCheck     = false,
    AimEnabled    = false, AimFOV     = 150, AimSmooth = 0.15, AimPart = "Head",
    SpeedOn       = false, SpeedVal   = 50,
    FlyOn         = false, FlySpeed   = 80,
}

local T = {
    bg      = Color3.fromRGB(10,10,18),
    panel   = Color3.fromRGB(15,15,25),
    tab     = Color3.fromRGB(20,20,35),
    tabAct  = Color3.fromRGB(110,70,240),
    accent  = Color3.fromRGB(110,70,240),
    accent2 = Color3.fromRGB(70,140,255),
    txt     = Color3.fromRGB(225,225,240),
    sub     = Color3.fromRGB(150,150,175),
    tog_on  = Color3.fromRGB(110,70,240),
    tog_off = Color3.fromRGB(45,45,65),
    slbg    = Color3.fromRGB(35,35,55),
    border  = Color3.fromRGB(55,55,85),
}

local function Tween(obj, props, t, style, dir)
    pcall(function()
        TweenSvc:Create(obj,
            TweenInfo.new(t or 0.2, style or Enum.EasingStyle.Quad, dir or Enum.EasingDirection.Out),
            props):Play()
    end)
end

local function MakeCorner(parent, radius)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, radius or 8)
    c.Parent = parent
end

local function MakeGrad(parent, c1, c2, rot)
    local g = Instance.new("UIGradient")
    g.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, c1),
        ColorSequenceKeypoint.new(1, c2),
    })
    g.Rotation = rot or 0
    g.Parent = parent
end

local function MakePadding(parent, p)
    local pad = Instance.new("UIPadding")
    pad.PaddingLeft   = UDim.new(0, p)
    pad.PaddingRight  = UDim.new(0, p)
    pad.PaddingTop    = UDim.new(0, p)
    pad.PaddingBottom = UDim.new(0, p)
    pad.Parent = parent
end

local function MakeLabel(parent, text, size, color, pos, anchor)
    local l = Instance.new("TextLabel")
    l.BackgroundTransparency = 1
    l.Text = text or ""
    l.TextColor3 = color or T.txt
    l.TextSize = size or 13
    l.Font = Enum.Font.GothamBold
    l.Position = pos or UDim2.new(0,0,0,0)
    l.AnchorPoint = anchor or Vector2.new(0,0)
    l.Size = UDim2.new(1,0,0,(size and size+4) or 18)
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.Parent = parent
    return l
end

local SG = Instance.new("ScreenGui")
SG.Name = "NyxUI"
SG.ResetOnSpawn = false
SG.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
SG.DisplayOrder = 999
SG.IgnoreGuiInset = true
pcall(function() SG.Parent = CoreGui end)
if not SG.Parent then SG.Parent = LP.PlayerGui end

-- FIX: TextButton em vez de Frame
local FloatBtn = Instance.new("TextButton")
FloatBtn.Name            = "FloatBtn"
FloatBtn.Size            = UDim2.new(0,52,0,52)
FloatBtn.Position        = UDim2.new(0,14,0.45,0)
FloatBtn.BackgroundColor3 = T.accent
FloatBtn.BorderSizePixel = 0
FloatBtn.Text            = ""
FloatBtn.AutoButtonColor = false
FloatBtn.ZIndex          = 100
FloatBtn.Parent          = SG
MakeCorner(FloatBtn, 100)
MakeGrad(FloatBtn, T.accent, T.accent2, 135)

local FloatIcon = Instance.new("TextLabel")
FloatIcon.BackgroundTransparency = 1
FloatIcon.Size      = UDim2.new(1,0,1,0)
FloatIcon.Text      = "✦"
FloatIcon.TextColor3 = Color3.fromRGB(255,255,255)
FloatIcon.TextSize  = 22
FloatIcon.Font      = Enum.Font.GothamBold
FloatIcon.ZIndex    = 101
FloatIcon.Parent    = FloatBtn

local spinAngle = 0
RunService.Heartbeat:Connect(function(dt)
    spinAngle = (spinAngle + dt * 55) % 360
    FloatBtn.Rotation = spinAngle
end)

local Panel = Instance.new("Frame")
Panel.Name = "Panel"
Panel.Size = UDim2.new(0,320,0,400)
Panel.Position = UDim2.new(0.5,-160,0.5,-200)
Panel.BackgroundColor3 = T.panel
Panel.BorderSizePixel = 0
Panel.Visible = false
Panel.ZIndex = 10
Panel.ClipsDescendants = true
Panel.Parent = SG
MakeCorner(Panel, 14)

local AnimeBG = Instance.new("ImageLabel")
AnimeBG.Size = UDim2.new(1,0,1,0)
AnimeBG.BackgroundTransparency = 1
AnimeBG.Image = "rbxassetid://7072706234"
AnimeBG.ImageTransparency = 0.65
AnimeBG.ScaleType = Enum.ScaleType.Crop
AnimeBG.ZIndex = 10
AnimeBG.Parent = Panel
MakeCorner(AnimeBG, 14)

local Overlay = Instance.new("Frame")
Overlay.Size = UDim2.new(1,0,1,0)
Overlay.BackgroundColor3 = T.bg
Overlay.BackgroundTransparency = 0.25
Overlay.BorderSizePixel = 0
Overlay.ZIndex = 11
Overlay.Parent = Panel
MakeCorner(Overlay, 14)

local BorderFrame = Instance.new("Frame")
BorderFrame.Size = UDim2.new(1,0,0,2)
BorderFrame.Position = UDim2.new(0,0,0,0)
BorderFrame.BackgroundColor3 = T.accent
BorderFrame.BorderSizePixel = 0
BorderFrame.ZIndex = 20
BorderFrame.Parent = Panel
MakeGrad(BorderFrame, T.accent, T.accent2, 0)

local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1,0,0,42)
TitleBar.Position = UDim2.new(0,0,0,2)
TitleBar.BackgroundTransparency = 1
TitleBar.ZIndex = 20
TitleBar.Parent = Panel

local TitleLbl = Instance.new("TextLabel")
TitleLbl.BackgroundTransparency = 1
TitleLbl.Size = UDim2.new(1,-50,1,0)
TitleLbl.Position = UDim2.new(0,14,0,0)
TitleLbl.Text = "✦ NYX PANEL"
TitleLbl.TextColor3 = Color3.fromRGB(255,255,255)
TitleLbl.TextSize = 15
TitleLbl.Font = Enum.Font.GothamBold
TitleLbl.TextXAlignment = Enum.TextXAlignment.Left
TitleLbl.ZIndex = 21
TitleLbl.Parent = TitleBar

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0,28,0,28)
CloseBtn.Position = UDim2.new(1,-38,0.5,-14)
CloseBtn.BackgroundColor3 = Color3.fromRGB(200,50,80)
CloseBtn.BorderSizePixel = 0
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255,255,255)
CloseBtn.TextSize = 13
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.ZIndex = 22
CloseBtn.Parent = TitleBar
MakeCorner(CloseBtn, 6)

local dragging, dragStart, startPos = false, nil, nil
TitleBar.InputBegan:Connect(function(inp)
    if inp.UserInputType == Enum.UserInputType.MouseButton1
    or inp.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = inp.Position
        startPos = Panel.Position
    end
end)
UIS.InputChanged:Connect(function(inp)
    if dragging and (
        inp.UserInputType == Enum.UserInputType.MouseMovement or
        inp.UserInputType == Enum.UserInputType.Touch) then
        local delta = inp.Position - dragStart
        Panel.Position = UDim2.new(
            startPos.X.Scale, startPos.X.Offset + delta.X,
            startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
UIS.InputEnded:Connect(function(inp)
    if inp.UserInputType == Enum.UserInputType.MouseButton1
    or inp.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

local TabBar = Instance.new("Frame")
TabBar.Size = UDim2.new(1,0,0,32)
TabBar.Position = UDim2.new(0,0,0,44)
TabBar.BackgroundColor3 = T.tab
TabBar.BorderSizePixel = 0
TabBar.ZIndex = 20
TabBar.Parent = Panel

local TabLayout = Instance.new("UIListLayout")
TabLayout.FillDirection = Enum.FillDirection.Horizontal
TabLayout.SortOrder = Enum.SortOrder.LayoutOrder
TabLayout.Parent = TabBar

local ContentArea = Instance.new("Frame")
ContentArea.Size = UDim2.new(1,0,1,-78)
ContentArea.Position = UDim2.new(0,0,0,78)
ContentArea.BackgroundTransparency = 1
ContentArea.ZIndex = 20
ContentArea.Parent = Panel

local tabs = {}
local activeTab = nil
local tabDefs = {"ESP","Combat","Move","Anim","Config"}

local function MakeScrollFrame(parent)
    local sf = Instance.new("ScrollingFrame")
    sf.Size = UDim2.new(1,0,1,0)
    sf.BackgroundTransparency = 1
    sf.BorderSizePixel = 0
    sf.ScrollBarThickness = 3
    sf.ScrollBarImageColor3 = T.accent
    sf.CanvasSize = UDim2.new(0,0,0,0)
    sf.AutomaticCanvasSize = Enum.AutomaticSize.Y
    sf.ZIndex = 21
    sf.Parent = parent
    MakePadding(sf, 8)
    local layout = Instance.new("UIListLayout")
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0,6)
    layout.Parent = sf
    return sf
end

local function SelectTab(name)
    if activeTab == name then return end
    activeTab = name
    for n, t in pairs(tabs) do
        t.btn.BackgroundColor3 = (n == name) and T.tabAct or T.tab
        t.btn.TextColor3 = (n == name) and Color3.fromRGB(255,255,255) or T.sub
        t.frame.Visible = (n == name)
    end
end

for i, name in ipairs(tabDefs) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0,64,1,0)
    btn.BackgroundColor3 = T.tab
    btn.BorderSizePixel = 0
    btn.Text = name
    btn.TextColor3 = T.sub
    btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold
    btn.LayoutOrder = i
    btn.ZIndex = 22
    btn.Parent = TabBar

    local frame = MakeScrollFrame(ContentArea)
    frame.Visible = false
    frame.ZIndex = 22

    tabs[name] = {btn = btn, frame = frame}
    btn.MouseButton1Click:Connect(function() SelectTab(name) end)
end

local function MakeToggle(parent, label, state, callback)
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1,-16,0,36)
    row.BackgroundColor3 = T.tab
    row.BorderSizePixel = 0
    row.ZIndex = 25
    row.Parent = parent
    MakeCorner(row, 8)

    local lbl = MakeLabel(row, label, 12, T.txt, UDim2.new(0,10,0.5,-6))
    lbl.ZIndex = 26

    local track = Instance.new("Frame")
    track.Size = UDim2.new(0,36,0,18)
    track.Position = UDim2.new(1,-46,0.5,-9)
    track.BackgroundColor3 = state and T.tog_on or T.tog_off
    track.BorderSizePixel = 0
    track.ZIndex = 26
    track.Parent = row
    MakeCorner(track, 9)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0,14,0,14)
    knob.Position = state and UDim2.new(1,-16,0.5,-7) or UDim2.new(0,2,0.5,-7)
    knob.BackgroundColor3 = Color3.fromRGB(255,255,255)
    knob.BorderSizePixel = 0
    knob.ZIndex = 27
    knob.Parent = track
    MakeCorner(knob, 7)

    local val = state
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1,0,1,0)
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.ZIndex = 28
    btn.Parent = row

    btn.MouseButton1Click:Connect(function()
        val = not val
        Tween(track, {BackgroundColor3 = val and T.tog_on or T.tog_off}, 0.18)
        Tween(knob, {Position = val and UDim2.new(1,-16,0.5,-7) or UDim2.new(0,2,0.5,-7)}, 0.18)
        callback(val)
    end)

    return row
end

local function MakeSlider(parent, label, min, max, default, fmt, callback)
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1,-16,0,52)
    row.BackgroundColor3 = T.tab
    row.BorderSizePixel = 0
    row.ZIndex = 25
    row.Parent = parent
    MakeCorner(row, 8)

    local lbl = MakeLabel(row, label, 11, T.txt, UDim2.new(0,10,0,6))
    lbl.ZIndex = 26

    local valLbl = MakeLabel(row, tostring(default), 11, T.accent, UDim2.new(1,-54,0,6))
    valLbl.TextXAlignment = Enum.TextXAlignment.Right
    valLbl.Size = UDim2.new(0,44,0,16)
    valLbl.ZIndex = 26

    local track = Instance.new("Frame")
    track.Size = UDim2.new(1,-20,0,6)
    track.Position = UDim2.new(0,10,1,-18)
    track.BackgroundColor3 = T.slbg
    track.BorderSizePixel = 0
    track.ZIndex = 26
    track.Parent = row
    MakeCorner(track, 3)

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default-min)/(max-min),0,1,0)
    fill.BackgroundColor3 = T.accent
    fill.BorderSizePixel = 0
    fill.ZIndex = 27
    fill.Parent = track
    MakeCorner(fill, 3)
    MakeGrad(fill, T.accent, T.accent2, 0)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0,14,0,14)
    knob.Position = UDim2.new((default-min)/(max-min),0,0.5,-7)
    knob.AnchorPoint = Vector2.new(0.5,0)
    knob.BackgroundColor3 = Color3.fromRGB(255,255,255)
    knob.BorderSizePixel = 0
    knob.ZIndex = 28
    knob.Parent = track
    MakeCorner(knob, 7)

    local val = default
    local draggingSlider = false

    local function Update(x)
        local rel = math.clamp((x - track.AbsolutePosition.X) / track.AbsoluteSize.X, 0, 1)
        val = math.floor(min + (max - min) * rel)
        fill.Size = UDim2.new(rel,0,1,0)
        knob.Position = UDim2.new(rel,0,0.5,-7)
        valLbl.Text = fmt and string.format(fmt, val) or tostring(val)
        callback(val)
    end

    track.InputBegan:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.MouseButton1
        or inp.UserInputType == Enum.UserInputType.Touch then
            draggingSlider = true
            Update(inp.Position.X)
        end
    end)
    UIS.InputChanged:Connect(function(inp)
        if draggingSlider and (
            inp.UserInputType == Enum.UserInputType.MouseMovement or
            inp.UserInputType == Enum.UserInputType.Touch) then
            Update(inp.Position.X)
        end
    end)
    UIS.InputEnded:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.MouseButton1
        or inp.UserInputType == Enum.UserInputType.Touch then
            draggingSlider = false
        end
    end)

    return row
end

local COLORS = {
    {Color3.fromRGB(255,50,50),   "Red"},
    {Color3.fromRGB(50,255,50),   "Green"},
    {Color3.fromRGB(50,150,255),  "Blue"},
    {Color3.fromRGB(255,255,50),  "Yellow"},
    {Color3.fromRGB(255,100,255), "Pink"},
    {Color3.fromRGB(255,165,0),   "Orange"},
    {Color3.fromRGB(255,255,255), "White"},
    {Color3.fromRGB(180,50,255),  "Purple"},
}

local function MakeColorPicker(parent, label, default, callback)
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1,-16,0,56)
    row.BackgroundColor3 = T.tab
    row.BorderSizePixel = 0
    row.ZIndex = 25
    row.Parent = parent
    MakeCorner(row, 8)

    local lbl = MakeLabel(row, label, 11, T.txt, UDim2.new(0,10,0,4))
    lbl.ZIndex = 26

    local swatchFrame = Instance.new("Frame")
    swatchFrame.Size = UDim2.new(1,-20,0,24)
    swatchFrame.Position = UDim2.new(0,10,1,-30)
    swatchFrame.BackgroundTransparency = 1
    swatchFrame.ZIndex = 26
    swatchFrame.Parent = row

    local swatchLayout = Instance.new("UIListLayout")
    swatchLayout.FillDirection = Enum.FillDirection.Horizontal
    swatchLayout.Padding = UDim.new(0,4)
    swatchLayout.Parent = swatchFrame

    for _, pair in ipairs(COLORS) do
        local sw = Instance.new("TextButton")
        sw.Size = UDim2.new(0,22,0,22)
        sw.BackgroundColor3 = pair[1]
        sw.BorderSizePixel = 0
        sw.Text = ""
        sw.ZIndex = 27
        sw.Parent = swatchFrame
        MakeCorner(sw, 4)
        sw.MouseButton1Click:Connect(function() callback(pair[1]) end)
    end

    return row
end

local function MakeSection(parent, text)
    local s = Instance.new("TextLabel")
    s.Size = UDim2.new(1,-16,0,20)
    s.BackgroundTransparency = 1
    s.Text = "  " .. text
    s.TextColor3 = T.accent
    s.TextSize = 11
    s.Font = Enum.Font.GothamBold
    s.TextXAlignment = Enum.TextXAlignment.Left
    s.ZIndex = 25
    s.Parent = parent
    return s
end

local function MakeButton(parent, text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1,-16,0,34)
    btn.BackgroundColor3 = T.accent
    btn.BorderSizePixel = 0
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.TextSize = 12
    btn.Font = Enum.Font.GothamBold
    btn.ZIndex = 25
    btn.Parent = parent
    MakeCorner(btn, 8)
    MakeGrad(btn, T.accent, T.accent2, 45)
    btn.MouseButton1Click:Connect(callback)
    return btn
end

local ESPObjects = {}
local HighlightObjects = {}

local SKELETON_PAIRS = {
    {"Head","UpperTorso"},
    {"UpperTorso","LowerTorso"},
    {"UpperTorso","LeftUpperArm"},
    {"LeftUpperArm","LeftLowerArm"},
    {"LeftLowerArm","LeftHand"},
    {"UpperTorso","RightUpperArm"},
    {"RightUpperArm","RightLowerArm"},
    {"RightLowerArm","RightHand"},
    {"LowerTorso","LeftUpperLeg"},
    {"LeftUpperLeg","LeftLowerLeg"},
    {"LeftLowerLeg","LeftFoot"},
    {"LowerTorso","RightUpperLeg"},
    {"RightUpperLeg","RightLowerLeg"},
    {"RightLowerLeg","RightFoot"},
}

local function WorldToViewport(pos)
    local vp, onScreen = Cam:WorldToViewportPoint(pos)
    return Vector2.new(vp.X, vp.Y), onScreen, vp.Z
end

local function GetBoundingBox(char)
    local minX,minY,minZ =  math.huge, math.huge, math.huge
    local maxX,maxY,maxZ = -math.huge,-math.huge,-math.huge
    for _, part in ipairs(char:GetDescendants()) do
        if part:IsA("BasePart") then
            local pos  = part.Position
            local size = part.Size / 2
            minX = math.min(minX, pos.X - size.X)
            minY = math.min(minY, pos.Y - size.Y)
            minZ = math.min(minZ, pos.Z - size.Z)
            maxX = math.max(maxX, pos.X + size.X)
            maxY = math.max(maxY, pos.Y + size.Y)
            maxZ = math.max(maxZ, pos.Z + size.Z)
        end
    end
    return Vector3.new(minX,minY,minZ), Vector3.new(maxX,maxY,maxZ)
end

local function ClearESP(player)
    if ESPObjects[player] then
        local obj = ESPObjects[player]
        if obj.box then
            for _, l in pairs(obj.box) do pcall(function() l.Visible = false end) end
        end
        if obj.skel then
            for _, l in ipairs(obj.skel) do pcall(function() l.Visible = false end) end
        end
        pcall(function() if obj.line then obj.line.Visible = false end end)
        pcall(function() if obj.nametag then obj.nametag.Visible = false end end)
        ESPObjects[player] = nil
    end
    if HighlightObjects[player] then
        pcall(function() HighlightObjects[player]:Destroy() end)
        HighlightObjects[player] = nil
    end
end

local function SetupESP(player)
    if player == LP then return end
    ClearESP(player)

    local box = {
        top   = NewDrawing("Line"),
        bot   = NewDrawing("Line"),
        left  = NewDrawing("Line"),
        right = NewDrawing("Line"),
    }
    for _, l in pairs(box) do
        pcall(function()
            l.Thickness = 1.5
            l.Transparency = 1
            l.Visible = false
        end)
    end

    local skel = {}
    for i = 1, #SKELETON_PAIRS do
        skel[i] = NewDrawing("Line")
        pcall(function()
            skel[i].Thickness = 1
            skel[i].Transparency = 1
            skel[i].Visible = false
        end)
    end

    local line = NewDrawing("Line")
    pcall(function()
        line.Thickness = 1
        line.Transparency = 1
        line.Visible = false
    end)

    local nametag = NewDrawing("Text")
    pcall(function()
        nametag.Size = 13
        nametag.Center = true
        nametag.Outline = true
        nametag.Color = T.txt
        nametag.Visible = false
    end)

    ESPObjects[player] = {box = box, skel = skel, line = line, nametag = nametag}
end

local function IsEnemy(player)
    if not CFG.TeamCheck then return true end
    return player.Team \~= LP.Team
end

RunService.RenderStepped:Connect(function()
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LP then continue end
        local obj = ESPObjects[player]
        if not obj then continue end

        local char  = player.Character
        local root  = char and char:FindFirstChild("HumanoidRootPart")
        local head  = char and char:FindFirstChild("Head")
        local alive = char and root and head and IsEnemy(player)

        local screenRoot, onScreen
        if alive then
            screenRoot, onScreen = WorldToViewport(root.Position)
        end

        if CFG.BoxESP and alive and onScreen then
            local min3d, max3d = GetBoundingBox(char)
            local corners = {
                Vector3.new(min3d.X,min3d.Y,min3d.Z), Vector3.new(max3d.X,min3d.Y,min3d.Z),
                Vector3.new(max3d.X,max3d.Y,min3d.Z), Vector3.new(min3d.X,max3d.Y,min3d.Z),
                Vector3.new(min3d.X,min3d.Y,max3d.Z), Vector3.new(max3d.X,min3d.Y,max3d.Z),
                Vector3.new(max3d.X,max3d.Y,max3d.Z), Vector3.new(min3d.X,max3d.Y,max3d.Z),
            }
            local minSX,minSY,maxSX,maxSY = math.huge,math.huge,-math.huge,-math.huge
            local allOn = true
            for _, c in ipairs(corners) do
                local s, on = WorldToViewport(c)
                if not on then allOn = false end
                minSX = math.min(minSX,s.X); minSY = math.min(minSY,s.Y)
                maxSX = math.max(maxSX,s.X); maxSY = math.max(maxSY,s.Y)
            end
            if allOn then
                local b, col = obj.box, CFG.BoxColor
                pcall(function()
                    b.top.From   = Vector2.new(minSX,minSY); b.top.To   = Vector2.new(maxSX,minSY); b.top.Color   = col; b.top.Visible   = true
                    b.bot.From   = Vector2.new(minSX,maxSY); b.bot.To   = Vector2.new(maxSX,maxSY); b.bot.Color   = col; b.bot.Visible   = true
                    b.left.From  = Vector2.new(minSX,minSY); b.left.To  = Vector2.new(minSX,maxSY); b.left.Color  = col; b.left.Visible  = true
                    b.right.From = Vector2.new(maxSX,minSY); b.right.To = Vector2.new(maxSX,maxSY); b.right.Color = col; b.right.Visible = true
                    obj.nametag.Position = Vector2.new((minSX+maxSX)/2, minSY-16)
                    obj.nametag.Text     = player.DisplayName
                    obj.nametag.Visible  = true
                end)
            else
                pcall(function()
                    for _, l in pairs(obj.box) do l.Visible = false end
                    obj.nametag.Visible = false
                end)
            end
        else
            pcall(function()
                for _, l in pairs(obj.box) do l.Visible = false end
                obj.nametag.Visible = false
            end)
        end

        if CFG.SkelESP and alive then
            for i, pair in ipairs(SKELETON_PAIRS) do
                local a  = char:FindFirstChild(pair[1])
                local b2 = char:FindFirstChild(pair[2])
                if a and b2 then
                    local s1, on1 = WorldToViewport(a.Position)
                    local s2, on2 = WorldToViewport(b2.Position)
                    pcall(function()
                        if on1 and on2 then
                            obj.skel[i].From = s1; obj.skel[i].To = s2
                            obj.skel[i].Color = CFG.SkelColor
                            obj.skel[i].Visible = true
                        else
                            obj.skel[i].Visible = false
                        end
                    end)
                end
            end
        else
            pcall(function()
                for _, l in ipairs(obj.skel) do l.Visible = false end
            end)
        end

        if CFG.LineESP and alive and onScreen then
            local vps = Cam.ViewportSize
            pcall(function()
                obj.line.From      = Vector2.new(vps.X/2, vps.Y)
                obj.line.To        = screenRoot
                obj.line.Color     = CFG.LineColor
                obj.line.Thickness = 1
                obj.line.Visible   = true
            end)
        else
            pcall(function() obj.line.Visible = false end)
        end

        if CFG.HLEnabled and alive then
            if not HighlightObjects[player] then
                local ok, hl = pcall(function()
                    local h = Instance.new("Highlight")
                    h.FillColor          = CFG.HL_Fill
                    h.OutlineColor       = CFG.HL_Outline
                    h.FillTransparency   = CFG.HL_FillAlpha
                    h.Adornee            = char
                    h.Parent             = CoreGui
                    return h
                end)
                if ok then HighlightObjects[player] = hl end
            end
        else
            if HighlightObjects[player] then
                pcall(function() HighlightObjects[player]:Destroy() end)
                HighlightObjects[player] = nil
            end
        end
    end
end)

Players.PlayerRemoving:Connect(ClearESP)
Players.PlayerAdded:Connect(function(p)
    p.CharacterAdded:Connect(function()
        task.wait(1)
        SetupESP(p)
    end)
end)
for _, p in ipairs(Players:GetPlayers()) do
    if p \~= LP then SetupESP(p) end
end

local AimCircle = NewDrawing("Circle")
pcall(function()
    AimCircle.Visible   = false
    AimCircle.Thickness = 1.5
    AimCircle.Color     = Color3.fromRGB(255,255,255)
    AimCircle.Filled    = false
    AimCircle.NumSides  = 64
end)

RunService.RenderStepped:Connect(function()
    local vps = Cam.ViewportSize
    pcall(function()
        AimCircle.Position = Vector2.new(vps.X/2, vps.Y/2)
        AimCircle.Radius   = CFG.AimFOV
        AimCircle.Visible  = CFG.AimEnabled
    end)
end)

local function GetClosestTarget()
    local vps    = Cam.ViewportSize
    local center = Vector2.new(vps.X/2, vps.Y/2)
    local closest, closestDist = nil, math.huge
    for _, p in ipairs(Players:GetPlayers()) do
        if p == LP or not IsEnemy(p) then continue end
        local char = p.Character
        if not char then continue end
        local part = char:FindFirstChild(CFG.AimPart) or char:FindFirstChild("Head")
        if not part then continue end
        local s2, on = WorldToViewport(part.Position)
        if not on then continue end
        local dist = (s2 - center).Magnitude
        if dist < CFG.AimFOV and dist < closestDist then
            closest     = part
            closestDist = dist
        end
    end
    return closest
end

RunService.RenderStepped:Connect(function()
    if not CFG.AimEnabled then return end
    local target = GetClosestTarget()
    if not target then return end
    local _, on = WorldToViewport(target.Position)
    if not on then return end
    local currentCF = Cam.CFrame
    local dir       = (target.Position - currentCF.Position).Unit
    local targetCF  = CFrame.lookAt(currentCF.Position, currentCF.Position + dir)
    pcall(function() Cam.CFrame = currentCF:Lerp(targetCF, CFG.AimSmooth) end)
end)

RunService.Heartbeat:Connect(function()
    if not CFG.SpeedOn then return end
    local char = LP.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if hum then hum.WalkSpeed = CFG.SpeedVal end
end)

local flyConn, flyBV, flyAG

local function StartFly()
    local char = LP.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if hum then pcall(function() hum.PlatformStand = true end) end

    flyBV = Instance.new("BodyVelocity")
    flyBV.MaxForce = Vector3.new(1e9,1e9,1e9)
    flyBV.Velocity = Vector3.zero
    flyBV.Parent   = root

    flyAG = Instance.new("BodyAngularVelocity")
    flyAG.AngularVelocity = Vector3.zero
    flyAG.MaxTorque       = Vector3.new(1e9,1e9,1e9)
    flyAG.Parent          = root

    flyConn = RunService.Heartbeat:Connect(function()
        if not CFG.FlyOn then return end
        local cf  = Cam.CFrame
        local vel = Vector3.zero
        if UIS:IsKeyDown(Enum.KeyCode.W)         then vel = vel + cf.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.S)         then vel = vel - cf.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.A)         then vel = vel - cf.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.D)         then vel = vel + cf.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.Space)     then vel = vel + Vector3.new(0,1,0) end
        if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then vel = vel - Vector3.new(0,1,0) end
        flyBV.Velocity = vel * CFG.FlySpeed
    end)
end

local function StopFly()
    if flyConn then flyConn:Disconnect(); flyConn = nil end
    pcall(function() if flyBV then flyBV:Destroy(); flyBV = nil end end)
    pcall(function() if flyAG then flyAG:Destroy(); flyAG = nil end end)
    local char = LP.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        pcall(function() if hum then hum.PlatformStand = false end end)
    end
end

local EMOTES = {
    {name="Wave",       id="128777973"},
    {name="Point",      id="128773449"},
    {name="Cheer",      id="129423030"},
    {name="Laugh",      id="129423131"},
    {name="Dance",      id="182436215"},
    {name="Dance 2",    id="182436215"},
    {name="Salute",     id="3393860007"},
    {name="Tilt",       id="3869685646"},
    {name="Robot",      id="3869285686"},
    {name="Shrug",      id="3973211385"},
    {name="Facepalm",   id="3338330615"},
    {name="Air Guitar", id="5915772928"},
}

local WALK_ANIMS = {
    {name="Default",  id="180426354"},
    {name="Ninja",    id="182724289"},
    {name="Cartoony", id="182393478"},
    {name="Cowboy",   id="182765429"},
    {name="Robot",    id="182772487"},
    {name="Stylish",  id="182770523"},
    {name="Zombie",   id="616163682"},
    {name="Superhero",id="616066566"},
}

local IDLE_ANIMS = {
    {name="Default",   id="180435571"},
    {name="Ninja",     id="182729830"},
    {name="Superhero", id="616072836"},
    {name="Cartoony",  id="182393825"},
}

local JUMP_ANIMS = {
    {name="Default",  id="125750702"},
    {name="Ninja",    id="182721778"},
    {name="Cartoony", id="182393820"},
}

local currentEmoteTrack = nil

local function PlayEmote(id)
    pcall(function()
        local char = LP.Character; if not char then return end
        local hum  = char:FindFirstChildOfClass("Humanoid"); if not hum then return end
        local anim = char:FindFirstChildOfClass("Animator") or hum:FindFirstChildOfClass("Animator")
        if not anim then return end
        if currentEmoteTrack then currentEmoteTrack:Stop() end
        local a = Instance.new("Animation")
        a.AnimationId = "rbxassetid://" .. id
        currentEmoteTrack = anim:LoadAnimation(a)
        currentEmoteTrack:Play()
    end)
end

local function SetAnimInScript(slot, id)
    pcall(function()
        local char = LP.Character; if not char then return end
        local animScript = char:FindFirstChild("Animate"); if not animScript then return end
        local node = animScript:FindFirstChild(slot); if not node then return end
        for _, v in ipairs(node:GetChildren()) do
            if v:IsA("StringValue") then v.Value = "rbxassetid://" .. id end
        end
    end)
end

local function UnlockEmotes()
    pcall(function()
        local char = LP.Character; if not char then return end
        local hum  = char:FindFirstChildOfClass("Humanoid"); if not hum then return end
        local hd   = hum:GetAppliedDescription()
        local newHD = hd:Clone()
        local emoteList = {}
        for _, e in ipairs(EMOTES) do emoteList[e.name] = {tonumber(e.id)} end
        newHD:SetEmotes(emoteList)
        newHD:SetEquippedEmotes({
            {Slot="Emote1",Name="Wave"},   {Slot="Emote2",Name="Cheer"},
            {Slot="Emote3",Name="Laugh"},  {Slot="Emote4",Name="Point"},
            {Slot="Emote5",Name="Dance"},  {Slot="Emote6",Name="Robot"},
            {Slot="Emote7",Name="Salute"}, {Slot="Emote8",Name="Tilt"},
        })
        hum:ApplyDescription(newHD)
    end)
end

local SAVE_PATH = "NyxCFG.json"

local function SaveCFG()
    local data = {
        BoxESP=CFG.BoxESP, SkelESP=CFG.SkelESP, LineESP=CFG.LineESP,
        HLEnabled=CFG.HLEnabled, TeamCheck=CFG.TeamCheck,
        AimEnabled=CFG.AimEnabled, AimFOV=CFG.AimFOV, AimSmooth=CFG.AimSmooth,
        SpeedOn=CFG.SpeedOn, SpeedVal=CFG.SpeedVal,
        FlyOn=CFG.FlyOn, FlySpeed=CFG.FlySpeed,
    }
    safeWritefile(SAVE_PATH, HttpSvc:JSONEncode(data))
end

local function LoadCFG()
    local raw = safeReadfile(SAVE_PATH)
    if not raw then return end
    local ok, data = pcall(function() return HttpSvc:JSONDecode(raw) end)
    if ok and type(data) == "table" then
        for k, v in pairs(data) do CFG[k] = v end
    end
end
LoadCFG()

local espF = tabs["ESP"].frame
MakeSection(espF, "■ BOX ESP")
MakeToggle(espF, "Box ESP", CFG.BoxESP, function(v) CFG.BoxESP = v end)
MakeColorPicker(espF, "Box Color", CFG.BoxColor, function(c) CFG.BoxColor = c end)

MakeSection(espF, "■ SKELETON ESP")
MakeToggle(espF, "Skeleton ESP", CFG.SkelESP, function(v) CFG.SkelESP = v end)
MakeColorPicker(espF, "Skeleton Color", CFG.SkelColor, function(c) CFG.SkelColor = c end)

MakeSection(espF, "■ LINE ESP")
MakeToggle(espF, "Line ESP", CFG.LineESP, function(v) CFG.LineESP = v end)
MakeColorPicker(espF, "Line Color", CFG.LineColor, function(c) CFG.LineColor = c end)

MakeSection(espF, "■ HIGHLIGHT ESP")
MakeToggle(espF, "Highlight", CFG.HLEnabled, function(v)
    CFG.HLEnabled = v
    if not v then
        for p, hl in pairs(HighlightObjects) do
            pcall(function() hl:Destroy() end)
            HighlightObjects[p] = nil
        end
    end
end)
MakeColorPicker(espF, "Fill Color", CFG.HL_Fill, function(c)
    CFG.HL_Fill = c
    for _, hl in pairs(HighlightObjects) do pcall(function() hl.FillColor = c end) end
end)
MakeColorPicker(espF, "Outline Color", CFG.HL_Outline, function(c)
    CFG.HL_Outline = c
    for _, hl in pairs(HighlightObjects) do pcall(function() hl.OutlineColor = c end) end
end)
MakeSlider(espF, "Fill Transparency", 0, 100, 70, "%d%%", function(v)
    CFG.HL_FillAlpha = v / 100
    for _, hl in pairs(HighlightObjects) do pcall(function() hl.FillTransparency = v/100 end) end
end)

MakeSection(espF, "■ MISC")
MakeToggle(espF, "Team Check (ignore allies)", CFG.TeamCheck, function(v) CFG.TeamCheck = v end)

local combF = tabs["Combat"].frame
MakeSection(combF, "■ AIMBOT")
MakeToggle(combF, "Aimbot", CFG.AimEnabled, function(v) CFG.AimEnabled = v end)
MakeSlider(combF, "FOV", 50, 500, CFG.AimFOV, "%d", function(v) CFG.AimFOV = v end)
MakeSlider(combF, "Smoothing (lower = faster)", 1, 30, 15, "%d", function(v) CFG.AimSmooth = v/100 end)

MakeSection(combF, "■ AIM PART")
local aimParts = {"Head","UpperTorso","LowerTorso","HumanoidRootPart"}
for _, part in ipairs(aimParts) do
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1,-16,0,30)
    row.BackgroundColor3 = T.tab
    row.BorderSizePixel = 0
    row.ZIndex = 25
    row.Parent = combF
    MakeCorner(row, 8)
    local lbl = MakeLabel(row, part, 12, T.txt, UDim2.new(0,10,0.5,-7))
    lbl.ZIndex = 26
    local dot = Instance.new("Frame")
    dot.Size = UDim2.new(0,10,0,10)
    dot.Position = UDim2.new(1,-20,0.5,-5)
    dot.BackgroundColor3 = CFG.AimPart == part and T.accent or T.tog_off
    dot.BorderSizePixel = 0
    dot.ZIndex = 26
    dot.Parent = row
    MakeCorner(dot, 5)
    local hitBtn = Instance.new("TextButton")
    hitBtn.Size = UDim2.new(1,0,1,0)
    hitBtn.BackgroundTransparency = 1
    hitBtn.Text = ""
    hitBtn.ZIndex = 27
    hitBtn.Parent = row
    hitBtn.MouseButton1Click:Connect(function()
        CFG.AimPart = part
        for _, child in ipairs(combF:GetChildren()) do
            if child:IsA("Frame") then
                local d = child:FindFirstChildWhichIsA("Frame")
                if d and d.Size == UDim2.new(0,10,0,10) then
                    d.BackgroundColor3 = T.tog_off
                end
            end
        end
        dot.BackgroundColor3 = T.accent
    end)
end

local moveF = tabs["Move"].frame
MakeSection(moveF, "■ SPEED")
MakeToggle(moveF, "Speed Hack", CFG.SpeedOn, function(v)
    CFG.SpeedOn = v
    if not v then
        local char = LP.Character
        local hum  = char and char:FindFirstChildOfClass("Humanoid")
        pcall(function() if hum then hum.WalkSpeed = 16 end end)
    end
end)
MakeSlider(moveF, "Speed Value", 16, 300, CFG.SpeedVal, "%d", function(v) CFG.SpeedVal = v end)

MakeSection(moveF, "■ FLY")
MakeToggle(moveF, "Fly", CFG.FlyOn, function(v)
    CFG.FlyOn = v
    if v then StartFly() else StopFly() end
end)
MakeSlider(moveF, "Fly Speed", 10, 300, CFG.FlySpeed, "%d", function(v) CFG.FlySpeed = v end)

local animF = tabs["Anim"].frame
MakeSection(animF, "■ EMOTES")
MakeButton(animF, "Unlock All Native Emotes", UnlockEmotes)

local emoteGrid = Instance.new("Frame")
emoteGrid.Size = UDim2.new(1,-16,0,10)
emoteGrid.AutomaticSize = Enum.AutomaticSize.Y
emoteGrid.BackgroundTransparency = 1
emoteGrid.ZIndex = 25
emoteGrid.Parent = animF

local egLayout = Instance.new("UIGridLayout")
egLayout.CellSize    = UDim2.new(0.5,-4,0,32)
egLayout.CellPadding = UDim2.new(0,4,0,4)
egLayout.SortOrder   = Enum.SortOrder.LayoutOrder
egLayout.Parent      = emoteGrid

for i, emote in ipairs(EMOTES) do
    local btn = Instance.new("TextButton")
    btn.BackgroundColor3 = T.tab
    btn.BorderSizePixel  = 0
    btn.Text             = emote.name
    btn.TextColor3       = T.txt
    btn.TextSize         = 11
    btn.Font             = Enum.Font.GothamBold
    btn.ZIndex           = 26
    btn.LayoutOrder      = i
    btn.Parent           = emoteGrid
    MakeCorner(btn, 6)
    btn.MouseButton1Click:Connect(function()
        PlayEmote(emote.id)
        Tween(btn, {BackgroundColor3 = T.accent}, 0.1)
        task.delay(0.2, function() Tween(btn, {BackgroundColor3 = T.tab}, 0.1) end)
    end)
end

MakeButton(animF, "Stop Emote", function()
    pcall(function() if currentEmoteTrack then currentEmoteTrack:Stop() end end)
end)

MakeSection(animF, "■ WALK ANIMATIONS")
for _, anim in ipairs(WALK_ANIMS) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1,-16,0,30)
    btn.BackgroundColor3 = T.tab; btn.BorderSizePixel = 0
    btn.Text = anim.name; btn.TextColor3 = T.txt; btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold; btn.ZIndex = 25; btn.Parent = animF
    MakeCorner(btn, 8)
    btn.MouseButton1Click:Connect(function()
        SetAnimInScript("walk", anim.id)
        SetAnimInScript("run",  anim.id)
        Tween(btn, {BackgroundColor3 = T.accent}, 0.1)
        task.delay(0.2, function() Tween(btn, {BackgroundColor3 = T.tab}, 0.1) end)
    end)
end

MakeSection(animF, "■ IDLE ANIMATIONS")
for _, anim in ipairs(IDLE_ANIMS) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1,-16,0,30)
    btn.BackgroundColor3 = T.tab; btn.BorderSizePixel = 0
    btn.Text = anim.name; btn.TextColor3 = T.txt; btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold; btn.ZIndex = 25; btn.Parent = animF
    MakeCorner(btn, 8)
    btn.MouseButton1Click:Connect(function()
        SetAnimInScript("idle", anim.id)
        Tween(btn, {BackgroundColor3 = T.accent}, 0.1)
        task.delay(0.2, function() Tween(btn, {BackgroundColor3 = T.tab}, 0.1) end)
    end)
end

MakeSection(animF, "■ JUMP ANIMATIONS")
for _, anim in ipairs(JUMP_ANIMS) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1,-16,0,30)
    btn.BackgroundColor3 = T.tab; btn.BorderSizePixel = 0
    btn.Text = anim.name; btn.TextColor3 = T.txt; btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold; btn.ZIndex = 25; btn.Parent = animF
    MakeCorner(btn, 8)
    btn.MouseButton1Click:Connect(function()
        SetAnimInScript("jump", anim.id)
        Tween(btn, {BackgroundColor3 = T.accent}, 0.1)
        task.delay(0.2, function() Tween(btn, {BackgroundColor3 = T.tab}, 0.1) end)
    end)
end

local cfgF = tabs["Config"].frame
MakeSection(cfgF, "■ CONFIG")
MakeButton(cfgF, "Save Config", SaveCFG)
MakeButton(cfgF, "Load Config", function() LoadCFG() end)
MakeSection(cfgF, "■ PANEL BG IMAGE")
local infoLbl = MakeLabel(cfgF, "Troque o ID do anime na variavel\nAnimeBG.Image no topo do script", 10, T.sub)
infoLbl.TextWrapped = true
infoLbl.Size = UDim2.new(1,-16,0,36)
infoLbl.Parent = cfgF
MakeSection(cfgF, "■ ABOUT")
MakeLabel(cfgF, "NYX PANEL  |  Delta Mobile\nESP · Combat · Move · Anim", 11, T.sub)

local panelOpen = false

local function TogglePanel()
    panelOpen = not panelOpen
    Panel.Visible = panelOpen
    if panelOpen then
        Panel.Size = UDim2.new(0,320,0,0)
        Tween(Panel, {Size = UDim2.new(0,320,0,400)}, 0.22,
            Enum.EasingStyle.Back, Enum.EasingDirection.Out)
    end
    if activeTab == nil then SelectTab("ESP") end
end

FloatBtn.MouseButton1Click:Connect(TogglePanel)
CloseBtn.MouseButton1Click:Connect(function()
    panelOpen = false
    Panel.Visible = false
end)

local fbDragging   = false
local fbDragStart  = nil
local fbStartPos   = nil
local fbMoved      = false
local DRAG_THRESH  = 6

FloatBtn.InputBegan:Connect(function(inp)
    if inp.UserInputType == Enum.UserInputType.MouseButton1
    or inp.UserInputType == Enum.UserInputType.Touch then
        fbDragging  = true
        fbMoved     = false
        fbDragStart = inp.Position
        fbStartPos  = FloatBtn.Position
    end
end)

UIS.InputChanged:Connect(function(inp)
    if not fbDragging then return end
    if inp.UserInputType == Enum.UserInputType.MouseMovement
    or inp.UserInputType == Enum.UserInputType.Touch then
        local delta = inp.Position - fbDragStart
        if delta.Magnitude > DRAG_THRESH then
            fbMoved = true
        end
        if fbMoved then
            FloatBtn.Position = UDim2.new(
                fbStartPos.X.Scale, fbStartPos.X.Offset + delta.X,
                fbStartPos.Y.Scale, fbStartPos.Y.Offset + delta.Y)
        end
    end
end)

UIS.InputEnded:Connect(function(inp)
    if not fbDragging then return end
    if inp.UserInputType == Enum.UserInputType.MouseButton1
    or inp.UserInputType == Enum.UserInputType.Touch then
        fbDragging = false
    end
end)

local tapCount, lastTap = 0, 0
UIS.TouchTap:Connect(function(positions, processed)
    if processed then return end
    local now = tick()
    tapCount = (now - lastTap < 0.4) and tapCount + 1 or 1
    lastTap  = now
    if tapCount >= 3 then tapCount = 0; TogglePanel() end
end)

SelectTab("ESP")

LP.CharacterAdded:Connect(function()
    task.wait(1)
    for _, p in ipairs(Players:GetPlayers()) do
        if p \~= LP then SetupESP(p) end
    end
end)

print("[NYX] loaded — triple-tap or press ✦ to open")
