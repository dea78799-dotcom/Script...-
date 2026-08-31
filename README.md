-- [[ TẢI RAYFIELD ]]
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- [[ CỬA SỔ CHÍNH ]]
local Window = Rayfield:CreateWindow({
   Name = "🧠 Brainrot Hub v7.0",
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
local _G_AutoLuckyBlock = false
local _G_LuckyBlockInterval = 5
local _G_IsTeleporting = false
local _G_AutoBuyTokens = false
local _G_AutoFarmEvent = false
local _G_AutoFarmEvent2 = false

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

-- Hàm bay chung (có khóa)
local function flyTo(pos, duration)
    if _G_IsTeleporting then
        Rayfield:Notify({Title = "⛔", Content = "Đang bay, vui lòng đợi!", Duration = 2})
        return false
    end
    _G_IsTeleporting = true
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
    _G_IsTeleporting = false
    return true
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

-- Hàm mở Lucky Block
local function triggerLuckyBlock()
    local Remotes = getRemotes()
    local luckyRemote = Remotes:FindFirstChild("LuckyBlockRevealFX")
    if not luckyRemote then
        return false
    end
    pcall(function()
        luckyRemote:FireServer({ state = "rouletteStart", duration = 3 })
    end)
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

-- ===== LUCKY BLOCK =====
FarmTab:CreateParagraph({
    Title = "🎲 LUCKY BLOCK",
    Content = "Mở lucky block Brainrot (gửi remote)."
})

FarmTab:CreateButton({
    Name = "🎲 Mở Lucky Block 1 lần",
    Callback = function()
        if triggerLuckyBlock() then
            Rayfield:Notify({Title = "🎲", Content = "Đã gửi yêu cầu mở Lucky Block!", Duration = 3})
        end
    end
})

FarmTab:CreateSlider({
    Name = "Khoảng cách tự động mở (giây)",
    Range = {1, 60},
    Increment = 1,
    Suffix = "s",
    CurrentValue = 5,
    Flag = "LuckyBlockInterval",
    Callback = function(Value)
        _G_LuckyBlockInterval = Value
    end
})

FarmTab:CreateToggle({
    Name = "♻️ Tự động mở Lucky Block",
    CurrentValue = false,
    Flag = "ToggleAutoLuckyBlock",
    Callback = function(Value)
        _G_AutoLuckyBlock = Value
        if _G_AutoLuckyBlock then
            task.spawn(function()
                while _G_AutoLuckyBlock do
                    triggerLuckyBlock()
                    task.wait(_G_LuckyBlockInterval)
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
-- TAB 5: MUA ĐỒ
-- ============================
local ShopTab = Window:CreateTab("Mua Đồ", 4483362458)

local buyPos = Vector3.new(-133.639587, -2.40998483, 523.576233)
local shopAuraPos = Vector3.new(33.5341225, -0.37499249, 503.479218)

local function flyToBuyPosition()
    flyTo(buyPos, 2.5)
    task.wait(0.3)
end

local function flyToShopAura()
    flyTo(shopAuraPos, 2.5)
    task.wait(0.3)
end

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

-- Blast Styles
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

-- Aura
ShopTab:CreateParagraph({
    Title = "✨ MUA AURA",
    Content = "Mua Aura theo rarity – bay đến shop Aura."
})

local auraRarities = {
    "Common", "Uncommon", "Rare", "Epic", "Legendary", "Mythic",
    "Secret", "Ancient", "Celestial", "Divine", "OG", "Godly",
    "Omega", "Immortal", "End", "Everlast"
}

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
local brainrotFreePos = Vector3.new(-158.893799, -0.352245808, 536.106323)

local function flyToSpinPosition()
    flyTo(spinPos, 2.5)
    task.wait(0.2)
end

local function flyToBrainrotFree()
    flyTo(brainrotFreePos, 2.5)
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

SpinTab:CreateToggle({
    Name = "🎁 Lấy Brainrot Free",
    CurrentValue = false,
    Flag = "ToggleBrainrotFree",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                local Remotes = getRemotes()
                local RequestGroupJoinReward = Remotes:FindFirstChild("RequestGroupJoinReward")
                if not RequestGroupJoinReward then
                    Rayfield:Notify({Title = "❌", Content = "Không tìm thấy RequestGroupJoinReward!", Duration = 4})
                    setToggleState("ToggleBrainrotFree", false)
                    return
                end

                flyToBrainrotFree()
                RequestGroupJoinReward:FireServer()
                Rayfield:Notify({Title = "🎁", Content = "Đã gửi yêu cầu nhận Brainrot Free!", Duration = 4})
                setToggleState("ToggleBrainrotFree", false)
            end)
        end
    end
})

SpinTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "• 'Quay vòng quay' – bay đến vị trí và thực hiện quay miễn phí (1 lần/ngày)\n• 'Lấy Brainrot Free' – bay đến vị trí và gửi lệnh nhận Brainrot\n⚠️ Cần phải bấm tham gia nhóm mới nhận được Brainrot Free!"
})

-- ============================
-- TAB 7: DỊCH CHUYỂN
-- ============================
local TeleportTab = Window:CreateTab("Dịch Chuyển", 4483362458)

local leaderboardPos = Vector3.new(170.230042, 12.0595999, 461.961761)
local strongestPlayerPos = Vector3.new(141.363464, -2.42998457, 513.962036)
local tradePos = Vector3.new(-201.208679, -2.20987535, 477.569733)

local housePos1 = Vector3.new(-169.029572, -2.39060259, 433.355438)
local housePos2 = Vector3.new(-90.4595871, -2.39060259, 433.355438)
local housePos3 = Vector3.new(-15.7095871, -2.39060259, 433.355438)
local housePos4 = Vector3.new(59.7704086, -2.39060259, 433.355438)
local housePos5 = Vector3.new(135.430405, -2.39060259, 433.355438)

local raidSpeedLeaderboardPos = Vector3.new(-207.46228, -2.01588202, 517.521118)
local raidMostLeaderboardPos = Vector3.new(-179.443375, -4.2977047, 546.751038)
local raidEntryPos = Vector3.new(-192.448151, 0.109633803, 531.327576)

local sellPosFast = Vector3.new(-78.43927, -0.749984742, 505.715668)
local shopAuraFast = Vector3.new(33.5341225, -0.74998498, 503.479218)
local spinFast = Vector3.new(105.983093, -0.637485504, 525.639038)

local blastPos = Vector3.new(-27.2284851, -14.5883188, 576.082825)
local followerBlastPos = Vector3.new(74.9184494, -1.88000011, 527.988464)
local eventPos = Vector3.new(-22.657959, -2.05540633, 482.850311)

TeleportTab:CreateButton({
    Name = "📊 Xem bảng xếp hạng",
    Callback = function()
        task.spawn(function()
            if flyTo(leaderboardPos, 2.5) then
                Rayfield:Notify({Title = "📊", Content = "Đã bay đến bảng xếp hạng!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🏆 Xem người chơi mạnh nhất",
    Callback = function()
        task.spawn(function()
            if flyTo(strongestPlayerPos, 2.5) then
                Rayfield:Notify({Title = "🏆", Content = "Đã bay đến người chơi mạnh nhất!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🔄 Chỗ trao đổi",
    Callback = function()
        task.spawn(function()
            if flyTo(tradePos, 2.5) then
                Rayfield:Notify({Title = "🔄", Content = "Đã bay đến chỗ trao đổi!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🏠 Nhà 1",
    Callback = function()
        task.spawn(function()
            if flyTo(housePos1, 2.5) then
                Rayfield:Notify({Title = "🏠", Content = "Đã bay đến Nhà 1!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🏠 Nhà 2",
    Callback = function()
        task.spawn(function()
            if flyTo(housePos2, 2.5) then
                Rayfield:Notify({Title = "🏠", Content = "Đã bay đến Nhà 2!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🏠 Nhà 3",
    Callback = function()
        task.spawn(function()
            if flyTo(housePos3, 2.5) then
                Rayfield:Notify({Title = "🏠", Content = "Đã bay đến Nhà 3!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🏠 Nhà 4",
    Callback = function()
        task.spawn(function()
            if flyTo(housePos4, 2.5) then
                Rayfield:Notify({Title = "🏠", Content = "Đã bay đến Nhà 4!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🏠 Nhà 5",
    Callback = function()
        task.spawn(function()
            if flyTo(housePos5, 2.5) then
                Rayfield:Notify({Title = "🏠", Content = "Đã bay đến Nhà 5!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "⚡ Bảng xếp hạng Raid nhanh nhất",
    Callback = function()
        task.spawn(function()
            if flyTo(raidSpeedLeaderboardPos, 2.5) then
                Rayfield:Notify({Title = "⚡", Content = "Đã bay đến bảng xếp hạng Raid nhanh nhất!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🔄 Bảng xếp hạng vòng Raid nhiều nhất",
    Callback = function()
        task.spawn(function()
            if flyTo(raidMostLeaderboardPos, 2.5) then
                Rayfield:Notify({Title = "🔄", Content = "Đã bay đến bảng xếp hạng vòng Raid nhiều nhất!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🗡️ Chỗ đi Raid",
    Callback = function()
        task.spawn(function()
            if flyTo(raidEntryPos, 2.5) then
                Rayfield:Notify({Title = "🗡️", Content = "Đã bay đến chỗ đi Raid!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "💵 Bán Brainrot",
    Callback = function()
        task.spawn(function()
            if flyTo(sellPosFast, 2.5) then
                Rayfield:Notify({Title = "💵", Content = "Đã bay đến NPC bán Brainrot!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🏪 Shop Aura",
    Callback = function()
        task.spawn(function()
            if flyTo(shopAuraFast, 2.5) then
                Rayfield:Notify({Title = "🏪", Content = "Đã bay đến Shop Aura!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🎡 Vòng quay",
    Callback = function()
        task.spawn(function()
            if flyTo(spinFast, 2.5) then
                Rayfield:Notify({Title = "🎡", Content = "Đã bay đến Vòng quay!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "💥 Chỗ nổ (lấy Brainrot)",
    Callback = function()
        task.spawn(function()
            if flyTo(blastPos, 2.5) then
                Rayfield:Notify({Title = "💥", Content = "Đã bay đến chỗ nổ lấy Brainrot!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "👥 Vụ nổ kèm người theo",
    Callback = function()
        task.spawn(function()
            if flyTo(followerBlastPos, 2.5) then
                Rayfield:Notify({Title = "👥", Content = "Đã bay đến vụ nổ kèm người theo!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateButton({
    Name = "🎉 Event",
    Callback = function()
        task.spawn(function()
            if flyTo(eventPos, 2.5) then
                Rayfield:Notify({Title = "🎉", Content = "Đã bay đến khu vực Event!", Duration = 3})
            end
        end)
    end
})

TeleportTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "• Bấm nút để bay đến vị trí tương ứng\n• KHÔNG ẤN 2 NÚT CÙNG LÚC\n• Đã có cơ chế khóa tự động"
})

-- ============================
-- TAB 8: EVENT (CẬP NHẬT)
-- ============================
local EventTab = Window:CreateTab("Event", 4483362458)

local eventBuyTokensPos = Vector3.new(-33.3844643, -1.74115956, 476.667206)

local function flyToEventBuyTokens()
    flyTo(eventBuyTokensPos, 2.5)
    task.wait(0.3)
end

-- ===== MUA TOKENS =====
EventTab:CreateParagraph({
    Title = "🪙 MUA TOKENS",
    Content = "Bay đến vị trí và gửi remote mua tokens (cần thêm code)."
})

EventTab:CreateToggle({
    Name = "🪙 Mua Tokens",
    CurrentValue = false,
    Flag = "ToggleBuyTokens",
    Callback = function(Value)
        _G_AutoBuyTokens = Value
        if _G_AutoBuyTokens then
            task.spawn(function()
                flyToEventBuyTokens()
                -- TODO: Thêm remote mua tokens vào đây
                Rayfield:Notify({Title = "⚠️", Content = "Chưa có remote mua Tokens!", Duration = 4})
                setToggleState("ToggleBuyTokens", false)
            end)
        end
    end
})

-- ===== CÀY EVENT 1 (CLAW MACHINE) =====
EventTab:CreateParagraph({
    Title = "⚙️ CÀY EVENT 1 (CLAW MACHINE)",
    Content = "Tự động thực hiện chuỗi: Start → Drop → OpenReward → Claim"
})

EventTab:CreateToggle({
    Name = "⚙️ Cày event 1",
    CurrentValue = false,
    Flag = "ToggleFarmEvent",
    Callback = function(Value)
        _G_AutoFarmEvent = Value
        if _G_AutoFarmEvent then
            task.spawn(function()
                local Remotes = getRemotes()
                local clawRemote = Remotes:FindFirstChild("ClawMachineAction")
                if not clawRemote then
                    Rayfield:Notify({Title = "❌", Content = "Không tìm thấy ClawMachineAction!", Duration = 4})
                    setToggleState("ToggleFarmEvent", false)
                    return
                end

                while _G_AutoFarmEvent do
                    -- Bước 1: Start
                    local success, err = pcall(function()
                        clawRemote:InvokeServer("Start")
                    end)
                    if not success then
                        Rayfield:Notify({Title = "❌", Content = "Lỗi Start: " .. tostring(err), Duration = 3})
                        break
                    end
                    Rayfield:Notify({Title = "▶️", Content = "Start - đợi 3s...", Duration = 2})
                    task.wait(3)

                    if not _G_AutoFarmEvent then break end

                    -- Bước 2: Drop
                    pcall(function()
                        clawRemote:InvokeServer("Drop")
                    end)
                    Rayfield:Notify({Title = "⬇️", Content = "Drop - đợi 5s...", Duration = 2})
                    task.wait(5)

                    if not _G_AutoFarmEvent then break end

                    -- Bước 3: OpenReward
                    pcall(function()
                        clawRemote:InvokeServer("OpenReward")
                    end)
                    Rayfield:Notify({Title = "🎁", Content = "OpenReward - đợi 2s...", Duration = 2})
                    task.wait(2)

                    if not _G_AutoFarmEvent then break end

                    -- Bước 4: Claim
                    pcall(function()
                        clawRemote:InvokeServer("Claim")
                    end)
                    Rayfield:Notify({Title = "✅", Content = "Claim thành công!", Duration = 2})

                    task.wait(1)
                end

                if not _G_AutoFarmEvent then
                    setToggleState("ToggleFarmEvent", false)
                    Rayfield:Notify({Title = "⏹️", Content = "Đã dừng Cày event 1!", Duration = 3})
                end
            end)
        end
    end
})

-- ===== CÀY EVENT 2 (TRAIT MACHINE) =====
EventTab:CreateParagraph({
    Title = "⚙️ CÀY EVENT 2 (TRAIT MACHINE)",
    Content = "Tự động thực hiện chuỗi: GetState (20s) → Roll (5s) → Return"
})

EventTab:CreateToggle({
    Name = "⚙️ Cày event 2",
    CurrentValue = false,
    Flag = "ToggleFarmEvent2",
    Callback = function(Value)
        _G_AutoFarmEvent2 = Value
        if _G_AutoFarmEvent2 then
            task.spawn(function()
                local Remotes = getRemotes()
                local traitRemote = Remotes:FindFirstChild("TraitMachineRequest")
                if not traitRemote then
                    Rayfield:Notify({Title = "❌", Content = "Không tìm thấy TraitMachineRequest!", Duration = 4})
                    setToggleState("ToggleFarmEvent2", false)
                    return
                end

                while _G_AutoFarmEvent2 do
                    -- Bước 1: GetState
                    local success, err = pcall(function()
                        traitRemote:InvokeServer("GetState")
                    end)
                    if not success then
                        Rayfield:Notify({Title = "❌", Content = "Lỗi GetState: " .. tostring(err), Duration = 3})
                        break
                    end
                    Rayfield:Notify({Title = "⏳", Content = "GetState - đợi 20s để bỏ Brainrot vào...", Duration = 3})
                    task.wait(20)

                    if not _G_AutoFarmEvent2 then break end

                    -- Bước 2: Roll
                    pcall(function()
                        traitRemote:InvokeServer("Roll")
                    end)
                    Rayfield:Notify({Title = "🎲", Content = "Roll - đợi 5s...", Duration = 2})
                    task.wait(5)

                    if not _G_AutoFarmEvent2 then break end

                    -- Bước 3: Return
                    pcall(function()
                        traitRemote:InvokeServer("Return")
                    end)
                    Rayfield:Notify({Title = "✅", Content = "Return thành công!", Duration = 2})

                    task.wait(1)
                end

                if not _G_AutoFarmEvent2 then
                    setToggleState("ToggleFarmEvent2", false)
                    Rayfield:Notify({Title = "⏹️", Content = "Đã dừng Cày event 2!", Duration = 3})
                end
            end)
        end
    end
})

EventTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "• 'Cày event 1' – ClawMachine: Start → Drop → OpenReward → Claim\n• 'Cày event 2' – TraitMachine: GetState (20s) → Roll (5s) → Return\n• Mỗi toggle hoạt động độc lập, có thể bật cùng lúc"
})

print("✅ Brainrot Hub v7.0 đã sẵn sàng!")
