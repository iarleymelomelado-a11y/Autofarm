--[[
Auto Mine Script for Roblox
By: iarleymelomelado-a11y

Esse script teleporta o jogador até "coal vein" e "gem vein" no workspace e usa a "Pickaxe" para minerar automaticamente.
]]

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

-- Função para encontrar a Pickaxe no inventário
function getPickaxe()
    for _, item in ipairs(player.Backpack:GetChildren()) do
        if item.Name:lower():find("pickaxe") then
            return item
        end
    end
    for _, item in ipairs(character:GetChildren()) do
        if item.Name:lower():find("pickaxe") then
            return item
        end
    end
    return nil
end

-- Função para teletransportar
function teleportTo(pos)
    humanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0, 2, 0))
end

-- Função para minerar
function mineVein(vein)
    local pickaxe = getPickaxe()
    if pickaxe then
        if pickaxe.Parent ~= character then
            pickaxe.Parent = character
        end
        teleportTo(vein.Position)
        -- Tenta ativar a Pickaxe na vein (para maioria dos jogos)
        local toolEvent = pickaxe:FindFirstChildWhichIsA("RemoteEvent") or pickaxe:FindFirstChildWhichIsA("RemoteFunction")
        if toolEvent then
            toolEvent:FireServer(vein)
        else
            pickaxe:Activate()
        end
        -- Aguarda um pouco para não bugar
        wait(0.5)
    end
end

-- Loop principal
while true do
    for _, obj in ipairs(workspace:GetDescendants()) do
        if (obj.Name:lower():find("coal vein") or obj.Name:lower():find("gem vein"))
        and obj:IsA("BasePart") and obj.Parent then
            mineVein(obj)
            wait(0.3)
        end
    end
    wait(2)
end
