local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local frame = script.Parent

-- Configurações de Cores
local COR_ATIVADO = Color3.fromRGB(0, 255, 127)   -- Verde brilhante
local COR_DESATIVADO = Color3.fromRGB(255, 65, 65) -- Vermelho

-- [SISTEMA DE ARRASTAR O PAINEL]
local dragging, dragInput, dragStart, startPos
frame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = frame.Position
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

frame.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
end)

-- [BOTÃO: VELOCIDADE]
local velAtiva = false
frame.VelBtn.MouseButton1Click:Connect(function()
	velAtiva = not velAtiva -- Inverte o estado (Ativa/Desativa)
	local char = player.Character
	if char and char:FindFirstChild("Humanoid") then
		char.Humanoid.WalkSpeed = velAtiva and 60 or 16
		frame.VelBtn.BackgroundColor3 = velAtiva and COR_ATIVADO or COR_DESATIVADO
		frame.VelBtn.Text = "Velocidade: " .. (velAtiva and "LIGADO" or "DESLIGADO")
	end
end)

-- [BOTÃO: SUPER PULO]
local puloAtivo = false
frame.PuloBtn.MouseButton1Click:Connect(function()
	puloAtivo = not puloAtivo
	local char = player.Character
	if char and char:FindFirstChild("Humanoid") then
		char.Humanoid.JumpPower = puloAtivo and 120 or 50
		char.Humanoid.UseJumpPower = true
		frame.PuloBtn.BackgroundColor3 = puloAtivo and COR_ATIVADO or COR_DESATIVADO
		frame.PuloBtn.Text = "Super Pulo: " .. (puloAtivo and "LIGADO" or "DESLIGADO")
	end
end)

-- [BOTÃO: ESP (VER JOGADORES)]
local espAtivo = false
frame.EspBtn.MouseButton1Click:Connect(function()
	espAtivo = not espAtivo
	frame.EspBtn.BackgroundColor3 = espAtivo and COR_ATIVADO or COR_DESATIVADO
	frame.EspBtn.Text = "ESP: " .. (espAtivo and "LIGADO" or "DESLIGADO")
	
	-- Aplica o efeito em todos os outros jogadores
	for _, p in pairs(Players:GetPlayers()) do
		if p ~= player and p.Character then
			local h = p.Character:FindFirstChild("ESPHighlight")
			if not h then
				h = Instance.new("Highlight")
				h.Name = "ESPHighlight"
				h.Parent = p.Character
				h.FillColor = Color3.fromRGB(255, 0, 0)
			end
			h.Enabled = espAtivo
		end
	end
end)

-- [TECLA V PARA ABRIR/FECHAR O MENU]
UserInputService.InputBegan:Connect(function(input, processed)
	if not processed and input.KeyCode == Enum.KeyCode.V then
		frame.Visible = not frame.Visible
	end
end)
