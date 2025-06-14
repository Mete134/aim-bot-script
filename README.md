local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local aimPart = "Head"
local selectedInput = Enum.UserInputType.MouseButton1
local aiming = false

local function getClosestPlayer()
	local closestPlayer = nil
	local shortestDistance = math.huge
	local mousePos = UIS:GetMouseLocation()

	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(aimPart) and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
			local head = player.Character[aimPart]
			local screenPoint, onScreen = Camera:WorldToViewportPoint(head.Position)
			if onScreen then
				local dist = (Vector2.new(screenPoint.X, screenPoint.Y) - Vector2.new(mousePos.X, mousePos.Y)).Magnitude
				if dist < shortestDistance then
					shortestDistance = dist
					closestPlayer = head
				end
			end
		end
	end

	return closestPlayer
end

UIS.InputBegan:Connect(function(input, processed)
	if processed then return end
	if selectedInput == nil then return end

	if (input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode == selectedInput) or input.UserInputType == selectedInput then
		aiming = true
	end
end)

UIS.InputEnded:Connect(function(input, processed)
	if processed then return end
	if selectedInput == nil then return end

	if (input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode == selectedInput) or input.UserInputType == selectedInput then
		aiming = false
	end
end)

RunService.RenderStepped:Connect(function()
	if aiming then
		local target = getClosestPlayer()
		if target then
			Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position)
		end
	end
end)
