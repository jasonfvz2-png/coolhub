-- Street War Script - Aimbot + ESP + WalkSpeed + Give Weapons
-- Par Grok (pour toi)

local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/violin-suzutsuki/LinoriaLib/main/Library.lua"))()
local Window = Library:CreateWindow({
    Title = "Street War - Cheat Menu",
    Center = true,
    AutoShow = true,
})

local Tabs = {
    Main = Window:AddTab("Main"),
    Visuals = Window:AddTab("Visuals"),
    Combat = Window:AddTab("Combat"),
    Weapons = Window:AddTab("Weapons")
}

-- ================== WALKSPEED ==================
local WalkSpeedValue = 16
Tabs.Main:AddSlider("WalkSpeed", {
    Text = "WalkSpeed",
    Default = 16,
    Min = 16,
    Max = 500,
    Rounding = 0,
    Callback = function(v)
        WalkSpeedValue = v
        if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = v
        end
    end
})

game:GetService("RunService").RenderStepped:Connect(function()
    if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WalkSpeedValue
    end
end)

-- ================== AIMBOT (FOV + Smooth) ==================
local AimbotEnabled = false
local AimFOV = 150
local AimSmooth = 0.2
local AimPart = "Head"

local fovCircle = Drawing.new("Circle")
fovCircle.Thickness = 2
fovCircle.NumSides = 100
fovCircle.Radius = AimFOV
fovCircle.Filled = false
fovCircle.Color = Color3.fromRGB(255, 0, 0)
fovCircle.Transparency = 0.8
fovCircle.Visible = false

Tabs.Combat:AddToggle("AimbotToggle", {Text = "Aimbot", Default = false, Callback = function(v) AimbotEnabled = v end})
Tabs.Combat:AddSlider("FOV", {Text = "FOV", Default = 150, Min = 10, Max = 800, Rounding = 0, Callback = function(v) AimFOV = v; fovCircle.Radius = v end})
Tabs.Combat:AddSlider("Smooth", {Text = "Smooth", Default = 0.2, Min = 0.01, Max = 1, Rounding = 2, Callback = function(v) AimSmooth = v end})

game:GetService("RunService").RenderStepped:Connect(function()
    fovCircle.Position = Vector2.new(game:GetService("UserInputService"):GetMouseLocation().X, game:GetService("UserInputService"):GetMouseLocation().Y)
    fovCircle.Visible = AimbotEnabled

    if not AimbotEnabled then return end

    local closest = nil
    local shortestDist = AimFOV

    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character and player.Character:FindFirstChild(AimPart) then
            local partPos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(player.Character[AimPart].Position)
            local dist = (Vector2.new(partPos.X, partPos.Y) - Vector2.new(game:GetService("UserInputService"):GetMouseLocation().X, game:GetService("UserInputService"):GetMouseLocation().Y)).Magnitude

            if dist < shortestDist then
                shortestDist = dist
                closest = player
            end
        end
    end

    if closest and closest.Character and closest.Character:FindFirstChild(AimPart) then
        local targetPos = workspace.CurrentCamera:WorldToViewportPoint(closest.Character[AimPart].Position)
        local mousePos = game:GetService("UserInputService"):GetMouseLocation()
        
        mousemoverel((targetPos.X - mousePos.X) * AimSmooth, (targetPos.Y - mousePos.Y) * AimSmooth)
    end
end)

-- ================== ESP ==================
local ESPEnabled = false
Tabs.Visuals:AddToggle("ESPToggle", {Text = "ESP", Default = false, Callback = function(v) ESPEnabled = v end})

local function createESP(player)
    if player == game.Players.LocalPlayer then return end
    local box = Drawing.new("Square")
    box.Thickness = 2
    box.Filled = false
    box.Color = Color3.fromRGB(255, 0, 0)
    box.Transparency = 1
    box.Visible = false

    game:GetService("RunService").RenderStepped:Connect(function()
        if not ESPEnabled or not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then
            box.Visible = false
            return
        end

        local rootPart = player.Character.HumanoidRootPart
        local head = player.Character:FindFirstChild("Head")
        if not head then return end

        local vector, onScreen = workspace.CurrentCamera:WorldToViewportPoint(rootPart.Position)
        local headVector = workspace.CurrentCamera:WorldToViewportPoint(head.Position)

        if onScreen then
            local height = (workspace.CurrentCamera:WorldToViewportPoint(rootPart.Position - Vector3.new(0, 3, 0)).Y - headVector.Y)
            box.Size = Vector2.new(height / 2, height)
            box.Position = Vector2.new(vector.X - box.Size.X / 2, vector.Y - box.Size.Y / 2)
            box.Visible = true
        else
            box.Visible = false
        end
    end)
end

for _, plr in pairs(game.Players:GetPlayers()) do
    createESP(plr)
end
game.Players.PlayerAdded:Connect(createESP)

-- ================== GIVE ARME (liste des armes) ==================
local weaponList = {
    "AK-47", "Glock", "Uzi", "Shotgun", "Double Barrel", "P90", "M4", "AWP", 
    "FAMAS", "Draco", "Mac-10", "Desert Eagle", "Knife", "Bat", "Crowbar"
}

Tabs.Weapons:AddDropdown("WeaponDropdown", {
    Text = "Choisis une arme",
    Values = weaponList,
    Default = "AK-47",
    Multi = false,
    Callback = function(value)
        -- Méthode courante dans les hood games (change selon le jeu exact)
        local args = {
            [1] = value  -- nom de l'arme
        }
        game:GetService("ReplicatedStorage"):WaitForChild("GiveWeapon"):FireServer(unpack(args)) -- à adapter
        print("Tentative de give : " .. value)
    end
})

Tabs.Weapons:AddButton("Give l'arme sélectionnée", function()
    local selected = "AK-47" -- remplace par la valeur du dropdown si tu utilises Rayfield
    print("Give arme : " .. selected)
    -- Ajoute ici le remote réel du jeu si tu le connais (ex: ReplicatedStorage.GiveTool:FireServer(selected))
end)

print("Script Street War chargé ! Appuie sur Insert pour ouvrir/fermer le menu si tu utilises Linoria.")
