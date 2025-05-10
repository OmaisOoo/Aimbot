local players = {}
local localPlayer = GetLocalPlayer()
local aimEnabled = false
local teamCheck = true
local aimFOV = 30

function isSameTeam(player1, player2)
    return player1.team == player2.team
end

function getClosestEnemy()
    local closest = nil
    local minDistance = aimFOV

    for _, player in ipairs(players) do
        if player ~= localPlayer and player.alive then
            if not teamCheck or not isSameTeam(localPlayer, player) then
                local dist = GetScreenDistance(player.headPosition)
                if dist < minDistance then
                    minDistance = dist
                    closest = player
                end
            end
        end
    end

    return closest
end

function aimAt(target)
    if not target then return end
    AimAtPosition(target.headPosition)
end

function updateAimbot()
    if not aimEnabled then return end
    local target = getClosestEnemy()
    if target then
        aimAt(target)
    end
end

function setAimbotSettings(enabled, checkTeam, fov)
    aimEnabled = enabled
    teamCheck = checkTeam
    aimFOV = fov
end

function buildAimbotUI()
    CreateSimpleUI({
        title = "Aimbot Test Tool",
        elements = {
            {type = "checkbox", label = "Ativar Aimbot", onChange = function(val) aimEnabled = val end},
            {type = "checkbox", label = "Verificação de Time", onChange = function(val) teamCheck = val end},
            {type = "slider", label = "Campo de Visão", min = 10, max = 100, onChange = function(val) aimFOV = val end},
        }
    })
end
