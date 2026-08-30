-- [[ TẢI THƯ VIỆN RAYFIELD - TỐI ƯU MOBILE 100% ]]
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- [[ KHỞI TẠO CỬA SỔ CHÍNH ]]
local Window = Rayfield:CreateWindow({
   Name = "🧠 Brainrot Hub v1.1",
   Icon = 0,
   LoadingTitle = "Đang tải Brainrot Hub...",
   LoadingSubtitle = "by Assistant",
   ConfigurationSaving = { Enabled = false },
   Discord = { Enabled = false },
   KeySystem = false
})

-- [[ SERVICES & LOCAL VARS ]]
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer

-- LOGIC STATE
local _G_AutoBrainrot = false
local _G_AutoMoney = false

-- HÀM CẬP NHẬT UI (bắt buộc dùng để tự động bỏ tick khi tắt toggle kia)
local function setToggleState(flag, value)
    Rayfield:SetToggle(flag, value)  -- Cập nhật giao diện
end

-- HÀM LẤY REMOTE (cache để tránh lỗi)
local function getRemotes()
    return ReplicatedStorage:WaitForChild("Remotes", 5)
end

-- HÀM LẤY NHÂN VẬT (chờ nếu chưa có)
local function getCharacter()
    local char = player.Character
    if not char or not char.Parent then
        char = player.CharacterAdded:Wait()
    end
    return char
end

-- [[ TẠO TAB CHI TIẾT ]]
local MainTab = Window:CreateTab("Auto Farm", 4483362458)

MainTab:CreateParagraph({
    Title = "⚠️ LƯU Ý",
    Content = "KHÔNG BẬT 2 TÍNH NĂNG CÙNG LÚC (sẽ tự tắt cái còn lại)."
})

-- ==========================================
-- 1. TOGGLE: AUTO FARM BRAINROT (lặp vô hạn)
-- ==========================================
local ToggleBrainrot = MainTab:CreateToggle({
   Name = "Auto Farm Brainrot",
   CurrentValue = false,
   Flag = "ToggleBrainrotFlag",
   Callback = function(Value)
      _G_AutoBrainrot = Value

      if _G_AutoBrainrot then
          -- Tắt toggle kia nếu đang bật
          if _G_AutoMoney then
              _G_AutoMoney = false
              setToggleState("ToggleMoneyFlag", false)
              Rayfield:Notify({Title = "🔄", Content = "Đã tự tắt Auto Farm Tiền!", Duration = 3})
          end

          task.spawn(function()
              local Remotes = getRemotes()
              local targetPos = Vector3.new(-27.2284851, -14.5883188, 576.082825)

              while _G_AutoBrainrot do
                  local char = getCharacter()
                  local hrp = char:WaitForChild("HumanoidRootPart")
                  local humanoid = char:WaitForChild("Humanoid")

                  -- Bật fly
                  humanoid:ChangeState(Enum.HumanoidStateType.Flying)

                  -- Bay đến đích (3s)
                  local startCFrame = hrp.CFrame
                  local targetCFrame = CFrame.new(targetPos) * (startCFrame - startCFrame.Position)
                  local tween = TweenService:Create(hrp, TweenInfo.new(3, Enum.EasingStyle.Linear), {CFrame = targetCFrame})
                  tween:Play()
                  tween.Completed:Wait()

                  if not _G_AutoBrainrot then break end

                  -- Kích nổ
                  Remotes.RequestBlastStart:FireServer()
                  task.wait(2)

                  if not _G_AutoBrainrot then break end
                  Remotes.RequestBlastRelease:FireServer(4.286670573987067)

                  -- Đợi 3s rồi lặp (tuỳ chỉnh)
                  task.wait(3)
              end

              -- Khi vòng lặp dừng, đảm bảo toggle ngoài UI đúng
              if not _G_AutoBrainrot then
                  setToggleState("ToggleBrainrotFlag", false)
                  Rayfield:Notify({Title = "⏹️", Content = "Đã dừng Auto Farm Brainrot!", Duration = 3})
              end
          end)
      end
   end,
})

-- ==========================================
-- 2. TOGGLE: AUTO FARM TIỀN (lặp vô hạn)
-- ==========================================
local ToggleMoney = MainTab:CreateToggle({
   Name = "Auto Farm Tiền",
   CurrentValue = false,
   Flag = "ToggleMoneyFlag",
   Callback = function(Value)
      _G_AutoMoney = Value

      if _G_AutoMoney then
          -- Tắt toggle kia nếu đang bật
          if _G_AutoBrainrot then
              _G_AutoBrainrot = false
              setToggleState("ToggleBrainrotFlag", false)
              Rayfield:Notify({Title = "🔄", Content = "Đã tự tắt Auto Farm Brainrot!", Duration = 3})
          end

          task.spawn(function()
              local Remotes = getRemotes()
              local targetPos = Vector3.new(135.430405, -2.39060259, 433.355438)
              local maxSlots = 40
              local delayBetweenSlots = 0.05
              local waitAfterCollect = 3

              while _G_AutoMoney do
                  local char = getCharacter()
                  local hrp = char:WaitForChild("HumanoidRootPart")
                  local humanoid = char:WaitForChild("Humanoid")

                  humanoid:ChangeState(Enum.HumanoidStateType.Flying)

                  -- Bay đến đích (2.5s)
                  local tween = TweenService:Create(hrp, TweenInfo.new(2.5, Enum.EasingStyle.Linear), {
                      CFrame = CFrame.new(targetPos) * (hrp.CFrame - hrp.Position)
                  })
                  tween:Play()
                  tween.Completed:Wait()

                  if not _G_AutoMoney then break end

                  -- Kích nổ
                  Remotes.RequestBlastStart:FireServer()
                  task.wait(2)

                  if not _G_AutoMoney then break end
                  Remotes.RequestBlastRelease:FireServer(4.286670573987067)
                  task.wait(1.5)  -- Chờ tiền xuất hiện

                  if not _G_AutoMoney then break end

                  -- Nhặt từng slot
                  for slotID = 1, maxSlots do
                      if not _G_AutoMoney then break end
                      Remotes.CollectSlot:FireServer(slotID)
                      task.wait(delayBetweenSlots)
                  end

                  if not _G_AutoMoney then break end
                  task.wait(waitAfterCollect)
              end

              -- Khi vòng lặp dừng, cập nhật UI
              if not _G_AutoMoney then
                  setToggleState("ToggleMoneyFlag", false)
                  Rayfield:Notify({Title = "⏹️", Content = "Đã dừng Auto Farm Tiền!", Duration = 3})
              end
          end)
      end
   end,
})
