-- Cool Hub v10 - Smooth Fix + Give Gun Category (Street War)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()

local aimEnabled = false
local fov = 140
local walkSpeed = 50
local smooth = 0.12
local aimPart = "Head"

-- Sauvegarde vitesse originale
local originalWalkSpeed = 16
if player.Character and player.Character:FindFirstChild("Humanoid") then
    originalWalkSpeed = player.Character.Humanoid.WalkSpeed
end

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 205, 0, 340)
mainFrame.Position = UDim2.new(0.86, -102, 0.25, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 26)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 12)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.Text = "Cool Hub"
title.TextColor3 = Color3.fromRGB(0, 170, 255)
title.TextScaled = true
title.Font = Enum.Font.GothamBlack
title.Parent = mainFrame

local scroller = Instance.new("ScrollingFrame")
scroller.Size = UDim2.new(1, -20, 1, -100)
scroller.Position = UDim2.new(0, 10, 0, 48)
scroller.BackgroundTransparency = 1
scroller.ScrollBarThickness = 5
scroller.Parent = mainFrame

Instance.new("UIListLayout", scroller).Padding = UDim.new(0, 10)

-- Toggle
local function createToggle(text, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1,0,0,45)
    frame.BackgroundColor3 = Color3.fromRGB(30,30,37)
    frame.Parent = scroller
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0,10)
    
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0.6,0,1,0)
    lbl.BackgroundTransparency = 1
    lbl.Text = text
    lbl.TextColor3 = Color3.new(1,1,1)
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.TextScaled = true
    lbl.Font = Enum.Font.Gotham
    lbl.Parent = frame
    
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.35,0,0.75,0)
    btn.Position = UDim2.new(0.62,0,0.125,0)
    btn.BackgroundColor3 = default and Color3.fromRGB(0,200,100) or Color3.fromRGB(200,50,70)
    btn.Text = default and "ON" or "OFF"
    btn.TextColor3 = Color3.new(1,1,1)
    btn.TextScaled = true
    btn.Font = Enum.Font.GothamBold
    btn.Parent = frame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,8)
    
    local state = default
    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = state and "ON" or "OFF"
        btn.BackgroundColor3 = state and Color3.fromRGB(0,200,100) or Color3.fromRGB(200,50,70)
        callback(state)
    end)
end

-- Slider fixe (Smooth)
local function createSlider(text, minVal, maxVal, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1,0,0,68)
    frame.BackgroundColor3 = Color3.fromRGB(30,30,37)
    frame.Parent = scroller
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0,10)
    
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1,0,0,25)
    lbl.BackgroundTransparency = 1
    lbl.Text = text .. ": " .. default
    lbl.TextColor3 = Color3.new(1,1,1)
    lbl.TextScaled = true
    lbl.Font = Enum.Font.Gotham
    lbl.Parent = frame
    
    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(1,-24,0,14)
    bar.Position = UDim2.new(0,12,0,37)
    bar.BackgroundColor3 = Color3.fromRGB(45,45,52)
    bar.Parent = frame
    Instance.new("UICorner", bar).CornerRadius = UDim.new(0,7)
    
    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default-minVal)/(maxVal-minVal),0,1,0)
    fill.BackgroundColor3 = Color3.fromRGB(0,170,255)
    fill.Parent = bar
    Instance.new("UICorner", fill).CornerRadius = UDim.new(0,7)
    
    local value = default
    local dragging = false
    
    bar.InputBegan:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.MouseButton1 or inp.UserInputType == Enum.UserInputType.Touch then
            dragging = true
        end
    end)
    
    UserInputService.InputEnded:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.MouseButton1 or inp.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    
    RunService.RenderStepped:Connect(function()
        if dragging then
            local percent = math.clamp((UserInputService:GetMouseLocation().X - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
            value = math.floor(minVal + (maxVal - minVal) * percent * 100) / 100   -- arrondi propre
            fill.Size = UDim2.new(percent, 0, 1, 0)
            lbl.Text = text .. ": " .. value
            callback(value)
        end
    end)
end

-- Création des toggles et sliders
createToggle("Aimbot", false, function(v) aimEnabled = v end)
createSlider("FOV", 60, 320, 140, function(v) fov = v end)
createSlider("Speed", 16, 90, 50, function(v) walkSpeed = v end)
createSlider("Smooth", 0.02, 0.35, 0.12, function(v) smooth = v end)

-- ==================== CATÉGORIE GIVE GUN ====================
local guns = {
    "Glock", "Uzi", "Draco", "Skorpion", "AK-47", "Double Barrel", 
    "FAMAS", "Tec-9", "P90", "BR14", "AUG", "PPSH41", "1911", 
    "Mini Draco", "Vector", "UMP", "MPX", "SAIGA 12K"
}

local selectedGun = guns[1]

local gunFrame = Instance.new("Frame")
gunFrame.Size = UDim2.new(1,0,0,95)
gunFrame.BackgroundColor3 = Color3.fromRGB(30,30,37)
gunFrame.Parent = scroller
Instance.new("UICorner", gunFrame).CornerRadius = UDim.new(0,10)

local gunTitle = Instance.new("TextLabel")
gunTitle.Size = UDim2.new(1,0,0,25)
gunTitle.BackgroundTransparency = 1
gunTitle.Text = "Give Gun"
gunTitle.TextColor3 = Color3.fromRGB(0,170,255)
gunTitle.TextScaled = true
gunTitle.Font = Enum.Font.GothamBold
gunTitle.Parent = gunFrame

local selectedLabel = Instance.new("TextLabel")
selectedLabel.Size = UDim2.new(1,0,0,25)
selectedLabel.Position = UDim2.new(0,0,0,28)
selectedLabel.BackgroundTransparency = 1
selectedLabel.Text = "Sélection : " .. selectedGun
selectedLabel.TextColor3 = Color3.new(1,1,1)
selectedLabel.TextScaled = true
selectedLabel.Font = Enum.Font.Gotham
selectedLabel.Parent = gunFrame

local giveBtn = Instance.new("TextButton")
giveBtn.Size = UDim2.new(0.95,0,0,35)
giveBtn.Position = UDim2.new(0.025,0,0,58)
giveBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
giveBtn.Text = "Donner l'arme"
giveBtn.TextColor3 = Color3.new(1,1,1)
giveBtn.TextScaled = true
giveBtn.Font = Enum.Font.GothamBold
giveBtn.Parent = gunFrame
Instance.new("UICorner", giveBtn).CornerRadius = UDim.new(0,8)

-- Changer d'arme (clic sur le bouton donne et change)
giveBtn.MouseButton1Click:Connect(function()
    -- Changer l'arme sélectionnée
    local idx = table.find(guns, selectedGun) or 1
    idx = (idx % #guns) + 1
    selectedGun = guns[idx]
    selectedLabel.Text = "Sélection : " .. selectedGun
    
    -- Give l'arme
    if player.Backpack then
        local tool = nil
        -- Tentative de trouver l'arme dans ReplicatedStorage ou Workspace
        for _, obj in ipairs(game:GetDescendants()) do
            if obj:IsA("Tool") and (obj.Name:lower() == selectedGun:lower() or obj.Name:find(selectedGun)) then
                tool = obj:Clone()
                break
            end
        end
        
        if tool then
            tool.Parent = player.Backpack
            print("✅ Arme donnée : " .. selectedGun)
        else
            print("⚠️ Arme " .. selectedGun .. " non trouvée. Essaie d'acheter en jeu ou utilise un spawner.")
        end
    end
end)

-- Boutons Hide & Unload
local bottom = Instance.new("Frame")
bottom.Size = UDim2.new(1,-20,0,40)
bottom.Position = UDim2.new(0,10,1,-55)
bottom.BackgroundTransparency = 1
bottom.Parent = mainFrame

local hideBtn = Instance.new("TextButton")
hideBtn.Size = UDim2.new(0.48,0,1,0)
hideBtn.BackgroundColor3 = Color3.fromRGB(40,40,48)
hideBtn.Text = "Hide"
hideBtn.TextColor3 = Color3.new(1,1,1)
hideBtn.TextScaled = true
hideBtn.Font = Enum.Font.GothamSemibold
hideBtn.Parent = bottom
Instance.new("UICorner", hideBtn).CornerRadius = UDim.new(0,8)

local unloadBtn = Instance.new("TextButton")
unloadBtn.Size = UDim2.new(0.48,0,1,0)
unloadBtn.Position = UDim2.new(0.52,0,0,0)
unloadBtn.BackgroundColor3 = Color3.fromRGB(200,40,60)
unloadBtn.Text = "Unload"
unloadBtn.TextColor3 = Color3.new(1,1,1)
unloadBtn.TextScaled = true
unloadBtn.Font = Enum.Font.GothamSemibold
unloadBtn.Parent = bottom
Instance.new("UICorner", unloadBtn).CornerRadius = UDim.new(0,8)

hideBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = not mainFrame.Visible
    hideBtn.Text = mainFrame.Visible and "Hide" or "Show"
end)

-- Unload parfait
local renderConnection
local circle = Drawing.new("Circle")
circle.Thickness = 2
circle.Color = Color3.fromRGB(0,170,255)
circle.Filled = false
circle.Transparency = 0.7
circle.NumSides = 60

local function unloadHub()
    if renderConnection then renderConnection:Disconnect() end
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid.WalkSpeed = originalWalkSpeed
    end
    circle:Remove()
    screenGui:Destroy()
    print("Cool Hub déchargé complètement")
end

unloadBtn.MouseButton1Click:Connect(unloadHub)

-- Boucle principale
renderConnection = RunService.RenderStepped:Connect(function()
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid.WalkSpeed = walkSpeed
    end
    
    circle.Radius = fov
    circle.Position = Vector2.new(mouse.X, mouse.Y + 36)
    
    if not aimEnabled then return end
    
    local closest = nil
    local bestDist = fov
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild(aimPart) and plr.Character:FindFirstChild("Humanoid") then
            if plr.Character.Humanoid.Health > 0 then
                local pos, onScreen = camera:WorldToViewportPoint(plr.Character[aimPart].Position)
                if onScreen then
                    local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(mouse.X, mouse.Y)).Magnitude
                    if dist < bestDist then
                        bestDist = dist
                        closest = plr
                    end
                end
            end
        end
    end
    
    if closest and closest.Character and closest.Character:FindFirstChild(aimPart) then
        local targetPos = closest.Character[aimPart].Position
        local current = camera.CFrame
        local targetCF = CFrame.lookAt(current.Position, targetPos)
        camera.CFrame = current:Lerp(targetCF, smooth)
    end
end)

print("Cool Hub v10 chargé - Smooth fixé + Give Gun ajouté")
