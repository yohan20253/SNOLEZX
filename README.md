local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Highlight = Instance.new("Highlight")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

local aimbotEnabled = false
local espEnabled = false

-- Armazena ESPs para remover depois
local activeESP = {}

-- Função para ativar/desativar ESP
local function toggleESP()
	espEnabled = not espEnabled

	for _, esp in pairs(activeESP) do
		esp:Destroy()
	end
	activeESP = {}

	if espEnabled then
		for _, target in ipairs(Players:GetPlayers()) do
			if target ~= player and target.Team ~= player.Team and target.Character then
				local esp = Instance.new("Highlight")
				esp.Name = "ESP_Highlight"
				esp.Adornee = target.Character
				esp.FillColor = Color3.new(1, 0, 0) -- vermelho
				esp.FillTransparency = 0.5
				esp.OutlineColor = Color3.new(1, 0, 0)
				esp.OutlineTransparency = 0
				esp.Parent = target.Character
				table.insert(activeESP, esp)
			end
		end
	end

	print("ESP:", espEnabled and "Ativado" or "Desativado")
end

-- Função para pegar o inimigo mais centralizado visível
local function getClosestEnemyOnScreen()
	local closest = nil
	local shortestDistance = math.huge
	local screenCenter = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)

	for _, target in ipairs(Players:GetPlayers()) do
		if target ~= player and target.Team ~= player.Team and target.Character then
			local head = target.Character:FindFirstChild("Head")
			if head then
				local screenPos, onScreen = camera:WorldToScreenPoint(head.Position)
				if onScreen then
					local dist = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
					if dist < shortestDistance then
						shortestDistance = dist
						closest = head
					end
				end
