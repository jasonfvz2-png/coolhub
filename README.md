-- Cool Hub - Rivals (Version Rework - Sans Rayfield - Mobile Friendly)

print("✅ Cool Hub Rivals Rework chargé")

-- Création d'un menu simple et petit
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CoolHubRivals"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 220, 0, 280)
Frame.Position = UDim2.new(0.05, 0, 0.3, 0)
Frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Frame.BorderSizePixel = 0
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundColor3 = Color3.fromRGB(255, 0, 100)
Title.Text = "Cool Hub - Rivals"
Title.TextColor3 = Color3.new(1,1,1)
Title.TextScaled = true
Title.Parent = Frame

local function CreateButton(name, yPos, callback)
    local Btn = Instance.new("TextButton")
    Btn.Size = UDim2.new(0.9, 0, 0, 35)
    Btn.Position = UDim2.new(0.05, 0, 0, yPos)
    Btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    Btn.Text = name
    Btn.TextColor3 = Color3.new(1,1,1)
    Btn.TextScaled = true
    Btn.Parent = Frame
    Btn.MouseButton1Click:Connect(callback)
    return Btn
end

-- ================== INFINITE KEYS ==================
CreateButton("🔑 Give 100k Keys (x10)", 40, function()
    for i = 1, 10 do
        pcall(function()
            game:GetService("ReplicatedStorage").Remotes.GiveKeys:FireServer(100000)
            game:GetService("ReplicatedStorage").Remotes.AddKeys:FireServer(100000)
        end)
    end
    print("100k Keys envoyées x10")
end)

CreateButton("🔁 Infinite Keys Loop ON", 85, function()
    getgenv().KeysLoop = true
    spawn(function()
        while getgenv().KeysLoop do
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.GiveKeys:FireServer(50000)
            end)
            task.wait(0.4)
        end
    end)
    print("Infinite Keys Loop activé")
end)

CreateButton("⛔ Stop Keys Loop", 130, function()
    getgenv().KeysLoop = false
    print("Keys Loop arrêté")
end)

-- ================== UNLOCK ALL ==================
CreateButton("🔫 Unlock ALL Weapons", 175, function()
    pcall(function()
        local weapons = game:GetService("ReplicatedStorage"):FindFirstChild("Weapons") or game:GetService("ReplicatedStorage"):FindFirstChild("WeaponData")
        if weapons then
            for _, weapon in pairs(weapons:GetDescendants()) do
                if weapon:IsA("StringValue") or weapon:IsA("Folder") then
                    game:GetService("ReplicatedStorage").Remotes.UnlockWeapon:FireServer(weapon.Name)
                end
            end
        end
    end)
    print("Tentative Unlock All Weapons")
end)

CreateButton("🌟 Unlock ALL Cosmetics", 220, function()
    pcall(function()
        game:GetService("ReplicatedStorage").Remotes.UnlockAllCosmetics:FireServer()
        game:GetService("ReplicatedStorage").Events.UnlockSkin:FireServer("all")
    end)
    print("Tentative Unlock All Cosmetics")
end)

-- Bouton pour cacher le menu
CreateButton("🔄 Cacher Menu", 265, function()
    Frame.Visible = not Frame.Visible
end)

print("Menu Cool Hub prêt - Appuie sur les boutons !")
