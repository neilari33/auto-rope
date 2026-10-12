function StartAutoRope()
	if TrollSettings.AutoUnrope.Connection then
		TrollSettings.AutoUnrope.Connection:Disconnect()
	end

	TrollSettings.AutoUnrope.Connection = RunService.Heartbeat:Connect(function()
		-- Sempre ativo para rodar a lógica sem precisar de ativação manual constante
		for _, plr in pairs(Players:GetPlayers()) do
			if plr ~= player and plr.Character then
				local tempStats = plr:FindFirstChild("TempPlayerStatsModule")
				if tempStats then
					local ragdoll = tempStats:FindFirstChild("Ragdoll")
					if ragdoll and ragdoll.Value then
						local myChar = player.Character
						if myChar and myChar:FindFirstChild("BeastPowers") then
							local myHrp = myChar:FindFirstChild("HumanoidRootPart")
							local targetHrp = plr.Character:FindFirstChild("HumanoidRootPart")

							if myHrp and targetHrp then
								local distance = (myHrp.Position - targetHrp.Position).Magnitude
								if distance <= RopeHelperSettings.MaxDistance then
									AutoRopePlayer(plr)
								end
							end
						end
					end
				end
			end
		end
	end)
end

-- Inicializa o Auto Rope permanentemente ativo
StartAutoRope()
