-- Time Bomb 👑 Script (Compact Version)
-- Developer: عبدالله العتيبي

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local RunService = game:GetService("RunService")

-- إزالة أي واجهة قديمة لتجنب التكرار
if PlayerGui:FindFirstChild("TimeBombGUI") then
    PlayerGui.TimeBombGUI:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "TimeBombGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

-- النافذة الرئيسية (حجم صغير وأنيق)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 210, 0, 125)
MainFrame.Position = UDim2.new(0.5, -105, 0.5, -62)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 10)
UICorner.Parent = MainFrame

-- اسم السكربت والمطور في سطر واحد مختصر
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(0, 130, 0, 30)
TitleLabel.Position = UDim2.new(0, 10, 0, 8)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "Time Bomb 👑"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 15
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = MainFrame

local DevLabel = Instance.new("TextLabel")
DevLabel.Size = UDim2.new(0, 130, 0, 15)
DevLabel.Position = UDim2.new(0, 10, 0, 28)
DevLabel.BackgroundTransparency = 1
DevLabel.Text = "عبدالله العتيبي"
DevLabel.TextColor3 = Color3.fromRGB(220, 40, 40)
DevLabel.TextSize = 11
DevLabel.Font = Enum.Font.GothamBold
DevLabel.TextXAlignment = Enum.TextXAlignment.Left
DevLabel.Parent = MainFrame

-- أزرار التحكم العلوية (القفل والحذف)
local isLocked = false

local TrashBtn = Instance.new("TextButton")
TrashBtn.Size = UDim2.new(0, 24, 0, 24)
TrashBtn.Position = UDim2.new(1, -29, 0, 10)
TrashBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TrashBtn.Text = "🗑️"
TrashBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
TrashBtn.TextSize = 11
TrashBtn.Parent = MainFrame
Instance.new("UICorner", TrashBtn).CornerRadius = UDim.new(0, 5)

TrashBtn.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

local LockBtn = Instance.new("TextButton")
LockBtn.Size = UDim2.new(0, 24, 0, 24)
LockBtn.Position = UDim2.new(1, -56, 0, 10)
LockBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
LockBtn.Text = "🔓"
LockBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
LockBtn.TextSize = 10
LockBtn.Parent = MainFrame
Instance.new("UICorner", LockBtn).CornerRadius = UDim.new(0, 5)

LockBtn.MouseButton1Click:Connect(function()
    isLocked = not isLocked
    MainFrame.Draggable = not isLocked
    LockBtn.Text = isLocked and "🔒" or "🔓"
    LockBtn.BackgroundColor3 = isLocked and Color3.fromRGB(180, 40, 40) or Color3.fromRGB(40, 40, 40)
end)

-- زر Auto 💣
local savedPosition = nil
local autoBombActive = false
local AutoBombBtn = Instance.new("TextButton")
AutoBombBtn.Size = UDim2.new(1, -20, 0, 36)
AutoBombBtn.Position = UDim2.new(0, 10, 0, 55)
AutoBombBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
AutoBombBtn.Text = "Auto 💣"
AutoBombBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoBombBtn.TextSize = 14
AutoBombBtn.Font = Enum.Font.GothamBold
AutoBombBtn.Parent = MainFrame
Instance.new("UICorner", AutoBombBtn).CornerRadius = UDim.new(0, 6)

AutoBombBtn.MouseButton1Click:Connect(function()
    autoBombActive = not autoBombActive
    if autoBombActive then
        AutoBombBtn.BackgroundColor3 = Color3.fromRGB(40, 180, 40)
        
        local character = LocalPlayer.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            savedPosition = character.HumanoidRootPart.CFrame
        end
    else
        AutoBombBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
        savedPosition = nil
    end
end)

-- نظام الأوتو بومب
RunService.RenderStepped:Connect(function()
    if not autoBombActive then return end
    
    local character = LocalPlayer.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return end
    
    local backpack = LocalPlayer:FindFirstChild("Backpack")
    local hasBomb = (character:FindFirstChild("TimeBomb") or character:FindFirstChild("Bomb") or 
                     (backpack and (backpack:FindFirstChild("TimeBomb") or backpack:FindFirstChild("Bomb"))))
    
    if not hasBomb then return end
    
    if not savedPosition then
        savedPosition = character.HumanoidRootPart.CFrame
    end
    
    local closestEnemy = nil
    local shortestDistance = math.huge
    local myPos = character.HumanoidRootPart.Position
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local sameTeam = false
            if LocalPlayer.Team and player.Team and LocalPlayer.Team == player.Team then
                sameTeam = true
            end
            
            if not sameTeam then
                local hrp = player.Character.HumanoidRootPart
                if math.abs(hrp.Position.X) < 1500 and math.abs(hrp.Position.Z) < 1500 then
                    local distance = (hrp.Position - myPos).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestEnemy = hrp
                    end
                end
            end
        end
    end
    
    if closestEnemy then
        while autoBombActive and closestEnemy and closestEnemy.Parent do
            local currentBackpack = LocalPlayer:FindFirstChild("Backpack")
            local stillHasBomb = (character:FindFirstChild("TimeBomb") or character:FindFirstChild("Bomb") or 
                                  (currentBackpack and (currentBackpack:FindFirstChild("TimeBomb") or currentBackpack:FindFirstChild("Bomb"))))
            
            if not stillHasBomb then break end
            
            character.HumanoidRootPart.CFrame = closestEnemy.CFrame
            RunService.RenderStepped:Wait()
        end
        
        if savedPosition then
            character.HumanoidRootPart.CFrame = savedPosition
        end
        
        autoBombActive = false
        AutoBombBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
        savedPosition = nil
    end
end)