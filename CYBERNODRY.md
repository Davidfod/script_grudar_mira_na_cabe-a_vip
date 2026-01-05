local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- // CONFIGURAÇÕES UNIVERSAIS
local Settings = {
    Enabled = false,
    ESP_Enabled = false,
    AimPart = "Head", 
    Sensitivity = 1.0, 
    FOV = 150,
    MaxHeightDiff = 25 -- Ignora alvos muito abaixo ou acima do seu nível
}
local Holding = false

-- // CÍRCULO DE FOV
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2
FOVCircle.Filled = false
FOVCircle.Transparency = 0.7
FOVCircle.Visible = false

-- // INTERFACE PRINCIPAL
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AimbotHub"
ScreenGui.Parent = game:GetService("CoreGui") or LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 220, 0, 210)
MainFrame.Position = UDim2.new(0.05, 0, 0.4, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.Active = true
MainFrame.Draggable = true 
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 10)

local UIStroke = Instance.new("UIStroke", MainFrame)
UIStroke.Thickness = 2.5
UIStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.BackgroundTransparency = 1
Title.Text = "AIMBOT HUB"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16

local Credits = Instance.new("TextLabel", MainFrame)
Credits.Size = UDim2.new(1, -10, 0, 20)
Credits.Position = UDim2.new(0, 5, 1, -22)
Credits.BackgroundTransparency = 1
Credits.Text = "Script Criado por CyberNoDry"
Credits.Font = Enum.Font.Code
Credits.TextSize = 11
Credits.TextXAlignment = Enum.TextXAlignment.Right

local PowerBtn = Instance.new("TextButton", MainFrame)
PowerBtn.Size = UDim2.new(0, 180, 0, 40)
PowerBtn.Position = UDim2.new(0.5, -90, 0.25, 0)
PowerBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
PowerBtn.Text = "AIMBOT: OFF"
PowerBtn.TextColor3 = Color3.fromRGB(255, 50, 50)
PowerBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", PowerBtn)

local EspBtn = Instance.new("TextButton", MainFrame)
EspBtn.Size = UDim2.new(0, 180, 0, 40)
EspBtn.Position = UDim2.new(0.5, -90, 0.52, 0)
EspBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
EspBtn.Text = "ESP STICKMAN: OFF"
EspBtn.TextColor3 = Color3.fromRGB(255, 50, 50)
EspBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", EspBtn)

local MinBtn = Instance.new("TextButton", MainFrame)
MinBtn.Size = UDim2.new(0, 30, 0, 30)
MinBtn.Position = UDim2.new(1, -35, 0, 2)
MinBtn.BackgroundTransparency = 1
MinBtn.Text = "-"
MinBtn.TextColor3 = Color3.new(1,1,1)
MinBtn.TextSize = 25

-- // BOTÃO AIM MOBILE
local MobileAimBtn = Instance.new("TextButton", ScreenGui)
MobileAimBtn.Size = UDim2.new(0, 80, 0, 80)
MobileAimBtn.Position = UDim2.new(0.75, 0, 0.5, 0)
MobileAimBtn.Text = "AIM"
MobileAimBtn.Font = Enum.Font.GothamBold
MobileAimBtn.TextColor3 = Color3.new(1,1,1)
MobileAimBtn.BackgroundColor3 = Color3.new(0,0,0)
MobileAimBtn.BackgroundTransparency = 0.4
MobileAimBtn.Visible = UserInputService.TouchEnabled
Instance.new("UICorner", MobileAimBtn).CornerRadius = UDim.new(1,0)
Instance.new("UIStroke", MobileAimBtn).Thickness = 2

MobileAimBtn.MouseButton1Down:Connect(function() Holding = true end)
MobileAimBtn.MouseButton1Up:Connect(function() Holding = false end)

-- // LÓGICA MINIMIZAR
local isMin = false
MinBtn.MouseButton1Click:Connect(function()
    isMin = not isMin
    MainFrame:TweenSize(isMin and UDim2.new(0, 220, 0, 35) or UDim2.new(0, 220, 0, 210), "Out", "Quad", 0.3, true)
    PowerBtn.Visible, EspBtn.Visible, Credits.Visible = not isMin, not isMin, not isMin
    MinBtn.Text = isMin and "+" or "-"
end)

-- // FUNÇÕES DO ESP STICKMAN (RESTAURADAS)
local function CreateStick(parent, name)
    local f = Instance.new("Frame", parent)
    f.Name = name; f.BorderSizePixel = 0; f.AnchorPoint = Vector2.new(0.5, 0.5)
    return f
end

local function ApplyESP(plr)
    if plr == LocalPlayer then return end
    local function setup(char)
        local root = char:WaitForChild("HumanoidRootPart", 10)
        if not root then return end
        if root:FindFirstChild("StickmanESP") then root.StickmanESP:Destroy() end
        
        local bgui = Instance.new("BillboardGui", root)
        bgui.Name = "StickmanESP"; bgui.AlwaysOnTop = true; bgui.Adornee = root; bgui.Enabled = false
        
        local container = Instance.new("Frame", bgui)
        container.Name = "Container"; container.Size = UDim2.new(1,0,1,0); container.BackgroundTransparency = 1
        
        local head = Instance.new("Frame", container)
        head.Name = "HeadPart"
        head.Size = UDim2.new(0.25, 0, 0.2, 0); head.Position = UDim2.new(0.5, 0, 0.1, 0)
        head.AnchorPoint = Vector2.new(0.5, 0.5); head.BackgroundTransparency = 1
        Instance.new("UIStroke", head).Thickness = 2; Instance.new("UICorner", head).CornerRadius = UDim.new(1,0)
        
        local spine = CreateStick(container, "Spine")
        spine.Size = UDim2.new(0.03, 0, 0.4, 0); spine.Position = UDim2.new(0.5, 0, 0.4, 0)
        
        local armL = CreateStick(container, "ArmL")
        armL.Size = UDim2.new(0.25, 0, 0.03, 0); armL.Position = UDim2.new(0.35, 0, 0.3, 0)
        
        local armR = CreateStick(container, "ArmR")
        armR.Size = UDim2.new(0.25, 0, 0.03, 0); armR.Position = UDim2.new(0.65, 0, 0.3, 0)
        
        local legL = CreateStick(container, "LegL")
        legL.Size = UDim2.new(0.03, 0, 0.35, 0); legL.Position = UDim2.new(0.43, 0, 0.75, 0); legL.Rotation = 15
        
        local legR = CreateStick(container, "LegR")
        legR.Size = UDim2.new(0.03, 0, 0.35, 0); legR.Position = UDim2.new(0.57, 0, 0.75, 0); legR.Rotation = -15
    end
    if plr.Character then setup(plr.Character) end
    plr.CharacterAdded:Connect(setup)
end

for _, p in pairs(Players:GetPlayers()) do ApplyESP(p) end
Players.PlayerAdded:Connect(ApplyESP)

-- // FUNÇÃO DE BUSCA COM FILTRO DE ALTURA
local function GetClosestTarget()
    local target = nil
    local minDist = Settings.FOV
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return nil end
    local myPos = LocalPlayer.Character.HumanoidRootPart.Position

    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            local part = p.Character:FindFirstChild(Settings.AimPart) or p.Character:FindFirstChild("HumanoidRootPart")
            local hum = p.Character:FindFirstChild("Humanoid")

            if part and hum and hum.Health > 0 then
                -- FILTRO: Só mira se a altura (Eixo Y) for parecida com a sua
                local heightDiff = math.abs(myPos.Y - part.Position.Y)
                
                -- FILTRO: Garante que o alvo está na frente da câmera (evita puxar pra trás)
                local directionToTarget = (part.Position - Camera.CFrame.Position).Unit
                local dot = directionToTarget:Dot(Camera.CFrame.LookVector)

                if heightDiff <= Settings.MaxHeightDiff and dot > 0.4 then
                    local pos, onScreen = Camera:WorldToScreenPoint(part.Position)
                    if onScreen then
                        local mag = (Vector2.new(pos.X, pos.Y) - UserInputService:GetMouseLocation()).Magnitude
                        if mag < minDist then
                            target = part
                            minDist = mag
                        end
                    end
                end
            end
        end
    end
    return target
end

-- // LOOP PRINCIPAL (RENDER STEPPED)
RunService.RenderStepped:Connect(function()
    local color = Color3.fromHSV(tick() % 5 / 5, 1, 1)
    UIStroke.Color = color
    Title.TextColor3 = color
    Credits.TextColor3 = color
    if UserInputService.TouchEnabled then MobileAimBtn.UIStroke.Color = color end
    
    -- FOV Update
    if Settings.Enabled then
        FOVCircle.Visible = true; FOVCircle.Radius = Settings.FOV
        FOVCircle.Position = UserInputService:GetMouseLocation(); FOVCircle.Color = color
    else 
        FOVCircle.Visible = false 
    end
    
    -- ESP Stickman Update Visuals
    for _, p in pairs(Players:GetPlayers()) do
        if p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local esp = p.Character.HumanoidRootPart:FindFirstChild("StickmanESP")
            if esp then
                esp.Enabled = Settings.ESP_Enabled
                local s = p.Character:GetExtentsSize()
                esp.Size = UDim2.new(s.X, 0, s.Y, 0)
                -- Pinta todas as partes do stickman com a cor RGB do menu
                for _, v in pairs(esp:GetDescendants()) do
                    if v:IsA("Frame") and v.Name ~= "Container" then v.BackgroundColor3 = color end
                    if v:IsA("UIStroke") then v.Color = color end
                end
            end
        end
    end

    -- Lógica do Aimbot (Grudar no alvo)
    if Settings.Enabled and Holding then
        local targetPart = GetClosestTarget()
        if targetPart then
            Camera.CFrame = Camera.CFrame:Lerp(CFrame.lookAt(Camera.CFrame.Position, targetPart.Position), Settings.Sensitivity)
        end
    end
end)

PowerBtn.MouseButton1Click:Connect(function()
    Settings.Enabled = not Settings.Enabled
    PowerBtn.Text = Settings.Enabled and "AIMBOT: ON" or "AIMBOT: OFF"
    PowerBtn.TextColor3 = Settings.Enabled and Color3.fromRGB(50, 255, 100) or Color3.fromRGB(255, 50, 50)
end)

EspBtn.MouseButton1Click:Connect(function()
    Settings.ESP_Enabled = not Settings.ESP_Enabled
    EspBtn.Text = Settings.ESP_Enabled and "ESP STICKMAN: ON" or "ESP STICKMAN: OFF"
    EspBtn.TextColor3 = Settings.ESP_Enabled and Color3.fromRGB(50, 255, 100) or Color3.fromRGB(255, 50, 50)
end)

-- Controles de clique
UserInputService.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton2 then Holding = true end end)
UserInputService.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton2 then Holding = false end end)
