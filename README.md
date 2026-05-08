_G.HitAssist = true
_G.AntiPuddle = true
_G.AntiFurry = true
_G.ESP = true

local AdonisSucks = game:GetService("Players")
local GameHasAnticheatEZZZBYPASS = game:GetService("ReplicatedStorage")
local MyGarage = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local Sigma = AdonisSucks.LocalPlayer
local HitTheBozos = GameHasAnticheatEZZZBYPASS:WaitForChild("SovaSystemMeleeR").Remotes.OnHit
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
local function RetardedPuddle()
    for _, part in pairs(MyGarage:GetDescendants()) do
        if part:IsA("BasePart") and part.Name == "TransfurPart" then
            local touch = part:FindFirstChildOfClass("TouchTransmitter")
            if touch then touch:Destroy() end
        end
    end
end
local function AdonisIsCrying()
    if Sigma.Team and Sigma.Team.Name == "Transfured" then
        if Sigma.Character and Sigma.Character:FindFirstChild("Humanoid") then
            Sigma.Character:BreakJoints()
        end
    end
end
local ESPFolder = Instance.new("Folder")
ESPFolder.Name = "TransfuredESP"
ESPFolder.Parent = MyGarage
local assignedESP = {}
local function assignESP(player)
    if assignedESP[player] then return end
    if not player.Character then return end
    local char = player.Character
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local highlight = Instance.new("Highlight")
    highlight.Name = "TransfuredHighlight"
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
local function checkPlayerTeam(player)
    if player.Team and player.Team.Name == "Transfured" then
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
        if _G.HitAssist then
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
        if _G.AntiPuddle then
            RetardedPuddle()
        end
        wait(1)
    end
end)
spawn(function()
    while true do
        if _G.AntiFurry then
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

-- AntiCheat Bypass---------------- 
-- ITS TO PREVENT THE SHITTY ADONIS FROM KICKING YOU {Your Welcome}
local NamecallInstanceDetector = nil
for Index, Table in getgc(true) do
    if typeof(Table) ~= "table" then continue end
    if not rawget(Table, "namecallInstance") then continue end
    for SecondIndex, StackContainerTable in Table do
        if typeof(StackContainerTable) ~= "table" then continue end
        for ThirdIndex, Upvalues in StackContainerTable do
            if StackContainerTable[ThirdIndex] ~= "kick" then continue end
            if typeof(StackContainerTable[ThirdIndex + 1]) ~= "function" then continue end
            local FrozenDetectionFunction = StackContainerTable[ThirdIndex + 1]
            for _, DetectionIdentifier in getconstants(FrozenDetectionFunction) do
                if DetectionIdentifier == "namecallInstance" then
                    NamecallInstanceDetector = FrozenDetectionFunction
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
