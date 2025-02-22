local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = game:GetService("Workspace").CurrentCamera

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Criar Frame principal
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 150)
Frame.Position = UDim2.new(0.5, -100, 0.5, -75)
Frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
Frame.BorderSizePixel = 2
Frame.Active = true
Frame.Draggable = true
Frame.Visible = false  -- Inicialmente invisível
Frame.Parent = ScreenGui

-- Criar título
local Title = Instance.new("TextLabel")
Title.Text = "Assistir Jogador"
Title.Size = UDim2.new(1, -30, 0, 30)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 18
Title.Parent = Frame

-- Criar botão de fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Text = "X"
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -30, 0, 0)
CloseButton.BackgroundColor3 = Color3.new(1, 0, 0)
CloseButton.TextColor3 = Color3.new(1, 1, 1)
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.TextSize = 18
CloseButton.Parent = Frame

-- Criar Dropdown (Lista de Jogadores)
local PlayerList = Instance.new("TextButton")
PlayerList.Text = "Selecionar Jogador"
PlayerList.Size = UDim2.new(1, -20, 0, 30)
PlayerList.Position = UDim2.new(0, 10, 0, 40)
PlayerList.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
PlayerList.TextColor3 = Color3.new(1, 1, 1)
PlayerList.Font = Enum.Font.SourceSans
PlayerList.TextSize = 16
PlayerList.Parent = Frame

-- Criar Botão de Assistir
local WatchButton = Instance.new("TextButton")
WatchButton.Text = "Assistir"
WatchButton.Size = UDim2.new(1, -20, 0, 30)
WatchButton.Position = UDim2.new(0, 10, 0, 80)
WatchButton.BackgroundColor3 = Color3.new(0, 0.5, 0)
WatchButton.TextColor3 = Color3.new(1, 1, 1)
WatchButton.Font = Enum.Font.SourceSansBold
WatchButton.TextSize = 16
WatchButton.Parent = Frame

-- Criar Botão de Parar de Assistir
local StopWatchingButton = Instance.new("TextButton")
StopWatchingButton.Text = "Parar de Assistir"
StopWatchingButton.Size = UDim2.new(1, -20, 0, 30)
StopWatchingButton.Position = UDim2.new(0, 10, 0, 120)
StopWatchingButton.BackgroundColor3 = Color3.new(1, 0, 0)
StopWatchingButton.TextColor3 = Color3.new(1, 1, 1)
StopWatchingButton.Font = Enum.Font.SourceSansBold
StopWatchingButton.TextSize = 16
StopWatchingButton.Parent = Frame

-- Criar ScrollingFrame para a lista de jogadores
local DropDown = Instance.new("ScrollingFrame")
DropDown.Size = UDim2.new(1, -20, 0, 100)
DropDown.Position = UDim2.new(0, 10, 0, 70)
DropDown.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
DropDown.BorderSizePixel = 2
DropDown.ScrollingEnabled = true  -- Habilita a rolagem
DropDown.CanvasSize = UDim2.new(0, 0, 0, 0)  -- Inicializa o tamanho da tela para se ajustar dinamicamente
DropDown.Visible = false
DropDown.Parent = Frame

local PlayerButtons = {}

-- Atualizar lista de jogadores online
local function UpdatePlayerList()
    -- Salvar jogador selecionado antes de atualizar a lista
    local SelectedPlayer = PlayerList.Text

    -- Limpar botões antigos
    for _, button in pairs(PlayerButtons) do
        button:Destroy()
    end
    PlayerButtons = {}

    local yOffset = 0
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local Button = Instance.new("TextButton")
            Button.Text = player.Name
            Button.Size = UDim2.new(1, 0, 0, 25)
            Button.Position = UDim2.new(0, 0, 0, yOffset)
            Button.BackgroundColor3 = Color3.new(0.4, 0.4, 0.4)
            Button.TextColor3 = Color3.new(1, 1, 1)
            Button.Font = Enum.Font.SourceSans
            Button.TextSize = 14
            Button.Parent = DropDown
            Button.MouseButton1Click:Connect(function()
                PlayerList.Text = player.Name
                DropDown.Visible = false
            end)
            table.insert(PlayerButtons, Button)
            yOffset = yOffset + 25
        end
    end

    -- Atualizar o tamanho do Canvas da ScrollingFrame
    DropDown.CanvasSize = UDim2.new(0, 0, 0, yOffset)

    -- Restaurar a seleção do jogador caso ainda esteja na lista
    local playerStillExists = Players:FindFirstChild(SelectedPlayer)
    if playerStillExists then
        PlayerList.Text = SelectedPlayer
    else
        PlayerList.Text = "Selecionar Jogador"
    end
end

-- Abrir Dropdown ao clicar
PlayerList.MouseButton1Click:Connect(function()
    DropDown.Visible = not DropDown.Visible
    UpdatePlayerList()
end)

-- Atualizar lista automaticamente quando jogadores entram ou saem
Players.PlayerAdded:Connect(UpdatePlayerList)
Players.PlayerRemoving:Connect(UpdatePlayerList)

local isWatching = false
local TargetPlayer

-- Assistir o jogador selecionado
WatchButton.MouseButton1Click:Connect(function()
    local TargetPlayerName = PlayerList.Text
    if TargetPlayerName == "Selecionar Jogador" then
        -- Notificar que o jogador não foi selecionado
        local Notification = Instance.new("TextLabel")
        Notification.Size = UDim2.new(0, 300, 0, 50)
        Notification.Position = UDim2.new(0.5, -150, 0.5, 100)
        Notification.BackgroundColor3 = Color3.new(1, 0, 0)
        Notification.TextColor3 = Color3.new(1, 1, 1)
        Notification.Text = "Por favor, selecione um jogador primeiro!"
        Notification.TextSize = 18
        Notification.Parent = ScreenGui
        wait(3)
        Notification:Destroy()
        return
    end

    -- Proceder com a câmera assistindo o jogador
    TargetPlayer = Players:FindFirstChild(TargetPlayerName)
    if TargetPlayer and TargetPlayer.Character and TargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        -- Alterar a posição da câmera para seguir o jogador
        Camera.CameraSubject = TargetPlayer.Character.Humanoid
        Camera.CFrame = TargetPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, -10) -- Ajuste a posição da câmera
        Camera.FieldOfView = 70 -- Ajuste o campo de visão, se necessário
        isWatching = true
    end
end)

-- Parar de Assistir o jogador
StopWatchingButton.MouseButton1Click:Connect(function()
    if isWatching then
        -- Restaurar a câmera para a visão normal do jogador local
        Camera.CameraSubject = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") or LocalPlayer.Character
        Camera.CFrame = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame or Camera.CFrame
        isWatching = false
    end
end)

-- Fechar GUI ao clicar no botão "X"
CloseButton.MouseButton1Click:Connect(function()
    Frame.Visible = false
end)

-- Criar botão para abrir/fechar a GUI
local ToggleButton = Instance.new("TextButton")
ToggleButton.Text = "="  -- Ícone para indicar menu
ToggleButton.Size = UDim2.new(0, 40, 0, 40) -- Pequeno e quadrado
ToggleButton.Position = UDim2.new(0, 10, 1, -50)  -- Posição inicial
ToggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 24
ToggleButton.Parent = ScreenGui

-- Tornar o botão movível
ToggleButton.Active = true
ToggleButton.Draggable = true

-- Alternar a visibilidade do Frame ao clicar no ToggleButton
ToggleButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)
