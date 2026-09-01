-- [[ TẢI RAYFIELD ]]
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- [[ CỬA SỔ CHÍNH ]]
local Window = Rayfield:CreateWindow({
   Name = "🧠 Brainrot Hub v8.5 vip",
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
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
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

-- Hàm bay mượt (không khóa, dùng cho Auto RAID)
local function smoothFlyTo(pos, duration)
    local char = getCharacter()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local humanoid = char:WaitForChild("Humanoid")
    humanoid:ChangeState(Enum.HumanoidStateType.Flying)
    local tween = TweenService:Create(hrp, TweenInfo.new(duration or 0.3, Enum.EasingStyle.Linear), {
        CFrame = CFrame.new(pos) * (hrp.CFrame - hrp.Position)
    })
    tween:Play()
    tween.Completed:Wait()
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
                  local aura = plr.Backpack:FindFirstChild("Aura") or char:FindFirstChild("Aura")
                  if aura and aura.Parent == plr.Backpack then
                      humanoid:EquipTool(aura)
                      task.wait(0.2)
                  end
                  if remote then
                      local id = math.random(10000000000, 99999999999)
                      local float = math.random() * 10000
                      local floatStr = string.format("%.14f", float)
                      local int = math.random(1, 100000)
                      remote:FireServer(tostring(id) .. ":" .. floatStr .. ":" .. tostring(int))
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
        return BlastStyleAction:InvokeServer({ action = "buyCash", styleId = styleId })
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

ShopTab:CreateParagraph({ Title = "💥 MUA BLAST STYLE", Content = "Các style vụ nổ – bay đến shop cũ." })

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

ShopTab:CreateParagraph({ Title = "✨ MUA AURA", Content = "Mua Aura theo rarity – bay đến shop Aura." })

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

SpinTab:CreateToggle({
    Name = "📝 Nhập code",
    CurrentValue = false,
    Flag = "ToggleRedeemCode",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                local Remotes = getRemotes()
                local PlayerSettings = Remotes:FindFirstChild("PlayerSettings")
                local RedeemCode = Remotes:FindFirstChild("RedeemCode")
                local SeasonPassAction = Remotes:FindFirstChild("SeasonPassAction")
                if not PlayerSettings or not RedeemCode or not SeasonPassAction then
                    Rayfield:Notify({Title = "❌", Content = "Thiếu remote cần thiết!", Duration = 4})
                    setToggleState("ToggleRedeemCode", false)
                    return
                end
                local success, err = pcall(function()
                    PlayerSettings:InvokeServer("get")
                end)
                if not success then
                    Rayfield:Notify({Title = "❌", Content = "Lỗi PlayerSettings: " .. tostring(err), Duration = 3})
                    setToggleState("ToggleRedeemCode", false)
                    return
                end
                Rayfield:Notify({Title = "📤", Content = "Đã gửi PlayerSettings - đợi 1s", Duration = 2})
                task.wait(1)
                pcall(function()
                    RedeemCode:InvokeServer("3MILLY")
                end)
                Rayfield:Notify({Title = "📤", Content = "Đã gửi RedeemCode - đợi 3s", Duration = 2})
                task.wait(3)
                pcall(function()
                    SeasonPassAction:InvokeServer({ action = "state" })
                end)
                Rayfield:Notify({Title = "📤", Content = "Đã gửi SeasonPass state - đợi 1s", Duration = 2})
                task.wait(1)
                pcall(function()
                    SeasonPassAction:InvokeServer({ action = "openTitanChest" })
                end)
                Rayfield:Notify({Title = "✅", Content = "Đã nhập code và mở rương thành công!", Duration = 4})
                setToggleState("ToggleRedeemCode", false)
            end)
        end
    end
})

SpinTab:CreateToggle({
    Name = "📅 Nhận thưởng hàng ngày",
    CurrentValue = false,
    Flag = "ToggleDailyReward",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                local Remotes = getRemotes()
                local GetDailyRewards = Remotes:FindFirstChild("GetDailyRewards")
                local ClaimDailyReward = Remotes:FindFirstChild("ClaimDailyReward")
                if not GetDailyRewards or not ClaimDailyReward then
                    Rayfield:Notify({Title = "❌", Content = "Thiếu remote Daily Rewards!", Duration = 4})
                    setToggleState("ToggleDailyReward", false)
                    return
                end
                local success, err = pcall(function()
                    GetDailyRewards:InvokeServer()
                end)
                if not success then
                    Rayfield:Notify({Title = "❌", Content = "Lỗi GetDailyRewards: " .. tostring(err), Duration = 3})
                    setToggleState("ToggleDailyReward", false)
                    return
                end
                Rayfield:Notify({Title = "📤", Content = "Đã lấy danh sách thưởng - đợi 3s", Duration = 2})
                task.wait(3)
                local claimedCount = 0
                for i = 1, 7 do
                    local claimSuccess = pcall(function()
                        ClaimDailyReward:InvokeServer(i)
                    end)
                    if claimSuccess then
                        claimedCount = claimedCount + 1
                        Rayfield:Notify({Title = "✅", Content = "Đã nhận ô " .. i .. "/7", Duration = 2})
                    else
                        Rayfield:Notify({Title = "⚠️", Content = "Lỗi nhận ô " .. i, Duration = 2})
                    end
                    task.wait(1.5)
                end
                Rayfield:Notify({Title = "🎉", Content = "Đã nhận " .. claimedCount .. "/7 ô thưởng!", Duration = 4})
                setToggleState("ToggleDailyReward", false)
            end)
        end
    end
})

SpinTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "• 'Quay vòng quay' – bay đến vị trí và thực hiện quay miễn phí (1 lần/ngày)\n• 'Lấy Brainrot Free' – bay đến vị trí và gửi lệnh nhận Brainrot\n⚠️ Cần tham gia nhóm mới nhận được Brainrot Free!\n• 'Nhập code' – chạy chuỗi remote: PlayerSettings → RedeemCode → SeasonPassAction (state + openTitanChest)\n• 'Nhận thưởng hàng ngày' – GetDailyRewards → Claim ô 1-7 (mỗi ô cách 1.5s)"
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
-- TAB 8: EVENT
-- ============================
local EventTab = Window:CreateTab("Event", 4483362458)

local eventBuyTokensPos = Vector3.new(-33.3844643, -1.74115956, 476.667206)
local eventClawPos = Vector3.new(-13.0710115, -1.69945204, 477.126282)

local function flyToEventBuyTokens()
    flyTo(eventBuyTokensPos, 2.5)
    task.wait(0.3)
end

local function flyToEventClaw()
    flyTo(eventClawPos, 2.5)
    task.wait(0.3)
end

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
                Rayfield:Notify({Title = "⚠️", Content = "Chưa có remote mua Tokens!", Duration = 4})
                setToggleState("ToggleBuyTokens", false)
            end)
        end
    end
})

EventTab:CreateParagraph({
    Title = "⚙️ CÀY EVENT 1 (CLAW MACHINE)",
    Content = "Tự động thực hiện chuỗi: Start → Drop (8s) → OpenReward → Claim"
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
                flyToEventClaw()
                while _G_AutoFarmEvent do
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
                    pcall(function() clawRemote:InvokeServer("Drop") end)
                    Rayfield:Notify({Title = "⬇️", Content = "Drop - đợi 8s...", Duration = 2})
                    task.wait(8)
                    if not _G_AutoFarmEvent then break end
                    pcall(function() clawRemote:InvokeServer("OpenReward") end)
                    Rayfield:Notify({Title = "🎁", Content = "OpenReward - đợi 2s...", Duration = 2})
                    task.wait(2)
                    if not _G_AutoFarmEvent then break end
                    pcall(function() clawRemote:InvokeServer("Claim") end)
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
                    pcall(function() traitRemote:InvokeServer("Roll") end)
                    Rayfield:Notify({Title = "🎲", Content = "Roll - đợi 5s...", Duration = 2})
                    task.wait(5)
                    if not _G_AutoFarmEvent2 then break end
                    pcall(function() traitRemote:InvokeServer("Return") end)
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
    Content = "• 'Cày event 1' – ClawMachine: Start → Drop (8s) → OpenReward → Claim\n• 'Cày event 2' – TraitMachine: GetState (20s) → Roll (5s) → Return"
})

-- ============================
-- TAB 9: RAID (CHIẾN ĐẤU + AUTO BAY ĐẾN NPC) - PHIÊN BẢN CUỐI CÙNG
-- ============================
local RaidTab = Window:CreateTab("RAID", 4483362458)

local raidStartPos = Vector3.new(-192.448151, 0.648801327, 531.327576)
-- Vị trí an toàn mặc định (có thể thay đổi qua các nút)
local safePosition = Vector3.new(84.7016678, 50.0205612, -197.187851)

local function flyToRaidStart()
    flyTo(raidStartPos, 2.5)
    task.wait(0.3)
end

local function flyToSafePosition()
    smoothFlyTo(safePosition, 1.0)
    -- Đảm bảo vẫn bay sau khi đến nơi
    local char = getCharacter()
    if char then
        local humanoid = char:FindFirstChild("Humanoid")
        if humanoid then
            humanoid:ChangeState(Enum.HumanoidStateType.Flying)
        end
    end
end

-- Tìm NPC hoặc boss còn sống, trả về model và part (bất kỳ BasePart)
local function findAliveNPC()
    -- Tìm NPC thường
    for i = 1, 200 do
        local targetName = "Evil Tung Sahur_" .. i
        for _, obj in pairs(Workspace:GetDescendants()) do
            if obj:IsA("Model") and obj.Name == targetName then
                local hrp = obj:FindFirstChild("HumanoidRootPart")
                local part = hrp or obj:FindFirstChildWhichIsA("BasePart") or obj:FindFirstChildWhichIsA("BasePart", true)
                if part and obj.Parent and part.Position.Y > -50 then
                    return obj, part
                end
            end
        end
    end

    -- Tìm boss John Pork
    local boss = Workspace:FindFirstChild("John Pork")
    if boss and boss:IsA("Model") then
        local hrp = boss:FindFirstChild("HumanoidRootPart")
        local part = hrp or boss:FindFirstChildWhichIsA("BasePart") or boss:FindFirstChildWhichIsA("BasePart", true)
        if part and boss.Parent and part.Position.Y > -50 then
            return boss, part
        end
    end

    -- Tìm boss trong descendants (nếu không phải con trực tiếp)
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name == "John Pork" then
            local hrp = obj:FindFirstChild("HumanoidRootPart")
            local part = hrp or obj:FindFirstChildWhichIsA("BasePart") or obj:FindFirstChildWhichIsA("BasePart", true)
            if part and obj.Parent and part.Position.Y > -50 then
                return obj, part
            end
        end
    end

    return nil, nil
end

-- Quét và hiển thị NPC
local function scanEvilTungSahurNPCs()
    local found = {}
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and (obj.Name:find("Evil Tung Sahur_") or obj.Name == "John Pork") then
            table.insert(found, obj)
        end
    end
    table.sort(found, function(a, b)
        local numA = tonumber(a.Name:match("_(%d+)$")) or 0
        local numB = tonumber(b.Name:match("_(%d+)$")) or 0
        if a.Name == "John Pork" then numA = 99999 end
        if b.Name == "John Pork" then numB = 99999 end
        return numA < numB
    end)
    if #found == 0 then
        Rayfield:Notify({Title = "🔍", Content = "Không tìm thấy NPC nào cần đánh.", Duration = 5})
        return
    end
    local showCount = math.min(#found, 20)
    for i = 1, showCount do
        local npc = found[i]
        local part = npc:FindFirstChild("HumanoidRootPart") or npc:FindFirstChildWhichIsA("BasePart") or npc:FindFirstChildWhichIsA("BasePart", true)
        local posText = part and string.format("%.1f, %.1f, %.1f", part.Position.X, part.Position.Y, part.Position.Z) or "không có part"
        Rayfield:Notify({Title = "🔍 NPC " .. i, Content = npc.Name .. " | Vị trí: " .. posText, Duration = 2})
        task.wait(0.3)
    end
    Rayfield:Notify({Title = "✅", Content = "Tìm thấy " .. #found .. " NPC cần đánh.", Duration = 3})
end

local isAutoRaidRunning = false

-- ===== CHỌN CHẾ ĐỘ =====
RaidTab:CreateParagraph({ Title = "🎯 CHỌN CHẾ ĐỘ", Content = "Chọn độ khó cho RAID." })

local difficulties = {"Easy", "Normal", "Hard", "Insane", "Nightmare"}
local selectedDifficulty = "Easy"

RaidTab:CreateDropdown({
    Name = "Chế độ",
    Options = difficulties,
    CurrentOption = "Easy",
    Flag = "RaidDifficulty",
    Callback = function(Value)
        selectedDifficulty = Value
        Rayfield:Notify({ Title = "📌", Content = "Đã chọn: " .. Value, Duration = 2 })
    end
})

RaidTab:CreateButton({
    Name = "✅ Áp dụng chế độ",
    Callback = function()
        task.spawn(function()
            local Remotes = getRemotes()
            local dungeonRemote = Remotes:FindFirstChild("DungeonAction")
            if not dungeonRemote then
                Rayfield:Notify({ Title = "❌", Content = "Không tìm thấy DungeonAction!", Duration = 3 })
                return
            end
            local success, err = pcall(function()
                dungeonRemote:InvokeServer({ action = "setDifficulty", difficulty = selectedDifficulty })
            end)
            if success then
                Rayfield:Notify({ Title = "✅", Content = "Đã chọn chế độ: " .. selectedDifficulty, Duration = 3 })
            else
                Rayfield:Notify({ Title = "❌", Content = "Lỗi: " .. tostring(err), Duration = 3 })
            end
        end)
    end
})

-- ===== ĐIỀU KHIỂN RAID =====
RaidTab:CreateParagraph({ Title = "⚔️ ĐIỀU KHIỂN RAID", Content = "Bắt đầu hoặc dừng." })

RaidTab:CreateToggle({
    Name = "⚔️ Bắt đầu RAID",
    CurrentValue = false,
    Flag = "ToggleStartRaid",
    Callback = function(Value)
        if Value then
            task.spawn(function()
                local Remotes = getRemotes()
                local dungeonRemote = Remotes:FindFirstChild("DungeonAction")
                if not dungeonRemote then
                    Rayfield:Notify({ Title = "❌", Content = "Không tìm thấy DungeonAction!", Duration = 3 })
                    setToggleState("ToggleStartRaid", false)
                    return
                end
                flyToRaidStart()
                local success, err = pcall(function()
                    dungeonRemote:InvokeServer({ action = "start" })
                end)
                if success then
                    Rayfield:Notify({ Title = "⚔️", Content = "Đã bắt đầu RAID!", Duration = 3 })
                else
                    Rayfield:Notify({ Title = "❌", Content = "Lỗi: " .. tostring(err), Duration = 3 })
                end
                setToggleState("ToggleStartRaid", false)
            end)
        end
    end
})

-- ===== NÚT QUÉT NPC =====
RaidTab:CreateButton({
    Name = "🔍 Quét NPC/ Boss",
    Callback = function()
        task.spawn(function()
            scanEvilTungSahurNPCs()
        end)
    end
})

-- ===== CÁC NÚT LƯU VỊ TRÍ AN TOÀN =====
RaidTab:CreateParagraph({ Title = "📍 VỊ TRÍ AN TOÀN", Content = "Chọn vị trí an toàn khi hết quái." })

RaidTab:CreateButton({
    Name = "1️⃣ Lưu vị trí an toàn 1 (Map 1)",
    Callback = function()
        safePosition = Vector3.new(84.7016678, 50.0205612, -197.187851)
        Rayfield:Notify({ Title = "📍", Content = "Đã đặt vị trí an toàn: Map 1", Duration = 3 })
    end
})

RaidTab:CreateButton({
    Name = "2️⃣ Qua map 2 (Vị trí an toàn 2)",
    Callback = function()
        safePosition = Vector3.new(-6.41163158, 16.9227524, -159.776367)
        Rayfield:Notify({ Title = "📍", Content = "Đã đặt vị trí an toàn: Map 2", Duration = 3 })
    end
})

RaidTab:CreateButton({
    Name = "3️⃣ Qua map 3 (Vị trí an toàn 3)",
    Callback = function()
        safePosition = Vector3.new(470.037537, 12.4059668, 348.623535)
        Rayfield:Notify({ Title = "📍", Content = "Đã đặt vị trí an toàn: Map 3", Duration = 3 })
    end
})

RaidTab:CreateButton({
    Name = "👹 Qua map boss",
    Callback = function()
        safePosition = Vector3.new(-1345.2467, -18.7066288, -301.6008)
        Rayfield:Notify({ Title = "📍", Content = "Đã đặt vị trí an toàn: Map Boss", Duration = 3 })
    end
})

RaidTab:CreateButton({
    Name = "🔥 Triệu hồi boss",
    Callback = function()
        safePosition = Vector3.new(-1359.5603, 13.8127136, -121.295326)
        Rayfield:Notify({ Title = "📍", Content = "Đã đặt vị trí an toàn: Triệu hồi boss", Duration = 3 })
    end
})

RaidTab:CreateButton({
    Name = "📌 Lưu vị trí hiện tại làm an toàn",
    Callback = function()
        local char = getCharacter()
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            safePosition = hrp.Position
            Rayfield:Notify({ Title = "📍", Content = "Đã lưu vị trí hiện tại làm an toàn.", Duration = 3 })
        else
            Rayfield:Notify({ Title = "❌", Content = "Không lấy được vị trí nhân vật!", Duration = 3 })
        end
    end
})

-- ===== AUTO RAID (BAY MƯỢT, BAY LIÊN TỤC, TỰ ĐỘNG LẶP ĐỢT) =====
RaidTab:CreateParagraph({
    Title = "🤖 AUTO RAID",
    Content = "Bay mượt liên tục bám theo NPC hoặc Boss, nhìn thẳng vào mục tiêu, đánh đến chết rồi tự chuyển sang con tiếp theo. Khi hết quái, tự bay về vị trí an toàn đã chọn và chờ đợt mới. Không bao giờ dừng bay. NoClip chỉ bật khi có mục tiêu để tránh té xuống đất."
})

RaidTab:CreateToggle({
    Name = "🤖 Bật Auto RAID",
    CurrentValue = false,
    Flag = "ToggleAutoRaid",
    Callback = function(Value)
        isAutoRaidRunning = Value
        if isAutoRaidRunning then
            task.spawn(function()
                local Remotes = getRemotes()
                local combatRemote = Remotes:FindFirstChild("DungeonCombatAction")
                if not combatRemote then
                    Rayfield:Notify({ Title = "❌", Content = "Không tìm thấy DungeonCombatAction!", Duration = 4 })
                    setToggleState("ToggleAutoRaid", false)
                    isAutoRaidRunning = false
                    return
                end

                -- Bật fly cho nhân vật
                local char = getCharacter()
                local hrp = char:WaitForChild("HumanoidRootPart")
                local humanoid = char:WaitForChild("Humanoid")
                humanoid:ChangeState(Enum.HumanoidStateType.Flying)

                -- Biến quản lý noclip
                local noclipConnection = nil
                local noclipActive = false

                -- Hàm bật/tắt noclip
                local function setNoclip(enabled)
                    if enabled and not noclipActive then
                        noclipConnection = RunService.Stepped:Connect(function()
                            for _, part in pairs(char:GetDescendants()) do
                                if part:IsA("BasePart") then part.CanCollide = false end
                            end
                        end)
                        noclipActive = true
                    elseif not enabled and noclipActive then
                        if noclipConnection then noclipConnection:Disconnect() end
                        noclipConnection = nil
                        noclipActive = false
                        -- Cho nhân vật về trạng thái bay bình thường (không noclip)
                        humanoid:ChangeState(Enum.HumanoidStateType.Flying)
                    end
                end

                local lastNPCName = nil
                local lastAttackTime = 0

                while isAutoRaidRunning do
                    -- Tìm NPC hoặc boss còn sống
                    local npc, npcPart = findAliveNPC()
                    if not npc or not npcPart then
                        -- Không có mục tiêu: TẮT NOCLIP, bay về vị trí an toàn
                        setNoclip(false)
                        if lastNPCName ~= nil then
                            Rayfield:Notify({ Title = "⏳", Content = "Đã hết quái, bay về vị trí an toàn...", Duration = 2 })
                            lastNPCName = nil
                        end

                        local distanceToSafe = (hrp.Position - safePosition).Magnitude
                        if distanceToSafe > 5 then
                            flyToSafePosition()
                        else
                            -- Đã ở gần, vẫn duy trì bay
                            humanoid:ChangeState(Enum.HumanoidStateType.Flying)
                        end
                        task.wait(1)
                        continue
                    end

                    -- Có mục tiêu: BẬT NOCLIP
                    setNoclip(true)

                    -- Kiểm tra mục tiêu còn sống không
                    if not npc.Parent or not npc:FindFirstChildWhichIsA("BasePart") then
                        continue
                    end

                    -- Hiển thị tên mục tiêu khi chuyển mục tiêu
                    if lastNPCName ~= npc.Name then
                        Rayfield:Notify({ Title = "✈️", Content = "Bay đến " .. npc.Name .. "...", Duration = 2 })
                        lastNPCName = npc.Name
                    end

                    -- Cập nhật vị trí bay phía sau lưng mục tiêu
                    local npcCFrame = npcPart.CFrame
                    local behindOffset = 4
                    local behindPos = npcCFrame.Position - npcCFrame.LookVector * behindOffset
                    behindPos = Vector3.new(behindPos.X, npcPart.Position.Y + 1, behindPos.Z)

                    -- Bay mượt đến vị trí sau lưng
                    smoothFlyTo(behindPos, 0.2)
                    -- Xoay người nhìn về mục tiêu
                    hrp.CFrame = CFrame.lookAt(hrp.Position, npcPart.Position)

                    -- Đánh liên tục (giới hạn tần suất)
                    local now = tick()
                    if now - lastAttackTime > 0.1 then
                        pcall(function()
                            combatRemote:FireServer({ action = "attack", comboIndex = 1 })
                        end)
                        lastAttackTime = now
                    end

                    task.wait(0.05)
                end

                -- Khi tắt Auto RAID: tắt noclip, hạ cánh
                setNoclip(false)
                humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
                if not isAutoRaidRunning then
                    setToggleState("ToggleAutoRaid", false)
                    Rayfield:Notify({ Title = "⏹️", Content = "Đã dừng Auto RAID!", Duration = 3 })
                end
            end)
        end
    end
})

RaidTab:CreateParagraph({
    Title = "📌 HƯỚNG DẪN",
    Content = "1. Chọn chế độ và bấm 'Áp dụng chế độ'.\n2. Bấm 'Bắt đầu RAID' để vào trận.\n3. Chọn vị trí an toàn bằng các nút ở trên (Map 1, 2, 3, Map Boss, Triệu hồi boss, hoặc lưu vị trí hiện tại).\n4. Bật 'Auto RAID' để bay mượt liên tục bám theo NPC/Boss, đánh đến chết rồi tự chuyển.\n5. Khi hết quái, tự bay về vị trí an toàn và chờ đợt mới. NoClip chỉ bật khi có mục tiêu, tắt khi hết để không bị té."
})
