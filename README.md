local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local hitboxes = {}

-- 🔍 Detecta personagem (player ou NPC)
local function isCharacter(part)
	local model = part:FindFirstAncestorOfClass("Model")
	if model and model:FindFirstChildOfClass("Humanoid") then
		return true
	end
	return false
end

-- 🎯 Filtra o que mostrar
local function shouldShow(part)
	return part:IsA("BasePart") and not part:IsDescendantOf(workspace.Terrain)
end

-- 🧱 Cria hitbox visual (melhor que Part)
local function createHitbox(part)
	if not shouldShow(part) then return end
	if hitboxes[part] then return end

	local box = Instance.new("BoxHandleAdornment")
	box.Name = "HitboxVisual"
	box.Adornee = part
	box.AlwaysOnTop = true
	box.ZIndex = 10
	box.Transparency = 0.5
	box.Size = part.Size
	box.Parent = part -- 🔥 importante

	hitboxes[part] = box
end

-- 🚀 Criar para tudo que já existe
for _, obj in ipairs(workspace:GetDescendants()) do
	createHitbox(obj)
end

-- 🆕 Novos objetos
workspace.DescendantAdded:Connect(function(obj)
	createHitbox(obj)
end)

-- 🔄 Atualização contínua (cor)
RunService.RenderStepped:Connect(function()
	for part, box in pairs(hitboxes) do
		if part and part.Parent then
			box.Size = part.Size

			if isCharacter(part) then
				box.Color3 = Color3.new(0, 1, 0) -- 🟩 personagem
			else
				box.Color3 = Color3.new(1, 0, 0) -- 🟥 objeto
			end
		else
			if box then box:Destroy() end
			hitboxes[part] = nil
		end
	end
end)
