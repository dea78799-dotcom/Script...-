-- [[ TẢI RAYFIELD ]]
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- [[ CỬA SỔ CHÍNH ]]
local Window = Rayfield:CreateWindow({
   Name = "🧠 Brainrot Hub v6.0",
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
local _G_AutoRebirth = false
local _G_RebirthInterval = 5

-- HÀM TIỆN
local function setToggleState(flag, value)
    Rayfield:SetToggle(flag, value)
end

local function getRemotes()
    return ReplicatedStorage:WaitForChild("Remotes", 5)
end

local function getEvents()
    return ReplicatedStorage:WaitForChild("Events", 5)
end

local function getCharacter()
    local char = player.Character
    if not char or not char.Parent then
        char = player.CharacterAdded:Wait()
    end
    return char
end

-- Hàm bay chung
local function flyTo(pos, duration)
    duration = duration or 2.5
    local char = getCharacter()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local humanoid = char:WaitForChild("Humanoid")
    humanoid:ChangeState(Enum.HumanoidStateType.Flying)
    local tween = TweenService:Create(hrp, TweenInfo.new(duration, Enum.EasingStyle.Linear), {
        CFrame = CFrame.new(pos) * (hrp.CFrame - hrp.Position)
    })
    tween:Play()
    tween.Completed:Wait()
end

-- Hàm gọi rebirth
local function requestRebirth()
    local Events = getEvents()
    local RequestRebirth = Events:FindFirstChild("RequestRebirth")
    if not RequestRebirth then
        return false
    end
    RequestRebirth:FireServer()
    return true
end

-- ============================
-- TAB 1: AUTO FARM
-- ============================
local FarmTab = Window:CreateTab("Auto Farm", 4483362458)

FarmTab:CreateParagraph({
    Title = "⚠️ LƯU Ý",
    Content = "Không bật 2 tính năng farm cùng lúc (sẽ tự tắt cái còn lại)."
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

-- ===== TÁI SINH =====
FarmTab:CreateParagraph({
    Title = "🔄 TÁI SINH (Rebirth)",
    Content = "Gửi lệnh rebirth mỗi X giây."
})

FarmTab:CreateButton({
    Name = "🔄 Tái sinh 1 lần",
    Callback = function()
        if requestRebirth() then
            Rayfield:Notify({Title = "🔄", Content = "Đã gửi lệnh tái sinh!", Duration = 3})
        else
            Rayfield:Notify({Title = "❌", Content = "Không tìm thấy RequestRebirth!", Duration = 3})
        end
    end
})

FarmTab:CreateSlider({
    Name = "Khoảng cách tái sinh (giây)",
    Range = {1, 60},
    Increment = 1,
    Suffix = "s",
    CurrentValue = 5,
    Flag = "RebirthInterval",
    Callback = function(Value)
        _G_RebirthInterval = Value
    end
})

FarmTab:CreateToggle({
    Name = "♻️ Tự động tái sinh",
    CurrentValue = false,
    Flag = "ToggleAutoRebirth",
    Callback = function(Value)
        _G_AutoRebirth = Value
        if _G_AutoRebirth then
            task.spawn(function()
                while _G_AutoRebirth do
                    requestRebirth()
                    task.wait(_G_RebirthInterval)
                end
            end)
        end
    end
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

local sellPos = Vector3.new(-78.43927, -0.374992371, 505.715668)

local function flyToSellPosition()
    flyTo(sellPos, 2.5)
    task.wait(0.3)
end

SellTab:CreateButton({
   Name = "💰 Kiểm tra giá trị Brainrot",
   Callback = function()
       local Remotes = getRemotes()
       flyToSellPosition()
       local worth = Remotes.RequestBrainrotWorth:InvokeServer()
       Rayfield:Notify({Title = "💰", Content = "Giá trị: $" .. tostring(worth or 0), Duration = 5})
   end
})

SellTab:CreateButton({
   Name = "💸 Bán 1 con đang cầm",
   Callback = function()
       local Remotes = getRemotes()
       flyToSellPosition()
       local worth = Remotes.RequestBrainrotWorth:InvokeServer()
       Remotes.RequestSellHeldBrainrot:FireServer()
       Rayfield:Notify({Title = "💸", Content = "Đã bán 1 con (giá: $" .. tostring(worth or 0) .. ")", Duration = 4})
   end
})

SellTab:CreateButton({
   Name = "🗑️ Bán tất cả trong túi",
   Callback = function()
       local Remotes = getRemotes()
       flyToSellPosition()
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
               flyToSellPosition()
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
    Content = "• Tự động bay đến NPC bán trước khi kiểm tra/bán\n• 'Bán tất cả' cũng bay đến vị trí\n• Tự động bán sẽ dừng khi hết Brainrot"
})

-- ============================
-- TAB 4: AUTO TRAIN
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

                  local aura = plr.Backpack:FindFirstChild("Aura")
                  if not aura then
                      aura = char:FindFirstChild("Aura")
                  end

                  if aura and aura.Parent == plr.Backpack then
                      humanoid:EquipTool(aura)
                      task.wait(0.2)
                  end

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

-- ============================
-- TAB 5: MUA ĐỒ (CẬP NHẬT LỚN)
-- ============================
local ShopTab = Window:CreateTab("Mua Đồ", 4483362458)

-- Vị trí mua Blast Style (cũ)
local buyPos = Vector3.new(-133.639587, -2.40998483, 523.576233)

-- Vị trí mua Aura (mới)
local shopAuraPos = Vector3.new(33.5341225, -0.37499249, 503.479218)

local function flyToBuyPosition()
    flyTo(buyPos, 2.5)
    task.wait(0.3)
end

local function flyToShopAura()
    flyTo(shopAuraPos, 2.5)
    task.wait(0.3)
end

-- Hàm mua Blast Style
local function buyStyle(styleId, styleName, toggleFlag)
    local Remotes = getRemotes()
    local BlastStyleAction = Remotes:FindFirstChild("BlastStyleAction")
    if not BlastStyleAction then
        Rayfield:Notify({Title = "❌", Content = "Không tìm thấy remote BlastStyleAction!", Duration = 4})
        setToggleState(toggleFlag, false)
        return
    end

    flyToBuyPosition()

    local success, result = pcall(function()
        return BlastStyleAction:InvokeServer({
            action = "buyCash",
            styleId = styleId
        })
    end)

    if success and result then
        if result.success == true then
            Rayfield:Notify({Title = "✅", Content = "Mua thành công " .. styleName .. "!", Duration = 5})
        else
            local reason = "Không rõ lý do"
            if result.shouldPromptGamePass then
                reason = "Cần mua GamePass hoặc thiếu tiền"
            elseif result.data and result.data.stylesById and result.data.stylesById[styleId] then
                local style = result.data.stylesById[styleId]
                if style.owned then
                    reason = "Bạn đã sở hữu style này rồi!"
                elseif not style.affordable then
                    reason = "Không đủ tiền hoặc chưa đủ điều kiện"
                else
                    reason = "Mua thất bại, vui lòng thử lại"
                end
            else
                reason = "Dữ liệu trả về không hợp lệ"
            end
            Rayfield:Notify({Title = "❌", Content = "Mua thất bại: " .. reason, Duration = 5})
        end
    else
        Rayfield:Notify({Title = "❌", Content = "Lỗi khi gọi remote: " .. tostring(success and "unknown" or "pcall failed"), Duration = 4})
    end

    setToggleState(toggleFlag, false)
end

-- ===== PHẦN 1: MUA BLAST STYLE =====
ShopTab:CreateParagraph({
    Title = "💥 MUA BLAST STYLE",
    Content = "Các style vụ nổ – bay đến shop cũ."
})

ShopTab:CreateToggle({
    Name = "🛒 Mua Blast Style Purple",
    CurrentValue = false,
    Flag = "ToggleBuyPurple",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                buyStyle("Purple", "True Purple Blast Style", "ToggleBuyPurple")
            end)
        end
    end
})

ShopTab:CreateToggle({
    Name = "🛒 Mua Blast Style Flame Arrow",
    CurrentValue = false,
    Flag = "ToggleBuyFlameArrow",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                buyStyle("FlameArrow", "Flame Arrow Blast Style", "ToggleBuyFlameArrow")
            end)
        end
    end
})

ShopTab:CreateToggle({
    Name = "🛒 Mua Blast Style Divine Slash",
    CurrentValue = false,
    Flag = "ToggleBuyDivineSlash",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                buyStyle("DivineSlash", "Divine Slash Blast Style", "ToggleBuyDivineSlash")
            end)
        end
    end
})

ShopTab:CreateToggle({
    Name = "🛒 Mua Blast Style Independence Smash",
    CurrentValue = false,
    Flag = "ToggleBuyIndependenceSmash",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                buyStyle("IndependenceSmash", "Independence Smash Blast Style", "ToggleBuyIndependenceSmash")
            end)
        end
    end
})

-- ===== PHẦN 2: MUA AURA (16 RARITY) =====
ShopTab:CreateParagraph({
    Title = "✨ MUA AURA",
    Content = "Mua Aura theo rarity – bay đến shop Aura."
})

-- Danh sách các rarity
local auraRarities = {
    "Common", "Uncommon", "Rare", "Epic", "Legendary", "Mythic",
    "Secret", "Ancient", "Celestial", "Divine", "OG", "Godly",
    "Omega", "Immortal", "End", "Everlast"
}

-- Hàm mua Aura
local function buyAura(rarity)
    local Remotes = getRemotes()
    local RequestAuraPurchase = Remotes:FindFirstChild("RequestAuraPurchase")
    if not RequestAuraPurchase then
        Rayfield:Notify({Title = "❌", Content = "Không tìm thấy RequestAuraPurchase!", Duration = 4})
        return
    end

    flyToShopAura()

    local success, result = pcall(function()
        return RequestAuraPurchase:InvokeServer(rarity)
    end)

    if success then
        Rayfield:Notify({Title = "✅", Content = "Đã mua Aura " .. rarity .. "!", Duration = 4})
    else
        Rayfield:Notify({Title = "❌", Content = "Lỗi mua Aura " .. rarity .. ": " .. tostring(result), Duration = 4})
    end
end

-- Tạo 16 nút mua Aura
for _, rarity in ipairs(auraRarities) do
    ShopTab:CreateButton({
        Name = "🛒 Mua Aura " .. rarity,
        Callback = function()
            task.spawn(function()
                buyAura(rarity)
            end)
        end
    })
end

ShopTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "• Mỗi nút mua một Aura rarity tương ứng\n• Tất cả đều bay đến vị trí shop Aura trước khi gọi remote"
})

-- ============================
-- TAB 6: VÒNG QUAY
-- ============================
local SpinTab = Window:CreateTab("Vòng Quay", 4483362458)

local spinPos = Vector3.new(105.983093, -0.318742752, 525.639038)

local function flyToSpinPosition()
    flyTo(spinPos, 2.5)
    task.wait(0.2)
end

SpinTab:CreateToggle({
    Name = "🎡 Quay vòng quay miễn phí",
    CurrentValue = false,
    Flag = "ToggleSpin",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                local Events = getEvents()
                local RequestSpin = Events:FindFirstChild("RequestSpin")
                if not RequestSpin then
                    Rayfield:Notify({Title = "❌", Content = "Không tìm thấy RequestSpin!", Duration = 4})
                    setToggleState("ToggleSpin", false)
                    return
                end

                flyToSpinPosition()

                local success, result = pcall(function()
                    return RequestSpin:InvokeServer()
                end)

                if success then
                    Rayfield:Notify({Title = "🎡", Content = "Quay thành công! Phần thưởng đã được nhận.", Duration = 5})
                else
                    Rayfield:Notify({Title = "❌", Content = "Lỗi khi quay: " .. tostring(result), Duration = 4})
                end

                setToggleState("ToggleSpin", false)
            end)
        end
    end
})

SpinTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "• Bật toggle để bay đến vị trí quay và thực hiện quay miễn phí\n• Chỉ dùng được 1 lần mỗi ngày (theo game)\n• Toggle sẽ tự tắt sau khi quay"
})

-- ============================
-- TAB 7: DỊCH CHUYỂN
-- ============================
local TeleportTab = Window:CreateTab("Dịch Chuyển", 4483362458)

local leaderboardPos = Vector3.new(170.230042, 12.0595999, 461.961761)
local strongestPlayerPos = Vector3.new(141.363464, -2.42998457, 513.962036)

TeleportTab:CreateButton({
    Name = "📊 Xem bảng xếp hạng",
    Callback = function()
        task.spawn(function()
            flyTo(leaderboardPos, 2.5)
            Rayfield:Notify({Title = "📊", Content = "Đã bay đến bảng xếp hạng!", Duration = 3})
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🏆 Xem người chơi mạnh nhất",
    Callback = function()
        task.spawn(function()
            flyTo(strongestPlayerPos, 2.5)
            Rayfield:Notify({Title = "🏆", Content = "Đã bay đến người chơi mạnh nhất!", Duration = 3})
        end)
    end
})

TeleportTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "• Bấm nút để bay đến vị trí tương ứng"
})

print("✅ Brainrot Hub v6.0 đã sẵn sàng!")
