local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.ResetOnSpawn = false -- Evita que seja apagado ao renascer
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Função para aplicar Squircle
local function applySquircle(element, radius)
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, radius)
    UICorner.Parent = element
end

-- Criar Frame principal
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 150)
Frame.Position = UDim2.new(0.5, -290, 0.6, -125)
Frame.BackgroundColor3 = Color3.new(0, 0, 0) -- Preto
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Visible = false
Frame.Parent = ScreenGui
applySquircle(Frame, 15)

-- Criar título
local Title = Instance.new("TextLabel")
Title.Text = "Assistir Jogador"
Title.Size = UDim2.new(1, 0, 0, 20)
Title.BackgroundColor3 = Color3.new(0, 0, 0) -- Preto
Title.TextColor3 = Color3.new(1, 1, 1) -- Branco
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 19
Title.Parent = Frame
applySquircle(Title, 10)

-- Criar botão de seleção de jogador
local PlayerList = Instance.new("TextButton")
PlayerList.Text = "Selecionar Jogador"
PlayerList.Size = UDim2.new(1, -20, 0, 30)
PlayerList.Position = UDim2.new(0, 10, 0, 40)
PlayerList.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
PlayerList.TextColor3 = Color3.new(1, 1, 1) -- Branco
PlayerList.BackgroundTransparency = 0.2 -- Transparência leve no botão
PlayerList.Font = Enum.Font.SourceSans
PlayerList.TextSize = 16
PlayerList.Parent = Frame
applySquircle(PlayerList, 10)

-- Criar ScrollingFrame para a lista de jogadores
local DropDown = Instance.new("ScrollingFrame")
DropDown.Size = UDim2.new(0, 150, 0, 100)
DropDown.Position = UDim2.new(1, 10, 0, 40) -- Aparece ao lado direito
DropDown.BackgroundColor3 = Color3.new(0, 0, 0) -- Preto
DropDown.BackgroundTransparency = 0.3 -- Deixa o dropdown um pouco transparente
DropDown.BorderSizePixel = 2
DropDown.ScrollingEnabled = true
DropDown.CanvasSize = UDim2.new(0, 0, 0, 0)
DropDown.Visible = false
DropDown.Parent = Frame

-- Atualizar lista de jogadores
local function UpdatePlayerList()
    for _, child in pairs(DropDown:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end
    
    local yOffset = 0
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local Button = Instance.new("TextButton")
            Button.Text = player.Name
            Button.Size = UDim2.new(1, 0, 0, 25)
            Button.Position = UDim2.new(0, 0, 0, yOffset)
            Button.BackgroundColor3 = Color3.new(0, 0, 0) -- Preto
            Button.TextColor3 = Color3.new(1, 1, 1) -- Branco
            Button.Font = Enum.Font.SourceSans
            Button.TextSize = 14
            Button.Parent = DropDown
            Button.MouseButton1Click:Connect(function()
                PlayerList.Text = player.Name
                DropDown.Visible = false
            end)
            yOffset = yOffset + 25
        end
    end
    DropDown.CanvasSize = UDim2.new(0, 0, 0, yOffset)
end

PlayerList.MouseButton1Click:Connect(function()
    DropDown.Visible = not DropDown.Visible
    UpdatePlayerList()
end)

Players.PlayerAdded:Connect(UpdatePlayerList)
Players.PlayerRemoving:Connect(UpdatePlayerList)

-- Criar botão de assistir
local WatchButton = Instance.new("TextButton")
WatchButton.Text = "Assistir"
WatchButton.Size = UDim2.new(1, -20, 0, 30)
WatchButton.Position = UDim2.new(0, 10, 0, 80)
WatchButton.BackgroundColor3 = Color3.new(0, 0.3, 0) -- Preto com tom verde escuro
WatchButton.TextColor3 = Color3.new(1, 1, 1) -- Branco
WatchButton.Font = Enum.Font.SourceSansBold
WatchButton.TextSize = 16
WatchButton.Parent = Frame
applySquircle(WatchButton, 10)

local isWatching = false

WatchButton.MouseButton1Click:Connect(function()
    local TargetPlayer = Players:FindFirstChild(PlayerList.Text)
    if TargetPlayer and TargetPlayer.Character and TargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        Camera.CameraSubject = TargetPlayer.Character.Humanoid
        Camera.CFrame = TargetPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, -10)
        Camera.FieldOfView = 70
        isWatching = true
    end
end)

-- Criar botão de parar de assistir
local StopWatchingButton = Instance.new("TextButton")
StopWatchingButton.Text = "Parar de Assistir"
StopWatchingButton.Size = UDim2.new(1, -20, 0, 30)
StopWatchingButton.Position = UDim2.new(0, 10, 0, 120)
StopWatchingButton.BackgroundColor3 = Color3.new(0.5, 0, 0) -- Vermelho escuro
StopWatchingButton.TextColor3 = Color3.new(1, 1, 1) -- Branco
StopWatchingButton.Font = Enum.Font.SourceSansBold
StopWatchingButton.TextSize = 16
StopWatchingButton.Parent = Frame
applySquircle(StopWatchingButton, 10)


StopWatchingButton.MouseButton1Click:Connect(function()
    if isWatching then
        Camera.CameraSubject = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") or LocalPlayer.Character
        isWatching = false
    end
end)

-- Criar botão para abrir/fechar a GUI
local ToggleButton = Instance.new("TextButton")
ToggleButton.Text = "="
ToggleButton.Size = UDim2.new(0, 30, 0, 30)
ToggleButton.Position = UDim2.new(0, 10, 1, -90)
ToggleButton.BackgroundColor3 = Color3.new(0, 0, 0) -- Preto
ToggleButton.TextColor3 = Color3.new(1, 1, 1) -- Branco
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 24
ToggleButton.Parent = ScreenGui
applySquircle(ToggleButton, 10)

-- Tornar o botão movível
ToggleButton.Active = true
ToggleButton.Draggable = true

ToggleButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
    LocalPlayer:SetAttribute("GUI_Visivel", Frame.Visible) -- Salvar estado
end)
