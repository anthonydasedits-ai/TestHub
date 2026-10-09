--[[
    MOBILE SCRIPT HUB - TESTE
    Features: Speed, Infinite Jump, Noclip, Fly, Spider-Man Fly
    Platform: MOBILE (Touch optimized)
    Language: English
    Minimize: Yes (drag ball to reopen)
--]]

-- SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- ===== VARIABLES =====
local SpeedValue = 16
local JumpPowerValue = 50
local InfiniteJump = false
local Noclip = false
local FlyEnabled = false
local FlySpeed = 50
local SpiderEnabled = false
local SpiderSpeed = 70
local SpiderAttachRange = 200

-- ===== REMOVE OLD GUI =====
pcall(function()
    if CoreGui:FindFirstChild("MobileHub") then
        CoreGui.MobileHub:Destroy()
    end
end)

-- ===== CREATE GUI =====
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MobileHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.IgnoreGuiInset = true
ScreenGui.Parent = CoreGui

-- ==========================================
-- MINIMIZED BALL (floating ball when closed)
-- ==========================================
local MinimizedBall = Instance.new("TextButton")
MinimizedBall.Name = "MinimizedBall"
MinimizedBall.Parent = ScreenGui
MinimizedBall.Size = UDim2.new(0, 65, 0, 65)
MinimizedBall.Position = UDim2.new(0, 20, 0.5, -32)
MinimizedBall.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
MinimizedBall.Text = "⚡"
MinimizedBall.TextColor3 = Color3.fromRGB(0, 255, 200)
MinimizedBall.Font = Enum.Font.GothamBold
MinimizedBall.TextSize = 28
MinimizedBall.BorderSizePixel = 0
MinimizedBall.Active = true
MinimizedBall.Draggable = true
MinimizedBall.Visible = true

local BallCorner = Instance.new("UICorner")
BallCorner.CornerRadius = UDim.new(1, 0)
BallCorner.Parent = MinimizedBall

local BallStroke = Instance.new("UIStroke")
BallStroke.Color = Color3.fromRGB(0, 255, 200)
BallStroke.Thickness = 2.5
BallStroke.Parent = MinimizedBall

-- Pulse animation on ball
task.spawn(function()
    while MinimizedBall.Parent do
        TweenService:Create(BallStroke, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
            Transparency = 0.6
        }):Play()
        task.wait(1)
        TweenService:Create(BallStroke, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
            Transparency = 0
        }):Play()
        task.wait(1)
    end
end)

-- ==========================================
-- MAIN FRAME
-- ==========================================
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.Size = UDim2.new(0, 340, 0, 500)
MainFrame.Position = UDim2.new(0.5, -170, 0.5, -250)
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 26)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = false

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(0, 255, 200)
MainStroke.Thickness = 2
MainStroke.Parent = MainFrame

-- ==========================================
-- TITLE BAR (drag handle)
-- ==========================================
local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Parent = MainFrame
TitleBar.Size = UDim2.new(1, 0, 0, 50)
TitleBar.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
TitleBar.BorderSizePixel = 0
TitleBar.Active = true

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 14)
TitleCorner.Parent = TitleBar

-- Fix bottom corners of title bar
local TitleFix = Instance.new("Frame")
TitleFix.Parent = TitleBar
TitleFix.Size = UDim2.new(1, 0, 0, 14)
TitleFix.Position = UDim2.new(0, 0, 1, -14)
TitleFix.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
TitleFix.BorderSizePixel = 0
TitleFix.ZIndex = 0

local Title = Instance.new("TextLabel")
Title.Parent = TitleBar
Title.Size = UDim2.new(1, -90, 1, 0)
Title.Position = UDim2.new(0, 12, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "⚡ MOBILE HUB ⚡"
Title.TextColor3 = Color3.fromRGB(0, 255, 200)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 2

-- Minimize Button (turns into ball)
local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Parent = TitleBar
MinimizeBtn.Size = UDim2.new(0, 38, 0, 38)
MinimizeBtn.Position = UDim2.new(1, -88, 0, 6)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 90)
MinimizeBtn.Text = "—"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.TextSize = 20
MinimizeBtn.BorderSizePixel = 0
MinimizeBtn.ZIndex = 2

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 8)
MinCorner.Parent = MinimizeBtn

-- Close Button (fully hides GUI + ball)
local CloseBtn = Instance.new("TextButton")
CloseBtn.Parent = TitleBar
CloseBtn.Size = UDim2.new(0, 38, 0, 38)
CloseBtn.Position = UDim2.new(1, -45, 0, 6)
CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 18
CloseBtn.BorderSizePixel = 0
CloseBtn.ZIndex = 2

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 8)
CloseCorner.Parent = CloseBtn

-- ==========================================
-- MINIMIZE / RESTORE LOGIC
-- ==========================================
local function minimizeToBall()
    -- Save current position as ball position
    MinimizedBall.Position = UDim2.new(0, 20, 0.5, -32)
    MinimizedBall.Visible = true
    
    -- Fade animation
    MainFrame.Visible = false
end

local function restoreFromBall()
    MinimizedBall.Visible = false
    MainFrame.Visible = true
end

MinimizeBtn.MouseButton1Click:Connect(minimizeToBall)

-- Tap ball to restore
MinimizedBall.MouseButton1Click:Connect(function()
    restoreFromBall()
end)

-- Close everything (GUI + ball hidden, can reopen via touch gesture)
local fullyClosed = false
CloseBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    MinimizedBall.Visible = false
    fullyClosed = true
    
    -- Show a small reopen hint
    local hint = Instance.new("TextButton")
    hint.Parent = ScreenGui
    hint.Size = UDim2.new(0, 45, 0, 45)
    hint.Position = UDim2.new(0, 20, 0.5, -22)
    hint.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    hint.Text = "?"
    hint.TextColor3 = Color3.fromRGB(255, 100, 100)
    hint.Font = Enum.Font.GothamBold
    hint.TextSize = 22
    hint.BorderSizePixel = 0
    hint.Draggable = true
    
    local hc = Instance.new("UICorner")
    hc.CornerRadius = UDim.new(1, 0)
    hc.Parent = hint
    
    local hs = Instance.new("UIStroke")
    hs.Color = Color3.fromRGB(255, 100, 100)
    hs.Thickness = 2
    hs.Parent = hint
    
    hint.MouseButton1Click:Connect(function()
        hint:Destroy()
        MinimizedBall.Visible = true
        fullyClosed = false
    end)
end)

-- ==========================================
-- SCROLL FRAME
-- ==========================================
local Scroll = Instance.new("ScrollingFrame")
Scroll.Parent = MainFrame
Scroll.Size = UDim2.new(1, -20, 1, -65)
Scroll.Position = UDim2.new(0, 10, 0, 56)
Scroll.BackgroundTransparency = 1
Scroll.BorderSizePixel = 0
Scroll.ScrollBarThickness = 6
Scroll.ScrollBarImageColor3 = Color3.fromRGB(0, 255, 200)
Scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Scroll.ScrollingDirection = Enum.ScrollingDirection.Y
Scroll.ElasticBehavior = Enum.ElasticBehavior.WhenScrollable

local UIList = Instance.new("UIListLayout")
UIList.Parent = Scroll
UIList.SortOrder = Enum.SortOrder.LayoutOrder
UIList.Padding = UDim.new(0, 10)

local UIPadding = Instance.new("UIPadding")
UIPadding.Parent = Scroll
UIPadding.PaddingTop = UDim.new(0, 5)

-- ==========================================
-- HELPER FUNCTIONS
-- ==========================================
local function createSectionLabel(text)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -5, 0, 28)
    label.BackgroundTransparency = 1
    label.Text = "— " .. text .. " —"
    label.TextColor3 = Color3.fromRGB(150, 150, 180)
    label.Font = Enum.Font.GothamBold
    label.TextSize = 13
    label.Parent = Scroll
end

local function createButton(text, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -5, 0, 45)
    btn.BackgroundColor3 = color or Color3.fromRGB(35, 35, 50)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 15
    btn.BorderSizePixel = 0
    btn.Parent = Scroll
    
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 8)
    c.Parent = btn
    
    return btn
end

-- TOUCH-FRIENDLY SLIDER
local function createSlider(name, min, max, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -5, 0, 70)
    frame.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
    frame.BorderSizePixel = 0
    frame.Parent = Scroll
    
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 8)
    c.Parent = frame
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -15, 0, 22)
    label.Position = UDim2.new(0, 10, 0, 6)
    label.BackgroundTransparency = 1
    label.Text = name .. ": " .. default
    label.TextColor3 = Color3.fromRGB(0, 255, 200)
    label.Font = Enum.Font.GothamBold
    label.TextSize = 14
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame
    
    local sliderBg = Instance.new("Frame")
    sliderBg.Size = UDim2.new(1, -30, 0, 16)
    sliderBg.Position = UDim2.new(0, 15, 0, 40)
    sliderBg.BackgroundColor3 = Color3.fromRGB(50, 50, 65)
    sliderBg.BorderSizePixel = 0
    sliderBg.Parent = frame
    
    local sliderCorner = Instance.new("UICorner")
    sliderCorner.CornerRadius = UDim.new(1, 0)
    sliderCorner.Parent = sliderBg
    
    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    fill.BackgroundColor3 = Color3.fromRGB(0, 255, 200)
    fill.BorderSizePixel = 0
    fill.Parent = sliderBg
    
    local fillCorner = Instance.new("UICorner")
    fillCorner.CornerRadius = UDim.new(1, 0)
    fillCorner.Parent = fill
    
    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 22, 0, 22)
    knob.Position = UDim2.new((default - min) / (max - min), -11, 0.5, -11)
    knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    knob.BorderSizePixel = 0
    knob.ZIndex = 2
    knob.Parent = sliderBg
    
    local knobCorner = Instance.new("UICorner")
    knobCorner.CornerRadius = UDim.new(1, 0)
    knobCorner.Parent = knob
    
    local knobStroke = Instance.new("UIStroke")
    knobStroke.Color = Color3.fromRGB(0, 255, 200)
    knobStroke.Thickness = 2
    knobStroke.Parent = knob
    
    local dragArea = Instance.new("TextButton")
    dragArea.Size = UDim2.new(1, 0, 3, 0)
    dragArea.Position = UDim2.new(0, 0, 0.5, -24)
    dragArea.BackgroundTransparency = 1
    dragArea.Text = ""
    dragArea.ZIndex = 3
    dragArea.Parent = sliderBg
    
    local dragging = false
    
    local function updateValue(input)
        local pos = math.clamp((input.Position.X - sliderBg.AbsolutePosition.X) / sliderBg.AbsoluteSize.X, 0, 1)
        local val = math.floor(min + (max - min) * pos)
        fill.Size = UDim2.new(pos, 0, 1, 0)
        knob.Position = UDim2.new(pos, -11, 0.5, -11)
        label.Text = name .. ": " .. val
        callback(val)
    end
    
    dragArea.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 
           or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            updateValue(input)
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement 
           or input.UserInputType == Enum.UserInputType.Touch) then
            updateValue(input)
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 
           or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
end

local function createToggle(name, callback)
    local btn = createButton(name .. ": OFF", Color3.fromRGB(35, 35, 50))
    local state = false
    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = name .. ": " .. (state and "ON" or "OFF")
        btn.BackgroundColor3 = state and Color3.fromRGB(0, 150, 120) or Color3.fromRGB(35, 35, 50)
        callback(state)
    end)
    return btn
end

-- ==========================================
-- CORE FUNCTIONS
-- ==========================================
local function applySpeed(val)
    SpeedValue = val
    local char = LocalPlayer.Character
    if char and char:FindFirstChildOfClass("Humanoid") then
        char:FindFirstChildOfClass("Humanoid").WalkSpeed = val
    end
end

LocalPlayer.CharacterAdded:Connect(function(char)
    task.wait(1)
    if char:FindFirstChildOfClass("Humanoid") then
        char:FindFirstChildOfClass("Humanoid").WalkSpeed = SpeedValue
        char:FindFirstChildOfClass("Humanoid").JumpPower = JumpPowerValue
    end
end)

-- ===== MOVEMENT SECTION =====
createSectionLabel("MOVEMENT")
createSlider("⚡ Speed", 16, 500, 16, applySpeed)
createSlider("🦘 Jump Power", 50, 500, 50, function(val)
    JumpPowerValue = val
    local char = LocalPlayer.Character
    if char and char:FindFirstChildOfClass("Humanoid") then
        char:FindFirstChildOfClass("Humanoid").JumpPower = val
    end
end)

createToggle("♾️ Infinite Jump", function(state)
    InfiniteJump = state
end)

UserInputService.JumpRequest:Connect(function()
    if InfiniteJump then
        local char = LocalPlayer.Character
        if char and char:FindFirstChildOfClass("Humanoid") then
            char:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
        end
    end
end)

-- ===== UTILITY SECTION =====
createSectionLabel("UTILITY")
createToggle("👻 Noclip", function(state)
    Noclip = state
end)

RunService.Stepped:Connect(function()
    if Noclip then
        local char = LocalPlayer.Character
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") and part.CanCollide then
                    part.CanCollide = false
                end
            end
        end
    end
end)

-- ===== FLY SECTION =====
createSectionLabel("FLY")
createToggle("🕊️ Fly", function(state)
    FlyEnabled = state
    local char = LocalPlayer.Character
    if not char then return end
    
    if state then
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Name = "FlyBV"
        bodyVelocity.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        bodyVelocity.Velocity = Vector3.zero
        bodyVelocity.Parent = hrp
        
        task.spawn(function()
            while FlyEnabled and hrp and hrp.Parent do
                local moveDir = Vector3.zero
                if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir += Camera.CFrame.LookVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir -= Camera.CFrame.LookVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir -= Camera.CFrame.RightVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir += Camera.CFrame.RightVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir += Vector3.new(0, 1, 0) end
                if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir -= Vector3.new(0, 1, 0) end
                
                if moveDir.Magnitude > 0 then
                    bodyVelocity.Velocity = moveDir.Unit * FlySpeed
                else
                    bodyVelocity.Velocity = Vector3.zero
                end
                RunService.RenderStepped:Wait()
            end
        end)
    else
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if hrp then
            local bv = hrp:FindFirstChild("FlyBV")
            if bv then bv:Destroy() end
        end
    end
end)

createSlider("🚀 Fly Speed", 10, 300, 50, function(val)
    FlySpeed = val
end)

-- ===== SPIDER-MAN FLY SECTION =====
createSectionLabel("SPIDER-MAN")

local function raycastForAttachment(origin, direction)
    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Exclude
    raycastParams.FilterDescendantsInstances = {LocalPlayer.Character}
    
    local result = workspace:Raycast(origin, direction, raycastParams)
    if result then
        return result.Position, result.Instance
    end
    return nil
end

createToggle("🕷️ Spider-Man Fly", function(state)
    SpiderEnabled = state
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    if state then
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then humanoid.PlatformStand = true end
        
        local rightArm = char:FindFirstChild("Right Arm") or char:FindFirstChild("RightHand")
        local webOrigin = rightArm or hrp
        
        local webAttachment0 = Instance.new("Attachment")
        webAttachment0.Parent = webOrigin
        
        local webAttachment1 = Instance.new("Attachment")
        webAttachment1.Parent = hrp
        
        local beam = Instance.new("Beam")
        beam.Attachment0 = webAttachment0
        beam.Attachment1 = webAttachment1
        beam.Width0 = 0.2
        beam.Width1 = 0.2
        beam.Color = ColorSequence.new(Color3.fromRGB(255, 255, 255))
        beam.Transparency = NumberSequence.new(0.2)
        beam.FaceCamera = true
        beam.Enabled = false
        beam.Parent = webOrigin
        
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Name = "SpiderBV"
        bodyVelocity.MaxForce = Vector3.new(1e6, 1e6, 1e6)
        bodyVelocity.Velocity = Vector3.zero
        bodyVelocity.Parent = hrp
        
        task.spawn(function()
            while SpiderEnabled and hrp and hrp.Parent do
                local rayOrigin = hrp.Position + Vector3.new(0, 5, 0)
                local rayDir = Camera.CFrame.LookVector * SpiderAttachRange
                local hitPos = raycastForAttachment(rayOrigin, rayDir)
                
                if not hitPos then
                    hitPos = raycastForAttachment(rayOrigin, Vector3.new(0, SpiderAttachRange, 0))
                end
                
                if hitPos then
                    beam.Enabled = true
                    webAttachment1.WorldPosition = hitPos
                    
                    local dir = (hitPos - hrp.Position).Unit
                    local tangent = Vector3.new(-dir.Z, 0, dir.X).Unit
                    local swingForce = tangent * SpiderSpeed + Vector3.new(0, 30, 0)
                    
                    if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                        swingForce += Camera.CFrame.LookVector * 20
                    end
                    if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                        swingForce -= Camera.CFrame.LookVector * 20
                    end
                    
                    bodyVelocity.Velocity = swingForce
                else
                    beam.Enabled = false
                    bodyVelocity.Velocity = Vector3.new(0, -50, 0)
                end
                
                RunService.RenderStepped:Wait()
            end
            
            if beam then beam:Destroy() end
            if webAttachment0 then webAttachment0:Destroy() end
            if webAttachment1 then webAttachment1:Destroy() end
            if bodyVelocity then bodyVelocity:Destroy() end
            if humanoid then humanoid.PlatformStand = false end
        end)
    else
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then humanoid.PlatformStand = false end
        
        local hrp2 = char:FindFirstChild("HumanoidRootPart")
        if hrp2 then
            local bv = hrp2:FindFirstChild("SpiderBV")
            if bv then bv:Destroy() end
            
            for _, child in pairs(hrp2:GetChildren()) do
                if child:IsA("Attachment") then
                    child:Destroy()
                end
            end
        end
        
        local rightArm = char:FindFirstChild("Right Arm") or char:FindFirstChild("RightHand")
        if rightArm then
            for _, child in pairs(rightArm:GetChildren()) do
                if child:IsA("Beam") or child:IsA("Attachment") then
                    child:Destroy()
                end
            end
        end
    end
end)

createSlider("🕸️ Spider Speed", 20, 200, 70, function(val)
    SpiderSpeed = val
end)

createSlider("📏 Web Range", 50, 500, 200, function(val)
    SpiderAttachRange = val
end)

-- ===== RESET BUTTON =====
createSectionLabel("PLAYER")
local resetBtn = createButton("🔄 Reset Character", Color3.fromRGB(180, 60, 60))
resetBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChildOfClass("Humanoid") then
        char:FindFirstChildOfClass("Humanoid").Health = 0
    end
end)

-- ==========================================
-- WELCOME NOTIFICATION
-- ==========================================
local notif = Instance.new("TextLabel")
notif.Parent = ScreenGui
notif.Size = UDim2.new(0, 300, 0, 60)
notif.Position = UDim2.new(0.5, -150, 0, 30)
notif.BackgroundColor3 = Color3.fromRGB(18, 18, 26)
notif.Text = "✅ MOBILE HUB LOADED!\nTap ⚡ ball to open menu"
notif.TextColor3 = Color3.fromRGB(0, 255, 200)
notif.Font = Enum.Font.GothamBold
notif.TextSize = 14
notif.BorderSizePixel = 0

local nc = Instance.new("UICorner")
nc.CornerRadius = UDim.new(0, 10)
nc.Parent = notif

local ns = Instance.new("UIStroke")
ns.Color = Color3.fromRGB(0, 255, 200)
ns.Parent = notif

task.wait(3)
TweenService:Create(notif, TweenInfo.new(0.5), {BackgroundTransparency = 1, TextTransparency = 1}):Play()
task.wait(0.5)
notif:Destroy()

print("✅ MOBILE HUB loaded successfully!")
