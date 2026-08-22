--// ============================================
--// PH XITER VIP - SCRIPT HUB
--// ============================================

--// Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Lighting = game:GetService("Lighting")

--// ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PhXiterVIP"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

--// ============================================
--// VARIABLES
--// ============================================
local dragging = false
local dragStart = nil
local startPos = nil
local BypassKey = Enum.KeyCode.K
local isSettingKey = false

--// Anti-Ban (JÁ ATIVADO)
local AntiBanEnabled = true

--// Modo Leve
local LightModeEnabled = false

--// Settings
local GodModeEnabled = false
local WalkSpeedEnabled = false
local WalkSpeedValue = 50

--// God Settings
local InvisibleEnabled = false
local FlyEnabled = false
local FlySpeed = 50
local NoClipEnabled = false

--// Fly Controls
local FlyKeys = {
    Forward = false,
    Backward = false,
    Left = false,
    Right = false,
    Up = false,
    Down = false
}

local ESPSettings = {
    Enabled = false,
    Lines = false,
    Names = false,
    Health = false
}

local AimbotSettings = {
    Enabled = false,
    FOV = 100
}

local SpinSettings = {
    Enabled = false,
    Speed = 10
}

--// Connections
local ESPObjects = {}
local AimbotConnection = nil
local SpinConnection = nil
local GodModeConnection = nil
local FlyConnection = nil
local NoClipConnection = nil
local CurrentTarget = nil

--// ============================================
--// SISTEMA ANTI-BAN (ATIVADO AUTOMATICAMENTE)
--// ============================================
spawn(function()
    while AntiBanEnabled do
        wait(10)
        
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                local isAdmin = false
                
                pcall(function()
                    if player:GetRankInGroup(0) > 0 then
                        isAdmin = true
                    end
                end)
                
                if player.UserId == game.CreatorId then
                    isAdmin = true
                end
                
                if isAdmin then
                    DeactivateAllFunctions()
                    MainFrame.Visible = false
                    wait(30)
                    MainFrame.Visible = true
                end
            end
        end
    end
end)

--// ============================================
--// MAIN FRAME
--// ============================================
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 550, 0, 420)
MainFrame.Position = UDim2.new(0.5, -275, 0.5, -210)
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

--// Top Bar
local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1, 0, 0, 45)
TopBar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
TopBar.BorderSizePixel = 0
TopBar.Parent = MainFrame

local TopCorner = Instance.new("UICorner")
TopCorner.CornerRadius = UDim.new(0, 12)
TopCorner.Parent = TopBar

--// Title
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -70, 0, 45)
Title.Position = UDim2.new(0, 15, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "PH XITER VIP"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 16
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TopBar

--// Minimize
local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Size = UDim2.new(0, 28, 0, 28)
MinimizeBtn.Position = UDim2.new(1, -65, 0, 9)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
MinimizeBtn.BorderSizePixel = 0
MinimizeBtn.Text = "—"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.TextSize = 14
MinimizeBtn.Parent = TopBar

local MinimizeCorner = Instance.new("UICorner")
MinimizeCorner.CornerRadius = UDim.new(0, 6)
MinimizeCorner.Parent = MinimizeBtn

--// Close
local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 28, 0, 28)
CloseBtn.Position = UDim2.new(1, -32, 0, 9)
CloseBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
CloseBtn.BorderSizePixel = 0
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 12
CloseBtn.Parent = TopBar

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 6)
CloseCorner.Parent = CloseBtn

--// Sidebar
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 120, 1, -45)
Sidebar.Position = UDim2.new(0, 0, 0, 45)
Sidebar.BackgroundColor3 = Color3.fromRGB(22, 22, 30)
Sidebar.BorderSizePixel = 0
Sidebar.Parent = MainFrame

--// Content Area
local ContentArea = Instance.new("Frame")
ContentArea.Size = UDim2.new(1, -120, 1, -45)
ContentArea.Position = UDim2.new(0, 120, 0, 45)
ContentArea.BackgroundColor3 = Color3.fromRGB(28, 28, 38)
ContentArea.BorderSizePixel = 0
ContentArea.Parent = MainFrame

--// Categories
local Categories = {
    {Name = "Player", Content = nil},
    {Name = "God", Content = nil},
    {Name = "Config", Content = nil}
}

--// ============================================
--// UI FUNCTIONS
--// ============================================
local function CreateLabel(parent, text, yPos)
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -30, 0, 20)
    Label.Position = UDim2.new(0, 15, 0, yPos)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(100, 150, 255)
    Label.Font = Enum.Font.GothamBold
    Label.TextSize = 11
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = parent
end

local function CreateToggle(parent, name, yPos, callback, defaultState)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, -30, 0, 35)
    Button.Position = UDim2.new(0, 15, 0, yPos)
    Button.BackgroundColor3 = Color3.fromRGB(35, 35, 48)
    Button.BorderSizePixel = 0
    Button.Text = name .. ": " .. (defaultState and "ON" or "OFF")
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.Font = Enum.Font.Gotham
    Button.TextSize = 12
    Button.Parent = parent
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 6)
    Corner.Parent = Button
    
    local isOn = defaultState or false
    
    if isOn then
        Button.BackgroundColor3 = Color3.fromRGB(40, 100, 60)
    end
    
    Button.MouseButton1Click:Connect(function()
        isOn = not isOn
        Button.Text = name .. ": " .. (isOn and "ON" or "OFF")
        Button.BackgroundColor3 = isOn and Color3.fromRGB(40, 100, 60) or Color3.fromRGB(35, 35, 48)
        callback(isOn)
    end)
end

local function CreateSlider(parent, name, yPos, minVal, maxVal, defaultVal, callback)
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -30, 0, 20)
    Label.Position = UDim2.new(0, 15, 0, yPos)
    Label.BackgroundTransparency = 1
    Label.Text = name .. ": " .. defaultVal
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 11
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = parent
    
    local Input = Instance.new("TextBox")
    Input.Size = UDim2.new(1, -30, 0, 25)
    Input.Position = UDim2.new(0, 15, 0, yPos + 20)
    Input.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    Input.BorderSizePixel = 0
    Input.Text = tostring(defaultVal)
    Input.TextColor3 = Color3.fromRGB(255, 255, 255)
    Input.Font = Enum.Font.Gotham
    Input.TextSize = 11
    Input.Parent = parent
    
    local InputCorner = Instance.new("UICorner")
    InputCorner.CornerRadius = UDim.new(0, 5)
    InputCorner.Parent = Input
    
    Input.FocusLost:Connect(function()
        local num = tonumber(Input.Text)
        if num then
            local clamped = math.clamp(num, minVal, maxVal)
            Input.Text = tostring(clamped)
            Label.Text = name .. ": " .. clamped
            callback(clamped)
        end
    end)
end

--// ============================================
--// CREATE CATEGORIES
--// ============================================
for i, cat in ipairs(Categories) do
    local CatButton = Instance.new("TextButton")
    CatButton.Size = UDim2.new(1, -20, 0, 38)
    CatButton.Position = UDim2.new(0, 10, 0, 10 + (i - 1) * 48)
    CatButton.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
    CatButton.BorderSizePixel = 0
    CatButton.Text = cat.Name
    CatButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    CatButton.Font = Enum.Font.GothamBold
    CatButton.TextSize = 12
    CatButton.Parent = Sidebar
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 8)
    Corner.Parent = CatButton
    
    local Content = Instance.new("ScrollingFrame")
    Content.Size = UDim2.new(1, 0, 1, 0)
    Content.BackgroundTransparency = 1
    Content.Visible = false
    Content.ScrollBarThickness = 3
    Content.ScrollBarImageColor3 = Color3.fromRGB(100, 150, 255)
    Content.CanvasSize = UDim2.new(0, 0, 0, 500)
    Content.Parent = ContentArea
    cat.Content = Content
    
    CatButton.MouseButton1Click:Connect(function()
        for _, otherCat in ipairs(Categories) do
            otherCat.Content.Visible = false
        end
        Content.Visible = true
        
        for _, otherCat in ipairs(Categories) do
            local btn = Sidebar:FindFirstChild(otherCat.Name .. "Btn")
            if btn then
                btn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
            end
        end
        
        CatButton.BackgroundColor3 = Color3.fromRGB(50, 80, 150)
    end)
    
    CatButton.Name = cat.Name .. "Btn"
end

Categories[1].Content.Visible = true

--// ============================================
--// FUNÇÕES GOD
--// ============================================
local function ActivateInvisible()
    if LocalPlayer.Character then
        for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Transparency = 1
            end
        end
    end
end

local function DeactivateInvisible()
    if LocalPlayer.Character then
        for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Transparency = 0
            end
        end
    end
end

local function ActivateFly()
    if FlyConnection then FlyConnection:Disconnect() end
    
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
    
    local rootPart = LocalPlayer.Character.HumanoidRootPart
    local humanoid = LocalPlayer.Character.Humanoid
    humanoid.PlatformStand = true
    
    FlyConnection = RunService.RenderStepped:Connect(function()
        if FlyEnabled and LocalPlayer.Character then
            local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if root then
                local moveDirection = Vector3.new(0, 0, 0)
                
                if FlyKeys.Forward then moveDirection += Camera.CFrame.LookVector end
                if FlyKeys.Backward then moveDirection -= Camera.CFrame.LookVector end
                if FlyKeys.Left then moveDirection -= Camera.CFrame.RightVector end
                if FlyKeys.Right then moveDirection += Camera.CFrame.RightVector end
                if FlyKeys.Up then moveDirection += Vector3.new(0, 1, 0) end
                if FlyKeys.Down then moveDirection -= Vector3.new(0, 1, 0) end
                
                if moveDirection.Magnitude > 0 then
                    moveDirection = moveDirection.Unit * FlySpeed
                end
                
                root.Velocity = moveDirection
            end
        end
    end)
end

local function DeactivateFly()
    if FlyConnection then FlyConnection:Disconnect() FlyConnection = nil end
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.PlatformStand = false
    end
end

local function ActivateNoClip()
    if NoClipConnection then NoClipConnection:Disconnect() end
    
    NoClipConnection = RunService.Stepped:Connect(function()
        if NoClipEnabled and LocalPlayer.Character then
            local rootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")
            
            if rootPart then
                for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
                
                if humanoid then
                    humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
                    humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
                end
                
                local rayOrigin = rootPart.Position
                local rayDirection = Vector3.new(0, -5, 0)
                local raycastParams = RaycastParams.new()
                raycastParams.FilterDescendantsInstances = {LocalPlayer.Character}
                raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
                
                local raycastResult = workspace:Raycast(rayOrigin, rayDirection, raycastParams)
                
                if raycastResult then
                    local groundDistance = (rayOrigin - raycastResult.Position).Magnitude
                    if groundDistance > 3 then
                        rootPart.Position = Vector3.new(rootPart.Position.X, raycastResult.Position.Y + 3, rootPart.Position.Z)
                    end
                end
            end
        end
    end)
end

local function DeactivateNoClip()
    if NoClipConnection then NoClipConnection:Disconnect() NoClipConnection = nil end
    if LocalPlayer.Character then
        for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
        
        local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
            humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, true)
        end
    end
end

--// ============================================
--// MODO LEVE (Texturas Leves)
--// ============================================
local function ActivateLightMode()
    -- Remove sombras
    Lighting.GlobalShadows = false
    
    -- Remove neblina
    Lighting.FogEnd = 100000
    
    -- Remove brilho
    Lighting.Brightness = 1
    
    -- Define horário fixo
    Lighting.ClockTime = 12
    Lighting.ExposureCompensation = 0
    
    -- Remove texturas de todos os objetos
    for _, part in ipairs(workspace:GetDescendants()) do
        if part:IsA("BasePart") then
            pcall(function()
                part.Material = Enum.Material.SmoothPlastic
                part.Reflectance = 0
            end)
        end
        
        if part:IsA("Texture") or part:IsA("Decal") then
            pcall(function()
                part:Destroy()
            end)
        end
    end
    
    -- Remove partículas
    for _, particle in ipairs(workspace:GetDescendants()) do
        if particle:IsA("ParticleEmitter") or particle:IsA("Smoke") or particle:IsA("Fire") then
            pcall(function()
                particle.Enabled = false
            end)
        end
    end
end

--// Fly Controls
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if FlyEnabled and not gameProcessed then
        if input.KeyCode == Enum.KeyCode.W then FlyKeys.Forward = true
        elseif input.KeyCode == Enum.KeyCode.S then FlyKeys.Backward = true
        elseif input.KeyCode == Enum.KeyCode.A then FlyKeys.Left = true
        elseif input.KeyCode == Enum.KeyCode.D then FlyKeys.Right = true
        elseif input.KeyCode == Enum.KeyCode.Space then FlyKeys.Up = true
        elseif input.KeyCode == Enum.KeyCode.LeftControl or input.KeyCode == Enum.KeyCode.LeftShift then FlyKeys.Down = true
        end
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.W then FlyKeys.Forward = false
    elseif input.KeyCode == Enum.KeyCode.S then FlyKeys.Backward = false
    elseif input.KeyCode == Enum.KeyCode.A then FlyKeys.Left = false
    elseif input.KeyCode == Enum.KeyCode.D then FlyKeys.Right = false
    elseif input.KeyCode == Enum.KeyCode.Space then FlyKeys.Up = false
    elseif input.KeyCode == Enum.KeyCode.LeftControl or input.KeyCode == Enum.KeyCode.LeftShift then FlyKeys.Down = false
    end
end)

--// ============================================
--// PLAYER CATEGORY
--// ============================================
local PlayerContent = Categories[1].Content

CreateLabel(PlayerContent, "PLAYER", 5)

CreateToggle(PlayerContent, "WalkSpeed", 30, function(state)
    WalkSpeedEnabled = state
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = state and WalkSpeedValue or 16
    end
end)

CreateSlider(PlayerContent, "Velocidade", 75, 30, 300, 50, function(value)
    WalkSpeedValue = value
    if WalkSpeedEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = value
    end
end)

CreateLabel(PlayerContent, "ESP", 130)

CreateToggle(PlayerContent, "ESP Normal", 155, function(state)
    ESPSettings.Enabled = state
end)

CreateToggle(PlayerContent, "ESP Linha", 200, function(state)
    ESPSettings.Lines = state
end)

CreateToggle(PlayerContent, "ESP Nome", 245, function(state)
    ESPSettings.Names = state
end)

CreateToggle(PlayerContent, "ESP Vida", 290, function(state)
    ESPSettings.Health = state
end)

CreateLabel(PlayerContent, "AIMBOT", 345)

CreateToggle(PlayerContent, "Aimbot", 370, function(state)
    AimbotSettings.Enabled = state
    if state then
        if AimbotConnection then AimbotConnection:Disconnect() end
        AimbotConnection = RunService.RenderStepped:Connect(function()
            if AimbotSettings.Enabled then
                if not CurrentTarget or not IsInFOV(CurrentTarget) then
                    CurrentTarget = FindBestTarget()
                end
                if CurrentTarget then AimAtTarget(CurrentTarget) end
            end
        end)
    else
        if AimbotConnection then AimbotConnection:Disconnect() AimbotConnection = nil end
        CurrentTarget = nil
    end
end)

CreateSlider(PlayerContent, "FOV", 415, 50, 300, 100, function(value)
    AimbotSettings.FOV = value
end)

CreateLabel(PlayerContent, "SPIN", 470)

CreateToggle(PlayerContent, "Spin", 495, function(state)
    SpinSettings.Enabled = state
    if state then
        if SpinConnection then SpinConnection:Disconnect() end
        SpinConnection = RunService.RenderStepped:Connect(function()
            if SpinSettings.Enabled and LocalPlayer.Character then
                local rootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")
                if rootPart and humanoid and humanoid.Health > 0 then
                    rootPart.CFrame = rootPart.CFrame * CFrame.Angles(0, math.rad(SpinSettings.Speed / 10), 0)
                end
            end
        end)
    else
        if SpinConnection then SpinConnection:Disconnect() SpinConnection = nil end
    end
end)

CreateSlider(PlayerContent, "Velocidade do Giro", 540, 0, 100, 10, function(value)
    SpinSettings.Speed = value
end)

PlayerContent.CanvasSize = UDim2.new(0, 0, 0, 600)

--// ============================================
--// GOD CATEGORY
--// ============================================
local GodContent = Categories[2].Content

CreateLabel(GodContent, "GOD MODE", 5)

CreateToggle(GodContent, "God Mode", 30, function(state)
    GodModeEnabled = state
    if state then
        if GodModeConnection then GodModeConnection:Disconnect() end
        GodModeConnection = RunService.RenderStepped:Connect(function()
            if GodModeEnabled and LocalPlayer.Character then
                local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")
                if humanoid then humanoid.Health = humanoid.MaxHealth end
            end
        end)
    else
        if GodModeConnection then GodModeConnection:Disconnect() GodModeConnection = nil end
    end
end)

CreateToggle(GodContent, "Invisible", 75, function(state)
    InvisibleEnabled = state
    if state then ActivateInvisible() else DeactivateInvisible() end
end)

CreateLabel(GodContent, "FLY", 125)

CreateToggle(GodContent, "Fly", 150, function(state)
    FlyEnabled = state
    if state then ActivateFly() else DeactivateFly() end
end)

CreateSlider(GodContent, "Velocidade do Fly", 195, 10, 200, 50, function(value)
    FlySpeed = value
end)

CreateLabel(GodContent, "NOCLIP", 250)

CreateToggle(GodContent, "NoClip", 275, function(state)
    NoClipEnabled = state
    if state then ActivateNoClip() else DeactivateNoClip() end
end)

GodContent.CanvasSize = UDim2.new(0, 0, 0, 350)

--// ============================================
--// CONFIG CATEGORY
--// ============================================
local ConfigContent = Categories[3].Content

CreateLabel(ConfigContent, "CONFIGURAÇÃO", 5)

local KeyButton = Instance.new("TextButton")
KeyButton.Size = UDim2.new(1, -30, 0, 35)
KeyButton.Position = UDim2.new(0, 15, 0, 30)
KeyButton.BackgroundColor3 = Color3.fromRGB(35, 35, 48)
KeyButton.BorderSizePixel = 0
KeyButton.Text = "Tecla Bypass: K"
KeyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyButton.Font = Enum.Font.Gotham
KeyButton.TextSize = 12
KeyButton.Parent = ConfigContent

local KeyCorner = Instance.new("UICorner")
KeyCorner.CornerRadius = UDim.new(0, 6)
KeyCorner.Parent = KeyButton

KeyButton.MouseButton1Click:Connect(function()
    isSettingKey = true
    KeyButton.Text = "Pressione uma tecla..."
    KeyButton.BackgroundColor3 = Color3.fromRGB(150, 80, 200)
    
    local connection
    connection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if isSettingKey and input.UserInputType == Enum.UserInputType.Keyboard then
            BypassKey = input.KeyCode
            KeyButton.Text = "Tecla Bypass: " .. input.KeyCode.Name
            KeyButton.BackgroundColor3 = Color3.fromRGB(35, 35, 48)
            isSettingKey = false
            connection:Disconnect()
        end
    end)
end)

CreateLabel(ConfigContent, "PERFORMANCE", 85)

CreateToggle(ConfigContent, "Modo Leve", 110, function(state)
    if state then
        LightModeEnabled = true
        ActivateLightMode()
    end
    -- Não permite desativar
end)

--// Aviso do Modo Leve
local LightModeWarning = Instance.new("TextLabel")
LightModeWarning.Size = UDim2.new(1, -30, 0, 35)
LightModeWarning.Position = UDim2.new(0, 15, 0, 150)
LightModeWarning.BackgroundColor3 = Color3.fromRGB(40, 30, 30)
LightModeWarning.BorderSizePixel = 0
LightModeWarning.Text = "⚠️ AO ATIVAR O MODO LEVE\nNÃO TEM COMO DESATIVAR"
LightModeWarning.TextColor3 = Color3.fromRGB(255, 150, 150)
LightModeWarning.Font = Enum.Font.GothamBold
LightModeWarning.TextSize = 10
LightModeWarning.TextWrapped = true
LightModeWarning.Parent = ConfigContent

local WarningCorner = Instance.new("UICorner")
WarningCorner.CornerRadius = UDim.new(0, 6)
WarningCorner.Parent = LightModeWarning

CreateLabel(ConfigContent, "ANTI-BAN", 200)

CreateToggle(ConfigContent, "Anti-Ban", 225, function(state)
    AntiBanEnabled = state
end, true) -- Anti-Ban já ativado

ConfigContent.CanvasSize = UDim2.new(0, 0, 0, 280)

--// ============================================
--// ESP FUNCTIONS
--// ============================================
local function CreateESP(player)
    if not player.Character then return end
    
    local highlight = Instance.new("Highlight")
    highlight.FillColor = Color3.fromRGB(100, 150, 255)
    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
    highlight.FillTransparency = 0.5
    highlight.Enabled = false
    highlight.Parent = player.Character
    
    local nameLabel = Instance.new("BillboardGui")
    nameLabel.Size = UDim2.new(0, 100, 0, 20)
    nameLabel.StudsOffset = Vector3.new(0, 3, 0)
    nameLabel.AlwaysOnTop = true
    nameLabel.Enabled = false
    nameLabel.Parent = player.Character
    
    local nameText = Instance.new("TextLabel")
    nameText.Size = UDim2.new(1, 0, 1, 0)
    nameText.BackgroundTransparency = 1
    nameText.Text = player.Name
    nameText.TextColor3 = Color3.fromRGB(255, 255, 255)
    nameText.TextSize = 14
    nameText.Font = Enum.Font.GothamBold
    nameText.Parent = nameLabel
    
    local healthBar = Instance.new("BillboardGui")
    healthBar.Size = UDim2.new(0, 60, 0, 5)
    healthBar.StudsOffset = Vector3.new(0, 2.5, 0)
    healthBar.AlwaysOnTop = true
    healthBar.Enabled = false
    healthBar.Parent = player.Character
    
    local healthFrame = Instance.new("Frame")
    healthFrame.Size = UDim2.new(1, 0, 1, 0)
    healthFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    healthFrame.Parent = healthBar
    
    local healthFill = Instance.new("Frame")
    healthFill.Size = UDim2.new(1, 0, 1, 0)
    healthFill.BackgroundColor3 = Color3.fromRGB(100, 150, 255)
    healthFill.Parent = healthFrame
    
    local line = Drawing.new("Line")
    line.Thickness = 2
    line.Color = Color3.fromRGB(100, 150, 255)
    line.Transparency = 1
    line.Visible = false
    
    ESPObjects[player] = {
        Highlight = highlight,
        NameLabel = nameLabel,
        HealthBar = healthBar,
        HealthFill = healthFill,
        Line = line
    }
end

local function RemoveESP(player)
    if ESPObjects[player] then
        local esp = ESPObjects[player]
        if esp.Highlight then esp.Highlight:Destroy() end
        if esp.NameLabel then esp.NameLabel:Destroy() end
        if esp.HealthBar then esp.HealthBar:Destroy() end
        if esp.Line then esp.Line:Remove() end
        ESPObjects[player] = nil
    end
end

RunService.RenderStepped:Connect(function()
    for player, esp in pairs(ESPObjects) do
        if player.Character and player.Character:FindFirstChild("Humanoid") and player.Character:FindFirstChild("HumanoidRootPart") then
            local humanoid = player.Character.Humanoid
            local rootPart = player.Character.HumanoidRootPart
            
            esp.Highlight.Enabled = ESPSettings.Enabled
            esp.Highlight.Parent = player.Character
            
            esp.NameLabel.Enabled = ESPSettings.Names
            esp.NameLabel.Parent = player.Character
            
            esp.HealthBar.Enabled = ESPSettings.Health
            esp.HealthBar.Parent = player.Character
            esp.HealthFill.Size = UDim2.new(humanoid.Health / humanoid.MaxHealth, 0, 1, 0)
            
            if ESPSettings.Lines then
                local screenPos, onScreen = Camera:WorldToScreenPoint(rootPart.Position)
                if onScreen then
                    esp.Line.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                    esp.Line.To = Vector2.new(screenPos.X, screenPos.Y)
                    esp.Line.Visible = true
                    esp.Line.Transparency = 0
                else
                    esp.Line.Visible = false
                    esp.Line.Transparency = 1
                end
            else
                esp.Line.Visible = false
                esp.Line.Transparency = 1
            end
        else
            esp.Line.Visible = false
            esp.Line.Transparency = 1
        end
    end
end)

--// ============================================
--// AIMBOT FUNCTIONS
--// ============================================
function IsInFOV(player)
    if not player.Character then return false end
    local head = player.Character:FindFirstChild("Head")
    local humanoid = player.Character:FindFirstChild("Humanoid")
    if not head or not humanoid or humanoid.Health <= 0 then return false end
    
    local screenPos, onScreen = Camera:WorldToScreenPoint(head.Position)
    if not onScreen then return false end
    
    local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    return (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude <= AimbotSettings.FOV
end

function FindBestTarget()
    local bestTarget = nil
    local bestDistance = AimbotSettings.FOV
    local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local head = player.Character:FindFirstChild("Head")
            local humanoid = player.Character:FindFirstChild("Humanoid")
            if head and humanoid and humanoid.Health > 0 then
                local screenPos, onScreen = Camera:WorldToScreenPoint(head.Position)
                if onScreen then
                    local distance = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                    if distance <= AimbotSettings.FOV and distance < bestDistance then
                        bestDistance = distance
                        bestTarget = player
                    end
                end
            end
        end
    end
    return bestTarget
end

function AimAtTarget(target)
    if not target or not target.Character then CurrentTarget = nil return end
    local head = target.Character:FindFirstChild("Head")
    local humanoid = target.Character:FindFirstChild("Humanoid")
    if not head or not humanoid or humanoid.Health <= 0 then CurrentTarget = nil return end
    if not IsInFOV(target) then CurrentTarget = nil return end
    
    Camera.CFrame = CFrame.lookAt(Camera.CFrame.Position, head.Position)
end

--// FOV Circle
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 1.5
FOVCircle.Color = Color3.fromRGB(100, 150, 255)
FOVCircle.Transparency = 0.7
FOVCircle.Visible = false

RunService.RenderStepped:Connect(function()
    FOVCircle.Visible = AimbotSettings.Enabled
    FOVCircle.Radius = AimbotSettings.FOV
    FOVCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
end)

--// ============================================
--// DESATIVAR TODAS AS FUNÇÕES
--// ============================================
function DeactivateAllFunctions()
    GodModeEnabled = false
    if GodModeConnection then GodModeConnection:Disconnect() GodModeConnection = nil end
    
    WalkSpeedEnabled = false
    DeactivateWalkSpeed()
    
    InvisibleEnabled = false
    DeactivateInvisible()
    
    FlyEnabled = false
    DeactivateFly()
    
    NoClipEnabled = false
    DeactivateNoClip()
    
    ESPSettings.Enabled = false
    ESPSettings.Lines = false
    ESPSettings.Names = false
    ESPSettings.Health = false
    
    for player, esp in pairs(ESPObjects) do
        if esp.Highlight then esp.Highlight.Enabled = false end
        if esp.NameLabel then esp.NameLabel.Enabled = false end
        if esp.HealthBar then esp.HealthBar.Enabled = false end
        if esp.Line then esp.Line.Visible = false esp.Line.Transparency = 1 end
    end
    
    AimbotSettings.Enabled = false
    if AimbotConnection then AimbotConnection:Disconnect() AimbotConnection = nil end
    CurrentTarget = nil
    
    SpinSettings.Enabled = false
    if SpinConnection then SpinConnection:Disconnect() SpinConnection = nil end
end

function DeactivateWalkSpeed()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 16
    end
end

--// ============================================
--// EVENTOS
--// ============================================
MinimizeBtn.MouseButton1Click:Connect(function()
    DeactivateAllFunctions()
    MainFrame.Visible = false
end)

CloseBtn.MouseButton1Click:Connect(function()
    DeactivateAllFunctions()
    MainFrame.Visible = false
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not isSettingKey and input.KeyCode == BypassKey and not gameProcessed then
        if MainFrame.Visible then
            DeactivateAllFunctions()
            MainFrame.Visible = false
        else
            MainFrame.Visible = true
        end
    end
end)

--// Dragging
TopBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

TopBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

--// Player Connections
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        wait(0.5)
        if player.Character then CreateESP(player) end
    end)
end)

Players.PlayerRemoving:Connect(function(player)
    RemoveESP(player)
end)

for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        player.CharacterAdded:Connect(function()
            wait(0.5)
            if player.Character then CreateESP(player) end
        end)
        if player.Character then CreateESP(player) end
    end
end

LocalPlayer.CharacterAdded:Connect(function(character)
    wait(0.5)
    if WalkSpeedEnabled then
        character:WaitForChild("Humanoid").WalkSpeed = WalkSpeedValue
    end
    if SpinSettings.Enabled then
        if SpinConnection then SpinConnection:Disconnect() end
        SpinConnection = RunService.RenderStepped:Connect(function()
            if SpinSettings.Enabled and LocalPlayer.Character then
                local rootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")
                if rootPart and humanoid and humanoid.Health > 0 then
                    rootPart.CFrame = rootPart.CFrame * CFrame.Angles(0, math.rad(SpinSettings.Speed / 10), 0)
                end
            end
        end)
    end
    if InvisibleEnabled then ActivateInvisible() end
    if FlyEnabled then ActivateFly() end
    if NoClipEnabled then ActivateNoClip() end
end)

ScreenGui.Destroying:Connect(function()
    if AimbotConnection then AimbotConnection:Disconnect() end
    if SpinConnection then SpinConnection:Disconnect() end
    if GodModeConnection then GodModeConnection:Disconnect() end
    if FlyConnection then FlyConnection:Disconnect() end
    if NoClipConnection then NoClipConnection:Disconnect() end
    for player, _ in pairs(ESPObjects) do RemoveESP(player) end
end)

--// Splash
local Splash = Instance.new("Frame")
Splash.Size = UDim2.new(0, 350, 0, 120)
Splash.Position = UDim2.new(0.5, -175, 0.5, -60)
Splash.BackgroundColor3 = Color3.fromRGB(18, 18, 25)
Splash.BorderSizePixel = 0
Splash.ZIndex = 100
Splash.Parent = ScreenGui

local SplashCorner = Instance.new("UICorner")
SplashCorner.CornerRadius = UDim.new(0, 12)
SplashCorner.Parent = Splash

local SplashText = Instance.new("TextLabel")
SplashText.Size = UDim2.new(1, 0, 0, 50)
SplashText.Position = UDim2.new(0, 0, 0, 20)
SplashText.BackgroundTransparency = 1
SplashText.Text = "PH XITER VIP"
SplashText.TextColor3 = Color3.fromRGB(100, 150, 255)
SplashText.Font = Enum.Font.GothamBlack
SplashText.TextSize = 28
SplashText.ZIndex = 101
SplashText.Parent = Splash

local SplashSub = Instance.new("TextLabel")
SplashSub.Size = UDim2.new(1, 0, 0, 20)
SplashSub.Position = UDim2.new(0, 0, 0, 75)
SplashSub.BackgroundTransparency = 1
SplashSub.Text = "Carregando..."
SplashSub.TextColor3 = Color3.fromRGB(150, 150, 150)
SplashSub.Font = Enum.Font.Gotham
SplashSub.TextSize = 12
SplashSub.ZIndex = 101
SplashSub.Parent = Splash

spawn(function()
    wait(2)
    TweenService:Create(Splash, TweenInfo.new(0.5), {BackgroundTransparency = 1}):Play()
    TweenService:Create(SplashText, TweenInfo.new(0.5), {TextTransparency = 1}):Play()
    TweenService:Create(SplashSub, TweenInfo.new(0.5), {TextTransparency = 1}):Play()
    wait(0.5)
    Splash:Destroy()
    MainFrame.Visible = true
end)

print("PH XITER VIP carregado!")
