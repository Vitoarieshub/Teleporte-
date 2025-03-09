local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false -- Faz com que a GUI não desapareça ao morrer

-- Função para aplicar Squircle (bordas arredondadas)
local function applySquircle(element, radius)
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, radius)
    UICorner.Parent = element
end

-- Criar Frame principal (com Squircle)
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 150)
Frame.Position = UDim2.new(0.6, -20, 0.4, -65)
Frame.BackgroundColor3 = Color3.new(0, 0, 0)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Visible = false
Frame.Parent = ScreenGui
applySquircle(Frame, 15) -- Aplica Squircle

-- Criar título
local Title = Instance.new("TextLabel")
Title.Text = "Teleporte"
Title.Size = UDim2.new(1, 0, 0, 20)
Title.BackgroundColor3 = Color3.new(0, 0, 0)
Title.BackgroundTransparency = 0.2
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 19
Title.Parent = Frame
applySquircle(Title, 10)

-- Criar Dropdown (Lista de Jogadores)
local PlayerList = Instance.new("TextButton")
PlayerList.Text = "Selecionar Jogador"
PlayerList.Size = UDim2.new(1, -20, 0, 30)
PlayerList.Position = UDim2.new(0, 10, 0, 40)
PlayerList.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
PlayerList.BackgroundTransparency = 0.2
PlayerList.TextColor3 = Color3.new(1, 1, 1)
PlayerList.Font = Enum.Font.SourceSans
PlayerList.TextSize = 16
PlayerList.Parent = Frame
applySquircle(PlayerList, 10)

-- Criar ScrollingFrame para a lista de jogadores
local DropDown = Instance.new("ScrollingFrame")
DropDown.Size = UDim2.new(0, 150, 0, 100)
DropDown.Position = UDim2.new(1, 10, 0, 40)
DropDown.BackgroundColor3 = Color3.new(0, 0, 0)
DropDown.BackgroundTransparency = 0.3
DropDown.BorderSizePixel = 0
DropDown.ScrollingEnabled = true
DropDown.CanvasSize = UDim2.new(0, 0, 0, 0)
DropDown.Visible = false
DropDown.Parent = Frame
applySquircle(DropDown, 10)

local PlayerButtons = {}

-- Atualizar lista de jogadores online
local function UpdatePlayerList()
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
            Button.BackgroundColor3 = Color3.new(0, 0, 0)
            Button.BackgroundTransparency = 0.2
            Button.TextColor3 = Color3.new(1, 1, 1)
            Button.Font = Enum.Font.SourceSans
            Button.TextSize = 14
            Button.Parent = DropDown
            applySquircle(Button, 8)
            Button.MouseButton1Click:Connect(function()
                PlayerList.Text = player.Name
                DropDown.Visible = false
            end)
            table.insert(PlayerButtons, Button)
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

-- Criar botão de teleporte
local TeleportButton = Instance.new("TextButton")
TeleportButton.Text = "Teleporta"
TeleportButton.Size = UDim2.new(1, -20, 0, 30)
TeleportButton.Position = UDim2.new(0, 10, 0, 80)
TeleportButton.BackgroundColor3 = Color3.new(0, 0.3, 0)
TeleportButton.BackgroundTransparency = 0.2
TeleportButton.TextColor3 = Color3.new(1, 1, 1)
TeleportButton.Font = Enum.Font.SourceSansBold
TeleportButton.TextSize = 16
TeleportButton.Parent = Frame
applySquircle(TeleportButton, 10)

TeleportButton.MouseButton1Click:Connect(function()
    local TargetPlayerName = PlayerList.Text
    if TargetPlayerName == "Selecionar Jogador" then return end
    
    local TargetPlayer = Players:FindFirstChild(TargetPlayerName)
    if TargetPlayer and TargetPlayer.Character and TargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character:SetPrimaryPartCFrame(TargetPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 2, 0))
    end
end)

-- Criar botão para abrir/fechar a GUI
local ToggleButton = Instance.new("TextButton")
ToggleButton.Text = "T"
ToggleButton.Size = UDim2.new(0, 40, 0, 40)
ToggleButton.Position = UDim2.new(1, -60, 0.1, 10) -- Ajustado para acompanhar o Frame mais para cima
ToggleButton.BackgroundColor3 = Color3.new(0, 0, 0)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 23
ToggleButton.Parent = ScreenGui
ToggleButton.Active = true
ToggleButton.Draggable = true
applySquircle(ToggleButton, 10)

ToggleButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)
