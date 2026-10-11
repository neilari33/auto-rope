local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local localPlayer = Players.LocalPlayer

-- Alcance ajustado (nível 5 / ou altere para maior se preferir)
local MAX_DISTANCE = 5 

local function GetBeast()
    for _, v in ipairs(Players:GetPlayers()) do
        local char = v.Character
        if char and char:FindFirstChild("BeastPowers") then
            return char
        end
    end
    return nil
end

local function AutoRopePlayer(targetPlayer)
    local beast = GetBeast()
    if not beast then return end

    local hammer = beast:FindFirstChild("Hammer")
    if not hammer then return end

    local hammerEvent = hammer:FindFirstChild("HammerEvent")
    local targetChar = targetPlayer.Character
    if not targetChar or not hammerEvent then return end

    local targetPart = targetChar:FindFirstChild("Torso") or targetChar:FindFirstChild("HumanoidRootPart")
    local hrp = targetChar:FindFirstChild("HumanoidRootPart")

    if targetPart and hrp then
        -- Estrutura exata extraída do código base para amarrar o jogador atingido[cite: 2]
        local args = {
            "HammerTieUp",
            targetPart,
            hrp.Position
        }
        hammerEvent:FireServer(unpack(args))
    end
end

local function monitorPlayer(plr)
    if plr == localPlayer then return end

    local function setupRagdollListener(char)
        local tempStats = plr:WaitForChild("TempPlayerStatsModule", 10)
        if not tempStats then return end

        local ragdoll = tempStats:WaitForChild("Ragdoll", 10)
        if not ragdoll then return end

        ragdoll.Changed:Connect(function(isRagdolled)
            if isRagdolled then
                local myChar = localPlayer.Character
                if not myChar or not myChar:FindFirstChild("BeastPowers") then return end

                local myHrp = myChar:FindFirstChild("HumanoidRootPart")
                local targetHrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")

                if myHrp and targetHrp then
                    local distance = (myHrp.Position - targetHrp.Position).Magnitude
                    if distance <= MAX_DISTANCE then
                        AutoRopePlayer(plr)
                    end
                end
            end
        end)
    end

    if plr.Character then
        setupRagdollListener(plr.Character)
    end
    plr.CharacterAdded:Connect(setupRagdollListener)
end

for _, p in ipairs(Players:GetPlayers()) do
    monitorPlayer(p)
end
Players.PlayerAdded:Connect(monitorPlayer)
