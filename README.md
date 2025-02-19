local teleportDistance = 5  -- DistÃ¢ncia para teleportar ao redor do jogador
local teleportSpeed = 200   -- Velocidade do teleporte
local isTeleporting = false -- VariÃ¡vel para controlar o teleporte

local Gui = Instance.new("ScreenGui")
local TextBox = Instance.new("TextBox")
local TestButton = Instance.new("TextButton")
local VoidButton = Instance.new("TextButton")
local DraggableFrame = Instance.new("Frame")
local StatusLabel = Instance.new("TextLabel")
local Background = Instance.new("ImageLabel") -- Adicionado ImageLabel para fundo

-- ConfiguraÃ§Ã£o do GUI
Gui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
Gui.Name = "ðŸŽ©FLING RED BLACK ðŸŽ©"

-- ConfiguraÃ§Ã£o do Frame arrastÃ¡vel
DraggableFrame.Parent = Gui
DraggableFrame.Size = UDim2.new(0, 300, 0, 250)
DraggableFrame.Position = UDim2.new(0.5, -150, 0.5, -125)
DraggableFrame.BackgroundColor3 = Color3.fromRGB(139, 69, 19)  -- Cor marrom
DraggableFrame.Active = true
DraggableFrame.Draggable = true

-- ConfiguraÃ§Ã£o do ImageLabel de fundo
Background.Parent = DraggableFrame
Background.Size = UDim2.new(1, 0, 1, 0)
Background.Position = UDim2.new(0, 0, 0, 0)
Background.Image = "rbxassetid://127522669208877" -- Certifique-se de que este Ã© o ID correto
Background.BackgroundColor3 = Color3.new(1, 1, 1) -- Cor branca como fundo
Background.BorderSizePixel = 0 -- Sem borda

-- ConfiguraÃ§Ã£o do TextBox
TextBox.Parent = DraggableFrame
TextBox.Size = UDim2.new(0, 200, 0, 50)
TextBox.Position = UDim2.new(0.5, -100, 0.05, 0)
TextBox.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor azul
TextBox.TextColor3 = Color3.fromRGB(0, 255, 0)  -- Texto verde
TextBox.PlaceholderText = "Digite parte do nome"
TextBox.Name = "PlayerNickBox"

-- ConfiguraÃ§Ã£o do botÃ£o "FLING BETA"
TestButton.Parent = DraggableFrame
TestButton.Size = UDim2.new(0, 200, 0, 50)
TestButton.Position = UDim2.new(0.5, -100, 0.3, 0)
TestButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor azul
TestButton.TextColor3 = Color3.fromRGB(0, 255, 0)  -- Texto verde
TestButton.Text = "FLING BETA"
TestButton.Name = "TestButton"

-- ConfiguraÃ§Ã£o do botÃ£o "JOGAR NO VOID"
VoidButton.Parent = DraggableFrame
VoidButton.Size = UDim2.new(0, 200, 0, 50)
VoidButton.Position = UDim2.new(0.5, -100, 0.55, 0)
VoidButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor azul
VoidButton.TextColor3 = Color3.fromRGB(0, 255, 0)  -- Texto verde
VoidButton.Text = "JOGAR NO VOID"
VoidButton.Name = "VoidButton"

-- ConfiguraÃ§Ã£o do Label de Status
StatusLabel.Parent = DraggableFrame
StatusLabel.Size = UDim2.new(0, 200, 0, 50)
StatusLabel.Position = UDim2.new(0.5, -100, 0.8, 0)
StatusLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor azul
StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)  -- Texto verde
StatusLabel.Text = ""
StatusLabel.Name = "StatusLabel"

-- FunÃ§Ã£o para teleportar ao redor do jogador ou carro
local function teleportAroundPlayer(player)
    spawn(function()
        while isTeleporting do
            if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local humanoidRootPart = player.Character.HumanoidRootPart
                local randomAngle = math.random() * 2 * math.pi
                local offsetX = math.cos(randomAngle) * teleportDistance
                local offsetZ = math.sin(randomAngle) * teleportDistance
                game.Players.LocalPlayer.Character:SetPrimaryPartCFrame(CFrame.new(humanoidRootPart.Position + Vector3.new(offsetX, 0, offsetZ)))

                -- Teleportar o carro se o jogador estiver dentro de um veÃ­culo desancorado
                local seat = game.Players.LocalPlayer.Character:FindFirstChildOfClass("VehicleSeat")
                if seat and not seat.Anchored then
                    seat.Parent:SetPrimaryPartCFrame(CFrame.new(humanoidRootPart.Position + Vector3.new(offsetX, 0, offsetZ)))
                end
            end
            wait(1/teleportSpeed)  -- Ajuste do intervalo para corresponder Ã  velocidade do teleporte
        end
    end)
end

-- FunÃ§Ã£o para teletransportar quando o jogador sentar no sofÃ¡
local function teleportOnSofa(player)
    if player and player.Character and player.Backpack:FindFirstChild("SofÃ¡") then
        local sofa = player.Backpack["SofÃ¡"]
        sofa.ChildAdded:Connect(function(child)
            if child:IsA("Seat") and child.Occupant then
                game.Players.LocalPlayer.Character:SetPrimaryPartCFrame(CFrame.new(67022016512, 67022016512, -30348027904))
            end
        end)
    end
end

-- FunÃ§Ã£o para teleportar para o "VOID"
local function teleportToVoid()
    game.Players.LocalPlayer.Character:SetPrimaryPartCFrame(CFrame.new(670222727727273737016512, 670220373737316512, -30348737373773747027904))
end

-- Evento para o botÃ£o "FLING BETA"
TestButton.MouseButton1Click:Connect(function()
    local searchText = TextBox.Text:lower()
    local player = nil
    for _, p in ipairs(game.Players:GetPlayers()) do
        if p.Name:lower():find(searchText) or p.DisplayName:lower():find(searchText) then
            player = p
            break
        end
    end

    if player then
        isTeleporting = not isTeleporting
        if isTeleporting then
            TestButton.Text = "Parar"
            StatusLabel.Text = "Teleportando ao redor do jogador..."
            
            -- Aumentar velocidade do humanoide
            local humanoid = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.WalkSpeed = 500
            end

            teleportAroundPlayer(player)
            teleportOnSofa(player)
        else
            TestButton.Text = "FLING BETA"
            StatusLabel.Text = "Teleporte parado."

            -- Restaurar velocidade do humanoide ao valor padrÃ£o (16)
            local humanoid = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.WalkSpeed = 16
            end
        end
    else
        StatusLabel.Text = "Jogador nÃ£o encontrado!"
    end
end)

-- Evento para o botÃ£o "JOGAR NO VOID"
VoidButton.MouseButton1Click:Connect(function()
    teleportToVoid()
    StatusLabel.Text = "Teletransportado para o VOID."
end)

-- Evento para parar o teleporte ao morrer (nÃ£o fechar GUI)
game.Players.LocalPlayer.Character.Humanoid.Died:Connect(function()
    isTeleporting = false
    TestButton.Text = "FLING BETA"
    StatusLabel.Text = "Teleporte parado."

    -- Restaurar velocidade do humanoide ao valor padrÃ£o (16)
    local humanoid = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = 16
    end
end)
