local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "Westbound Script Completo",
    LoadingTitle = "Westbound Script",
    LoadingSubtitle = "Carregando...",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "WestboundScriptAI",
        FileName = "Config"
    },
    Discord = {
        Enabled = false,
    },
    Themeable = true
})

-- Criando temas adicionais
Window:CreateTheme({
    Name = "Dark Mode",
    Color = Color3.fromRGB(25, 25, 25),
    SecondaryColor = Color3.fromRGB(40, 40, 40),
    Accent = Color3.fromRGB(255, 85, 85),
    TextColor = Color3.fromRGB(210, 210, 210),
    DropdownBackground = Color3.fromRGB(30, 30, 30)
})

Window:CreateTheme({
    Name = "Light Mode",
    Color = Color3.fromRGB(245, 245, 245),
    SecondaryColor = Color3.fromRGB(230, 230, 230),
    Accent = Color3.fromRGB(0, 140, 255),
    TextColor = Color3.fromRGB(30, 30, 30),
    DropdownBackground = Color3.fromRGB(255, 255, 255)
})

Window:CreateTheme({
    Name = "Red Mode",
    Color = Color3.fromRGB(70, 25, 25),
    SecondaryColor = Color3.fromRGB(100, 40, 40),
    Accent = Color3.fromRGB(255, 40, 40),
    TextColor = Color3.fromRGB(230, 180, 180),
    DropdownBackground = Color3.fromRGB(90, 30, 30)
})


local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Variáveis de estado
local aimbotEnabled = false
local aimbotFOV = 150
local aimbotSmoothness = 8
local aimbotTargetPart = "Head"
local espEnabled = false
local rapidFireEnabled = false

-- Criar círculo do FOV para o aimbot
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 1
FOVCircle.NumSides = 64
FOVCircle.Color = Color3.new(1, 1, 1)
FOVCircle.Filled = false
FOVCircle.Radius = aimbotFOV
FOVCircle.Transparency = 1
FOVCircle.Visible = false

-- Função para suavidade
local function lerp(a, b, t)
    return a + (b - a) * t
end

-- Criando abas e seus elementos

-- ABA AIMBOT
local AimbotTab = Window:CreateTab("Aimbot")
local AimbotSection = AimbotTab:CreateSection("Configurações do Aimbot")

AimbotTab:CreateToggle({
    Name = "Ativar Aimbot",
    CurrentValue = false,
    Flag = "aimbotToggle",
    Callback = function(value)
        aimbotEnabled = value
        FOVCircle.Visible = value
    end,
})

AimbotTab:CreateSlider({
    Name = "FOV do Aimbot",
    Range = {10, 300},
    Increment = 1,
    Suffix = "°",
    CurrentValue = aimbotFOV,
    Flag = "aimbotFOV",
    Callback = function(value)
        aimbotFOV = value
        FOVCircle.Radius = value
    end,
})

AimbotTab:CreateSlider({
    Name = "Suavidade",
    Range = {1, 20},
    Increment = 1,
    CurrentValue = aimbotSmoothness,
    Flag = "aimbotSmoothness",
    Callback = function(value)
        aimbotSmoothness = value
    end,
})

AimbotTab:CreateDropdown({
    Name = "Parte do corpo do alvo",
    Options = {"Head", "HumanoidRootPart"},
    CurrentOption = aimbotTargetPart,
    Flag = "aimbotTargetPart",
    Callback = function(value)
        aimbotTargetPart = value
    end,
})

-- Função para encontrar o jogador mais próximo dentro do FOV
local function getClosestPlayer()
    local mousePosition = UserInputService:GetMouseLocation()
    local closestPlayer = nil
    local shortestDistance = aimbotFOV

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(aimbotTargetPart) then
            local rootPos, onScreen = Camera:WorldToViewportPoint(player.Character[aimbotTargetPart].Position)
            if onScreen then
                local dist = (Vector2.new(rootPos.X, rootPos.Y) - Vector2.new(mousePosition.X, mousePosition.Y)).Magnitude
                if dist < shortestDistance then
                    shortestDistance = dist
                    closestPlayer = player
                end
            end
        end
    end
    return closestPlayer
end

-- Atualizar o aimbot a cada frame
RunService.RenderStepped:Connect(function()
    if aimbotEnabled then
        local target = getClosestPlayer()
        if target and target.Character then
            local targetPart = target.Character:FindFirstChild(aimbotTargetPart)
            if targetPart then
                local cameraCF = Camera.CFrame
                local desiredCF = CFrame.new(cameraCF.Position, targetPart.Position)
                local smoothFactor = aimbotSmoothness / 10
                Camera.CFrame = cameraCF:lerp(desiredCF, smoothFactor)
            end
        end
    end
end)

-- Atualizar o círculo do FOV
RunService.RenderStepped:Connect(function()
    if aimbotEnabled then
        local mousePos = UserInputService:GetMouseLocation()
        FOVCircle.Position = Vector2.new(mousePos.X, mousePos.Y)
        FOVCircle.Radius = aimbotFOV
        FOVCircle.Visible = true
    else
        FOVCircle.Visible = false
    end
end)

-- ABA ESP
local ESPTab = Window:CreateTab("ESP")
local ESPSection = ESPTab:CreateSection("Configurações do ESP")

espEnabled = false

local espToggle = ESPTab:CreateToggle({
    Name = "Ativar ESP",
    CurrentValue = false,
    Flag = "espToggle",
    Callback = function(value)
        espEnabled = value
    end,
})

local teamColors = {
    ["Sheriffs"] = Color3.fromRGB(0, 140, 255), -- Azul
    ["Outlaws"] = Color3.fromRGB(255, 50, 50),  -- Vermelho
    ["Bounty Hunters"] = Color3.fromRGB(0, 255, 0), -- Verde
}

local highlights = {}

local function createHighlight(player)
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and not highlights[player] then
        local highlight = Instance.new("Highlight")
        highlight.Name = "westboundHighlight"
        highlight.Adornee = player.Character
        highlight.Parent = player.Character
        highlight.FillColor = teamColors[player.Team.Name] or Color3.new(1,1,1)
        highlight.OutlineColor = teamColors[player.Team.Name] or Color3.new(1,1,1)
        highlights[player] = highlight
    end
end

local function removeHighlight(player)
    if highlights[player] then
        highlights[player]:Destroy()
        highlights[player] = nil
    end
end

RunService.RenderStepped:Connect(function()
    if espEnabled then
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                createHighlight(player)
            end
        end
    else
        for player, _ in pairs(highlights) do
            removeHighlight(player)
        end
    end
end)

-- ABA GUN MOD
local GunModTab = Window:CreateTab("Gun Mod")
local GunModSection = GunModTab:CreateSection("Configurações do Gun Mod")

rapidFireEnabled = false

GunModTab:CreateToggle({
    Name = "Tiro Rápido",
    CurrentValue = false,
    Flag = "rapidFireToggle",
    Callback = function(value)
        rapidFireEnabled = value
    end,
})

-- Função para pegar a ferramenta atual (arma)
local function getCurrentTool()
    if LocalPlayer.Character then
        for _, tool in pairs(LocalPlayer.Character:GetChildren()) do
            if tool:IsA("Tool") then
                return tool
            end
        end
    end
    return nil
end

-- Loop para ativar tiro rápido
RunService.Heartbeat:Connect(function()
    if rapidFireEnabled then
        local tool = getCurrentTool()
        if tool and tool:IsA("Tool") and tool:FindFirstChild("Handle") then
            tool:Activate()
        end
    end
end)

-- ABA TELEPORT
local TeleportTab = Window:CreateTab("Teleporte")
local TeleportSection = TeleportTab:CreateSection("Teleporte para locais reais")

local locations = {
    ["Central Town"] = Vector3.new(-746, 18, 1516),
    ["Gold Scale"] = Vector3.new(-1380, 19, 1174),
    ["Saloon"] = Vector3.new(-985, 18, 1520),
    ["Mine"] = Vector3.new(-1113, 18, 1914),
    ["Sheriff's Office"] = Vector3.new(-1180, 18, 1345),
    ["Train Station"] = Vector3.new(-769, 18, 1325),
    ["Farm"] = Vector3.new(-1500, 18, 900),
    ["Church"] = Vector3.new(-1200, 18, 1600),
    ["Outlaw Camp"] = Vector3.new(-1700, 18, 1200),
    ["Jail"] = Vector3.new(-1300, 18, 1400),
}

for name, pos in pairs(locations) do
    TeleportTab:CreateButton({
        Name = "Teleporte para " .. name,
        Callback = function()
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(pos)
            end
        end,
    })
end

-- Mensagem para afirmar script carregado
print("Script Westbound completo carregado com sucesso. Abas e funcionalidades ativas.")

