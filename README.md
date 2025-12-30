local P = game.Players.LocalPlayer
local KEY = "fonfindthe966i"
local VISITED = {}
local ACTIVE = false

-- get part from Part or Model
local function getPart(obj)
	if obj:IsA("BasePart") then
		return obj
	elseif obj:IsA("Model") then
		return obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart", true)
	end
end

local function collect(char)
	if not ACTIVE then return end
	local hrp = char:WaitForChild("HumanoidRootPart")
	local folder = workspace:WaitForChild("Brainrots")

	for _, obj in ipairs(folder:GetChildren()) do
		if not VISITED[obj] then
			local part = getPart(obj)
			if part then
				VISITED[obj] = true
				hrp.CFrame = part.CFrame
				task.wait(0.05)
			end
		end
	end
end

-- GUI
local gui = Instance.new("ScreenGui")
gui.ResetOnSpawn = false
gui.Parent = P.PlayerGui

-- Toggle Button (fon)
local toggle = Instance.new("TextButton", gui)
toggle.Size = UDim2.new(0,60,0,35)
toggle.Position = UDim2.new(0.5,-30,0.35,0)
toggle.BackgroundColor3 = Color3.new(0,0,0)
toggle.Text = "fon"
toggle.TextColor3 = Color3.new(1,1,1)
toggle.Font = Enum.Font.SourceSansBold
toggle.TextSize = 20
toggle.AutoButtonColor = true

-- Make toggle draggable (touch / mouse)
toggle.Active = true
toggle.Draggable = true

-- Frame (Code Menu)
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0,420,0,140)
frame.Position = UDim2.new(0.5,-210,0.45,0)
frame.BackgroundColor3 = Color3.new(0,0,0)
frame.Visible = false

-- Toggle logic
toggle.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
end)

-- Title
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,35)
title.BackgroundTransparency = 1
title.Text = "fon find the brainrot"
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 24

-- Label
local label = Instance.new("TextLabel", frame)
label.Size = UDim2.new(0,140,0,35)
label.Position = UDim2.new(0,20,0,70)
label.BackgroundTransparency = 1
label.Text = "Enter key"
label.TextColor3 = Color3.new(1,1,1)
label.Font = Enum.Font.SourceSansBold
label.TextSize = 22

-- TextBox
local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0,240,0,35)
box.Position = UDim2.new(1,-260,0,70)
box.BackgroundColor3 = Color3.new(1,1,1)
box.BackgroundTransparency = 0.75
box.Text = ""
box.TextSize = 18
box.ClearTextOnFocus = true

-- X mark
local wrong = Instance.new("TextLabel", frame)
wrong.Size = box.Size
wrong.Position = box.Position
wrong.BackgroundColor3 = Color3.fromRGB(200,0,0)
wrong.BackgroundTransparency = 0.2
wrong.Text = "✖"
wrong.TextColor3 = Color3.new(1,1,1)
wrong.Font = Enum.Font.SourceSansBold
wrong.TextSize = 30
wrong.Visible = false

-- Sound
local okSound = Instance.new("Sound", workspace)
okSound.SoundId = "rbxassetid://5419098673"
okSound.Volume = 1

-- Check key
box.FocusLost:Connect(function(enter)
	if not enter then return end

	if box.Text == KEY then
		ACTIVE = true
		okSound:Play()
		frame:Destroy()
		toggle:Destroy()

		if P.Character then
			collect(P.Character)
		end
	else
		box.Visible = false
		wrong.Visible = true
		task.wait(3)
		wrong.Visible = false
		box.Visible = true
		box.Text = ""
	end
end)

-- Resume after death
P.CharacterAdded:Connect(function(char)
	task.wait(0.5)
	collect(char)
end)