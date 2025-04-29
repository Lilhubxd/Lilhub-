-- LiL Hub Script - Inspirado no RedZ Hub | Para Delta Executor (Android)
-- Desenvolvido por segurança e teste. Interface roxa, arrastável e minimizável

local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AikaV3rm/UiLib/master/Lib.lua"))()
local Window = Library:CreateWindow("LiL Hub", Color3.fromRGB(128, 0, 128), Enum.KeyCode.RightControl)

-- Funções de utilidade
function GetSea()
    local placeId = game.PlaceId
    if placeId == 2753915549 then return "First Sea" elseif placeId == 4442272183 then return "Second Sea" elseif placeId == 7449423635 then return "Third Sea" else return "Unknown" end
end

-- FARM
local FarmTab = Window:CreateTab("Farm")
FarmTab:CreateToggle("Auto Farm", function(v) _G.AutoFarm = v end)
FarmTab:CreateToggle("Farm Maestria", function(v) _G.FarmMaestria = v end)
FarmTab:CreateSlider("Vida alvo", 1, 1000, function(v) _G.LifeTarget = v end)
FarmTab:CreateDropdown("Material", {"Leather","Scrap","Bones"}, function(mat) _G.FarmMaterial = mat end)

-- PvP
local PvPTab = Window:CreateTab("PvP")
PvPTab:CreateToggle("Auto PvP", function(v) _G.AutoPvP = v end)
PvPTab:CreateToggle("Farm Bounty", function(v) _G.AutoBounty = v end)
PvPTab:CreateToggle("Kill Aura", function(v) _G.KillAura = v end)

-- FRUTAS
local FruitTab = Window:CreateTab("Frutas")
FruitTab:CreateToggle("Auto Roletar Fruta", function(v) _G.AutoRollFruit = v end)
FruitTab:CreateToggle("Auto Armazenar Fruta", function(v) _G.AutoStoreFruit = v end)
FruitTab:CreateToggle("Auto Coletar Fruta", function(v) _G.AutoGrabFruit = v end)

-- RAID
local RaidTab = Window:CreateTab("Raid")
RaidTab:CreateDropdown("Escolher Chip", {"Flame", "Ice", "Quake", "Dark"}, function(chip) _G.RaidChip = chip end)
RaidTab:CreateToggle("Auto Raid", function(v) _G.AutoRaid = v end)
RaidTab:CreateToggle("Auto Retirar Fruta Ruim", function(v) _G.AutoDropWorstFruit = v end)

-- TELEPORTES
local TeleTab = Window:CreateTab("Teleporte")
TeleTab:CreateButton("Ir para Sea 1", function() print("Sea 1") end)
TeleTab:CreateButton("Ir para Sea 2", function() print("Sea 2") end)
TeleTab:CreateButton("Ir para Sea 3", function() print("Sea 3") end)
TeleTab:CreateButton("Ir para Templo do Tempo", function() print("Templo do Tempo") end)

-- ESP
local ESPTab = Window:CreateTab("ESP")
ESPTab:CreateToggle("ESP Frutas", function(v) _G.ESPFrutas = v end)

-- MISC
local MiscTab = Window:CreateTab("Misc")
MiscTab:CreateButton("Virar Pirata", function() game.ReplicatedStorage.Remotes.CommF_:InvokeServer("SetTeam","Pirates") end)
MiscTab:CreateButton("Virar Marinheiro", function() game.ReplicatedStorage.Remotes.CommF_:InvokeServer("SetTeam","Marines") end)
MiscTab:CreateToggle("Ativar Haki Buso", function(v) _G.AutoHaki = v end)
MiscTab:CreateToggle("Ativar Haki Observação", function(v) _G.ObsHaki = v end)
MiscTab:CreateToggle("Auto Raça V3", function(v) _G.AutoRaceV3 = v end)
MiscTab:CreateToggle("Auto Raça V4", function(v) _G.AutoRaceV4 = v end)
MiscTab:CreateButton("Remover Fumaça", function() for _,v in pairs(game:GetDescendants()) do if v:IsA("ParticleEmitter") then v:Destroy() end end end)
MiscTab:CreateToggle("Full Bright", function(v) if v then game.Lighting.Brightness = 5 else game.Lighting.Brightness = 1 end end)
MiscTab:CreateButton("Resgatar Todos Códigos", function()
    local codes = {
        "SECRET_ADMIN", "KITT_RESET", "CHANDLER", "fudd10", "fudd10_v2",
        "BIGNEWS", "THEGREATACE", "SUB2GAMERROBOT_EXP1", "SUB2GAMERROBOT_RESET1",
        "Axiore", "Sub2Daigrock", "TantaiGaming", "StrawHatMaine",
        "Sub2OfficialNoobie", "Starcodeheo", "Bluxxy", "Magicbus",
        "JCWK", "KittGaming", "Enyu_is_Pro"
    }

    for _, code in pairs(codes) do
        pcall(function()
            game:GetService("ReplicatedStorage").Remotes.Redeem:InvokeServer(code)
        end)
    end
end)

-- Funções contínuas (exemplo de execução)
spawn(function()
    while wait() do
        if _G.KillAura then
            for _,v in pairs(game:GetService("Players"):GetPlayers()) do
                if v.Character and v.Character:FindFirstChild("HumanoidRootPart") and v ~= game.Players.LocalPlayer then
                    local dist = (v.Character.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                    if dist < 20 then
                        game:GetService("VirtualInputManager"):SendMouseButtonEvent(0, 0, 0, true, game, 0)
                        wait()
                        game:GetService("VirtualInputManager"):SendMouseButtonEvent(0, 0, 0, false, game, 0)
                    end
                end
            end
        end

        -- Auto Farm (movimentação voando)
        if _G.AutoFarm then
            local mission = game:GetService("ReplicatedStorage").Remotes.Mission
            local level = game.Players.LocalPlayer.Data.Level.Value
            local missionToTake = ""
            
            -- Lógica para escolher missão baseada no nível
            if level <= 100 then
                missionToTake = "Beginner Mission"
            elseif level <= 300 then
                missionToTake = "Intermediate Mission"
            elseif level <= 600 then
                missionToTake = "Advanced Mission"
            else
                missionToTake = "Expert Mission"
            end
            
            -- Pegar missão automaticamente
            mission:InvokeServer("Accept", missionToTake)
            
            -- Executar farm em NPCs da missão
            for _, npc in pairs(game.Workspace.Mobs:GetChildren()) do
                if npc:FindFirstChild("Humanoid") then
                    -- Alvo encontrado, executar movimento "voando" até ele
                    local targetPos = npc.HumanoidRootPart.Position
                    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
                    wait(0.5)
                    -- Atacar NPC
                    game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool"):Activate()
                end
            end
        end
    end
end)
