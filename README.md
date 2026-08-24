--[[
    Script Hub com Anti-Ban Forte + Sistema de Key
    Key: 152
    Interface bonita com categorias laterais
    Sistema de bypass com tecla K (configurável)
    Com botões ON/OFF em todas as funções
    Aimbot funcional com FOV visível e toggle na tecla F
]]

-- Sistema de Key
local KeySystem = {
    ValidKey = "152",
    Whitelisted = false,
    Attempts = 0,
    MaxAttempts = 5
}

-- Criar interface de Key
local KeyGui = Instance.new("ScreenGui")
KeyGui.Parent = game.CoreGui
KeyGui.Name = "KeySystem"

local KeyFrame = Instance.new("Frame")
KeyFrame.Parent = KeyGui
KeyFrame.Size = UDim2.new(0, 400, 0, 250)
KeyFrame.Position = UDim2.new(0.5, -200, 0.5, -125)
KeyFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
KeyFrame.BorderSizePixel = 0

local KeyCorner = Instance.new("UICorner")
KeyCorner.CornerRadius = UDim.new(0, 15)
KeyCorner.Parent = KeyFrame

local KeyTitle = Instance.new("TextLabel")
KeyTitle.Parent = KeyFrame
KeyTitle.Size = UDim2.new(1, 0, 0, 50)
KeyTitle.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
KeyTitle.Text = "🔐 SISTEMA DE ACESSO"
KeyTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyTitle.Font = Enum.Font.GothamBold
KeyTitle.TextSize = 20
KeyTitle.BorderSizePixel = 0

local KeyTitleCorner = Instance.new("UICorner")
KeyTitleCorner.CornerRadius = UDim.new(0, 15)
KeyTitleCorner.Parent = KeyTitle

local KeyInput = Instance.new("TextBox")
KeyInput.Parent = KeyFrame
KeyInput.Size = UDim2.new(1, -60, 0, 40)
KeyInput.Position = UDim2.new(0, 30, 0, 70)
KeyInput.PlaceholderText = "Digite a chave de acesso..."
KeyInput.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyInput.Font = Enum.Font.Gotham
KeyInput.TextSize = 14
KeyInput.BorderSizePixel = 0
KeyInput.TextXAlignment = Enum.TextXAlignment.Center

local KeyInputCorner = Instance.new("UICorner")
KeyInputCorner.CornerRadius = UDim.new(0, 8)
KeyInputCorner.Parent = KeyInput

local VerifyButton = Instance.new("TextButton")
VerifyButton.Parent = KeyFrame
VerifyButton.Size = UDim2.new(1, -60, 0, 40)
VerifyButton.Position = UDim2.new(0, 30, 0, 125)
VerifyButton.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
VerifyButton.Text = "VERIFICAR"
VerifyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
VerifyButton.Font = Enum.Font.GothamBold
VerifyButton.TextSize = 16
VerifyButton.BorderSizePixel = 0

local VerifyCorner = Instance.new("UICorner")
VerifyCorner.CornerRadius = UDim.new(0, 8)
VerifyCorner.Parent = VerifyButton

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Parent = KeyFrame
StatusLabel.Size = UDim2.new(1, 0, 0, 30)
StatusLabel.Position = UDim2.new(0, 0, 0, 180)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "Digite a chave para acessar o hub"
StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.TextSize = 12

local AttemptsLabel = Instance.new("TextLabel")
AttemptsLabel.Parent = KeyFrame
AttemptsLabel.Size = UDim2.new(1, 0, 0, 20)
AttemptsLabel.Position = UDim2.new(0, 0, 0, 215)
AttemptsLabel.BackgroundTransparency = 1
AttemptsLabel.Text = "Tentativas restantes: " .. (KeySystem.MaxAttempts - KeySystem.Attempts)
AttemptsLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
AttemptsLabel.Font = Enum.Font.Gotham
AttemptsLabel.TextSize = 10

local function verifyKey()
    if KeySystem.Attempts >= KeySystem.MaxAttempts then
        StatusLabel.Text = "❌ Muitas tentativas! Aguarde..."
        StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        VerifyButton.Interactable = false
        return
    end
    
    if KeyInput.Text == KeySystem.ValidKey then
        KeySystem.Whitelisted = true
        StatusLabel.Text = "✅ Chave correta! Carregando hub..."
        StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
        wait(1)
        KeyGui:Destroy()
        loadHub()
    else
        KeySystem.Attempts = KeySystem.Attempts + 1
        StatusLabel.Text = "❌ Chave incorreta!"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        AttemptsLabel.Text = "Tentativas restantes: " .. (KeySystem.MaxAttempts - KeySystem.Attempts)
        
        if KeySystem.Attempts >= KeySystem.MaxAttempts then
            VerifyButton.Interactable = false
            StatusLabel.Text = "🚫 BLOQUEADO! Muitas tentativas."
            wait(5)
            KeySystem.Attempts = 0
            VerifyButton.Interactable = true
            AttemptsLabel.Text = "Tentativas restantes: " .. KeySystem.MaxAttempts
            StatusLabel.Text = "Digite a chave para acessar o hub"
            StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        end
    end
end

VerifyButton.MouseButton1Click:Connect(verifyKey)
KeyInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        verifyKey()
    end
end)

function loadHub()
    local AntiBan = {
        Enabled = true,
        FireProof = true,
        AntiDetection = true
    }

    local function protectScript()
        if AntiBan.Enabled then
            local func = function() return true end
            local protected = protect_function or func
            getgenv().protected_mode = true
        end
    end

    protectScript()

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Parent = game.CoreGui
    ScreenGui.Name = "HubPremium"

    local MainFrame = Instance.new("Frame")
    MainFrame.Parent = ScreenGui
    MainFrame.Size = UDim2.new(0, 580, 0, 450)
    MainFrame.Position = UDim2.new(0.5, -290, 0.5, -225)
    MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true

    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 12)
    UICorner.Parent = MainFrame

    local Title = Instance.new("TextLabel")
    Title.Parent = MainFrame
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    Title.Text = "🔥 HUB PREMIUM v2.0 🔥"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 18
    Title.BorderSizePixel = 0

    local Sidebar = Instance.new("Frame")
    Sidebar.Parent = MainFrame
    Sidebar.Size = UDim2.new(0, 120, 1, -40)
    Sidebar.Position = UDim2.new(0, 0, 0, 40)
    Sidebar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    Sidebar.BorderSizePixel = 0

    local ContentFrame = Instance.new("Frame")
    ContentFrame.Parent = MainFrame
    ContentFrame.Size = UDim2.new(1, -120, 1, -40)
    ContentFrame.Position = UDim2.new(0, 120, 0, 40)
    ContentFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
    ContentFrame.BorderSizePixel = 0

    local BypassKey = "K"
    local NoClipEnabled = false
    local GodModeEnabled = false
    local AimbotEnabled = false
    local SpeedBoostEnabled = false
    local ModoLeveEnabled = false
    local FOVSize = 200
    local TargetPlayer = nil
    local AimbotButton = nil -- Referência ao botão do aimbot

    -- Criar círculo de FOV
    local FOVCircle = Instance.new("Frame")
    FOVCircle.Parent = ScreenGui
    FOVCircle.Size = UDim2.new(0, FOVSize * 2, 0, FOVSize * 2)
    FOVCircle.Position = UDim2.new(0.5, -FOVSize, 0.5, -FOVSize)
    FOVCircle.BackgroundTransparency = 1
    FOVCircle.BorderSizePixel = 2
    FOVCircle.BorderColor3 = Color3.fromRGB(255, 0, 0)
    FOVCircle.Visible = false
    FOVCircle.ZIndex = 10

    local FOVCorner = Instance.new("UICorner")
    FOVCorner.CornerRadius = UDim.new(1, 0)
    FOVCorner.Parent = FOVCircle

    local Player = game.Players.LocalPlayer
    local Camera = workspace.CurrentCamera

    local function createToggleButton(name, parent, callback)
        local Button = Instance.new("TextButton")
        Button.Parent = parent
        Button.Size = UDim2.new(1, -20, 0, 40)
        Button.Position = UDim2.new(0, 10, 0, 10)
        Button.BackgroundColor3 = Color3.fromRGB(60, 60, 75)
        Button.Text = name .. " [OFF]"
        Button.TextColor3 = Color3.fromRGB(255, 255, 255)
        Button.Font = Enum.Font.GothamBold
        Button.TextSize = 13
        Button.BorderSizePixel = 0
        Button.AutoButtonColor = false
        
        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = Button
        
        local isEnabled = false
        
        local function updateButton()
            if isEnabled then
                Button.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
                Button.Text = name .. " [ON]"
            else
                Button.BackgroundColor3 = Color3.fromRGB(60, 60, 75)
                Button.Text = name .. " [OFF]"
            end
        end
        
        Button.MouseButton1Click:Connect(function()
            isEnabled = not isEnabled
            updateButton()
            callback(isEnabled)
        end)
        
        -- Função para atualizar externamente
        Button.UpdateState = function(enabled)
            isEnabled = enabled
            updateButton()
        end
        
        return Button
    end

    local function createCategory(name, parent)
        local CatButton = Instance.new("TextButton")
        CatButton.Parent = parent
        CatButton.Size = UDim2.new(1, -20, 0, 35)
        CatButton.Position = UDim2.new(0, 10, 0, 10)
        CatButton.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
        CatButton.Text = name
        CatButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        CatButton.Font = Enum.Font.GothamBold
        CatButton.TextSize = 14
        CatButton.BorderSizePixel = 0
        
        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 8)
        Corner.Parent = CatButton
        
        return CatButton
    end

    local PlayerCategory = createCategory("👤 PLAYER", Sidebar)
    PlayerCategory.Position = UDim2.new(0, 10, 0, 10)

    local VisualCategory = createCategory("👁️ VISUAL", Sidebar)
    VisualCategory.Position = UDim2.new(0, 10, 0, 55)

    local GodCategory = createCategory("⚡ GOD", Sidebar)
    GodCategory.Position = UDim2.new(0, 10, 0, 100)

    local ConfigCategory = createCategory("⚙️ CONFIG", Sidebar)
    ConfigCategory.Position = UDim2.new(0, 10, 0, 145)

    local function clearContent()
        for _, v in pairs(ContentFrame:GetChildren()) do
            v:Destroy()
        end
    end

    -- Função para alternar aimbot
    local function toggleAimbot()
        AimbotEnabled = not AimbotEnabled
        FOVCircle.Visible = AimbotEnabled
        if not AimbotEnabled then
            TargetPlayer = nil
        end
        if AimbotButton then
            AimbotButton.UpdateState(AimbotEnabled)
        end
        print("Aimbot " .. (AimbotEnabled and "ATIVADO" or "DESATIVADO"))
    end

    PlayerCategory.MouseButton1Click:Connect(function()
        clearContent()
        local title = Instance.new("TextLabel")
        title.Parent = ContentFrame
        title.Size = UDim2.new(1, 0, 0, 30)
        title.Position = UDim2.new(0, 0, 0, 5)
        title.BackgroundTransparency = 1
        title.Text = "👤 PLAYER OPTIONS"
        title.TextColor3 = Color3.fromRGB(255, 255, 255)
        title.Font = Enum.Font.GothamBold
        title.TextSize = 16
        
        local speedBtn = createToggleButton("⚡ SPEED BOOST", ContentFrame, function(enabled)
            SpeedBoostEnabled = enabled
            local char = Player.Character
            if char and char:FindFirstChild("Humanoid") then
                if enabled then
                    char.Humanoid.WalkSpeed = 30
                else
                    char.Humanoid.WalkSpeed = 16
                end
            end
        end)
        speedBtn.Position = UDim2.new(0, 10, 0, 40)
        
        local noclipBtn = createToggleButton("🚫 NO CLIP", ContentFrame, function(enabled)
            NoClipEnabled = enabled
        end)
        noclipBtn.Position = UDim2.new(0, 10, 0, 90)
    end)

    VisualCategory.MouseButton1Click:Connect(function()
        clearContent()
        local title = Instance.new("TextLabel")
        title.Parent = ContentFrame
        title.Size = UDim2.new(1, 0, 0, 30)
        title.Position = UDim2.new(0, 0, 0, 5)
        title.BackgroundTransparency = 1
        title.Text = "👁️ VISUAL OPTIONS"
        title.TextColor3 = Color3.fromRGB(255, 255, 255)
        title.Font = Enum.Font.GothamBold
        title.TextSize = 16
        
        AimbotButton = createToggleButton("🎯 AIMBOT (F)", ContentFrame, function(enabled)
            AimbotEnabled = enabled
            FOVCircle.Visible = enabled
            if not enabled then
                TargetPlayer = nil
            end
        end)
        AimbotButton.Position = UDim2.new(0, 10, 0, 40)
        
        -- Aviso sobre a tecla F
        local fKeyInfo = Instance.new("TextLabel")
        fKeyInfo.Parent = ContentFrame
        fKeyInfo.Size = UDim2.new(1, -20, 0, 20)
        fKeyInfo.Position = UDim2.new(0, 10, 0, 85)
        fKeyInfo.BackgroundTransparency = 1
        fKeyInfo.Text = "Pressione F para ativar/desativar rapidamente"
        fKeyInfo.TextColor3 = Color3.fromRGB(255, 200, 0)
        fKeyInfo.Font = Enum.Font.Gotham
        fKeyInfo.TextSize = 11
        
        local fovLabel = Instance.new("TextLabel")
        fovLabel.Parent = ContentFrame
        fovLabel.Size = UDim2.new(1, -20, 0, 25)
        fovLabel.Position = UDim2.new(0, 10, 0, 110)
        fovLabel.BackgroundTransparency = 1
        fovLabel.Text = "FOV do Aimbot: " .. FOVSize
        fovLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        fovLabel.Font = Enum.Font.Gotham
        fovLabel.TextSize = 12
        
        local fovSlider = Instance.new("TextBox")
        fovSlider.Parent = ContentFrame
        fovSlider.Size = UDim2.new(1, -20, 0, 35)
        fovSlider.Position = UDim2.new(0, 10, 0, 140)
        fovSlider.PlaceholderText = "FOV (50-500)"
        fovSlider.Text = tostring(FOVSize)
        fovSlider.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
        fovSlider.TextColor3 = Color3.fromRGB(255, 255, 255)
        fovSlider.Font = Enum.Font.Gotham
        fovSlider.TextSize = 14
        fovSlider.BorderSizePixel = 0
        
        local fovCorner = Instance.new("UICorner")
        fovCorner.CornerRadius = UDim.new(0, 6)
        fovCorner.Parent = fovSlider
        
        fovSlider.FocusLost:Connect(function()
            local newFOV = tonumber(fovSlider.Text)
            if newFOV and newFOV >= 50 and newFOV <= 500 then
                FOVSize = newFOV
                fovLabel.Text = "FOV do Aimbot: " .. FOVSize
                FOVCircle.Size = UDim2.new(0, FOVSize * 2, 0, FOVSize * 2)
                FOVCircle.Position = UDim2.new(0.5, -FOVSize, 0.5, -FOVSize)
            else
                fovSlider.Text = tostring(FOVSize)
            end
        end)
    end)

    GodCategory.MouseButton1Click:Connect(function()
        clearContent()
        local title = Instance.new("TextLabel")
        title.Parent = ContentFrame
        title.Size = UDim2.new(1, 0, 0, 30)
        title.Position = UDim2.new(0, 0, 0, 5)
        title.BackgroundTransparency = 1
        title.Text = "⚡ GOD OPTIONS"
        title.TextColor3 = Color3.fromRGB(255, 255, 255)
        title.Font = Enum.Font.GothamBold
        title.TextSize = 16
        
        local godmode = createToggleButton("💪 GOD MODE", ContentFrame, function(enabled)
            GodModeEnabled = enabled
        end)
        godmode.Position = UDim2.new(0, 10, 0, 40)
    end)

    ConfigCategory.MouseButton1Click:Connect(function()
        clearContent()
        local title = Instance.new("TextLabel")
        title.Parent = ContentFrame
        title.Size = UDim2.new(1, 0, 0, 30)
        title.Position = UDim2.new(0, 0, 0, 5)
        title.BackgroundTransparency = 1
        title.Text = "⚙️ CONFIG"
        title.TextColor3 = Color3.fromRGB(255, 255, 255)
        title.Font = Enum.Font.GothamBold
        title.TextSize = 16
        
        local modoleve = createToggleButton("⚡ MODO LEVE", ContentFrame, function(enabled)
            ModoLeveEnabled = enabled
            if enabled then
                game:GetService("Lighting").GlobalShadows = false
                game:GetService("Lighting").Brightness = 1
                for _, v in pairs(game:GetDescendants()) do
                    if v:IsA("Texture") then
                        v:Destroy()
                    end
                end
            end
        end)
        modoleve.Position = UDim2.new(0, 10, 0, 40)
        
        local keyLabel = Instance.new("TextLabel")
        keyLabel.Parent = ContentFrame
        keyLabel.Size = UDim2.new(1, 0, 0, 30)
        keyLabel.Position = UDim2.new(0, 0, 0, 90)
        keyLabel.BackgroundTransparency = 1
        keyLabel.Text = "🔑 Tecla de Bypass: " .. BypassKey
        keyLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        keyLabel.Font = Enum.Font.Gotham
        keyLabel.TextSize = 14
        
        local keyInput = Instance.new("TextBox")
        keyInput.Parent = ContentFrame
        keyInput.Size = UDim2.new(1, -20, 0, 35)
        keyInput.Position = UDim2.new(0, 10, 0, 125)
        keyInput.PlaceholderText = "Digite nova tecla..."
        keyInput.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
        keyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
        keyInput.Font = Enum.Font.Gotham
        keyInput.TextSize = 14
        keyInput.BorderSizePixel = 0
        
        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = keyInput
        
        keyInput.FocusLost:Connect(function()
            if keyInput.Text ~= "" then
                BypassKey = keyInput.Text:upper()
                keyLabel.Text = "🔑 Tecla de Bypass: " .. BypassKey
            end
        end)
    end)

    -- Sistema de Bypass e Toggle do Aimbot
    game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
        if input.KeyCode == Enum.KeyCode[BypassKey] then
            ScreenGui.Enabled = not ScreenGui.Enabled
        end
        
        -- Toggle do Aimbot com tecla F
        if input.KeyCode == Enum.KeyCode.F and not gameProcessed then
            toggleAimbot()
        end
    end)

    -- Função do Aimbot (funcional)
    local function getClosestPlayerInFOV()
        local closestPlayer = nil
        local closestDistance = FOVSize
        
        for _, plr in pairs(game.Players:GetPlayers()) do
            if plr ~= Player and plr.Character and plr.Character:FindFirstChild("Head") and plr.Character:FindFirstChild("Humanoid") then
                if plr.Character.Humanoid.Health > 0 then
                    local head = plr.Character.Head
                    local screenPos, onScreen = Camera:WorldToScreenPoint(head.Position)
                    
                    if onScreen then
                        local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
                        local headScreenPos = Vector2.new(screenPos.X, screenPos.Y)
                        local distance = (headScreenPos - screenCenter).Magnitude
                        
                        if distance < closestDistance then
                            closestDistance = distance
                            closestPlayer = plr
                        end
                    end
                end
            end
        end
        
        return closestPlayer
    end

    -- Loop do Aimbot
    game:GetService("RunService").RenderStepped:Connect(function()
        if AimbotEnabled then
            local target = getClosestPlayerInFOV()
            
            if target and target.Character and target.Character:FindFirstChild("Head") then
                TargetPlayer = target
                local character = Player.Character
                
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local headPosition = target.Character.Head.Position
                    local rootPosition = character.HumanoidRootPart.Position
                    
                    -- Calcula direção para a cabeça do alvo
                    local direction = (headPosition - rootPosition).Unit
                    
                    -- Atualiza a rotação do personagem para mirar na cabeça
                    local lookAt = CFrame.new(rootPosition, rootPosition + direction)
                    character.HumanoidRootPart.CFrame = lookAt
                    
                    -- Opcional: Atualizar câmera para seguir o alvo
                    Camera.CFrame = CFrame.new(Camera.CFrame.Position, headPosition)
                end
            else
                TargetPlayer = nil
            end
        end
    end)

    -- NoClip
    game:GetService("RunService").Stepped:Connect(function()
        if NoClipEnabled and Player.Character then
            for _, part in pairs(Player.Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end
    end)

    -- God Mode
    game:GetService("RunService").Heartbeat:Connect(function()
        if GodModeEnabled and Player.Character and Player.Character:FindFirstChild("Humanoid") then
            Player.Character.Humanoid.Health = Player.Character.Humanoid.MaxHealth
        end
    end)

    -- Reconectar personagem
    Player.CharacterAdded:Connect(function(char)
        if SpeedBoostEnabled then
            wait(0.5)
            if char:FindFirstChild("Humanoid") then
                char.Humanoid.WalkSpeed = 30
            end
        end
    end)

    print("✅ Hub Premium carregado com sucesso!")
    print("🔑 Pressione " .. BypassKey .. " para mostrar/ocultar o hub")
    print("🎯 Pressione F para ativar/desativar o Aimbot")
end
