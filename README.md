-- Street War Script - Version Mobile Optimisée (Rayfield UI)
-- Aimbot + FOV + Smooth + ESP + WalkSpeed + Give Weapons

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "Street War | Mobile",
    LoadingTitle = "Chargement du menu...",
    LoadingSubtitle = "Version Mobile Optimisée",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "StreetWarMobile",
        FileName = "Config"
    },
    Discord = {
        Enabled = false,
    },
    KeySystem = false
})

local Tabs = {
    Main = Window:CreateTab("Main", 4483362458),
    Combat = Window:CreateTab("Combat", 4483362458),
    Visuals = Window:CreateTab("Visuals", 4483362458),
    Weapons = Window:CreateTab("Weapons", 4483362458)
}

-- ================== WALKSPEED ==================
local WalkSpeedValue = 16

Tabs.Main:CreateSlider({
    Name = "WalkSpeed",
    Range = {16, 500},
    Increment = 1,
    CurrentValue = 16,
    Flag = "WalkSpeedFlag",
    Callback = function(Value)
        WalkSpeedValue = Value
        if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
        end
    end,
})

game:GetService("RunService").RenderStepped:Connect(function()
    pcall(function()
        if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WalkSpeedValue
        end
    end)
end)

-- ================== AIMBOT (FOV + Smooth) ==================
local AimbotEnabled = false
local AimFOV = 150
local AimSmooth = 0.25
local AimPart = "Head"

local fovCircle = Drawing.new("Circle")
fovCircle.Thickness = 2
fovCircle.NumSides = 80
fovCircle.Radius = AimFOV
fovCircle.Filled = false
fovCircle.Color = Color3.fromRGB(255, 50, 50)
fovCircle.Transparency = 0.75
fovCircle.Visible = false

Tabs.Combat:CreateToggle({
    Name = "Aimbot",
    CurrentValue = false,
    Flag = "AimbotFlag",
    Callback = function(Value)
        AimbotEnabled = Value
    end,
})

Tabs.Combat:CreateSlider({
    Name = "FOV",
    Range = {30, 800},
    Increment = 5,
    CurrentValue = 150,
    Flag = "FOVFlag",
    Callback = function(Value)
        AimFOV = Value
        fovCircle.Radius = Value
    end,
})

Tabs.Combat:CreateSlider({
    Name = "Smooth",
    Range = {0.01, 1},
    Increment = 0.01,
    CurrentValue = 0.25,
    Flag = "SmoothFlag",
    Callback = function(Value)
        AimSmooth = Value
    end,
})

Tabs.Combat:CreateDropdown({
    Name = "Aim Part",
    Options = {"Head", "UpperTorso", "HumanoidRootPart"},
    CurrentOption = {"Head"},
    MultipleOptions = false,
    Flag = "AimPartFlag",
    Callback = function(CurrentOption)
        AimPart = CurrentOption[1]
    end,
})

game:GetService("RunService").RenderStepped:Connect(function()
    fovCircle.Position = Vector2.new(game:GetService("UserInputService"):GetMouseLocation().X, game:GetService("UserInputService"):GetMouseLocation().Y)
    fovCircle.Visible = AimbotEnabled

    if not AimbotEnabled then return end

    local closest = nil
    local shortestDist = AimFOV

    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character and player.Character:FindFirstChild(AimPart) then
            local partPos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(player.Character[AimPart].Position)
            local mousePos = game:GetService("UserInputService"):GetMouseLocation()
            local dist = (Vector2.new(partPos.X, partPos.Y) - Vector2.new(mousePos.X, mousePos.Y)).Magnitude

            if onScreen and dist < shortestDist then
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

local function createESP(plr)
    if plr == game.Players.LocalPlayer then return end
    
    local box = Drawing.new("Square")
    box.Thickness = 2
    box.Filled = false
    box.Color = Color3.fromRGB(255, 0, 100)
    box.Transparency = 1
    box.Visible = false

    game:GetService("RunService").RenderStepped:Connect(function()
        if not ESPEnabled or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") then
            box.Visible = false
            return
        end

        local root = plr.Character.HumanoidRootPart
        local head = plr.Character:FindFirstChild("Head")
        if not head then return end

        local vector, onScreen = workspace.CurrentCamera:WorldToViewportPoint(root.Position)
        local headPos = workspace.CurrentCamera:WorldToViewportPoint(head.Position)

        if onScreen then
            local height = (workspace.CurrentCamera:WorldToViewportPoint(root.Position - Vector3.new(0, 3, 0)).Y - headPos.Y)
            box.Size = Vector2.new(height / 1.8, height)
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

Tabs.Visuals:CreateToggle({
    Name = "ESP Boxes",
    CurrentValue = false,
    Flag = "ESPFlag",
    Callback = function(Value)
        ESPEnabled = Value
    end,
})

-- ================== GIVE ARME ==================
local weaponList = {
    "AK-47", "Glock", "Uzi", "Shotgun", "Double Barrel", "P90", 
    "M4A1", "AWP", "FAMAS", "Draco", "Mac-10", "Desert Eagle", 
    "Knife", "Bat", "Crowbar", "Rifle"
}

Tabs.Weapons:CreateDropdown({
    Name = "Sélectionne une arme",
    Options = weaponList,
    CurrentOption = {"AK-47"},
    MultipleOptions = false,
    Flag = "WeaponFlag",
    Callback = function(CurrentOption)
        local selectedWeapon = CurrentOption[1]
        print("Tentative de give : " .. selectedWeapon)
        
        -- Change ce remote selon la version exacte de Street War / Streetz War 2
        -- Exemples courants :
        -- game:GetService("ReplicatedStorage").Remotes.GiveWeapon:FireServer(selectedWeapon)
        -- ou game.ReplicatedStorage:WaitForChild("BuyGun"):FireServer(selectedWeapon)
        
        Rayfield:Notify({
            Title = "Give Arme",
            Content = "Tentative pour : " .. selectedWeapon,
            Duration = 3,
            Image = 4483362458
        })
    end,
})

Tabs.Weapons:CreateButton({
    Name = "Give l'arme sélectionnée",
    Callback = function()
        Rayfield:Notify({
            Title = "Info",
            Content = "Vérifie le dropdown et appuie dessus pour donner l'arme",
            Duration = 4
        })
    end,
})

-- Bouton pour fermer/ouvrir facilement sur mobile
Rayfield:Notify({
    Title = "Script chargé !",
    Content = "Menu mobile optimisé - Touche l'écran pour interagir facilement",
    Duration = 5,
})

print("✅ Street War Mobile Script chargé avec succès !")
