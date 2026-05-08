_G.HitAssist = true       -- 命中辅助
_G.AntiPuddle = true      -- 防水坑变身
_G.AntiFurry = true       -- 反变身效果
_G.ESP = true             -- 透视功能

local AdonisSucks = game:GetService("Players")
local GameHasAnticheatEZZZBYPASS = game:GetService("ReplicatedStorage")
local MyGarage = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local Sigma = AdonisSucks.LocalPlayer
local HitTheBozos = GameHasAnticheatEZZZBYPASS:WaitForChild("SovaSystemMeleeR").Remotes.OnHit
-- 查找最近玩家(最大距离)
local function FuckAdonis(maxDist)
    local lpChar = Sigma.Character
    if not lpChar or not lpChar:FindFirstChild("HumanoidRootPart") then return nil end
    local nearestPlayer = nil
    local shortestDist = maxDist
    for _, player in pairs(AdonisSucks:GetPlayers()) do
        if player ~= Sigma and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (lpChar.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if dist <= shortestDist then shortestDist = dist nearestPlayer = player end
        end
    end
    return nearestPlayer
end
-- 清除变身水坑
local function RetardedPuddle()
    for _, part in pairs(MyGarage:GetDescendants()) do
        if part:IsA("BasePart") and part.Name == "TransfurPart" then -- 变身触发部件
            local touch = part:FindFirstChildOfClass("TouchTransmitter") -- 触碰检测器
            if touch then touch:Destroy() end
        end
    end
end
-- 防止被变身控制
local function AdonisIsCrying()
    if Sigma.Team and Sigma.Team.Name == "Transfured" then -- 变身阵营
        if Sigma.Character and Sigma.Character:FindFirstChild("Humanoid") then
            Sigma.Character:BreakJoints() -- 断开角色关节自救
        end
    end
end
local ESPFolder = Instance.new("Folder")
ESPFolder.Name = "TransfuredESP" -- 变身玩家透视文件夹
ESPFolder.Parent = MyGarage
local assignedESP = {}
-- 给玩家分配透视高亮
local function assignESP(player)
    if assignedESP[player] then return end
    if not player.Character then return end
    local char = player.Character
    local hrp = char:FindFirstChild("HumanoidRootPart") -- 人物根部件
    if not hrp then return end
    local highlight = Instance.new("Highlight") -- 高亮透视
    highlight.Name = "TransfuredHighlight" -- 变身高亮
    highlight.Adornee = char
    highlight.FillColor = Color3.fromRGB(255,105,180)
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 1
    highlight.Parent = ESPFolder
    local conn
    conn = RunService.RenderStepped:Connect(function()
        if char.Parent and hrp then
        else
            highlight:Destroy()
            assignedESP[player] = nil
            if conn then conn:Disconnect() end
        end
    end)
    assignedESP[player] = true
end
-- 检测玩家所属队伍
local function checkPlayerTeam(player)
    if player.Team and player.Team.Name == "Transfured" then -- 变身阵营
        assignESP(player)
    else
        if assignedESP[player] then
            local hl = ESPFolder:FindFirstChild("TransfuredHighlight")
            if hl then hl:Destroy() end
            assignedESP[player] = nil
        end
    end
end
spawn(function()
    while true do
        if _G.HitAssist then -- 开启命中辅助
            local target = FuckAdonis(10)
            if target and target.Character then
                local hrp = target.Character:FindFirstChild("HumanoidRootPart")
                local humanoid = target.Character:FindFirstChild("Humanoid")
                if hrp and humanoid then pcall(function() HitTheBozos:FireServer(hrp, humanoid) end) end
            end
        end
        wait(0.2)
    end
end)
spawn(function()
    while true do
        if _G.AntiPuddle then -- 开启防水坑
            RetardedPuddle()
        end
        wait(1)
    end
end)
spawn(function()
    while true do
        if _G.AntiFurry then -- 开启反变身
            AdonisIsCrying()
        end
        wait(5)
    end
end)
AdonisSucks.PlayerAdded:Connect(function(player)
    player:GetPropertyChangedSignal("Team"):Connect(function() checkPlayerTeam(player) end)
    player.CharacterAdded:Connect(function() checkPlayerTeam(player) end)
end)
for _,player in pairs(AdonisSucks:GetPlayers()) do
    checkPlayerTeam(player)
    player:GetPropertyChangedSignal("Team"):Connect(function() checkPlayerTeam(player) end)
    player.CharacterAdded:Connect(function() checkPlayerTeam(player) end)
end

-- 反作弊绕过---------------- 
-- 作用：防止劣质Adonis反作弊把你踢出游戏 {不用谢}
local NamecallInstanceDetector = nil
for Index, Table in getgc(true) do
    if typeof(Table) ~= "table" then continue end
    if not rawget(Table, "namecallInstance") then continue end
    for SecondIndex, StackContainerTable in Table do
        if typeof(StackContainerTable) ~= "table" then continue end
        for ThirdIndex, Upvalues in StackContainerTable do
            if StackContainerTable[ThirdIndex] ~= "kick" then continue end -- 踢出检测
            if typeof(StackContainerTable[ThirdIndex + 1]) ~= "function" then continue end
            local FrozenDetectionFunction = StackContainerTable[ThirdIndex + 1] -- 冻结检测函数
            for _, DetectionIdentifier in getconstants(FrozenDetectionFunction) do
                if DetectionIdentifier == "namecallInstance" then
                    NamecallInstanceDetector = FrozenDetectionFunction -- 名称调用检测
                end
            end
        end
    end
end
if NamecallInstanceDetector then
    hookfunction(NamecallInstanceDetector, function()
        return false
    end)
end
