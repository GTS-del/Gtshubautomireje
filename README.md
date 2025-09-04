# Gtshubautomireje-- 🌙 GTS Hub - Mirage Auto Farm (Mobile Final Ultra)
-- Compatível com KRNL / Mobile

-- // Services
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- // Variáveis de controle
local FarmEnabled = false
local MirageFound, GearFound = false, false
local FlightSpeed = 200 -- velocidade de voo

-- // GUI
local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
ScreenGui.Name = "GTSHub"

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 240, 0, 140)
MainFrame.Position = UDim2.new(0.5, -120, 0.1, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 85, 255)
MainFrame.BackgroundTransparency = 0.2
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "🌙 GTS Hub - Mirage"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 18
Title.BackgroundTransparency = 1

local Status = Instance.new("TextLabel", MainFrame)
Status.Size = UDim2.new(1, 0, 0, 30)
Status.Position = UDim2.new(0, 0, 0, 40)
Status.Text = "🛑 Farm parado"
Status.TextColor3 = Color3.fromRGB(255, 255, 255)
Status.Font = Enum.Font.SourceSans
Status.TextSize = 16
Status.BackgroundTransparency = 1

local ToggleButton = Instance.new("TextButton", MainFrame)
ToggleButton.Size = UDim2.new(1, -20, 0, 40)
ToggleButton.Position = UDim2.new(0, 10, 0, 80)
ToggleButton.Text = "▶️ Iniciar Farm"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 18
ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 45, 180)
ToggleButton.BorderSizePixel = 0

-- // Funções

-- 1️⃣ Falar com Rei Selado
local function TalkToSealedKing()
    local char = LocalPlayer.Character
    if not char then return end
    Status.Text = "🤴 Falando com o Rei Selado..."
    pcall(function()
        ReplicatedStorage.Remotes.CommF_:InvokeServer("TalkToNPC", "SealedKing", "O meu personagem está aqui!")
    end)
    task.wait(1)
    Status.Text = "✅ Conversa concluída!"
end

-- 2️⃣ Spawn + sentar no barco
local function SpawnGuardianBoat()
    Status.Text = "⛵ Preparando barco..."
    local args = {"BuyBoat", "Guardian"}
    pcall(function()
        ReplicatedStorage.Remotes.CommF_:InvokeServer(unpack(args))
    end)
    task.wait(2)

    for _, v in ipairs(workspace:GetDescendants()) do
        if v:IsA("VehicleSeat") and v.Parent.Name:find("Guardian") then
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char:MoveTo(v.Position + Vector3.new(0,5,0))
                task.wait(0.5)
                v:Sit(char.Humanoid)
                Status.Text = "✅ Sentado no barco!"
            end
            break
        end
    end
end

-- 3️⃣ Barco andando infinito até Mirage (ultra-rápido + atravessa tudo)
local function DriveBoatInfinite()
    spawn(function()
        while FarmEnabled and not MirageFound do
            task.wait(0.01)
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                -- Desativa colisão do personagem
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
                -- Move o barco em frente super rápido
                char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame * CFrame.new(0,0,-500)
                Status.Text = "🌊 Navegando no Mar 6 (ULTRA TURBO + atravessando)..."
            end
        end
    end)
end

-- 4️⃣ Detecta Mirage Island
local function DetectMirage()
    spawn(function()
        while FarmEnabled and not MirageFound do
            task.wait(1)
            if workspace:FindFirstChild("Mirage Island") then
                Status.Text = "🌟 Mirage encontrada!"
                game.StarterGui:SetCore("SendNotification", {
                    Title = "🌙 GTS Hub",
                    Text = "Mirage Island spawnou! Indo para lá!",
                    Duration = 10
                })
                MirageFound = true
                break
            end
        end
    end)
end

-- 5️⃣ Voo rápido
local function FlyTo(position)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local hrp = char.HumanoidRootPart

    for _, part in ipairs(char:GetDescendants()) do
        if part:IsA("BasePart") then part.CanCollide = false end
    end

    while (hrp.Position - position).Magnitude > 5 and FarmEnabled do
        hrp.CFrame = hrp.CFrame + (position - hrp.Position).Unit * FlightSpeed * task.wait()
    end
end

-- 6️⃣ Ponto mais alto da Mirage
local function GoToTop()
    local Mirage = workspace:FindFirstChild("Mirage Island")
    if not Mirage then return end
    local highestY, target = -math.huge, nil
    for _, v in ipairs(Mirage:GetDescendants()) do
        if v:IsA("BasePart") and v.Position.Y > highestY then
            highestY = v.Position.Y
            target = v
        end
    end
    if target then
        Status.Text = "🧗 Subindo ponto mais alto..."
        FlyTo(target.Position + Vector3.new(0,10,0))
    end
end

-- 7️⃣ Olhar para lua
local function LookAtMoon()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local hrp = char.HumanoidRootPart
    Status.Text = "🌙 Olhando para a lua..."
    hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + Vector3.new(0,100,0))
end

-- 8️⃣ Ativar Race V3
local function ActivateRaceV3()
    Status.Text = "⚡ Ativando Race V3..."
    pcall(function()
        ReplicatedStorage.Remotes.CommF_:InvokeServer("ActivateRaceV3")
    end)
end

-- 9️⃣ Pegar engrenagem azul
local function GetGear()
    spawn(function()
        while FarmEnabled and MirageFound and not GearFound do
            task.wait(1)
            local Gear = workspace:FindFirstChild("BlueGear")
            if Gear then
                Status.Text = "🔧 Pegando engrenagem azul!"
                FlyTo(Gear.Position + Vector3.new(0,5,0))
                GearFound = true
                game.StarterGui:SetCore("SendNotification", {
                    Title = "🌙 GTS Hub",
                    Text = "Engrenagem azul coletada!",
                    Duration = 10
                })
                break
            end
        end
    end)
end

-- // Inicia / para farm
ToggleButton.MouseButton1Click:Connect(function()
    FarmEnabled = not FarmEnabled
    if FarmEnabled then
        ToggleButton.Text = "⏹ Parar Farm"
        Status.Text = "⛵ Preparando..."
        spawn(function()
            TalkToSealedKing()
            SpawnGuardianBoat()
            DriveBoatInfinite()
            DetectMirage()

            while FarmEnabled do
                task.wait(1)
                if MirageFound then
                    FlyTo(workspace["Mirage Island"].PrimaryPart.Position + Vector3.new(0,50,0))
                    GoToTop()
                    LookAtMoon()
                    ActivateRaceV3()
                    GetGear()
                    break
                end
            end
        end)
    else
        ToggleButton.Text = "▶️ Iniciar Farm"
        Status.Text = "🛑 Farm parado"
        MirageFound, GearFound = false, false
    end
end)
