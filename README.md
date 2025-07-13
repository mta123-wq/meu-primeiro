repeat task.wait() until game:IsLoaded()
repeat task.wait() until game.Players.LocalPlayer.Character

local rs = game:GetService("ReplicatedStorage")
local placeUnit = rs.Remote:WaitForChild("PlaceUnit")
local upgradeUnit = rs.Remote:WaitForChild("UpgradeUnit")
local activateSkill = rs.Remote:FindFirstChild("ActivateSkill")
local skipWave = rs.Remote:FindFirstChild("SkipWave")
local unitsFolder = workspace:WaitForChild("Units")
local lp = game.Players.LocalPlayer

local nomeFarm, nomeAsaEscura = "Mestre da Grelha de Ramen", "Asa Escura"
local farmSlot = 4  -- "Mestre da Grelha de Ramen" no slot 4
local asaEscuraSlot = 1  -- "Asa Escura" no slot 1

local farmPositions = {
    Vector3.new(-15, 0, 10), 
    Vector3.new(5, 0, 15),    
    Vector3.new(20, 0, -10), 
    Vector3.new(0, 0, -15)
}

local asaEscuraPosition = Vector3.new(-20, 0, 0)  -- "Asa Escura" em uma posição específica

task.wait(5)  -- Reduziu o tempo de espera inicial para 5 segundos, para velocidade 2x

-- Colocar 4x "Mestre da Grelha de Ramen" no slot 4
for _, pos in ipairs(farmPositions) do
    pcall(function() placeUnit:InvokeServer(farmSlot, pos) end)
    task.wait(0.5)  -- Reduzido pela metade para 0.5s (velocidade 2x)
end

-- Colocar 1x "Asa Escura" no slot 1
pcall(function() placeUnit:InvokeServer(asaEscuraSlot, asaEscuraPosition) end)
task.wait(0.5)  -- Reduzido pela metade para 0.5s (velocidade 2x)

-- Upgrades iniciais para "Asa Escura"
task.wait(1.5)  -- Reduzido para 1.5s, que é metade de 3s
for _, unit in ipairs(unitsFolder:GetChildren()) do
    if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
        pcall(function() 
            upgradeUnit:InvokeServer(unit)
            task.wait(0.25)  -- Reduzido para 250ms (velocidade 2x)
            upgradeUnit:InvokeServer(unit)
        end)
    end
end

-- Upgrade contínuo do "Mestre da Grelha de Ramen"
task.spawn(function()
    while task.wait(1.5) do  -- Reduzido para 1.5s (velocidade 2x)
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp and unit.Name == nomeFarm then
                pcall(function() 
                    upgradeUnit:InvokeServer(unit)
                end)
            end
        end
    end
end)

-- Upgrade contínuo das "Asas Escura" após 45s
task.spawn(function()
    task.wait(22.5)  -- Reduzido para 22.5s, que é metade de 45s (velocidade 2x)
    while task.wait(1.5) do  -- Reduzido para 1.5s entre upgrades (velocidade 2x)
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
                pcall(function() 
                    upgradeUnit:InvokeServer(unit)
                end)
            end
        end
    end
end)

-- Ativar habilidades das "Asa Escura" (espaciado aleatoriamente)
task.spawn(function()
    while task.wait(7.5) do  -- Reduzido para 7.5s (metade de 15s)
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
                if unit:FindFirstChild("HumanoidRootPart") then
                    pcall(function() 
                        activateSkill:FireServer(unit)
                    end)
                    task.wait(math.random(500, 1000) / 1000)  -- Delay reduzido para 500ms a 1s
                end
            end
        end
    end
end)

-- Skip Wave (executar de forma mais espaçada)
task.spawn(function()
    while task.wait(5) do  -- Reduzido para 5s entre skips (velocidade 2x)
        pcall(function()
            skipWave:FireServer()
        end)
        task.wait(math.random(1000, 2000) / 1000)  -- Delay reduzido para 1s a 2s entre skips
    end
end)
