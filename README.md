local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Terrain = workspace:FindFirstChildOfClass("Terrain")

local player = Players.LocalPlayer

--------------------------------------------------
-- FPS GUI
--------------------------------------------------

local gui = Instance.new("ScreenGui")
gui.Name = "FPSCounter"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local label = Instance.new("TextLabel")
label.Parent = gui

label.Size = UDim2.new(0,180,0,30)
label.Position = UDim2.new(1,-190,1,-45)

label.BackgroundColor3 = Color3.fromRGB(20,20,20)
label.BackgroundTransparency = 0.25

label.TextColor3 = Color3.new(1,1,1)

label.TextScaled = false
label.TextSize = 20

label.Font = Enum.Font.SourceSansBold
label.Text = "FPS : 0"

local corner = Instance.new("UICorner")
corner.Parent = label

local stroke = Instance.new("UIStroke")
stroke.Parent = label

--------------------------------------------------
-- FPS SYSTEM
--------------------------------------------------

task.spawn(function()

	local fps = 0
	local last = tick()

	RunService.RenderStepped:Connect(function()

		fps += 1

		if tick() - last >= 1 then

			label.Text = "FPS : "..fps

			if fps >= 50 then

				label.TextColor3 =
					Color3.fromRGB(0,255,0)

			elseif fps >= 30 then

				label.TextColor3 =
					Color3.fromRGB(255,255,0)

			else

				label.TextColor3 =
					Color3.fromRGB(255,0,0)

			end

			fps = 0
			last = tick()

		end
	end)
end)

--------------------------------------------------
-- FULL BRIGHT + REMOVE FOG
--------------------------------------------------

Lighting.Brightness = 4
Lighting.ClockTime = 14
Lighting.FogStart = 999999
Lighting.FogEnd = 999999
Lighting.GlobalShadows = false

Lighting.Ambient = Color3.new(1,1,1)
Lighting.OutdoorAmbient = Color3.new(1,1,1)

--------------------------------------------------
-- LOW GRAPHICS
--------------------------------------------------

settings().Rendering.QualityLevel =
	Enum.QualityLevel.Level01

for _,v in pairs(game:GetDescendants()) do

	task.wait()

	--------------------------------------------------
	-- REMOVE EFFECTS
	--------------------------------------------------

	if v:IsA("ParticleEmitter")
	or v:IsA("Trail")
	or v:IsA("Smoke")
	or v:IsA("Fire")
	or v:IsA("Sparkles") then

		v:Destroy()
	end

	--------------------------------------------------
	-- REMOVE TEXTURES
	--------------------------------------------------

	if v:IsA("Texture")
	or v:IsA("Decal") then

		v:Destroy()
	end

	--------------------------------------------------
	-- REMOVE LIGHTING EFFECTS
	--------------------------------------------------

	if v:IsA("BloomEffect")
	or v:IsA("BlurEffect")
	or v:IsA("SunRaysEffect")
	or v:IsA("ColorCorrectionEffect")
	or v:IsA("DepthOfFieldEffect")
	or v:IsA("Atmosphere")
	or v:IsA("Sky") then

		v:Destroy()
	end

	--------------------------------------------------
	-- REMOVE ACCESSORIES
	--------------------------------------------------

	if v:IsA("Accessory") then
		v:Destroy()
	end

	--------------------------------------------------
	-- OPTIMIZE PARTS
	--------------------------------------------------

	if v:IsA("BasePart") then

		v.Material = Enum.Material.Plastic
		v.CastShadow = false
		v.Reflectance = 0

		if v:IsA("MeshPart") then
			v.TextureID = ""
		end
	end
end

--------------------------------------------------
-- TERRAIN OPTIMIZATION
--------------------------------------------------

if Terrain then

	Terrain.WaterWaveSize = 0
	Terrain.WaterWaveSpeed = 0
	Terrain.WaterReflectance = 0
	Terrain.WaterTransparency = 1

end

--------------------------------------------------
-- HIDE FAR OBJECTS
--------------------------------------------------

task.spawn(function()

	while task.wait(2) do

		local char = player.Character

		if char and char:FindFirstChild("HumanoidRootPart") then

			local root = char.HumanoidRootPart

			for _,v in pairs(workspace:GetDescendants()) do

				if v:IsA("BasePart") then

					local distance =
						(root.Position - v.Position).Magnitude

					if distance > 300 then
						v.LocalTransparencyModifier = 1
					end
				end
			end
		end
	end
end)

--------------------------------------------------
-- ZOMBIE OPTIMIZER
--------------------------------------------------

task.spawn(function()

	while task.wait(3) do

		-- Tự tìm folder zombie phổ biến
		local folder =
			workspace:FindFirstChild("Zombies")
			or workspace:FindFirstChild("Enemies")
			or workspace:FindFirstChild("NPCs")
			or workspace

		for _,v in pairs(folder:GetDescendants()) do

			if v:IsA("Model")
			and v:FindFirstChild("Humanoid") then

				-- Bỏ qua player
				if not Players:GetPlayerFromCharacter(v) then

					for _,x in pairs(v:GetDescendants()) do

						--------------------------------------------------
						-- REMOVE ACCESSORIES
						--------------------------------------------------

						if x:IsA("Accessory") then
							x:Destroy()
						end

						--------------------------------------------------
						-- REMOVE PARTICLES
						--------------------------------------------------

						if x:IsA("ParticleEmitter")
						or x:IsA("Trail")
						or x:IsA("Smoke")
						or x:IsA("Fire") then

							x:Destroy()
						end

						--------------------------------------------------
						-- OPTIMIZE ZOMBIE PARTS
						--------------------------------------------------

						if x:IsA("BasePart") then

							x.Material =
								Enum.Material.Plastic

							x.CastShadow = false
							x.Reflectance = 0

							-- Zombie xa sẽ mờ hơn
							if x.Name ~= "HumanoidRootPart" then

								local char =
									player.Character

								if char
								and char:FindFirstChild(
									"HumanoidRootPart") then

									local dist =
									(
									char.HumanoidRootPart.Position
									- x.Position
									).Magnitude

									if dist > 120 then

										x.LocalTransparencyModifier =
											0.6
									end
								end
							end
						end
					end
				end
			end
		end
	end
end)

--------------------------------------------------
-- MEMORY CLEAN
--------------------------------------------------

collectgarbage("collect")

print("EXTREME FPS BOOST LOADED")
