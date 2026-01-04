local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "Vortex Hub [Elite]",
   LoadingTitle = "Iniciando Edição Elite...",
   LoadingSubtitle = "by Gemini",
   Theme = "Default"
})

-- === VARIÁVEIS ===
_G.AutoFarm = false
_G.LargarBarra = false
_G.AntiAFK = true
local barrasModificadas = {}

-- === SISTEMA ANTI-AFK ===
local VirtualUser = game:GetService("VirtualUser")
game.Players.LocalPlayer.Idled:Connect(function()
    if _G.AntiAFK then 
        VirtualUser:CaptureController() 
        VirtualUser:ClickButton2(Vector2.new()) 
    end
end)

-- === ABA PRINCIPAL ===
local MainTab = Window:CreateTab("Principal", 4483362458)

MainTab:CreateSection("Auto Farm")

MainTab:CreateToggle({
   Name = "Auto Farm (Ritmo Elite 0.1s)",
   CurrentValue = false,
   Callback = function(Value)
      _G.AutoFarm = Value
      if Value then
          task.spawn(function()
              local remote = game:GetService("ReplicatedStorage"):WaitForChild("DigControl")
              while _G.AutoFarm do
                  pcall(function()
                      local tool = game.Players.LocalPlayer.Character:FindFirstChildWhichIsA("Tool")
                      if tool then
                          -- Coleta Instantânea
                          remote:FireServer("start", tool, 0)
                          remote:FireServer("finish", tool)
                      end
                  end)
                  -- Delay de 0.1s para sincronia perfeita
                  task.wait(0.1) 
              end
          end)
      end
   end,
})

MainTab:CreateSection("Minigame")

MainTab:CreateToggle({
   Name = "Largar Barra Verde",
   CurrentValue = false,
   Callback = function(Value)
      _G.LargarBarra = Value
      if Value then
          task.spawn(function()
              while _G.LargarBarra do
                  pcall(function()
                      for _, v in pairs(game.Players.LocalPlayer.PlayerGui:GetDescendants()) do
                          -- Filtro Elite: Tons de verde (ignora o resto)
                          if v:IsA("Frame") and v.Visible and 
                             v.BackgroundColor3.G > 0.7 and 
                             v.BackgroundColor3.R < 0.4 and 
                             v.BackgroundColor3.B < 0.4 then
                              
                              if v.AbsoluteSize.X < 500 then
                                  if not barrasModificadas[v] then barrasModificadas[v] = v.Size end
                                  v.Size = UDim2.new(1, 0, v.Size.Y.Scale, v.Size.Y.Offset)
                              end
                          end
                      end
                  end)
                  task.wait(0.1)
              end
          end)
      else
          -- Restaura o tamanho original das barras
          for b, t in pairs(barrasModificadas) do if b and b.Parent then b.Size = t end end
          barrasModificadas = {}
      end
   end,
})

-- === ABA CONFIGURAÇÕES ===
local ConfigTab = Window:CreateTab("Configurações", 4483362458)

ConfigTab:CreateToggle({
   Name = "Anti-AFK Ativo",
   CurrentValue = true,
   Callback = function(Value) _G.AntiAFK = Value end,
})

ConfigTab:CreateButton({
   Name = "Destruir Script [Elite]",
   Callback = function()
      _G.AutoFarm = false
      _G.LargarBarra = false
      _G.AntiAFK = false
      for b, t in pairs(barrasModificadas) do if b and b.Parent then b.Size = t end end
      Rayfield:Destroy()
   end,
})

Rayfield:Notify({
   Title = "Vortex Hub [Elite]",
   Content = "Pronto para ser VIP!",
   Duration = 5
})
