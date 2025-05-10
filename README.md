localPlayer = GetLocalPlayer()
aimEnabled = false
teamCheck = true
aimFOV = 50

function isSameTeam(objeto)
    return objeto.team == localPlayer.team
end

function getClosestObject()
    local closest = nil
    local minDistance = aimFOV

    for _, obj in ipairs(objetosComHitbox) do
        if obj.isActive then
            if not teamCheck or not isSameTeam(obj) then
                local dist = GetScreenDistance(obj.hitbox.center)
                if dist < minDistance then
                    minDistance = dist
                    closest = obj
                end
            end
        end
    end

    return closest
end

function aimAtObject(obj)
    if not obj then return end
    AimAtPosition(obj.hitbox.center)
end

function updateAimbot()
    if not aimEnabled then return end
    local alvo = getClosestObject()
    if alvo then
        aimAtObject(alvo)
    end
end

function criarInterface()
    CreateSimpleUI({
        title = "Aimbot Mobile",
        elements = {
            {type = "checkbox", label = "Ativar", onChange = function(v) aimEnabled = v end},
            {type = "checkbox", label = "Team Check", onChange = function(v) teamCheck = v end},
            {type = "slider", label = "FOV", min = 20, max = 100, onChange = function(v) aimFOV = v end},
        }
    })
end
