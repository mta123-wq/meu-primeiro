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

task.wait(3.33)  -- Reduziu o tempo de espera inicial para 1/3 do original (3.33s para simular 3x de velocidade)

-- Colocar 4x "Mestre da Grelha de Ramen" no slot 4
for _, pos in ipairs(farmPositions) do
    pcall(function() placeUnit:InvokeServer(farmSlot, pos) end)
    task.wait(0.33)  -- Reduzido para 1/3 do tempo original (0.33s)
end

-- Colocar 1x "Asa Escura" no slot 1
pcall(function() placeUnit:InvokeServer(asaEscuraSlot, asaEscuraPosition) end)
task.wait(0.33)  -- Reduzido para 1/3 do tempo original (0.33s)

-- Upgrades iniciais para "Asa Escura"
task.wait(1)  -- Reduzido para 1s (1/3 do tempo original de 3s)
for _, unit in ipairs(unitsFolder:GetChildren()) do
    if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
        pcall(function() 
            upgradeUnit:InvokeServer(unit)
            task.wait(0.17)  -- Reduzido para 1/3 do tempo original (170ms)
            upgradeUnit:InvokeServer(unit)
        end)
    end
end

-- Upgrade contínuo do "Mestre da Grelha de Ramen"
task.spawn(function()
    while task.wait(1) do  -- Reduzido para 1s (1/3 do tempo original de 3s)
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
    task.wait(15)  -- Reduzido para 1/3 do tempo original de 45s
    while task.wait(1) do  -- Reduzido para 1s entre upgrades
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
    while task.wait(5) do  -- Reduzido para 5s (1/3 do tempo original de 15s)
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
                if unit:FindFirstChild("HumanoidRootPart") then
                    pcall(function() 
                        activateSkill:FireServer(unit)
                    end)
                    task.wait(math.random(333, 666) / 1000)  -- Delay reduzido para 333ms a 666ms (1/3 do tempo original)
                end
            end
        end
    end
end)

-- Skip Wave (executar de forma mais espaçada)
task.spawn(function()
    while task.wait(3.33) do  -- Reduzido para 3.33s entre skips (1/3 do tempo original de 10s)
        pcall(function()
            skipWave:FireServer()
        end)
        task.wait(math.random(667, 1333) / 1000)  -- Delay reduzido para 667ms a 1.33s entre skips (1/3 do tempo original)
    end
end)
