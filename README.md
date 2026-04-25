-- =============================================
--   Cool Hub - Rivals Edition
--   Version propre & humaine (2026)
--   Infinite Keys + Unlock All Weapons & Cosmetics
-- =============================================

print("✅ Cool Hub - Rivals chargé avec succès !")

-- Création du GUI (petit et optimisé mobile)
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CoolHubRivals"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = playerGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 240, 0, 320)
MainFrame.Position = UDim2.new(0.05, 0, 0.25, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true  -- Tu peux déplacer le menu
MainFrame.Parent = ScreenGui

-- Titre
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundColor3 = Color3.fromRGB(255, 40, 100)
Title.Text = "Cool Hub - Rivals"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame

-- Fonction pour créer des boutons facilement
local function CreateButton(text, positionY, callback)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(0.9, 0, 0, 38)
    Button.Position = UDim2.new(0.05, 0, 0, positionY)
    Button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    Button.Text = text
    Button.TextColor3 = Color3.new(1, 1, 1)
    Button.TextScaled = true
    Button.Font = Enum.Font.GothamSemibold
    Button.Parent = MainFrame
    
    Button.MouseButton1Click:Connect(function()
        Button.BackgroundColor3 = Color3.fromRGB(70, 70, 70)  -- Effet visuel
        task.wait(0.1)
        Button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
        callback()
    end)
end

-- ================== INFINITE KEYS ==================
CreateButton("🔑 Donner 100 000 Keys (x10)", 50, function()
    for i = 1, 10 do
        pcall(function()
            game:GetService("ReplicatedStorage").Remotes.GiveKeys:FireServer(100000)
        end)
    end
    print("💰 100 000 Keys envoyées x10 fois")
end)

CreateButton("🔁 Activer Infinite Keys (Loop)", 95, function()
    getgenv().InfiniteKeysLoop = true
    
    spawn(function()
        while getgenv().InfiniteKeysLoop do
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.GiveKeys:FireServer(60000)
            end)
            task.wait(0.35)
        end
    end)
    
    print("♾️ Infinite Keys Loop activé")
end)

CreateButton("⛔ Arrêter Infinite Keys", 140, function()
    getgenv().InfiniteKeysLoop = false
    print("⛔ Infinite Keys arrêté")
end)

-- ================== UNLOCK ALL ==================
CreateButton("🔫 Débloquer TOUTES les Armes", 185, function()
    pcall(function()
        local weaponsFolder = game:GetService("ReplicatedStorage"):FindFirstChild("Weapons") 
                          or game:GetService("ReplicatedStorage"):FindFirstChild("WeaponData")
        
        if weaponsFolder then
            for _, item in pairs(weaponsFolder:GetDescendants()) do
                if item:IsA("StringValue") or item:IsA("Folder") then
                    pcall(function()
                        game:GetService("ReplicatedStorage").Remotes.UnlockWeapon:FireServer(item.Name)
                    end)
                end
            end
        end
    end)
    print("🔫 Tentative de déblocage de toutes les armes")
end)

CreateButton("🌟 Débloquer TOUS les Cosmetics", 230, function()
    pcall(function()
        game:GetService("ReplicatedStorage").Remotes.UnlockAllCosmetics:FireServer()
        game:GetService("ReplicatedStorage").Events.UnlockSkin:FireServer("all")
    end)
    print("🌟 Tentative de déblocage de tous les cosmétiques")
end)

-- Bouton pour cacher le menu (très utile sur mobile)
CreateButton("🔄 Cacher / Afficher le Menu", 275, function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- Message de bienvenue
print("🎉 Cool Hub prêt ! Déplace le menu en le glissant avec ton doigt.")
