-- [[ TẢI RAYFIELD ]]
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- [[ CỬA SỔ CHÍNH ]]
local Window = Rayfield:CreateWindow({
   Name = "🧠 Brainrot Hub v4.0",
   Icon = 0,
   LoadingTitle = "Đang tải...",
   LoadingSubtitle = "by Assistant",
   ConfigurationSaving = { Enabled = false },
   Discord = { Enabled = false },
   KeySystem = false
})

-- [[ SERVICES ]]
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player = Players.LocalPlayer

-- STATE
local _G_AutoBrainrot = false
local _G_AutoMoney = false
local _G_UpgradeLevel = 2
local _G_AutoTrain = false
local _G_TrainInterval = 0.1

-- HÀM TIỆN
local function setToggleState(flag, value)
    Rayfield:SetToggle(flag, value)
end

local function getRemotes()
    return ReplicatedStorage:WaitForChild("Remotes", 5)
end

local function getCharacter()
    local char = player.Character
    if not char or not char.Parent then
        char = player.CharacterAdded:Wait()
    end
    return char
end

-- ============================
-- TAB 1: AUTO FARM
-- ============================
local FarmTab = Window:CreateTab("Auto Farm", 4483362458)

FarmTab:CreateParagraph({
    Title = "⚠️ LƯU Ý",
    Content = "Không bật 2 tính năng cùng lúc (sẽ tự tắt cái còn lại)."
})

-- Auto Brainrot
FarmTab:CreateToggle({
   Name = "Auto Farm Brainrot (25s)",
   CurrentValue = false,
   Flag = "ToggleBrainrotFlag",
   Callback = function(Value)
      _G_AutoBrainrot = Value
      if _G_AutoBrainrot then
          if _G_AutoMoney then
              _G_AutoMoney = false
              setToggleState("ToggleMoneyFlag", false)
              Rayfield:Notify({Title = "🔄", Content = "Đã tắt Auto Farm Tiền!", Duration = 3})
          end
          task.spawn(function()
              local Remotes = getRemotes()
              local targetPos = Vector3.new(-27.2284851, -14.5883188, 576.082825)
              while _G_AutoBrainrot do
                  local char = getCharacter()
                  local hrp = char:WaitForChild("HumanoidRootPart")
                  local humanoid = char:WaitForChild("Humanoid")
                  humanoid:ChangeState(Enum.HumanoidStateType.Flying)
                  local tween = TweenService:Create(hrp, TweenInfo.new(3, Enum.EasingStyle.Linear), {
                      CFrame = CFrame.new(targetPos) * (hrp.CFrame - hrp.Position)
                  })
                  tween:Play()
                  tween.Completed:Wait()
                  if not _G_AutoBrainrot then break end
                  Remotes.RequestBlastStart:FireServer()
                  task.wait(2)
                  if not _G_AutoBrainrot then break end
                  Remotes.RequestBlastRelease:FireServer(4.286670573987067)
                  task.wait(25)
              end
              if not _G_AutoBrainrot then
                  setToggleState("ToggleBrainrotFlag", false)
                  Rayfield:Notify({Title = "⏹️", Content = "Đã dừng Auto Farm Brainrot!", Duration = 3})
              end
          end)
      end
   end,
})

-- Auto Money
FarmTab:CreateToggle({
   Name = "Auto Farm Tiền",
   CurrentValue = false,
   Flag = "ToggleMoneyFlag",
   Callback = function(Value)
      _G_AutoMoney = Value
      if _G_AutoMoney then
          if _G_AutoBrainrot then
              _G_AutoBrainrot = false
              setToggleState("ToggleBrainrotFlag", false)
              Rayfield:Notify({Title = "🔄", Content = "Đã tắt Auto Farm Brainrot!", Duration = 3})
          end
          task.spawn(function()
              local Remotes = getRemotes()
              local targetPos = Vector3.new(135.430405, -2.39060259, 433.355438)
              while _G_AutoMoney do
                  local char = getCharacter()
                  local hrp = char:WaitForChild("HumanoidRootPart")
                  local humanoid = char:WaitForChild("Humanoid")
                  humanoid:ChangeState(Enum.HumanoidStateType.Flying)
                  local tween = TweenService:Create(hrp, TweenInfo.new(2.5, Enum.EasingStyle.Linear), {
                      CFrame = CFrame.new(targetPos) * (hrp.CFrame - hrp.Position)
                  })
                  tween:Play()
                  tween.Completed:Wait()
                  if not _G_AutoMoney then break end
                  Remotes.RequestBlastStart:FireServer()
                  task.wait(2)
                  if not _G_AutoMoney then break end
                  Remotes.RequestBlastRelease:FireServer(4.286670573987067)
                  task.wait(1.5)
                  if not _G_AutoMoney then break end
                  for slotID = 1, 40 do
                      if not _G_AutoMoney then break end
                      Remotes.CollectSlot:FireServer(slotID)
                      task.wait(0.05)
                  end
                  if not _G_AutoMoney then break end
                  task.wait(3)
              end
              if not _G_AutoMoney then
                  setToggleState("ToggleMoneyFlag", false)
                  Rayfield:Notify({Title = "⏹️", Content = "Đã dừng Auto Farm Tiền!", Duration = 3})
              end
          end)
      end
   end,
})

-- ============================
-- TAB 2: NÂNG CẤP
-- ============================
local UpgradeTab = Window:CreateTab("Nâng Cấp", 4483362458)

UpgradeTab:CreateSlider({
   Name = "Chọn Level (1-40)",
   Range = {1, 40},
   Increment = 1,
   Suffix = "",
   CurrentValue = 2,
   Flag = "UpgradeLevel",
   Callback = function(Value)
       _G_UpgradeLevel = Value
       Rayfield:Notify({Title = "📊", Content = "Level: " .. Value, Duration = 2})
   end
})

UpgradeTab:CreateButton({
   Name = "⬆️ Nâng cấp 1 lần",
   Callback = function()
       local Remotes = getRemotes()
       Remotes.RequestUpgradeBrainrotLevel:FireServer(_G_UpgradeLevel)
       Rayfield:Notify({Title = "⬆️", Content = "Đã nâng cấp Level " .. _G_UpgradeLevel, Duration = 3})
   end
})

UpgradeTab:CreateButton({
   Name = "🔄 Tự động nâng cấp (0.1s/lần)",
   Callback = function()
       local Remotes = getRemotes()
       local isRunning = true
       local count = 0
       local stopBtn = UpgradeTab:CreateButton({
           Name = "⏹️ DỪNG NÂNG CẤP",
           Callback = function()
               isRunning = false
               Rayfield:Notify({Title = "⏹️", Content = "Đã dừng sau " .. count .. " lần!", Duration = 3})
           end
       })
       task.spawn(function()
           while isRunning do
               Remotes.RequestUpgradeBrainrotLevel:FireServer(_G_UpgradeLevel)
               count = count + 1
               task.wait(0.1)
           end
       end)
       Rayfield:Notify({Title = "🔄", Content = "Đang nâng cấp Level " .. _G_UpgradeLevel .. "...", Duration = 3})
   end
})

-- ============================
-- TAB 3: BÁN BRAINROT
-- ============================
local SellTab = Window:CreateTab("Bán Brainrot", 4483362458)

SellTab:CreateButton({
   Name = "💸 Bán 1 con đang cầm",
   Callback = function()
       local Remotes = getRemotes()
       local worth = Remotes.RequestBrainrotWorth:InvokeServer()
       Remotes.RequestSellHeldBrainrot:FireServer()
       Rayfield:Notify({Title = "💸", Content = "Đã bán 1 con (giá: $" .. tostring(worth or 0) .. ")", Duration = 4})
   end
})

SellTab:CreateButton({
   Name = "🗑️ Bán tất cả trong túi",
   Callback = function()
       local Remotes = getRemotes()
       Remotes.RequestSellInventoryBrainrots:FireServer()
       Rayfield:Notify({Title = "🗑️", Content = "Đã bán tất cả!", Duration = 3})
   end
})

SellTab:CreateButton({
   Name = "🔄 Tự động bán 1 con (0.5s/lần)",
   Callback = function()
       local Remotes = getRemotes()
       local isRunning = true
       local count = 0
       local stopBtn = SellTab:CreateButton({
           Name = "⏹️ DỪNG BÁN",
           Callback = function()
               isRunning = false
               Rayfield:Notify({Title = "⏹️", Content = "Đã dừng sau " .. count .. " lần!", Duration = 3})
           end
       })
       task.spawn(function()
           while isRunning do
               local worth = Remotes.RequestBrainrotWorth:InvokeServer()
               if worth and worth > 0 then
                   Remotes.RequestSellHeldBrainrot:FireServer()
                   count = count + 1
               else
                   Rayfield:Notify({Title = "⚠️", Content = "Hết Brainrot!", Duration = 3})
                   break
               end
               task.wait(0.5)
           end
       end)
       Rayfield:Notify({Title = "🔄", Content = "Đang bán tự động...", Duration = 3})
   end
})

SellTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "• Phải cầm Brainrot trên tay mới bán được từng con\n• 'Bán tất cả' bán toàn bộ trong túi\n• Tự động bán sẽ dừng khi hết Brainrot"
})

-- ============================
-- TAB 4: AUTO TRAIN (AURA)
-- ============================
local TrainTab = Window:CreateTab("Auto Train", 4483362458)

TrainTab:CreateParagraph({
    Title = "⚡ AUTO TRAIN AURA",
    Content = "Tự động đeo Aura và gửi gói dữ liệu random để train."
})

TrainTab:CreateToggle({
   Name = "Bật Auto Train Aura",
   CurrentValue = false,
   Flag = "ToggleTrainFlag",
   Callback = function(Value)
      _G_AutoTrain = Value

      if _G_AutoTrain then
          task.spawn(function()
              local plr = game.Players.LocalPlayer
              local remote = game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("AuraMultiplierClicked")

              while _G_AutoTrain do
                  -- Xử lý nhân vật
                  local char = plr.Character
                  if not char or not char.Parent then
                      char = plr.CharacterAdded:Wait()
                  end
                  local humanoid = char:WaitForChild("Humanoid")
                  local hrp = char:WaitForChild("HumanoidRootPart")
                  if not hrp or not humanoid then
                      task.wait(0.5)
                      break
                  end

                  -- Tìm Aura
                  local aura = plr.Backpack:FindFirstChild("Aura")
                  if not aura then
                      aura = char:FindFirstChild("Aura")
                  end

                  if aura and aura.Parent == plr.Backpack then
                      humanoid:EquipTool(aura)
                      task.wait(0.2)
                  end

                  -- Gửi gói train
                  if remote then
                      local id = math.random(10000000000, 99999999999)
                      local float = math.random() * 10000
                      local floatStr = string.format("%.14f", float)
                      local int = math.random(1, 100000)
                      local args = {
                          tostring(id) .. ":" .. floatStr .. ":" .. tostring(int)
                      }
                      remote:FireServer(unpack(args))
                  end

                  task.wait(_G_TrainInterval)
              end

              if not _G_AutoTrain then
                  Rayfield:SetToggle("ToggleTrainFlag", false)
                  Rayfield:Notify({Title = "⏹️", Content = "Đã dừng Auto Train!", Duration = 3})
              end
          end)
      end
   end,
})

TrainTab:CreateSlider({
   Name = "Tốc độ gửi (giây)",
   Range = {0.01, 1},
   Increment = 0.01,
   Suffix = "s",
   CurrentValue = 0.1,
   Flag = "TrainSpeed",
   Callback = function(Value)
       _G_TrainInterval = Value
       Rayfield:Notify({Title = "⚡", Content = "Tốc độ: " .. Value .. "s", Duration = 2})
   end
})

print("✅ Brainrot Hub v4.0 đã sẵn sàng!")
