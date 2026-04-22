-- Allusive Style Mini Hub v7 - FIX FINAL (Strength + Unload Parfait)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()

-- Variables
local aimEnabled = false
local fov = 135
local walkSpeed = 50
local smoothness = 0.14
local aimPart = "Head"

-- Sauvegarde de la vitesse originale (pour unload parfait)
local originalWalkSpeed = 16
if player.Character and player.Character:FindFirstChild("Humanoid") then
 originalWalkSpeed = player.Character.Humanoid.WalkSpeed
end

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 185, 0, 255)
mainFrame.Position = UDim2.new(0.87, -92, 0.28, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 14)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 38)
title.BackgroundTransparency = 1
title.Text = "Allusive"
title.TextColor3 = Color3.fromRGB(255, 55, 115)
title.TextScaled = true
title.Font = Enum.Font.GothamBlack
title.Parent = mainFrame

local scroller = Instance.new("ScrollingFrame")
scroller.Size = UDim2.new(1, -18, 1, -88)
scroller.Position = UDim2.new(0, 9, 0, 45)
scroller.BackgroundTransparency = 1
scroller.ScrollBarThickness = 4
scroller.Parent = mainFrame

Instance.new("UIListLayout", scroller).Padding = UDim.new(0, 10)

-- Toggle & Slider (fixés)
local function createToggle(text, default, callback)
 local frame = Instance.new("Frame")
 frame.Size = UDim2.new(1,0,0,44)
 frame.BackgroundColor3 = Color3.fromRGB(28 <img width="1080" height="717" alt="1000002710" src="https://github.com/user-attachments/assets/b4c4716d-8d74-4666-9f20-380d4ecc5278" />

