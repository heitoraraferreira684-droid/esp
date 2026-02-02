# esp
esp para ativar/desativar letra -- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

-- Configurações
local LOCALPLAYER = Players.LocalPlayer
local ESP_ENABLED = false -- Começa desativado
local HIGHLIGHT_COLOR = Color3.fromRGB(255, 0, 0) -- Vermelho

-- Tabela para armazenar os highlights
local highlights = {}

-- Função para ativar/desativar highlights
local function setHighlights(state)
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LOCALPLAYER and player.Character then
            local character = player.Character
            
            if state then
                -- Ativar highlight
                if not highlights[player] then
                    local hl = Instance.new("Highlight")
                    hl.FillColor = HIGHLIGHT_COLOR
                    hl.OutlineColor = Color3.new(1, 1, 1) -- Branco
                    hl.OutlineTransparency = 0.5
                    hl.FillTransparency = 0.5
                    hl.Parent = character
                    highlights[player] = hl
                end
            else
                -- Desativar highlight
                if highlights[player] then
                    highlights[player]:Destroy()
                    highlights[player] = nil
                end
            end
        end
    end
end

-- Quando um novo jogador entra
local function onPlayerAdded(player)
    player.CharacterAdded:Connect(function()
        -- Se ESP está ativado, adiciona highlight ao novo character
        if ESP_ENABLED then
            wait(0.1) -- Pequeno delay para garantir que o character carregou
            setHighlights(true)
        end
    end)
end

-- Conecta aos jogadores existentes
Players.PlayerAdded:Connect(onPlayerAdded)
for _, player in ipairs(Players:GetPlayers()) do
    onPlayerAdded(player)
end

-- Remove highlights quando um jogador sai
Players.PlayerRemoving:Connect(function(player)
    if highlights[player] then
        highlights[player]:Destroy()
        highlights[player] = nil
    end
end)

-- Tecla F para ativar/desativar
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then return end
    
    if input.KeyCode == Enum.KeyCode.F then
        ESP_ENABLED = not ESP_ENABLED
        setHighlights(ESP_ENABLED)
        
        -- Feedback visual (opcional)
        print("ESP " .. (ESP_ENABLED and "ATIVADO" or "DESATIVADO"))
    end
end)
