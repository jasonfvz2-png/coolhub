-- Street War | Mobile Ultra Optimized GUI (2026)
-- Meilleure adaptation tactile + moins de lag

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "Street War - Mobile",
    LoadingTitle = "Chargement...",
    LoadingSubtitle = "Version Ultra Mobile",
    ConfigurationSaving = {Enabled = true, FolderName = "SWMobile", FileName = "Config"},
    KeySystem = false
})

-- Un seul tab principal pour plus de simplicité sur mobile
local Tab = Window:CreateTab("Main", 4483362458)

-- ================== WALKSPEED ==================
local wsValue = 16

Tab:CreateSlider({
    Name = "WalkSpeed",
    Range = {16, 500},
    Increment = 2,
    CurrentValue = 16,
    Flag = "WS",
    Callback = function(v)
        wsValue = v
        pcall(function()
            if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
                game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = v
            end
        end)
    end
})

game:GetService("RunService").RenderStepped:Connect(function()
    pcall(function()
        local hum = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid")
        if hum then hum.WalkSpeed = wsValue end
    end)
end)

-- ================== AIMBOT (FOV + Smooth) ==================
local aimbot = false
local fov = 180
local smooth = 0.25
local aimPart = "Head"

local circle = Drawing.new("Circle")
circle.Thickness = 2.5
circle.NumSides = 90
circle.Radius = fov
circle.Filled = false
circle.Color = Color3.fromRGB(255, 60, 60)
circle.Transparency = 0.7
circle.Visible = false

Tab:CreateToggle({
    Name = "✅ Aimbot",
    CurrentValue = false,
    Flag = "Aimbot",
    Callback = function(v) aimbot = v end
})

Tab:CreateSlider({
    Name = "FOV",
    Range = {40, 700},
    Increment = 10,
    CurrentValue = 180,
    Flag = "FOV",
    Callback = function(v)
        fov = v
        circle.Radius = v
    end
})

Tab:CreateSlider({
    Name = "Smooth (plus bas = plus rapide)",
    Range = {0.01, 0.8},
    Increment = 0.01,
    CurrentValue = 0.25,
    Flag = "Smooth",
    Callback = function(v) smooth = v end
})

Tab:CreateDropdown({
    Name = "Part à viser",
    Options = {"Head", "UpperTorso", "HumanoidRootPart"},
    CurrentOption = {"Head"},
    MultipleOptions = false,
    Flag = "AimPart",
    Callback = function(opt) aimPart = opt[1] end
})

game:GetService("RunService").RenderStepped:Connect(function()
    circle.Position = Vector2.new(game:GetService("UserInputService"):GetMouseLocation().X, game:GetService("UserInputService"):GetMouseLocation().Y)
    circle.Visible = aimbot

    if not aimbot then return end

    local closest, dist = nil, fov
    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr ~= game.Players.LocalPlayer and plr.Character and plr.Character:FindFirstChild(aimPart) then
            local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(plr.Character[aimPart].Position)
            local mouse = game:GetService("UserInputService"):GetMouseLocation()
            local d = (Vector2.new(pos.X, pos.Y) - Vector2.new(mouse.X, mouse.Y)).Magnitude

            if onScreen and d < dist then
                dist = d
                closest = plr
            end
        end
    end

    if closest and closest.Character and closest.Character:FindFirstChild(aimPart) then
        local target = workspace.CurrentCamera:WorldToViewportPoint(closest.Character[aimPart].Position)
        local mouse = game:GetService("UserInputService"):GetMouseLocation()
        mousemoverel((target.X - mouse.X) * smooth, (target.Y - mouse.Y) * smooth)
    end
end)

-- ================== ESP ==================
local espOn = false

local function add
