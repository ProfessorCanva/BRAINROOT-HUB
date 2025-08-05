--[[ 
   LocalScript: Menu ADM Flutuante
   Coloque este script dentro de StarterPlayerScripts
   OBS: Para jogos que não são seus, scripts locais NÃO funcionarão.
--]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")

-- Criar GUI flutuante
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.Name = "ADMMenu"

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 250, 0, 260)
Frame.Position = UDim2.new(0.02, 0, 0.2, 0)
Frame.BackgroundColor3 = Color3.fromRGB(40,40,60)
Frame.BackgroundTransparency = 0.2
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Text = "ADM MENU"
Title.Size = UDim2.new(1,0,0,40)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(255,255,255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 28
Title.Parent = Frame

function createButton(text, order)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9,0,0,40)
    btn.Position = UDim2.new(0.05,0,0,50 + (order-1)*50)
    btn.BackgroundColor3 = Color3.fromRGB(70,70,100)
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 22
    btn.Text = text
    btn.Parent = Frame
    return btn
end

local btnFly = createButton("✈️ Fly", 1)
local btnTPBase = createButton("🏠 TP Base", 2)
local btnTPEnemies = createButton("💀 TP Base Inimigos", 3)
local btnAdmin = createButton("👑 Admin Máximo", 4)

-- Função FLY
local flying = false
local flyConn
function fly()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local hrp = char.HumanoidRootPart

    flying = not flying
    if flying then
        btnFly.Text = "❌ Parar Fly"
        local bodyVel = Instance.new("BodyVelocity")
        bodyVel.MaxForce = Vector3.new(1,1,1) * 1e6
        bodyVel.Parent = hrp

        flyConn = UIS.InputBegan:Connect(function(input, gpe)
            if gpe then return end
            if input.KeyCode == Enum.KeyCode.W then
                bodyVel.Velocity = hrp.CFrame.LookVector * 60
            elseif input.KeyCode == Enum.KeyCode.S then
                bodyVel.Velocity = -hrp.CFrame.LookVector * 60
            elseif input.KeyCode == Enum.KeyCode.Space then
                bodyVel.Velocity = Vector3.new(0, 60, 0)
            elseif input.KeyCode == Enum.KeyCode.LeftShift then
                bodyVel.Velocity = Vector3.new(0, -60, 0)
            end
        end)
        UIS.InputEnded:Connect(function(input, gpe)
            bodyVel.Velocity = Vector3.new(0,0,0)
        end)
    else
        btnFly.Text = "✈️ Fly"
        if hrp:FindFirstChildOfClass("BodyVelocity") then
            hrp:FindFirstChildOfClass("BodyVelocity"):Destroy()
        end
        if flyConn then flyConn:Disconnect() end
    end
end
btnFly.MouseButton1Click:Connect(fly)

-- TP para Base (pode precisar adaptar para o jogo)
btnTPBase.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        -- Substitua CFrame.new(x, y, z) pela posição de sua base:
        char.HumanoidRootPart.CFrame = CFrame.new(0, 25, 0)
    end
end)

-- TP para Base Inimigos (mude as coords conforme necessário)
btnTPEnemies.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        -- Substitua CFrame.new(x, y, z) pela posição da base inimiga:
        char.HumanoidRootPart.CFrame = CFrame.new(200, 25, 0)
    end
end)

-- Poder Admin Máximo (exemplo: invencibilidade e super velocidade)
btnAdmin.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char then
        if char:FindFirstChildOfClass("Humanoid") then
            char.Humanoid.WalkSpeed = 200
            char.Humanoid.JumpPower = 150
            char.Humanoid.NameDisplayDistance = 0
        end
        -- Invencibilidade (pode não funcionar em todos os jogos)
        if char:FindFirstChild("ForceField") == nil then
            local forceField = Instance.new("ForceField")
            forceField.Parent = char
        end
    end
end)
