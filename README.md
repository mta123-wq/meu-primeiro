repeat task.wait() until game:IsLoaded()
repeat task.wait() until game.Players.LocalPlayer.Character

local rs = game:GetService("ReplicatedStorage")
local placeUnit = rs.Remote:WaitForChild("PlaceUnit")
local upgradeUnit = rs.Remote:WaitForChild("UpgradeUnit")
local activateSkill = rs.Remote:FindFirstChild("ActivateSkill")
local skipWave = rs.Remote:FindFirstChild("SkipWave")
local unitsFolder = workspace:WaitForChild("Units")
local lp = game.Players.LocalPlayer

local nomeFarm = "Mestre da Grelha de Ramen"
local nomeAsaEscura = "Asa Escura"
local farmSlot = 4
local asaEscuraSlot = 1

local farmPositions = {
    Vector3.new(-15, 0, 10),
    Vector3.new(5, 0, 15),
    Vector3.new(20, 0, -10),
    Vector3.new(0, 0, -15)
}

local asaEscuraPosition = Vector3.new(-20, 0, 0)

-- Função para colocar as unidades nos slots
local function colocarUnidades()
    -- Colocar "Mestre da Grelha de Ramen"
    for _, pos in ipairs(farmPositions) do
        pcall(function() placeUnit:InvokeServer(farmSlot, pos) end)
    end

    -- Colocar "Asa Escura"
    pcall(function() placeUnit:InvokeServer(asaEscuraSlot, asaEscuraPosition) end)
end

-- Função para fazer o upgrade das unidades
local function upgradeUnidades()
    while true do
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp then
                -- Upgrade de "Mestre da Grelha de Ramen"
                if unit.Name == nomeFarm then
                    pcall(function()
                        upgradeUnit:InvokeServer(unit)
                    end)
                -- Upgrade de "Asa Escura"
                elseif unit.Name == nomeAsaEscura then
                    pcall(function()
                        upgradeUnit:InvokeServer(unit)
                    end)
                end
            end
        end
    end
end

-- Função para ativar as habilidades das "Asas Escura"
local function ativarHabilidadesAsaEscura()
    while true do
        for _, unit in ipairs(unitsFolder:GetChildren()) do
            if unit.Owner.Value == lp and unit.Name == nomeAsaEscura then
                if unit:FindFirstChild("HumanoidRootPart") then
                    pcall(function()
                        activateSkill:FireServer(unit)
                    end)
                end
            end
        end
    end
end

-- Função para pular ondas
local function skipWaveFunc()
    while true do
        pcall(function()
            skipWave:FireServer()
        end)
    end
end

-- Função para auto-start (colocar as unidades e iniciar as funções de upgrades e habilidades)
local function autoStart()
    -- Aguardar o jogo carregar completamente
    task.wait(3)  -- Espera 3 segundos para garantir que o jogo carregue antes de colocar as unidades

    -- Colocar as unidades nos slots
    colocarUnidades()

    -- Iniciar os upgrades das unidades
    task.spawn(upgradeUnidades)

    -- Ativar as habilidades das "Asas Escura"
    task.spawn(ativarHabilidadesAsaEscura)

    -- Iniciar o pular ondas
    task.spawn(skipWaveFunc)
end

-- Função para auto-replay (reiniciar a rodada após o término de uma wave)
local function autoReplay()
    while true do
        -- Verificar se a wave foi finalizada
        task.wait(5)  -- Espera 5 segundos entre as verificações de novas ondas
        if game.ReplicatedStorage:FindFirstChild("WaveInProgress") == nil then
            -- Reiniciar tudo quando a wave terminar
            pcall(function() skipWave:FireServer() end)
            task.wait(1)  -- Aguardar um pouco para reiniciar
            autoStart()  -- Chama a função autoStart novamente
        end
    end
end

-- Função principal que irá controlar o fluxo do script
local function executarScript()
    -- Iniciar o auto-start
    task.spawn(autoStart)

    -- Iniciar o auto-replay
    task.spawn(autoReplay)
end

-- Executa o script
executarScript()
