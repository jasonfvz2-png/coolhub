-- Street War - Cool Hub (Version Mobile Super Petite)

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "Cool Hub",           -- ← Renommé ici
    LoadingTitle = "Cool Hub",
    LoadingSubtitle = "Street War Mobile",
    ConfigurationSaving = {Enabled = true, FolderName = "CoolHub", FileName = "Config"},
    KeySystem = false
})

local Tab = Window:CreateTab("Main", 4483362458)

-- WALKSPEED
local ws = 16
Tab:CreateSlider({
    Name = "WS",
    Range = {16, 400},
    Increment = 5,
    CurrentValue = 16,
    Flag = "WS",
    Callback = function(v)
        ws = v
        pcall(function()
            local hum = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid")
            if hum then hum.WalkSpeed = v end
        end)
    end
})

game:GetService("RunService").RenderStepped:Connect(function()
    pcall(function()
        local hum = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid")
        if hum then hum.WalkSpeed = ws end
    end)
end)

-- AIMBOT
local aimbotOn = false
local fovVal = 160
local smoothVal = 0.28
local aimPart = "Head"

local fovCircle = Drawing.new("Circle")
fovCircle.Thickness = 2
fovCircle.NumSides = 80
fovCircle.Radius = fovVal
fovCircle.Filled = false
fovCircle.Color = Color3.fromRGB(255, 80, 80)
fovCircle.Transparency = 0.75
fovCircle.Visible = false

Tab:CreateToggle({
    Name = "Aimbot",
    CurrentValue = false,
    Flag = "Aim",
    Callback = function(v) aimbotOn = v end
})

Tab:CreateSlider({
    Name = "FOV",
    Range = {50, 600},
    Increment = 10,
    CurrentValue = 160,
    Flag = "FOV",
    Callback = function(v)
        fovVal = v
        fovCircle.Radius = v
    end
})

Tab:CreateSlider({
    Name = "Smooth",
    Range = {0.05, 0.6},
    Increment = 0.01,
    CurrentValue = 0.28,
    Flag = "Smooth",
    Callback = function(v) smoothVal = v end
})

Tab:CreateDropdown({
    Name = "Part",
    Options = {"Head", "UpperTorso", "HumanoidRootPart"},
    CurrentOption = {"Head"},
    MultipleOptions = false,
    Flag = "Part",
    Callback = function(opt) aimPart = opt[1] end
})

-- ESP
local espOn = false

local function createESP(plr)
    if plr == game.Players.LocalPlayer then return end
    local box = Drawing.new("Square")
    box.Thickness = 1.5
    box.Filled = false
    box.Color = Color3.fromRGB(255, 0, 150)
    box.Transparency = 1
    box.Visible = false

    game:GetService("RunService").RenderStepped:Connect(function()
        if not espOn or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") then
            box.Visible = false
            return
        end
        local root = plr.Character.HumanoidRootPart
        local head = plr.Character:FindFirstChild("Head")
        if not head then return end

        local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(root.Position)
        local hPos = workspace.CurrentCamera:WorldToViewportPoint(head.Position)

        if onScreen then
            local height = (workspace.CurrentCamera:WorldToViewportPoint(root.Position - Vector3.new(0,3,0)).Y - hPos.Y)
            box.Size = Vector2.new(height / 2, height)
            box.Position = Vector2.new(pos.X - box.Size.X/2, pos.Y - box.Size.Y/2)
            box.Visible = true
        else
            box.Visible = false
        end
    end)
end

for _, p in pairs(game.Players:GetPlayers()) do createESP(p) end
game.Players.PlayerAdded:Connect(createESP)

Tab:CreateToggle({
    Name = "ESP",
    CurrentValue = false,
    Flag = "ESP",
    Callback = function(v) espOn = v end
})

-- AIMBOT LOOP
game:GetService("RunService").RenderStepped:Connect(function()
    fovCircle.Position = Vector2.new(game:GetService("UserInputService"):GetMouseLocation().X, game:GetService("UserInputService"):GetMouseLocation().Y)
    fovCircle.Visible = aimbotOn

    if not aimbotOn then return end

    local closest, shortest = nil, fovVal
    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr ~= game.Players.LocalPlayer and plr.Character and plr.Character:FindFirstChild(aimPart) then
            local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(plr.Character[aimPart].Position)
            local mouse = game:GetService("UserInputService"):GetMouseLocation()
            local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(mouse.X, mouse.Y)).Magnitude

            if onScreen and dist < shortest then
                shortest = dist
                closest = plr
            end
        end
    end

    if closest and closest.Character and closest.Character:FindFirstChild(aimPart) then
        local target = workspace.CurrentCamera:WorldToViewportPoint(closest.Character[aimPart].Position)
        local mouse = game:GetService("UserInputService"):GetMouseLocation()
        mousemoverel((target.X - mouse.X) * smoothVal, (target.Y - mouse.Y) * smoothVal)
    end
end)

-- GIVE ARME
local weapons = {"AK-47", "Glock", "Uzi", "Shotgun", "DB", "P90", "M4", "AWP", "Draco", "Deagle", "Knife"}

Tab:CreateDropdown({
    Name = "Arme",
    Options = weapons,
    CurrentOption = {"AK-47"},
    MultipleOptions = false,
    Flag = "Weapon",
    Callback = function(opt)
        local w = opt[1]
        Rayfield:Notify({Title = "Give", Content = w, Duration = 2})
    end
})

-- Boutons rapides
Tab:CreateButton({
    Name = "🔄 Cacher Menu",
    Callback = function()
        Rayfield:Toggle()
    end
})

Tab:CreateButton({
    Name = "Give Arme",
    Callback = function()
        Rayfield:Notify({Title = "Info", Content = "Choisis dans le dropdown", Duration = 3})
    end
})

Rayfield:Notify({
    Title = "✅ Cool Hub chargé",
    Content = "Version super petite pour mobile",
    Duration = 4
})

print("Cool Hub - Street War Mini Mobile chargé")
