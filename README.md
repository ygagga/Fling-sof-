local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/jensonhirst/Orion/main/source')))()
local Window = OrionLib:MakeWindow({Name = "Brookhaven Fling & Spectate", HidePremium = false, SaveConfig = false})

local Tab = Window:MakeTab({Name = "Troll", Icon = "rbxassetid://4483345998", PremiumOnly = false})

local PlayersList = {}
for _, v in ipairs(game:GetService("Players"):GetPlayers()) do
    if v ~= game.Players.LocalPlayer then
        table.insert(PlayersList, v.Name)
    end
end

local SelectedPlayer = nil
Tab:AddDropdown({
    Name = "Select Player",
    Options = PlayersList,
    Callback = function(Value)
        SelectedPlayer = Value
    end
})

Tab:AddButton({
    Name = "Fling Player",
    Callback = function()
        if SelectedPlayer then
            local Target = game.Players:FindFirstChild(SelectedPlayer)
            local Player = game.Players.LocalPlayer
            if Target and Target.Character and Target.Character:FindFirstChild("HumanoidRootPart") and Player and Player.Character then
                local Root = Player.Character.HumanoidRootPart
                
                -- Teleportar para o local inicial
                Root.CFrame = CFrame.new(-82.61, 18.98, -129.97)
                task.wait(0.5)
                
                -- Equipar o sofá automaticamente
                local Backpack = Player.Backpack
                for _, Item in ipairs(Backpack:GetChildren()) do
                    if Item:IsA("Tool") and Item.Name:lower():find("sofa") then
                        Player.Character.Humanoid:EquipTool(Item)
                        break
                    end
                end
                task.wait(0.5)
                
                -- Fling girando como louco
                task.spawn(function()
                    while Target and Target.Character do
                        Root.CFrame = Target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -2)
                        Root.Velocity = Vector3.new(math.random(-500, 500), 500, math.random(-500, 500))
                        task.wait(0.1)
                        
                        -- Se o jogador sentar, teleportar os dois para o void e soltar o sofá
                        if Target.Character:FindFirstChildOfClass("Seat") and Target.Character:FindFirstChildOfClass("Seat").Occupant then
                            Root.CFrame = CFrame.new(0, -500, 0) -- Teleporta o executor para o void
                            Target.Character:MoveTo(Vector3.new(0, -500, 0)) -- Teleporta o alvo para o void
                            task.wait(0.2)
                            
                            -- Soltar o sofá
                            Player.Character.Humanoid:UnequipTools()
                            
                            -- Voltar para o local inicial
                            task.wait(0.2)
                            Root.CFrame = CFrame.new(-82.61, 18.98, -129.97)
                            break
                        end
                    end
                end)
            end
        end
    end
})

Tab:AddButton({
    Name = "Spectate Player",
    Callback = function()
        if SelectedPlayer then
            local Target = game.Players:FindFirstChild(SelectedPlayer)
            if Target and Target.Character then
                game.Workspace.CurrentCamera.CameraSubject = Target.Character.Humanoid
            end
        end
    end
})

Tab:AddButton({
    Name = "Stop Spectating",
    Callback = function()
        game.Workspace.CurrentCamera.CameraSubject = game.Players.LocalPlayer.Character.Humanoid
    end
})

OrionLib:Init()
