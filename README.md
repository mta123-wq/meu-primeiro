task.wait() until game:IsLoaded()
repeat task.wait() until game.Players.LocalPlayer.Character

local rs = game:GetService("ReplicatedStorage")
local placeUnit = rs.Remote:WaitForChild("PlaceUnit")
local upgradeUnit = rs.Remote:WaitForChild("UpgradeUnit")
local activateSkill = rs.Remote:FindFirstChild("ActivateSkill")
local skipWave = rs.Remote:FindFirstChild("SkipWave")
local unitsFolder = workspace:WaitForChild("Units")
local lp = game.Players.LocalPlayer

local nomeFarm, nomeAsaEscura = "Mestre da Grelha de Ramen", "Asa Escura"
local farmSlot, asaEscuraSlots = 4, {1, 2, 3, 4}
local farmPositions = {Vector3.new(-15, 0, 10), Vector3.new(5, 0, 15), Vector3.new(20, 0, -10), Vector3.new(0, 0, -15)}
local asaEscuraPositions = {Vector3.new(-20, 0, 0), Vector3.new(15, 0, -20), Vector3.new(-5, 0, -25), Vector3.new(10, 0, 10)}

task.wait(10)

-- Função para delay aleatório entre ações
local function randomDelay(min, max)
    task.wait(math.random(min, max) / 100)
end

-- Colocar 4x "Mestre da Grelha de Ramen"
for _, pos in ipairs(farmPositions) do
    pcall(function() placeUnit:InvokeServer(farmSlot, pos) end)
    randomDelay(500, 1500)  -- delay entre 0.5s a 1.5s
end

-- Colocar 4x "Asa Escura"
for i, slot in ipairs(asaEscuraSlots) do
    pcall(function() placeUnit:InvokeServer(slot, asaEscuraPositions[i]) end)
    randomDelay(500, 1500)
end

-- Upgrades iniciais para "Asa Escura"
task.wait(3)
for _, unit in ipairs(unitsFolder:GetChildren()) do
    if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
        pcall(function() 
            upgradeUnit:InvokeServer(unit)
            randomDelay(300, 700)  -- Delay entre os upgrades (300ms a 700ms)
            upgradeUnit:InvokeServer(unit)
        end)
    end
end

-- Upgrade contínuo do "Mestre da Grelha de Ramen"
task.spawn(function()
    while task.wait(3) do
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp and unit.Name == nomeFarm then
                pcall(function() 
                    upgradeUnit:InvokeServer(unit)
                end)
                randomDelay(200, 500)  -- Delay de 200ms a 500ms
            end
        end
    end
end)

-- Upgrade contínuo das "Asas Escura" após 45s
task.spawn(function()
    task.wait(45)
    while task.wait(3) do
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
                pcall(function() 
                    upgradeUnit:InvokeServer(unit)
                end)
                randomDelay(200, 500)  -- Delay entre upgrades de 200ms a 500ms
            end
        end
    end
end)

-- Ativar habilidades das "Asa Escura" (espaciado aleatoriamente)
task.spawn(function()
    while task.wait(15) do
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
                if unit:FindFirstChild("HumanoidRootPart") then
                    pcall(function() 
                        activateSkill:FireServer(unit)
                    end)
                    randomDelay(1000, 2000)  -- Delay de 1s a 2s entre habilidades
                end
            end
        end
    end
end)

-- Skip Wave (executar de forma mais espaçada)
task.spawn(function()
    while task.wait(10) do
        pcall(function()
            skipWave:FireServer()
        end)
        randomDelay(2000, 4000)  -- Delay de 2s a 4s entre skips
    end
end)
