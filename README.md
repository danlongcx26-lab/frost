local Players = game:GetService("Players")
local HttpService = game:GetService("HttpService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local RemoteFunction = ReplicatedStorage:WaitForChild("RemoteFunction")

local activeStratThread = nil

loadstring(game:HttpGet("https://raw.githubusercontent.com/AmonguszzZ/AetherModded/refs/heads/main/AetherHub.lua"))()
TDS:Loadout("Pyromancer", "Hunter", "EvolvedKingpin", "None", "None")
if game.PlaceId == 3260590327 then
    RemoteFunction:InvokeServer("Multiplayer", "v2:start", {
        difficulty = "Easy",
        mode = "hardcore",
        count = 1
    })
else
    print("Game Not Supported")
end

TDS:GameInfo("Wretched Front", {})

task.spawn(function()
    local GuiS = loadstring(game:HttpGet("https://raw.githubusercontent.com/AmonguszzZ/AetherModded/refs/heads/main/GuiTracker.lua"))()
end)

local function StartStrategy()
wait(2.5)
TDS:RemoveIndex()
TDS:Place("Pyromancer", -2.0989227294921875, 1.009635329246521, -34.57014465332031)
TDS:SetTarget(1, "Farthest")
TDS:Ready()

TDS:VoteSkip(1, 25)
TDS:Upgrade(1)
TDS:Upgrade(1)

TDS:VoteSkip(6)
TDS:Upgrade(1)

TDS:Upgrade(1)

TDS:Place("Hunter", 1.5090651512145996, 0.5713815689086914, 14.419164657592773)


TDS:Place("Hunter", 3.570600986480713, 0.9113662242889404, 18.20372200012207)
TDS:VoteSkip(14)
TDS:Place("Hunter", 0.24097543954849243, 1.317692518234253, 18.51652717590332)

TDS:VoteSkip(15)
TDS:Place("Hunter", 1.720466136932373, 1.384239912033081, 22.47343635559082)

TDS:Place("Hunter", 5.2701520919799805, 1.3515396118164062, 22.529003143310547)

TDS:Place("Hunter", 8.8175687789917, 1.3109925985336304, 22.54141616821289)

TDS:Place("Hunter", 9.301279067993164, 0.847939133644104, 18.969970703125)
TDS:Place("Hunter", 6.011168479919434, 1.3946723937988281, 28.527118682861328)

TDS:Place("EvolvedKingpin", 2.5719375610351562, 1.4263112545013428, 27.979778289794922)

TDS:Place("EvolvedKingpin", -1.284123420715332, 1.4632039070129395, 28.251195907592773)

end

local function GetMatchStatus()
    local success, status = pcall(function()
        local uiRoot = PlayerGui:FindFirstChild("ReactGameNewRewards")
        if not uiRoot then return nil end

        local mainFrame = uiRoot:FindFirstChild("Frame")
        if not mainFrame or not mainFrame.Visible then return nil end

        local gameOver = mainFrame:FindFirstChild("gameOver")
        if not gameOver or not gameOver.Visible then return nil end

        local rewardsScreen = gameOver:FindFirstChild("RewardsScreen")
        if not rewardsScreen or not rewardsScreen.Visible then return nil end

        local topBanner = rewardsScreen:FindFirstChild("RewardBanner")
        if not topBanner then return nil end

        local label = topBanner:FindFirstChild("textLabel") or topBanner:FindFirstChildOfClass("TextLabel")
        if not label then return nil end

        local txt = label.Text:upper()
        if txt == "" then return nil end

        if txt:find("TRIUMPH") or txt:find("VICTORY") or txt:find("WIN") then
            return "WIN"
        elseif txt:find("LOST") or txt:find("DEFEAT") or txt:find("FAIL") then
            return "LOSS"
        end
        return nil
    end)
    
    if success then return status end
    return nil
end

task.spawn(function()
    activeStratThread = task.spawn(StartStrategy)

    while true do
        task.wait(0.5)

        local currentStatus = GetMatchStatus()
        
        if currentStatus == "LOSS" then
       
            if activeStratThread and coroutine.status(activeStratThread) ~= "dead" then
                task.cancel(activeStratThread)
                activeStratThread = nil
            end
          
			wait(5.5)
            for i = 1, 1 do
				wait(1)
                local Event = game:GetService("ReplicatedStorage").RemoteFunction
                local Result = Event:InvokeServer(
                    "Voting",
                    "Skip"
                )
                local ExpectedResult = table.unpack({
                    true
                })
                
                if i < 1 then
                    task.wait(1)
                end
            end

            repeat 
                task.wait(1) 
            until GetMatchStatus() == nil

            task.wait(5)
			
            activeStratThread = task.spawn(StartStrategy)
        end
    end
end)

