local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

local AimEnabled = false
local TeamCheck = true
local AimFOV = 100

-- Usa posição da câmera como referência em mobile (Delta não tem Mouse.X/Y)
local function getClosestTarget()
    local closest = nil
    local shortestDistance = AimFOV

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            if not TeamCheck or player.Team ~= LocalPlayer.Team then
                local pos = player.Character.HumanoidRootPart.Position
                local screenPos, onScreen = Camera:WorldToViewportPoint(pos)
                local center = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

                if onScreen then
                    local dist = (Vector2.new(screenPos.X, screenPos.Y) - center).Magnitude
                    if dist < shortestDistance then
                        shortestDistance = dist
                        closest = player
                    end
                end
            end
        end
    end

    return closest
end

local function aimAt(target)
    if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
    end
end

RunService.RenderStepped:Connect(function()
    if AimEnabled then
        local target = getClosestTarget()
        if target then
            aimAt(target)
        end
    end
end)

-- Interface Delta-friendly (sem Tween, sem UI complexa)
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.ResetOnSpawn = false
gui.Name = "DeltaAimbotUI"

local toggle = Instance.new("TextButton", gui)
toggle.Size = UDim2.new(0, 120, 0, 40)
toggle.Position = UDim2.new(0, 10, 0, 10)
toggle.Text = "Aimbot: OFF"
toggle.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
toggle.TextColor3 = Color3.new(1, 1, 1)
toggle.TextScaled = true

toggle.MouseButton1Click:Connect(function()
    AimEnabled = not AimEnabled
    toggle.Text = AimEnabled and "Aimbot: ON" or "Aimbot: OFF"
    toggle.BackgroundColor3 = AimEnabled and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(255, 0, 0)
end)
