--[[
    SFAA - SMART FAST AUTO ARREST V8.0
    AUTO-ENABLED with instant police team switch
    
    FEATURES:
    - Auto-switches to Police team on startup
    - Auto-starts arrest system immediately
    - Server hop DISABLED by default
]]

--// AUTO-EXECUTE ON SERVER HOP //--
local TeleportCheck = false
local Players = game:GetService("Players")
local LP = Players.LocalPlayer

-- Script URL (use raw pastebin link)
local SCRIPT_URL = "https://pastebin.com/raw/RyMDe6r9"

LP.OnTeleport:Connect(function(State)
    if not TeleportCheck and (queueteleport or queue_on_teleport or (syn and syn.queue_on_teleport)) then
        TeleportCheck = true
        local queueFunc = queueteleport or queue_on_teleport or (syn and syn.queue_on_teleport)
        local success = pcall(function()
            queueFunc("loadstring(game:HttpGet('" .. SCRIPT_URL .. "'))()")
        end)
        if success then
            print("✅ SFAA queued for auto-execution in new server!")
        else
            warn("⚠️ Failed to queue SFAA for next server")
        end
    end
end)

local P = game:GetService("Players")
local W = game:GetService("Workspace")
local R = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local VIM = game:GetService("VirtualInputManager")
local HttpService = game:GetService("HttpService")
local RS = game:GetService("ReplicatedStorage")

local LP = P.LocalPlayer
local Camera = W.CurrentCamera

print("\n" .. string.rep("=", 70))
print("🚔 SFAA V8.0 - AUTO POLICE SWITCHER + MILITARY BASE SPAWNER")
print(string.rep("=", 70))

--// SIMPLE GUI BUTTON CLICKER - NO REMOTES //--
local function getCurrentTeam()
    if LP.Team then return LP.Team.Name end
    return "None"
end

local function clickPoliceButton()
    print("\n" .. string.rep("=", 70))
    print("🚓 AUTO POLICE TEAM SWITCHER - BUTTON CLICK METHOD")
    print(string.rep("=", 70))
    print("Current Team: " .. getCurrentTeam())
    
    local PlayerGui = LP:FindFirstChild("PlayerGui")
    if not PlayerGui then 
        print("❌ PlayerGui not found")
        return false
    end
    
    -- Method 1: Find PoliceTeam button in TeamSelectGui
    print("🔍 Searching for Police button...")
    local policeButton = PlayerGui:FindFirstChild("TeamSelectGui")
    if policeButton then
        policeButton = policeButton:FindFirstChild("TeamSelect")
        if policeButton then
            policeButton = policeButton:FindFirstChild("Frame")
            if policeButton then
                policeButton = policeButton:FindFirstChild("MiddleContainer")
                if policeButton then
                    policeButton = policeButton:FindFirstChild("TeamsContainer")
                    if policeButton then
                        policeButton = policeButton:FindFirstChild("ImagesContainer")
                        if policeButton then
                            policeButton = policeButton:FindFirstChild("PoliceTeam")
                            
                            if policeButton and policeButton:IsA("GuiButton") then
                                print("✅ Found PoliceTeam button!")
                                
                                -- Fire all connections
                                for attempt = 1, 5 do
                                    pcall(function()
                                        for _, connection in pairs(getconnections(policeButton.Activated)) do
                                            connection:Fire()
                                        end
                                        for _, connection in pairs(getconnections(policeButton.MouseButton1Click)) do
                                            connection:Fire()
                                        end
                                    end)
                                    print("🖱️ Click attempt " .. attempt)
                                    task.wait(0.5)
                                    
                                    if getCurrentTeam() == "Police" or getCurrentTeam() == "Cop" then
                                        print("🎉 SUCCESS! Now on Police team!")
                                        return true
                                    end
                                end
                            end
                        end
                    end
                end
            end
        end
    end
    
    -- Method 2: Search all GUI elements for Police buttons
    print("🔍 Searching all GUI elements...")
    for _, gui in ipairs(PlayerGui:GetDescendants()) do
        if gui:IsA("GuiButton") then
            local name = gui.Name:lower()
            local text = ""
            if gui:IsA("TextButton") then
                text = gui.Text:lower()
            end
            
            if name:find("police") or text:find("police") then
                print("✅ Found button: " .. gui.Name)
                
                for attempt = 1, 5 do
                    pcall(function()
                        for _, connection in pairs(getconnections(gui.Activated)) do
                            connection:Fire()
                        end
                        for _, connection in pairs(getconnections(gui.MouseButton1Click)) do
                            connection:Fire()
                        end
                    end)
                    print("🖱️ Click attempt " .. attempt)
                    task.wait(0.5)
                    
                    if getCurrentTeam() == "Police" or getCurrentTeam() == "Cop" then
                        print("🎉 SUCCESS! Now on Police team!")
                        return true
                    end
                end
            end
        end
    end
    
    print("❌ Could not find or click Police button")
    print("Current Team: " .. getCurrentTeam())
    return false
end

local militaryBaseRefIndex = 1981 -- Adjust this if the workspace object index changes

local function isAtMilitaryBase(position)
    -- 1) Check for explicit MilitaryBase.UFOBunker proximity (strong indicator)
    local ok, mb = pcall(function() return workspace:FindFirstChild("MilitaryBase") end)
    if ok and mb then
        local ufo = mb:FindFirstChild("UFOBunker", true) or mb:FindFirstChildWhichIsA("Model")
        if ufo then
            local part = (ufo:IsA("BasePart") and ufo) or ufo:FindFirstChildWhichIsA("BasePart")
            if part then
                local radius = (ArrestSettings and ArrestSettings.UFOBunkerRadius) or 250
                if (position - part.Position).Magnitude <= radius then
                    return true
                end
            end
        end
    end

    -- 2) Primary: check distance to military base reference object (legacy index method)
    local ok2, militaryBaseRef = pcall(function()
        return workspace:GetChildren()[militaryBaseRefIndex]
    end)

    if ok2 and militaryBaseRef then
        local refPos = nil
        if militaryBaseRef:IsA("Model") then
            local rootPart = militaryBaseRef:FindFirstChild("HumanoidRootPart") or militaryBaseRef.PrimaryPart
            if rootPart then refPos = rootPart.Position end
        elseif militaryBaseRef:IsA("BasePart") then
            refPos = militaryBaseRef.Position
        end
        if refPos then
            local distance = (position - refPos).Magnitude
            return distance <= 175
        end
    end

    -- 3) Fallback: bounding box check (approximate Jailbreak military base area)
    local militaryBaseX = {min = -1400, max = -600}
    local militaryBaseZ = {min = 1400, max = 2100}
    if position.X >= militaryBaseX.min and position.X <= militaryBaseX.max and
       position.Z >= militaryBaseZ.min and position.Z <= militaryBaseZ.max then
        return true
    end

    -- Default: not at military base
    return false
end

-- Distance helper: returns approximate distance (studs) to a reliable military base indicator (UFO or legacy ref)
local function distanceToMilitaryBase(position)
    if not position then return nil end
    local ok, mb = pcall(function() return workspace:FindFirstChild("MilitaryBase") end)
    if ok and mb then
        local ufo = mb:FindFirstChild("UFOBunker", true) or mb:FindFirstChildWhichIsA("Model")
        if ufo then
            local part = (ufo:IsA("BasePart") and ufo) or ufo:FindFirstChildWhichIsA("BasePart")
            if part then
                return (position - part.Position).Magnitude
            end
        end
    end
    local ok2, militaryBaseRef = pcall(function()
        return workspace:GetChildren()[militaryBaseRefIndex]
    end)
    if ok2 and militaryBaseRef then
        local refPos = nil
        if militaryBaseRef:IsA("Model") then
            local rootPart = militaryBaseRef:FindFirstChild("HumanoidRootPart") or militaryBaseRef.PrimaryPart
            if rootPart then refPos = rootPart.Position end
        elseif militaryBaseRef:IsA("BasePart") then
            refPos = militaryBaseRef.Position
        end
        if refPos then
            return (position - refPos).Magnitude
        end
    end
    -- Fallback: distance to center of approximate bounding box
    local militaryBaseX = {min = -1400, max = -600}
    local militaryBaseZ = {min = 1400, max = 2100}
    local center = Vector3.new((militaryBaseX.min + militaryBaseX.max) / 2, position.Y, (militaryBaseZ.min + militaryBaseZ.max) / 2)
    return (position - center).Magnitude
end

-- Door pass-through helpers (ensure we don't spawn stuck near police swing doors)
local TARGET_DOOR_PATTERNS = {"PoliceStation.SwingDoor.Model.Door", "SwingDoor.Model.Door"} 

local function getFullPath(inst)
    local path = inst.Name
    local parent = inst.Parent
    while parent and parent ~= game do
        path = parent.Name .. "." .. path
        parent = parent.Parent
    end
    return path
end

local function findNearestDoorWithin(position, radius)
    radius = radius or 100
    local best, bestDist = nil, math.huge
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") then
            local fullPath = getFullPath(obj)
            for _, pat in ipairs(TARGET_DOOR_PATTERNS) do
                if fullPath:sub(-#pat) == pat then
                    local d = (position - obj.Position).Magnitude
                    if d <= radius and d < bestDist then
                        best = obj
                        bestDist = d
                    end
                end
            end
        end
    end
    return best, bestDist
end

local function cframeFlyTo(root, targetPos, speed, timeout)
    speed = speed or 60
    timeout = timeout or 6
    local start = tick()
    while (root.Position - targetPos).Magnitude > 1 do
        if tick() - start > timeout then return false end
        pcall(function()
            local currentPos = root.Position
            local dir = (targetPos - currentPos)
            if dir.Magnitude > 0.01 then
                local step = dir.Unit * math.min(speed * 0.03, dir.Magnitude)
                root.CFrame = CFrame.new(currentPos + step)
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
            end
        end)
        task.wait(0.03)
    end
    return true
end

local function flyPastDoor(door)
    if not door or not LP.Character then return false end
    local root = LP.Character:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    local look = door.CFrame.LookVector
    if not look then return false end

    -- Approach the door face
    local approachPos = door.Position - look * 2
    print("⚠️ Approaching door: " .. tostring(door.Name))
    pcall(function() cframeFlyTo(root, Vector3.new(approachPos.X, approachPos.Y + 1, approachPos.Z), ArrestSettings.CharacterFlySpeed, 6) end)

    -- Step past in 10-stud increments until we're >=10 studs on the opposite side
    local maxAttempts = 6
    for i = 1, maxAttempts do
        local signed = (root.Position - door.Position):Dot(look)
        if signed <= -10 then
            print("✅ Passed to opposite side of door")
            return true
        end
        local stepTarget = root.Position - look * 10
        pcall(function() cframeFlyTo(root, Vector3.new(stepTarget.X, stepTarget.Y, stepTarget.Z), ArrestSettings.CharacterFlySpeed, 4) end)
        task.wait(0.08)
    end

    -- Fallback: force teleport a bit further past the door
    pcall(function()
        local fallback = door.Position - look * 12
        root.CFrame = CFrame.new(fallback.X, fallback.Y + 1, fallback.Z)
        root.AssemblyLinearVelocity = Vector3.zero
        root.AssemblyAngularVelocity = Vector3.zero
        print("⚠️ Fallback teleport used to clear door")
    end)
    return true
end

-- Repeated spawn routine: kill and respawn to Military Base until we're close enough or attempts exhaust
local function ensureSpawnAtMilitaryOrClearDoor(maxAttempts, attemptDelay)
    -- Prevent overlapping attempts
    if spawnAttemptInProgress then
        print("⏳ Spawn attempt already in progress - skipping duplicate call")
        return false
    end

    spawnAttemptInProgress = true
    local ok, result = pcall(function()
        maxAttempts = maxAttempts or 6
        local baseDelay = attemptDelay or ((ArrestSettings and ArrestSettings.SpawnAttemptInitialDelay) or 1.0)
        local backoff = (ArrestSettings and ArrestSettings.SpawnAttemptBackoff) or 1.0
        local settleTime = (ArrestSettings and ArrestSettings.SpawnAttemptSettleTime) or 1.0

        for attempt = 1, maxAttempts do
            local currentDelay = baseDelay + (attempt - 1) * backoff
            print("🔁 Spawn attempt " .. attempt .. " of " .. maxAttempts .. " (delay: " .. currentDelay .. "s)")

            -- Ensure we have a small pause; prevent rapid-fire killing
            task.wait(0.2)

            -- Force death first so spawn selection menu will be available
            pcall(function()
                if LP.Character and LP.Character:FindFirstChild("Humanoid") then
                    LP.Character.Humanoid.Health = 0
                end
            end)
            task.wait(0.6)

            -- Update spawn data on server (refresh choices) before selecting Military Base
            pcall(function()
                local upd = game:GetService("ReplicatedStorage"):FindFirstChild("SpawnSelectionUpdateSpawnData")
                if upd and upd.FireServer then
                    upd:FireServer()
                    print("🔁 SpawnSelectionUpdateSpawnData: fired")
                end
            end)
            task.wait(0.25)

            -- Now request Military Base spawn and load character
            pcall(function()
                local args = {"Military Base"}
                local sel = game:GetService("ReplicatedStorage"):FindFirstChild("SpawnSelectionSelect")
                if sel and sel.FireServer then
                    sel:FireServer(unpack(args))
                    print("🔁 SpawnSelectionSelect: 'Military Base' requested")
                end
            end)

            task.wait(0.5)
            pcall(function() LP:LoadCharacter() end)

            -- Allow character to fully load and settle before checking position
            task.wait(settleTime)

            if LP.Character then
                local root = LP.Character:FindFirstChild("HumanoidRootPart")
                if root then
                    local pos = root.Position
                    local dist = distanceToMilitaryBase(pos)
                    if dist then
                        print("📏 Distance to Military Base: " .. math.floor(dist) .. " studs")
                    else
                        print("📏 Distance to Military Base: unknown")
                    end

                    local radius = (ArrestSettings and ArrestSettings.UFOBunkerRadius) or 250
                    if dist and dist <= radius then
                        print("✅ Spawned at Military Base after attempt " .. attempt)
                        return true
                    else
                        print("⚠️ Not close enough to military base; killing and retrying...")
                        pcall(function()
                            if LP.Character and LP.Character:FindFirstChild("Humanoid") then
                                LP.Character.Humanoid.Health = 0
                            end
                        end)
                        task.wait(currentDelay)
                    end
                end
            end

            task.wait(currentDelay)
        end

        -- attempts exhausted
        if (ArrestSettings and ArrestSettings.ServerHopEnabled) then
            print("🔄 Spawn attempts failed after " .. maxAttempts .. " attempts; server hopping as fallback")
            pcall(serverHop)
        else
            print("⚠️ Spawn attempts failed after " .. maxAttempts .. " attempts and ServerHop disabled")
        end
        return false
    end)

    spawnAttemptInProgress = false
    if not ok then
        warn("⚠️ ensureSpawnAtMilitaryOrClearDoor error:", result)
    end
    return result
end

-- SWITCH TO POLICE IMMEDIATELY AT STARTUP
print("⚡ AUTO-SWITCHING TO POLICE TEAM...")
clickPoliceButton()
task.wait(2)
print("✅ Team switch complete - checking spawn location...")

-- Check if spawned at military base on startup
if LP.Character then
    local root = LP.Character:FindFirstChild("HumanoidRootPart")
    if root then
        local pos = root.Position
        -- Door handling removed in favor of repeated kill+respawn attempts
        local atMilitaryBase = isAtMilitaryBase(pos)
        
        -- Only respawn if we definitively know we're NOT at military base
        if atMilitaryBase == false then
            print("⚠️ Not at Military Base on startup!")
            print("🔄 Ensuring spawn is at Military Base (attempting repeated respawns)...")
            ensureSpawnAtMilitaryOrClearDoor(6)
        elseif atMilitaryBase == true then
            print("✅ Already at Military Base - good location!")
        else
            print("⚠️ Could not verify spawn location - continuing anyway")
        end
    end
end

print("✅ Initialization complete - starting arrest system...")

--// AUTO ARREST SETTINGS //--
local ArrestSettings = {
    Enabled = false, -- Will be set to true after GUI loads
    DetectionRange = 99999,
    FlyAltitude = 500,
    ApproachAltitude = 50,
    DropDistance = 30,
    ArrestRange = 15,
    OptimalRange = 6,
    CharacterFlySpeed = 125,
    VehicleFlySpeed = 300,
    DropSpeed = 40,
    UseCamaro = true,
    CamaroSpawnCooldown = 90,
    LastCamaroSpawn = 0,
    ChaseTeleportDistance = 1500,
    ReturnToFlyDistance = 500,
    DescentAlignmentThreshold = 8,
    EquipSpamEnabled = true,
    EquipSpamInterval = 1,
    BountyFilterEnabled = true,
    BountyThreshold = 2900,
    TargetMode = "closest",
    -- UFO / spawn tuning
    UFOBunkerRadius = 250, -- distance (studs) to consider "at Military Base" near UFO
    SpawnAttemptInitialDelay = 2.5, -- initial delay between spawn attempts
    SpawnAttemptBackoff = 1.0, -- incremental backoff added per attempt
    SpawnAttemptSettleTime = 1.0, -- wait after LoadCharacter before checking position
    ServerHopEnabled = true, -- ENABLED BY DEFAULT
    ServerHopDelay = 2,
    AutoStartAfterHop = false,
}

--// ARREST STATE //--
local arrestState = {
    active = false,
    targetPlayer = nil,
    phase = "SCANNING",
    chasing = false,
    arresting = false,
    handcuffsEquipped = false,
    lastEquipAttempt = 0,
    targetLastPos = nil,
    eHoldActive = false,
    currentVehicle = nil,
    inVehicle = false,
    originalCameraSubject = nil,
    originalCameraType = nil,
    spawnWaitStart = nil,
    startMoney = 0,
    startTime = 0,
    stuckStart = nil,
    lastPos = nil,
    awaitingRespawnStart = nil,
    awaitingRespawnPos = nil,
    respawnFallbackAttempts = 0,
    targetStartMoney = nil,
    prevLocalMoney = 0,
    targetCoverStart = nil,
    recentTargets = {},
    noTargetsStartTime = nil,
}

--// MAIN CONNECTIONS //--
local mainConnection = nil
local deathConnection = nil
local characterConnection = nil

-- Prevent overlapping spawn attempts
local spawnAttemptInProgress = false

--// UTILITY FUNCTIONS //--
local function getMoney()
    local leaderstats = LP:FindFirstChild("leaderstats")
    if leaderstats then
        local cash = leaderstats:FindFirstChild("Cash") or leaderstats:FindFirstChild("Money")
        if cash then
            return cash.Value
        end
    end
    return 0
end

local function getPlayerMoney(player)
    if not player then return nil end
    local leaderstats = player:FindFirstChild("leaderstats")
    if leaderstats then
        local cash = leaderstats:FindFirstChild("Cash") or leaderstats:FindFirstChild("Money")
        if cash then
            return cash.Value
        end
    end
    return nil
end

local function getPlayerBounty(player)
    if not player then return 0 end
    local leaderstats = player:FindFirstChild("leaderstats")
    if leaderstats then
        local b = leaderstats:FindFirstChild("Bounty")
        if b and (type(b.Value) == "number" or tonumber(b.Value)) then
            return tonumber(b.Value) or 0
        end
    end
    local bData = RS:FindFirstChild("BountyData", true)
    if bData then
        local dataString = bData.Value or tostring(bData)
        local success, decoded = pcall(function()
            return HttpService:JSONDecode(dataString)
        end)
        if success and type(decoded) == "table" then
            for _, entry in ipairs(decoded) do
                local entryUserId = tonumber(entry.UserId) or entry.UserId
                local entryName = entry.Name
                if entryUserId == player.UserId or entryName == player.Name then
                    local bountyAmount = tonumber(entry.Bounty) or tonumber(entry.Amount) or 0
                    return bountyAmount
                end
            end
        end
    end
    return 0
end

local function formatNumber(num) 
    local formatted = tostring(num)
    while true do
        formatted, k = formatted:gsub("^(-?%d+)(%d%d%d)", '%1,%2')
        if (k == 0) then
            break
        end
    end
    return formatted
end

local function isTargetValid(player)
    if not player or not player.Character then return false end
    if player == LP then return false end
    if not player.Team or player.Team.Name ~= "Criminal" then return false end
    local humanoid = player.Character:FindFirstChild("Humanoid")
    if not humanoid or humanoid.Health <= 0 then return false end
    local root = player.Character:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    return true
end

local function isPlayerUnderCover(player)
    if not player or not player.Character then return true end
    local root = player.Character:FindFirstChild("HumanoidRootPart")
    if not root then return true end
    local rayOrigin = root.Position + Vector3.new(0, 5, 0)
    local rayDirection = Vector3.new(0, 500, 0)
    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {LP.Character, player.Character}
    local result = W:Raycast(rayOrigin, rayDirection, raycastParams)
    if result then
        return true
    end
    return false
end

local function findNearestCriminal()
    local char = LP.Character
    if not char then return nil end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return nil end
    
    local nearestCriminal = nil
    local shortestDistance = ArrestSettings.DetectionRange
    local highestBounty = 0
    local now = tick()

    for userId, expiry in pairs(arrestState.recentTargets) do
        if expiry and expiry <= now then
            arrestState.recentTargets[userId] = nil
        end
    end

    for _, player in ipairs(P:GetPlayers()) do
        if isTargetValid(player) then
            local expiry = arrestState.recentTargets[player.UserId]
            if expiry and expiry > now then
            else
                if isPlayerUnderCover(player) then
                    print("⚠️ Skipping " .. player.Name .. " - under terrain/roof")
                else
                    if ArrestSettings.BountyFilterEnabled then
                        local b = getPlayerBounty(player)
                        if b < ArrestSettings.BountyThreshold then
                            if b == 0 then
                                print("⚠️ Skipping " .. player.Name .. " - no bounty")
                            else
                                print("⚠️ Skipping " .. player.Name .. " - bounty too low ($" .. tostring(b) .. " < $" .. ArrestSettings.BountyThreshold .. ")")
                            end
                        else
                            local targetRoot = player.Character:FindFirstChild("HumanoidRootPart")
                            if targetRoot then
                                local distance = (root.Position - targetRoot.Position).Magnitude
                                if ArrestSettings.TargetMode == "highest bounty" then
                                    if b > highestBounty then
                                        highestBounty = b
                                        nearestCriminal = player
                                        shortestDistance = distance
                                    end
                                else
                                    if distance < shortestDistance then
                                        shortestDistance = distance
                                        nearestCriminal = player
                                    end
                                end
                            end
                        end
                    else
                        local targetRoot = player.Character:FindFirstChild("HumanoidRootPart")
                        if targetRoot then
                            local distance = (root.Position - targetRoot.Position).Magnitude
                            if ArrestSettings.TargetMode == "highest bounty" then
                                local b = getPlayerBounty(player) or 0
                                if b > highestBounty then
                                    highestBounty = b
                                    nearestCriminal = player
                                    shortestDistance = distance
                                end
                            else
                                if distance < shortestDistance then
                                    shortestDistance = distance
                                    nearestCriminal = player
                                end
                            end
                        end
                    end
                end
            end
        end
    end
    
    return nearestCriminal, shortestDistance
end

local function findNearbyCriminalAround(position, radius)
    radius = radius or 75
    for _, player in ipairs(P:GetPlayers()) do
        if isTargetValid(player) and player ~= arrestState.targetPlayer then
            local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
            if root then
                local d = (root.Position - position).Magnitude
                if d <= radius and not isPlayerUnderCover(player) then
                    return player, d
                end
            end
        end
    end
    return nil, nil
end

local function findAvailableVehicleNear(position, radius, preferredNames)
    radius = radius or 400
    preferredNames = preferredNames or {"Camaro", "Jeep"}
    local vehiclesFolder = W:FindFirstChild("Vehicles")
    local candidates = {}
    local function considerModel(model)
        local seat = model:FindFirstChildWhichIsA("VehicleSeat") or model:FindFirstChildWhichIsA("Seat")
        if not seat then return end
        if seat.Occupant then return end
        local pos = seat.Position
        local d = (pos - position).Magnitude
        if d <= radius then
            table.insert(candidates, {model = model, seat = seat, dist = d})
        end
    end
    if vehiclesFolder then
        for _, v in pairs(vehiclesFolder:GetChildren()) do
            if v:IsA("Model") then
                considerModel(v)
            end
        end
    end
    for _, obj in pairs(W:GetChildren()) do
        if obj:IsA("Model") and obj:FindFirstChildWhichIsA("VehicleSeat") then
            considerModel(obj)
        end
    end
    table.sort(candidates, function(a, b)
        local an = a.model.Name or ""
        local bn = b.model.Name or ""
        local ap = 9999
        local bp = 9999
        for i, name in ipairs(preferredNames) do
            if an:lower():match(name:lower()) then ap = i end
            if bn:lower():match(name:lower()) then bp = i end
        end
        if ap ~= bp then return ap < bp end
        return a.dist < b.dist
    end)
    if #candidates > 0 then
        return candidates[1].model, candidates[1].seat, candidates[1].dist
    end
    return nil, nil, nil
end

local function isVehicleOnPath(vehiclePos, fromPos, toPos, threshold)
    threshold = threshold or 30
    local a = Vector3.new(fromPos.X, 0, fromPos.Z)
    local b = Vector3.new(toPos.X, 0, toPos.Z)
    local v = Vector3.new(vehiclePos.X, 0, vehiclePos.Z)
    local ab = b - a
    local abLen2 = ab.Magnitude^2
    if abLen2 == 0 then
        return (v - a).Magnitude <= threshold
    end
    local t = ((v - a):Dot(ab)) / abLen2
    if t < 0 or t > 1 then return false end
    local closest = a + ab * t
    return (v - closest).Magnitude <= threshold
end

local function equipHandcuffs(retries)
    retries = retries or 3
    for i = 1, retries do
        local currentTime = tick()
        if currentTime - arrestState.lastEquipAttempt < 0.45 then
            task.wait(0.45)
        end
        arrestState.lastEquipAttempt = tick()
        local ok = false
        pcall(function()
            local args = {true}
            local folder = LP:FindFirstChild("Folder")
            if folder then
                local cuffs = folder:FindFirstChild("Handcuffs")
                if cuffs and cuffs:FindFirstChild("InventoryEquipRemote") then
                    cuffs.InventoryEquipRemote:FireServer(unpack(args))
                    ok = true
                else
                    -- Try direct remote if structure differs
                    local rem = folder:FindFirstChildWhichIsA("RemoteEvent") or folder:FindFirstChild("InventoryEquipRemote")
                    if rem and rem.FireServer then
                        rem:FireServer(unpack(args))
                        ok = true
                    end
                end
            end
        end)
        if ok then
            arrestState.handcuffsEquipped = true
            return true
        end
        task.wait(0.2)
    end
    -- Final attempt failed, log once
    warn("⚠️ equipHandcuffs: failed to equip handcuffs after " .. retries .. " attempts")
    return false
end

local function getVehicle()
    local char = LP.Character
    if not char then return nil end
    local hum = char:FindFirstChild("Humanoid")
    if not hum then return nil end
    local vehiclesFolder = W:FindFirstChild("Vehicles")
    if vehiclesFolder then
        for _, vehicle in pairs(vehiclesFolder:GetChildren()) do
            if vehicle:IsA("Model") then
                for _, obj in pairs(vehicle:GetChildren()) do
                    if obj.Name:match("^_VehicleState_") then
                        local playerName = obj.Name:gsub("^_VehicleState_", "")
                        if playerName:lower() == LP.Name:lower() then
                            return vehicle
                        end
                    end
                end
            end
        end
    end
    if hum.SeatPart then
        local seat = hum.SeatPart
        if seat.Parent and seat.Parent:IsA("Model") then
            return seat.Parent
        end
    end
    if hum.Sit then
        local root = char:FindFirstChild("HumanoidRootPart")
        if root then
            for _, model in pairs(W:GetChildren()) do
                if model:IsA("Model") and model ~= char then
                    local seat = model:FindFirstChildWhichIsA("VehicleSeat") or model:FindFirstChildWhichIsA("Seat")
                    if seat then
                        local dist = (seat.Position - root.Position).Magnitude
                        if dist < 10 then
                            return model
                        end
                    end
                end
            end
        end
    end
    return nil
end

local function isInVehicle()
    local veh = getVehicle()
    return veh ~= nil
end

local function setVehicleHeight(vehicle)
    if not vehicle then return end
    pcall(function()
        for _, obj in pairs(vehicle:GetDescendants()) do
            if obj:IsA("Folder") or obj:IsA("Configuration") then
                if obj.Name:match("^_VehicleState_") then
                    local heightValue = obj:FindFirstChild("Height")
                    if heightValue and (heightValue:IsA("NumberValue") or heightValue:IsA("IntValue")) then
                        heightValue.Value = 50
                        print("✅ Set vehicle height to 50")
                    end
                end
            end
        end
    end)
end

local function canSpawnCamaro()
    local currentTime = tick()
    local timeSinceLastSpawn = currentTime - ArrestSettings.LastCamaroSpawn
    return timeSinceLastSpawn >= ArrestSettings.CamaroSpawnCooldown
end

local function getSpawnCooldownRemaining()
    local currentTime = tick()
    local timeSinceLastSpawn = currentTime - ArrestSettings.LastCamaroSpawn
    local remaining = ArrestSettings.CamaroSpawnCooldown - timeSinceLastSpawn
    return math.max(0, remaining)
end

local function spawnCamaro()
    if not canSpawnCamaro() then
        local remaining = getSpawnCooldownRemaining()
        print("⏳ Camaro spawn on cooldown: " .. math.ceil(remaining) .. "s remaining")
        return false
    end
    print("🚗 Spawning Camaro...")
    local success = pcall(function()
        local args = {"Chassis", "Camaro"}
        game:GetService("ReplicatedStorage"):WaitForChild("GarageSpawnVehicle"):FireServer(unpack(args))
    end)
    if success then
        ArrestSettings.LastCamaroSpawn = tick()
        print("✅ Camaro spawn requested!")
        return true
    else
        print("❌ Failed to spawn Camaro")
        return false
    end
end

local function findVehicleSeat(vehicle)
    if not vehicle then return nil end
    local seat = vehicle:FindFirstChild("Seat")
    if seat and (seat:IsA("Seat") or seat:IsA("VehicleSeat")) then
        return seat
    end
    for _, obj in pairs(vehicle:GetChildren()) do
        if obj:IsA("VehicleSeat") or obj:IsA("Seat") then
            return obj
        end
    end
    return nil
end

local function exitVehicle()
    local char = LP.Character
    if not char then return false end
    local hum = char:FindFirstChild("Humanoid")
    if not hum then return false end
    print("🚪 Exiting vehicle...")
    pcall(function()
        hum.Sit = false
        hum.Jump = true
    end)
    task.wait(0.3)
    arrestState.inVehicle = false
    arrestState.currentVehicle = nil
    pcall(function()
        local pHum = LP.Character and LP.Character:FindFirstChild("Humanoid")
        if pHum then
            Camera.CameraType = Enum.CameraType.Custom
            Camera.CameraSubject = pHum
        end
    end)
    if not arrestState.handcuffsEquipped then
        pcall(function()
            for i = 1, 3 do
                local ok = pcall(function()
                    local args = { true }
                    game:GetService("Players").LocalPlayer:WaitForChild("Folder"):WaitForChild("Handcuffs"):WaitForChild("InventoryEquipRemote"):FireServer(unpack(args))
                end)
                if ok then
                    arrestState.handcuffsEquipped = true
                    arrestState.lastEquipAttempt = tick()
                    print("🔫 Equipping handcuffs (exit attempt " .. i .. ")")
                    break
                else
                    print("⚠️ Equip attempt failed (exit) attempt " .. i)
                end
                task.wait(0.2)
            end
        end)
    end
    return true
end

local eCycleThread = nil
local equipSpamThread = nil

local function startEquipSpam()
    if equipSpamThread then return end
    if not ArrestSettings.EquipSpamEnabled then return end
    equipSpamThread = task.spawn(function()
        while ArrestSettings.EquipSpamEnabled do
            pcall(function()
                local args = { true }
                game:GetService("Players").LocalPlayer:WaitForChild("Folder"):WaitForChild("Handcuffs"):WaitForChild("InventoryEquipRemote"):FireServer(unpack(args))
            end)
            task.wait(ArrestSettings.EquipSpamInterval or 1)
        end
    end)
end

local function stopEquipSpam()
    if equipSpamThread then
        task.cancel(equipSpamThread)
        equipSpamThread = nil
    end
end

local function startECycle()
    if arrestState.eHoldActive then return end
    arrestState.eHoldActive = true
    eCycleThread = task.spawn(function()
        while arrestState.eHoldActive do
            if arrestState.phase == "ARRESTING" and arrestState.targetPlayer then
                VIM:SendKeyEvent(true, Enum.KeyCode.E, false, game)
                task.wait(1.5)
                VIM:SendKeyEvent(false, Enum.KeyCode.E, false, game)
                for i = 1, 5 do
                    VIM:SendKeyEvent(true, Enum.KeyCode.E, false, game)
                    task.wait(0.05)
                    VIM:SendKeyEvent(false, Enum.KeyCode.E, false, game)
                    task.wait(0.05)
                end
            else
                task.wait(0.1)
            end
        end
    end)
end

local function stopECycle()
    arrestState.eHoldActive = false
    if eCycleThread then
        task.cancel(eCycleThread)
        eCycleThread = nil
    end
    VIM:SendKeyEvent(false, Enum.KeyCode.E, false, game)
end

local function getRoot(model)
    if not model then return nil end
    if model:FindFirstChild("Engine") then return model.Engine end
    if model:FindFirstChild("BoundingBox") then return model.BoundingBox end
    return model.PrimaryPart or model:FindFirstChild("HumanoidRootPart") or model:FindFirstChildWhichIsA("BasePart")
end

local function findGroundHeight(position)
    local rayOrigin = Vector3.new(position.X, position.Y + 100, position.Z)
    local rayDirection = Vector3.new(0, -500, 0)
    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {LP.Character}
    local result = W:Raycast(rayOrigin, rayDirection, raycastParams)
    if result then
        return result.Position.Y
    end
    return position.Y - 200
end

local function teleportToHeight(targetHeight)
    local char = LP.Character
    if not char then return false end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    local currentPos = root.Position
    local groundHeight = findGroundHeight(currentPos)
    local safeHeight = math.max(targetHeight, groundHeight + 5)
    local newPos = Vector3.new(currentPos.X, safeHeight, currentPos.Z)
    pcall(function()
        root.CFrame = CFrame.new(newPos)
        root.AssemblyLinearVelocity = Vector3.zero
        root.AssemblyAngularVelocity = Vector3.zero
    end)
    return true
end

local function teleportToPosition(targetPos, allowHorizontal)
    local char = LP.Character
    if not char then return false end
    local veh = getVehicle()
    local root = veh and getRoot(veh) or char:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    if not allowHorizontal then
        local currentPos = root.Position
        targetPos = Vector3.new(currentPos.X, targetPos.Y, currentPos.Z)
    end
    pcall(function()
        root.CFrame = CFrame.new(targetPos)
        root.AssemblyLinearVelocity = Vector3.zero
        root.AssemblyAngularVelocity = Vector3.zero
    end)
    return true
end

local function teleportVerticalTo(targetY)
    local char = LP.Character
    if not char then return false end
    local veh = getVehicle()
    local root = veh and getRoot(veh) or char:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    pcall(function()
        local pos = root.Position
        root.CFrame = CFrame.new(pos.X, targetY, pos.Z)
        root.AssemblyLinearVelocity = Vector3.zero
        root.AssemblyAngularVelocity = Vector3.zero
    end)
    return true
end

local function performInstantDrop(targetRoot, descendY, preferFullXZ)
    local char = LP.Character
    if not char or not targetRoot then return false end
    local veh = getVehicle()
    local rootPart = veh and getRoot(veh) or char:FindFirstChild("HumanoidRootPart")
    if not rootPart then return false end
    local success = false
    for i = 1, 3 do
        pcall(function() teleportVerticalTo(descendY) end)
        task.wait(0.06)
        if rootPart.Position.Y <= descendY + 1 then
            success = true
            break
        end
    end
    if not success and preferFullXZ then
        print("⚠️ Y-only drop failed, using emergency full teleport")
        pcall(function()
            teleportToPosition(Vector3.new(targetRoot.Position.X, descendY, targetRoot.Position.Z), true)
        end)
        task.wait(0.06)
        success = rootPart.Position.Y <= descendY + 1
    end
    if not success then
        pcall(function()
            rootPart.Anchored = true
            rootPart.CFrame = CFrame.new(rootPart.Position.X, descendY, rootPart.Position.Z)
            rootPart.AssemblyLinearVelocity = Vector3.zero
            rootPart.AssemblyAngularVelocity = Vector3.zero
        end)
        task.wait(0.06)
        success = rootPart.Position.Y <= descendY + 1
        pcall(function() rootPart.Anchored = false end)
    end
    pcall(function()
        rootPart.AssemblyLinearVelocity = Vector3.zero
        rootPart.AssemblyAngularVelocity = Vector3.zero
    end)
    if success then
        arrestState.forceInstantDrop = nil
        arrestState.justTeleportedAbove = nil
        if isInVehicle() then
            if arrestState.originalCameraSubject then
                pcall(function()
                    Camera.CameraType = arrestState.originalCameraType or Enum.CameraType.Custom
                    Camera.CameraSubject = arrestState.originalCameraSubject
                end)
            end
            exitVehicle()
            task.wait(0.5)
            if not arrestState.handcuffsEquipped then
                equipHandcuffs()
                task.wait(0.2)
                equipHandcuffs()
                task.wait(0.2)
                equipHandcuffs()
            end
        end
    else
        print("⚠️ Drop fallback attempts exhausted - still not at descendY")
    end
    return success
end

local function flyToPosition(targetPos, speed)
    local char = LP.Character
    if not char then return false end
    local veh = getVehicle()
    local actualSpeed = veh and ArrestSettings.VehicleFlySpeed or ArrestSettings.CharacterFlySpeed
    local root = veh and getRoot(veh) or char:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    if not veh then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            pcall(function()
                hum.PlatformStand = true
            end)
        end
    end
    pcall(function()
        root.Anchored = false
        if veh then
            if root:CanSetNetworkOwnership() then
                root:SetNetworkOwner(LP)
            end
        end
    end)
    local currentPos = root.Position
    local distance = (targetPos - currentPos).Magnitude
    if distance < 5 then
        root.AssemblyLinearVelocity = Vector3.zero
        root.AssemblyAngularVelocity = Vector3.zero
        return true
    end
    local direction = (targetPos - currentPos).Unit
    pcall(function()
        root.CFrame = CFrame.new(currentPos, currentPos + direction)
        root.AssemblyLinearVelocity = direction * actualSpeed
        root.AssemblyAngularVelocity = Vector3.zero
    end)
    return false
end

local function flyHorizontalToPosition(targetPos, maintainHeight)
    local char = LP.Character
    if not char then return false end
    local veh = getVehicle()
    local root = veh and getRoot(veh) or char:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    local currentPos = root.Position
    local flyHeight = maintainHeight or currentPos.Y
    local horizontalTarget = Vector3.new(targetPos.X, flyHeight, targetPos.Z)
    return flyToPosition(horizontalTarget)
end

local function resetCharacterState()
    print("🔄 Resetting character state...")
    local char = LP.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")
    pcall(function()
        if root then
            root.AssemblyLinearVelocity = Vector3.zero
            root.AssemblyAngularVelocity = Vector3.zero
            root.Anchored = false
        end
        if hum then
            hum.PlatformStand = false
            hum.Sit = false
            hum:ChangeState(Enum.HumanoidStateType.GettingUp)
            task.wait(0.1)
            hum:SetStateEnabled(Enum.HumanoidStateType.Running, true)
            hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
            hum:SetStateEnabled(Enum.HumanoidStateType.Freefall, true)
            hum:SetStateEnabled(Enum.HumanoidStateType.Flying, false)
        end
    end)
end

local function respawnToMilitaryBase()
    print("⚠️ Respawning to Military Base due to stuck detection...")
    pcall(function()
        local args = {"Military Base"}
        game:GetService("ReplicatedStorage"):WaitForChild("SpawnSelectionSelect"):FireServer(unpack(args))
    end)
    pcall(function()
        LP:LoadCharacter()
    end)
end

-- duplicate fragment removed; single isAtMilitaryBase implementation exists above


local function displayAllBounties()
    print("\n" .. string.rep("=", 60))
    print("💰 ACTIVE BOUNTIES 💰")
    print(string.rep("=", 60))
    local bData = RS:FindFirstChild("BountyData", true)
    if not bData then
        print("❌ BountyData not found!")
        print(string.rep("=", 60) .. "\n")
        return
    end
    local dataString = bData.Value or tostring(bData)
    local success, decoded = pcall(function()
        return HttpService:JSONDecode(dataString)
    end)
    if not success or type(decoded) ~= "table" or #decoded == 0 then
        print("❌ No bounties found or failed to parse!")
        print(string.rep("=", 60) .. "\n")
        return
    end
    table.sort(decoded, function(a, b)
        return (a.Bounty or 0) > (b.Bounty or 0)
    end)
    for i, bounty in ipairs(decoded) do
        local name = bounty.Name or "Unknown"
        local amount = bounty.Bounty or 0
        local userId = bounty.UserId or "N/A"
        print(string.format(
            "🎯 #%d | %s | $%s | ID: %s",
            i,
            name,
            tostring(amount),
            tostring(userId)
        ))
    end
    print(string.rep("=", 60))
    print(string.format("Total Bounties: %d", #decoded))
    print(string.rep("=", 60) .. "\n")
end

local TeleportService = game:GetService("TeleportService")

local function serverHop()
    print("🔄 SERVER HOPPING - No valid targets found...")
    -- Move player up to Y=500 to 'chill' before hopping
    pcall(function()
        local char = LP and LP.Character
        if char then
            local root = char:FindFirstChild("HumanoidRootPart")
            if root then
                root.CFrame = CFrame.new(root.Position.X, 500, root.Position.Z)
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
                print("🔼 Moved to Y=500 in preparation for server hop")
            end
        end
    end)
    task.wait(0.35)
    local scriptToQueue = getgenv().SFAA_SCRIPT_SOURCE
    if not scriptToQueue or scriptToQueue == "" then
        warn("⚠️ Script source not found - attempting to capture current script")
        local info = debug.getinfo(1, "S")
        if info.source and info.source:sub(1,1) == "@" then
            pcall(function()
                scriptToQueue = readfile(info.source:sub(2))
                getgenv().SFAA_SCRIPT_SOURCE = scriptToQueue
            end)
        end
    end
    local queueSuccess = false
    local queueCount = 0
    pcall(function()
        if queue_on_teleport then
            queue_on_teleport(scriptToQueue or [[print("⚠️ SFAA failed to reload - script source missing")]])
            queueSuccess = true
            queueCount = queueCount + 1
            print("✅ Method 1: queue_on_teleport SUCCESS")
        end
    end)
    pcall(function()
        if syn and syn.queue_on_teleport then
            syn.queue_on_teleport(scriptToQueue or [[print("⚠️ SFAA failed to reload - script source missing")]])
            queueSuccess = true
            queueCount = queueCount + 1
            print("✅ Method 2: syn.queue_on_teleport SUCCESS")
        end
    end)
    pcall(function()
        if fluxus and fluxus.queue_on_teleport then
            fluxus.queue_on_teleport(scriptToQueue or [[print("⚠️ SFAA failed to reload - script source missing")]])
            queueSuccess = true
            queueCount = queueCount + 1
            print("✅ Method 3: fluxus.queue_on_teleport SUCCESS")
        end
    end)
    pcall(function()
        if writefile then
            writefile("SFAA_AutoReload.lua", scriptToQueue or [[print("⚠️ SFAA backup file empty")]])
            print("✅ Method 4: Saved to SFAA_AutoReload.lua")
        end
    end)
    if queueSuccess then
        print("🎉 Script queued " .. queueCount .. " time(s) - Will AUTO-RUN in new server!")
    else
        warn("⚠️ Auto-run may not work - Add script to autoexec for guaranteed reload")
    end
    task.wait(0.5)
    local success, servers = pcall(function()
        return game:GetService("HttpService"):JSONDecode(
            game:HttpGet("https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=Asc&limit=100")
        ).data
    end)
    if success and servers then
        local currentServerId = game.JobId
        local goodServers = {}
        
        -- Filter servers with 4-8 slots remaining
        for _, server in ipairs(servers) do
            if server.id ~= currentServerId then
                local slotsRemaining = server.maxPlayers - server.playing
                if slotsRemaining >= 4 and slotsRemaining <= 8 then
                    table.insert(goodServers, server)
                end
            end
        end
        
        -- Sort by most populated (descending) to get fuller servers first
        table.sort(goodServers, function(a, b)
            return a.playing > b.playing
        end)
        
        if #goodServers > 0 then
            local targetServer = goodServers[1]
            local slotsLeft = targetServer.maxPlayers - targetServer.playing
            print("🌐 Teleporting to POPULATED server: " .. targetServer.playing .. "/" .. targetServer.maxPlayers .. " players (" .. slotsLeft .. " slots left)")
            TeleportService:TeleportToPlaceInstance(game.PlaceId, targetServer.id, LP)
            return
        else
            print("⚠️ No servers found with 4-8 slots remaining, trying any server with space...")
            -- Fallback: find any server with at least 4 slots
            table.sort(servers, function(a, b)
                return a.playing > b.playing
            end)
            for _, server in ipairs(servers) do
                if server.id ~= currentServerId and server.playing < server.maxPlayers - 4 then
                    print("🌐 Teleporting to server with " .. server.playing .. " players...")
                    TeleportService:TeleportToPlaceInstance(game.PlaceId, server.id, LP)
                    return
                end
            end
        end
    end
    print("🌐 Teleporting to random server...")
    TeleportService:Teleport(game.PlaceId, LP)
end

local function resetArrestState()
    print("💀 Player died - resetting state...")
    arrestState.targetPlayer = nil
    arrestState.phase = "SCANNING"
    arrestState.chasing = false
    arrestState.arresting = false
    arrestState.handcuffsEquipped = false
    arrestState.currentVehicle = nil
    arrestState.inVehicle = false
    arrestState.startMoney = 0
    arrestState.startTime = 0
    arrestState.targetStartMoney = nil
    arrestState.prevLocalMoney = getMoney() or 0
    arrestState.targetCoverStart = nil
    arrestState.stuckStart = nil
    arrestState.lastPos = nil
    arrestState.awaitingRespawnStart = nil
    arrestState.awaitingRespawnPos = nil
    arrestState.respawnFallbackAttempts = 0
    pcall(function() stopECycle() end)
    pcall(function() resetCharacterState() end)
end

-- duplicate isAtMilitaryBase removed; single implementation exists above


local function onCharacterAdded(character)
    print("✅ Character respawned - reconnecting systems...")
    local root = character:WaitForChild("HumanoidRootPart")
    local hum = character:WaitForChild("Humanoid")
    
    task.wait(2) -- Wait for spawn to complete
    
    -- Check if spawned at military base
    local spawnPos = root.Position
    -- Door handling removed; use kill+respawn loop instead
    local atMilitaryBase = isAtMilitaryBase(spawnPos)
    
    -- Only respawn if we definitively know we're NOT at military base
    if atMilitaryBase == false then
        print("⚠️ Spawned at wrong location (not Military Base)")
        print("🔄 Ensuring spawn is at Military Base (attempting repeated respawns)...")
        local ok = ensureSpawnAtMilitaryOrClearDoor(6)
        if not ok then
            print("⚠️ Could not secure a good spawn; leaving and will reconnect on next spawn.")
            return -- Exit early, will reconnect when new character spawns
        end
    elseif atMilitaryBase == true then
        print("✅ Spawned at Military Base - good location!")
    else
        print("⚠️ Could not verify spawn location - continuing anyway")
    end
    
    resetArrestState()
    -- ensure equip-spam runs after respawn
    if ArrestSettings.EquipSpamEnabled then
        task.spawn(function()
            task.wait(0.5)
            startEquipSpam()
        end)
    end
    if ArrestSettings.Enabled then
        arrestState.startMoney = getMoney()
        arrestState.prevLocalMoney = getMoney()
        arrestState.startTime = tick()
    end
    if deathConnection then
        deathConnection:Disconnect()
    end
    if hum then
        deathConnection = hum.Died:Connect(function()
            print("💀 Player died - attempting to respawn at Military Base repeatedly...")
            -- Spawn attempts run in a background thread so we don't block the death event
            task.spawn(function()
                ensureSpawnAtMilitaryOrClearDoor(6)
            end)
            resetArrestState()
        end)
        -- Ensure equip spam is active after respawn
        if ArrestSettings.EquipSpamEnabled then
            task.spawn(function()
                task.wait(1)
                startEquipSpam()
            end)
        end
    end
end

local lastMainUpdate = 0
local mainUpdateInterval = 0.03

local function startArrestSystem()
    if mainConnection then return end
    print("🚨 SFAA Started - V8.0")
    print("💰 Bounty Filter: " .. (ArrestSettings.BountyFilterEnabled and "ACTIVE" or "DISABLED") .. " - Min bounty: " .. ArrestSettings.BountyThreshold)
    print("🎯 Target Mode: " .. ArrestSettings.TargetMode)
    print("🔄 Server Hop: " .. (ArrestSettings.ServerHopEnabled and "ENABLED (targets 4-8 slots)" or "DISABLED"))
    displayAllBounties()
    
    mainConnection = R.Heartbeat:Connect(function()
        if not ArrestSettings.Enabled then
            if mainConnection then
                mainConnection:Disconnect()
                mainConnection = nil
            end
            stopECycle()
            return
        end
        local currentTime = tick()
        if currentTime - lastMainUpdate < mainUpdateInterval then
            return
        end
        lastMainUpdate = currentTime
        local char = LP.Character
        if not char then return end
        local root = char:FindFirstChild("HumanoidRootPart")
        local hum = char:FindFirstChildOfClass("Humanoid")
        if not root or not hum then return end
        local currentPos = root.Position
        if arrestState.lastPos then
            local moved = (currentPos - arrestState.lastPos).Magnitude
            if moved < 1 then
                if not arrestState.stuckStart then
                    arrestState.stuckStart = tick()
                else
                    if tick() - arrestState.stuckStart >= 10 then
                        print("⚠️ Player stuck detected for 10s — requesting respawn to Military Base")
                        arrestState.awaitingRespawnStart = tick()
                        arrestState.awaitingRespawnPos = currentPos
                        arrestState.respawnFallbackAttempts = 0
                        respawnToMilitaryBase()
                        resetArrestState()
                        arrestState.recentTargets = {}
                        arrestState.stuckStart = nil
                        arrestState.lastPos = nil
                        task.wait(1)
                        return
                    end
                end
            else
                arrestState.stuckStart = nil
            end
        end
        arrestState.lastPos = currentPos
        if arrestState.awaitingRespawnStart then
            local elapsed = tick() - arrestState.awaitingRespawnStart
            local movedSince = (currentPos - (arrestState.awaitingRespawnPos or currentPos)).Magnitude
            if movedSince > 1 then
                print("✅ Respawn or movement detected; clearing awaiting respawn state")
                arrestState.awaitingRespawnStart = nil
                arrestState.awaitingRespawnPos = nil
                arrestState.respawnFallbackAttempts = 0
            else
                if elapsed >= 3 and arrestState.respawnFallbackAttempts == 0 then
                    print("⚠️ Respawn fallback #1: calling LoadCharacter()")
                    pcall(function() LP:LoadCharacter() end)
                    arrestState.respawnFallbackAttempts = 1
                    arrestState.awaitingRespawnStart = tick()
                elseif elapsed >= 6 and arrestState.respawnFallbackAttempts == 1 then
                    print("⚠️ Respawn fallback #2: teleporting character upward and retrying LoadCharacter()")
                    pcall(function()
                        local char2 = LP.Character
                        if char2 then
                            local root2 = char2:FindFirstChild("HumanoidRootPart")
                            if root2 then
                                root2.CFrame = root2.CFrame + Vector3.new(0, 120, 0)
                                root2.AssemblyLinearVelocity = Vector3.zero
                                print("⚠️ Teleported character up as fallback")
                            end
                        end
                        pcall(function() LP:LoadCharacter() end)
                    end)
                    arrestState.respawnFallbackAttempts = 2
                    arrestState.awaitingRespawnStart = tick()
                end
            end
        end
        if hum.Health <= 0 then
            resetArrestState()
            return
        end
        arrestState.inVehicle = isInVehicle()
        if arrestState.inVehicle then
            arrestState.currentVehicle = getVehicle()
        end
        pcall(function()
            local pHum = LP.Character and LP.Character:FindFirstChild("Humanoid")
            if pHum and Camera.CameraSubject ~= pHum then
                Camera.CameraSubject = pHum
                Camera.CameraType = Enum.CameraType.Custom
            end
        end)
        local currentLocalMoney = getMoney()
        if arrestState.prevLocalMoney and currentLocalMoney > arrestState.prevLocalMoney then
            print("💰 Earnings detected (money increased) - retargeting")
            if arrestState.targetPlayer then
                arrestState.recentTargets[arrestState.targetPlayer.UserId] = tick() + 10
            end
            arrestState.phase = "SCANNING"
            arrestState.targetPlayer = nil
            arrestState.targetStartMoney = nil
            arrestState.targetCoverStart = nil
            pcall(function() stopECycle() end)
            arrestState.handcuffsEquipped = false
            arrestState.prevLocalMoney = currentLocalMoney
            return
        end
        arrestState.prevLocalMoney = currentLocalMoney
        if arrestState.targetPlayer and arrestState.targetStartMoney ~= nil then
            local tMoney = getPlayerMoney(arrestState.targetPlayer)
            if tMoney and tMoney < arrestState.targetStartMoney then
                print("💸 Target money dropped - likely arrested. Retargeting.")
                arrestState.recentTargets[arrestState.targetPlayer.UserId] = tick() + 10
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                arrestState.targetStartMoney = nil
                arrestState.targetCoverStart = nil
                pcall(function() stopECycle() end)
                arrestState.handcuffsEquipped = false
                return
            end
        end
        if arrestState.targetPlayer and isTargetValid(arrestState.targetPlayer) then
            if ArrestSettings.BountyFilterEnabled then
                local tb = getPlayerBounty(arrestState.targetPlayer)
                if tb < ArrestSettings.BountyThreshold then
                    if tb == 0 then
                        print("⚠️ Current target " .. arrestState.targetPlayer.Name .. " lost their bounty - retargeting.")
                    else
                        print("⚠️ Current target " .. arrestState.targetPlayer.Name .. " bounty dropped below threshold ($" .. tostring(tb) .. " < $" .. ArrestSettings.BountyThreshold .. ") - retargeting.")
                    end
                    arrestState.recentTargets[arrestState.targetPlayer.UserId] = tick() + 10
                    arrestState.phase = "SCANNING"
                    arrestState.targetPlayer = nil
                    arrestState.targetStartMoney = nil
                    arrestState.targetCoverStart = nil
                    pcall(function() stopECycle() end)
                    arrestState.handcuffsEquipped = false
                    return
                end
            end
            local underCover = isPlayerUnderCover(arrestState.targetPlayer)
            if underCover then
                if not arrestState.targetCoverStart then
                    arrestState.targetCoverStart = tick()
                    print("⚠️ Target went under cover - waiting 8s for them to return...")
                else
                    if tick() - arrestState.targetCoverStart >= 8 then
                        print("⏳ Target remained under cover for 8s - retargeting.")
                        arrestState.recentTargets[arrestState.targetPlayer.UserId] = tick() + 10
                        arrestState.phase = "SCANNING"
                        arrestState.targetPlayer = nil
                        arrestState.targetStartMoney = nil
                        arrestState.targetCoverStart = nil
                        pcall(function() stopECycle() end)
                        arrestState.handcuffsEquipped = false
                        return
                    end
                end
            else
                if arrestState.targetCoverStart then
                    print("✅ Target returned to open sky - resuming engagement")
                    arrestState.targetCoverStart = nil
                end
            end
        end
        if arrestState.phase == "SCANNING" then
            if not arrestState.targetPlayer or not isTargetValid(arrestState.targetPlayer) then
                pcall(function() stopECycle() end)
                arrestState.handcuffsEquipped = false
                local newTarget, distance = findNearestCriminal()
                if newTarget then
                    arrestState.noTargetsStartTime = nil
                    arrestState.targetPlayer = newTarget
                    arrestState.targetStartMoney = getPlayerMoney(newTarget) or 0
                    local targetBounty = getPlayerBounty(newTarget)
                    print("🎯 NEW TARGET: " .. newTarget.Name .. " | Bounty: $" .. tostring(targetBounty) .. " | Distance: " .. math.floor(distance) .. " studs")
                    local veh, seat, vehDist = findAvailableVehicleNear(root.Position, 400, {"Camaro", "Jeep"})
                    if veh and (vehDist < distance or isVehicleOnPath(seat.Position, root.Position, newTarget.Character.HumanoidRootPart.Position, 40)) then
                        arrestState.targetVehicle = veh
                        arrestState.targetVehicleSeat = seat
                        arrestState.phase = "NAV_TO_VEHICLE"
                        arrestState.spawnWaitStart = tick()
                        print("🎯 Target: " .. newTarget.Name .. " (" .. math.floor(distance) .. " studs) - grabbing vehicle: " .. (veh.Name or "Unknown") .. " (" .. math.floor(vehDist) .. " studs)")
                        print("🚗 PHASE: Navigating to vehicle...")
                    else
                        if ArrestSettings.UseCamaro then
                            arrestState.phase = "FINDING_VEHICLE"
                            print("🎯 Target: " .. newTarget.Name .. " (" .. math.floor(distance) .. " studs)")
                            print("🚗 PHASE: Finding Camaro...")
                        else
                            arrestState.phase = "ASCENDING"
                            print("🎯 Target: " .. newTarget.Name .. " (" .. math.floor(distance) .. " studs)")
                            print("⬆️ PHASE: Ascending")
                        end
                    end
                else
                    if ArrestSettings.ServerHopEnabled then
                        if not arrestState.noTargetsStartTime then
                            arrestState.noTargetsStartTime = tick()
                            if ArrestSettings.BountyFilterEnabled then
                                print("⏳ No valid targets found (all criminals have bounty < " .. ArrestSettings.BountyThreshold .. " or are under cover)")
                            else
                                print("⏳ No valid targets found")
                            end
                            print("⏳ Server hop timer started - will hop in " .. ArrestSettings.ServerHopDelay .. "s if no targets appear...")
                        else
                            local timeWithoutTargets = tick() - arrestState.noTargetsStartTime
                            if timeWithoutTargets >= ArrestSettings.ServerHopDelay then
                                print("🔄 No targets for " .. ArrestSettings.ServerHopDelay .. "s - Server hopping!")
                                serverHop()
                            end
                        end
                    else
                        if ArrestSettings.BountyFilterEnabled then
                            print("⏳ No valid targets found (all criminals have bounty < " .. ArrestSettings.BountyThreshold .. " or are under cover)")
                        else
                            print("⏳ No valid targets found")
                        end
                    end
                end
            end
        elseif arrestState.phase == "FINDING_VEHICLE" then
            if not isTargetValid(arrestState.targetPlayer) then
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                return
            end
            if isInVehicle() then
                print("✅ Already in vehicle!")
                arrestState.phase = "ASCENDING"
                return
            end
            if canSpawnCamaro() then
                local success = spawnCamaro()
                if success then
                    arrestState.phase = "ENTERING_VEHICLE"
                    arrestState.spawnWaitStart = tick()
                else
                    arrestState.phase = "ASCENDING"
                end
            else
                print("⏳ Camaro on cooldown")
                arrestState.phase = "ASCENDING"
            end
        elseif arrestState.phase == "NAV_TO_VEHICLE" then
            local veh = arrestState.targetVehicle
            local seat = arrestState.targetVehicleSeat
            if not veh or not seat or not seat.Parent then
                arrestState.targetVehicle = nil
                arrestState.targetVehicleSeat = nil
                arrestState.phase = "ASCENDING"
                return
            end
            if isInVehicle() then
                print("✅ Entered vehicle, switching to ASCENDING")
                arrestState.phase = "ASCENDING"
                arrestState.targetVehicle = nil
                arrestState.targetVehicleSeat = nil
                return
            end
            local seatPos = seat.Position
            local approachPos = Vector3.new(seatPos.X, seatPos.Y + 1.5, seatPos.Z)
            flyHorizontalToPosition(approachPos, approachPos.Y)
            local d = (root.Position - seatPos).Magnitude
            if d < 6 then
                print("⏳ Attempting to enter vehicle: ensuring unequip and pressing E")
                if seat.Occupant then
                    print("⚠️ Seat is occupied - aborting vehicle entry")
                    arrestState.targetVehicle = nil
                    arrestState.targetVehicleSeat = nil
                    arrestState.phase = "ASCENDING"
                    return
                end
                local unequipStop = false
                local unequipThread = task.spawn(function()
                    local startUnequip = tick()
                    while not isInVehicle() and (tick() - startUnequip) < 6 and not unequipStop do
                        local ok = pcall(function()
                            local args = { false }
                            LP:WaitForChild("Folder"):WaitForChild("Handcuffs"):WaitForChild("InventoryEquipRemote"):FireServer(unpack(args))
                        end)
                        if ok then
                            arrestState.handcuffsEquipped = false
                        end
                        task.wait(0.2)
                    end
                end)
                task.wait(0.12)
                print("🔓 Persistent unequip started")
                if d > 2 then
                    pcall(function()
                        teleportToPosition(Vector3.new(seatPos.X, seatPos.Y + 1.5, seatPos.Z), false)
                    end)
                    task.wait(0.12)
                end
                for i = 1, 8 do
                    VIM:SendKeyEvent(true, Enum.KeyCode.E, false, game)
                    task.wait(0.05)
                    VIM:SendKeyEvent(false, Enum.KeyCode.E, false, game)
                    task.wait(0.18)
                    if isInVehicle() then break end
                end
                if not isInVehicle() then
                    pcall(function()
                        local args = { false }
                        LP:WaitForChild("Folder"):WaitForChild("Handcuffs"):WaitForChild("InventoryEquipRemote"):FireServer(unpack(args))
                    end)
                    task.wait(0.12)
                    pcall(function()
                        flyToPosition(Vector3.new(seatPos.X, seatPos.Y + 1.5, seatPos.Z), 50)
                    end)
                    task.wait(0.12)
                    for i = 1, 6 do
                        VIM:SendKeyEvent(true, Enum.KeyCode.E, false, game)
                        task.wait(0.05)
                        VIM:SendKeyEvent(false, Enum.KeyCode.E, false, game)
                        task.wait(0.18)
                        if isInVehicle() then break end
                    end
                end
                unequipStop = true
                if isInVehicle() then
                    print("✅ Successfully entered vehicle")
                    arrestState.phase = "ASCENDING"
                    arrestState.targetVehicle = nil
                    arrestState.targetVehicleSeat = nil
                    local vehModel = getVehicle()
                    if vehModel then
                        setVehicleHeight(vehModel)
                    end
                    return
                else
                    print("⚠️ Failed to enter vehicle - reverting to ascending")
                    arrestState.targetVehicle = nil
                    arrestState.targetVehicleSeat = nil
                    arrestState.phase = "ASCENDING"
                    return
                end
            end
            if arrestState.spawnWaitStart and tick() - arrestState.spawnWaitStart > 10 then
                print("⏰ NAV_TO_VEHICLE timeout - falling back to ASCENDING")
                arrestState.targetVehicle = nil
                arrestState.targetVehicleSeat = nil
                arrestState.phase = "ASCENDING"
                return
            end
        elseif arrestState.phase == "ENTERING_VEHICLE" then
            if not isTargetValid(arrestState.targetPlayer) then
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                return
            end
            if isInVehicle() then
                print("✅ Entered vehicle!")
                local veh = getVehicle()
                if veh then
                    setVehicleHeight(veh)
                end
                if not arrestState.originalCameraSubject then
                    arrestState.originalCameraSubject = Camera.CameraSubject
                    arrestState.originalCameraType = Camera.CameraType
                end
                local pHum = LP.Character and LP.Character:FindFirstChild("Humanoid")
                if pHum then
                    pcall(function()
                        Camera.CameraSubject = pHum
                        Camera.CameraType = Enum.CameraType.Custom
                    end)
                end
                arrestState.phase = "ASCENDING"
            else
                local waitTime = tick() - (arrestState.spawnWaitStart or tick())
                if waitTime > 5 then
                    print("⏰ Timeout")
                    arrestState.phase = "ASCENDING"
                end
            end
        elseif arrestState.phase == "ASCENDING" then
            if not isTargetValid(arrestState.targetPlayer) then
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                return
            end
            if isInVehicle() then
                local pHum = LP.Character and LP.Character:FindFirstChild("Humanoid")
                if pHum then
                    pcall(function()
                        Camera.CameraSubject = pHum
                        Camera.CameraType = Enum.CameraType.Custom
                    end)
                end
            end
            if root.Position.Y < ArrestSettings.FlyAltitude - 10 then
                teleportVerticalTo(ArrestSettings.FlyAltitude)
            end
            arrestState.forceInstantDrop = nil
            if root.Position.Y >= ArrestSettings.FlyAltitude - 15 then
                arrestState.phase = "FLYING"
                local speed = isInVehicle() and ArrestSettings.VehicleFlySpeed or ArrestSettings.CharacterFlySpeed
                print("✈️ PHASE: Flying (speed: " .. speed .. ")")
            end
        elseif arrestState.phase == "FLYING" then
            if not isTargetValid(arrestState.targetPlayer) then
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                return
            end
            local targetRoot = arrestState.targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if not targetRoot then
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                return
            end
            local targetPosAbove = Vector3.new(
                targetRoot.Position.X,
                ArrestSettings.FlyAltitude,
                targetRoot.Position.Z
            )
            local horizontalDist = math.sqrt(
                (targetRoot.Position.X - root.Position.X)^2 +
                (targetRoot.Position.Z - root.Position.Z)^2
            )
            if horizontalDist >= ArrestSettings.ChaseTeleportDistance then
                -- Suppressed noisy console print about distant target
                teleportVerticalTo(ArrestSettings.FlyAltitude)
                arrestState.forceInstantDrop = true
                flyHorizontalToPosition(targetPosAbove, ArrestSettings.FlyAltitude)
                if horizontalDist < ArrestSettings.DropDistance then
                    arrestState.phase = "DROPPING"
                    print("⬇️ PHASE: Dropping!")
                end
            else
                teleportVerticalTo(ArrestSettings.FlyAltitude)
                flyHorizontalToPosition(targetPosAbove, ArrestSettings.FlyAltitude)
                if horizontalDist < ArrestSettings.DropDistance then
                    arrestState.phase = "DROPPING"
                    print("⬇️ PHASE: Dropping!")
                end
            end
        elseif arrestState.phase == "DROPPING" then
            if not isTargetValid(arrestState.targetPlayer) then
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                stopECycle()
                return
            end
            local targetRoot = arrestState.targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if not targetRoot then
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                stopECycle()
                return
            end
            local distance = (root.Position - targetRoot.Position).Magnitude
            if distance < 100 and isInVehicle() then
                print("🚪 Exiting vehicle")
                if arrestState.originalCameraSubject then
                    pcall(function()
                        Camera.CameraType = arrestState.originalCameraType or Enum.CameraType.Custom
                        Camera.CameraSubject = arrestState.originalCameraSubject
                    end)
                end
                exitVehicle()
                task.wait(1)
                if not arrestState.handcuffsEquipped then
                    print("🔫 Equipping handcuffs after vehicle exit...")
                    equipHandcuffs()
                    task.wait(0.2)
                    equipHandcuffs()
                    task.wait(0.2)
                    equipHandcuffs()
                end
            end
            local groundHeight = findGroundHeight(targetRoot.Position)
            local targetHeight = math.max(targetRoot.Position.Y + 8, groundHeight + 6)
            local descendY = targetHeight
            local horizontalXZDist = Vector3.new(root.Position.X - targetRoot.Position.X, 0, root.Position.Z - targetRoot.Position.Z).Magnitude
            if arrestState.justTeleportedAbove then
                print("⚡ Performing instant drop to target location (was teleported above)")
                performInstantDrop(targetRoot, descendY, true)
                arrestState.justTeleportedAbove = nil
            elseif horizontalXZDist <= (ArrestSettings.DescentAlignmentThreshold or 8) or arrestState.forceInstantDrop then
                print("⚡ Instant vertical drop to target Y (preserving X,Z)")
                performInstantDrop(targetRoot, descendY, false)
                arrestState.forceInstantDrop = nil
            else
                if horizontalXZDist < (ArrestSettings.ReturnToFlyDistance or 500) then
                    flyHorizontalToPosition(Vector3.new(targetRoot.Position.X, descendY, targetRoot.Position.Z), descendY)
                else
                    local nearby, ndist = findNearbyCriminalAround(targetRoot.Position, 75)
                    if nearby then
                        print("🔁 Nearby criminal found (" .. nearby.Name .. ") within 75 studs - engaging directly at low altitude")
                        arrestState.targetPlayer = nearby
                        local altRoot = nearby.Character and nearby.Character:FindFirstChild("HumanoidRootPart")
                        if altRoot then
                            flyHorizontalToPosition(Vector3.new(altRoot.Position.X, descendY, altRoot.Position.Z), descendY)
                        end
                    else
                        -- Suppressed noisy console print about target moving far away
                        teleportVerticalTo(ArrestSettings.FlyAltitude)
                        flyHorizontalToPosition(Vector3.new(targetRoot.Position.X, ArrestSettings.FlyAltitude, targetRoot.Position.Z), ArrestSettings.FlyAltitude)
                    end
                end
            end
            if distance < ArrestSettings.ArrestRange then
                arrestState.phase = "ARRESTING"
                print("🚨 PHASE: ARRESTING!")
                if not arrestState.handcuffsEquipped then
                    print("🔫 Equipping handcuffs...")
                    equipHandcuffs()
                    task.wait(0.2)
                    equipHandcuffs()
                    task.wait(0.2)
                    equipHandcuffs()
                end
                startECycle()
            end
        elseif arrestState.phase == "ARRESTING" then
            if not isTargetValid(arrestState.targetPlayer) then
                print("✅ Target arrested or died!")
                if arrestState.targetPlayer then
                    arrestState.recentTargets[arrestState.targetPlayer.UserId] = tick() + 10
                end
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                stopECycle()
                arrestState.handcuffsEquipped = false
                return
            end
            local targetRoot = arrestState.targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if not targetRoot then
                arrestState.phase = "SCANNING"
                arrestState.targetPlayer = nil
                stopECycle()
                return
            end
            local distance = (root.Position - targetRoot.Position).Magnitude
            if distance > ArrestSettings.ArrestRange then
                print("⚠️ Target escaping! Re-engaging...")
                arrestState.phase = "DROPPING"
                stopECycle()
            else
                local stickPos = Vector3.new(
                    targetRoot.Position.X,
                    targetRoot.Position.Y + 2,
                    targetRoot.Position.Z
                )
                flyToPosition(stickPos, 25)
                if not arrestState.eHoldActive then
                    startECycle()
                end
                if not arrestState.handcuffsEquipped then
                    equipHandcuffs()
                end
            end
        end
    end)
end

local function stopArrestSystem()
    print("🛑 SFAA Stopped")
    if mainConnection then
        mainConnection:Disconnect()
        mainConnection = nil
    end
    if deathConnection then
        deathConnection:Disconnect()
        deathConnection = nil
    end
    pcall(function()
        stopECycle()
    end)
    pcall(function()
        stopEquipSpam()
    end)
    pcall(function()
        local char = LP.Character
        if char then
            local hum = char:FindFirstChild("Humanoid")
            Camera.CameraType = Enum.CameraType.Custom
            Camera.CameraSubject = hum or char
            Camera.FieldOfView = 70
            arrestState.originalCameraSubject = nil
            arrestState.originalCameraType = nil
            print("📷 Camera reset to humanoid")
        end
    end)
    pcall(function()
        resetArrestState()
    end)
    pcall(function()
        resetCharacterState()
    end)
    task.wait(0.2)
    print("✅ System fully stopped - movement restored!")
end

local function initializeCharacterConnection()
    if LP.Character then
        onCharacterAdded(LP.Character)
    end
    if characterConnection then
        characterConnection:Disconnect()
    end
    characterConnection = LP.CharacterAdded:Connect(onCharacterAdded)
end

--// GUI //--
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SFAA_GUI"
ScreenGui.Parent = LP:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local MainFrame = Instance.new("Frame")
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 18, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.02, 0, 0.3, 0)
MainFrame.Size = UDim2.new(0, 320, 0, 480)

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(60, 70, 90)
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame

local Header = Instance.new("Frame")
Header.Parent = MainFrame
Header.BackgroundColor3 = Color3.fromRGB(20, 24, 32)
Header.BorderSizePixel = 0
Header.Size = UDim2.new(1, 0, 0, 50)

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 12)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Parent = Header
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 15, 0, 8)
Title.Size = UDim2.new(1, -30, 0, 22)
Title.Font = Enum.Font.GothamBold
Title.Text = "SFAA"
Title.TextColor3 = Color3.fromRGB(120, 180, 255)
Title.TextSize = 20
Title.TextXAlignment = Enum.TextXAlignment.Left

local Version = Instance.new("TextLabel")
Version.Parent = Header
Version.BackgroundTransparency = 1
Version.Position = UDim2.new(0, 15, 0, 28)
Version.Size = UDim2.new(1, -30, 0, 16)
Version.Font = Enum.Font.Gotham
Version.Text = "Smart Fast Auto Arrest v8.0"
Version.TextColor3 = Color3.fromRGB(140, 150, 170)
Version.TextSize = 11
Version.TextXAlignment = Enum.TextXAlignment.Left

local CloseBtn = Instance.new("TextButton")
CloseBtn.Parent = Header
CloseBtn.BackgroundColor3 = Color3.fromRGB(220, 60, 70)
CloseBtn.Position = UDim2.new(1, -40, 0, 10)
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.TextSize = 16
CloseBtn.BorderSizePixel = 0
CloseBtn.AutoButtonColor = false

local CloseBtnCorner = Instance.new("UICorner")
CloseBtnCorner.CornerRadius = UDim.new(0, 6)
CloseBtnCorner.Parent = CloseBtn

CloseBtn.MouseButton1Click:Connect(function()
    if ArrestSettings.Enabled then
        ArrestSettings.Enabled = false
        stopArrestSystem()
    end
    ScreenGui:Destroy()
    print("👋 SFAA GUI Closed")
end)

CloseBtn.MouseEnter:Connect(function()
    CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 80, 90)
end)

CloseBtn.MouseLeave:Connect(function()
    CloseBtn.BackgroundColor3 = Color3.fromRGB(220, 60, 70)
end)

local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Parent = MainFrame
ToggleBtn.BackgroundColor3 = Color3.fromRGB(70, 180, 100)
ToggleBtn.Position = UDim2.new(0, 15, 0, 65)
ToggleBtn.Size = UDim2.new(1, -30, 0, 42)
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.Text = "ACTIVE - Min: $" .. ArrestSettings.BountyThreshold
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleBtn.TextSize = 14
ToggleBtn.BorderSizePixel = 0
ToggleBtn.AutoButtonColor = false

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 8)
ToggleCorner.Parent = ToggleBtn

local MainSection = Instance.new("Frame")
MainSection.Parent = MainFrame
MainSection.BackgroundColor3 = Color3.fromRGB(20, 24, 32)
MainSection.BorderSizePixel = 0
MainSection.Position = UDim2.new(0, 15, 0, 117)
MainSection.Size = UDim2.new(1, -30, 0, 155)

local MainSectionCorner = Instance.new("UICorner")
MainSectionCorner.CornerRadius = UDim.new(0, 8)
MainSectionCorner.Parent = MainSection

local MainLabel = Instance.new("TextLabel")
MainLabel.Parent = MainSection
MainLabel.BackgroundTransparency = 1
MainLabel.Position = UDim2.new(0, 12, 0, 8)
MainLabel.Size = UDim2.new(1, -24, 0, 20)
MainLabel.Font = Enum.Font.GothamBold
MainLabel.Text = "main"
MainLabel.TextColor3 = Color3.fromRGB(200, 210, 230)
MainLabel.TextSize = 13
MainLabel.TextXAlignment = Enum.TextXAlignment.Left

local TargetDropdown = Instance.new("TextButton")
TargetDropdown.Parent = MainSection
TargetDropdown.BackgroundColor3 = Color3.fromRGB(15, 18, 25)
TargetDropdown.Position = UDim2.new(0, 12, 0, 35)
TargetDropdown.Size = UDim2.new(1, -24, 0, 32)
TargetDropdown.Font = Enum.Font.Gotham
TargetDropdown.Text = ""
TargetDropdown.TextColor3 = Color3.fromRGB(180, 190, 210)
TargetDropdown.TextSize = 12
TargetDropdown.BorderSizePixel = 0
TargetDropdown.AutoButtonColor = false
TargetDropdown.ZIndex = 5

local DropdownCorner = Instance.new("UICorner")
DropdownCorner.CornerRadius = UDim.new(0, 6)
DropdownCorner.Parent = TargetDropdown

local DropdownLabel = Instance.new("TextLabel")
DropdownLabel.Parent = TargetDropdown
DropdownLabel.BackgroundTransparency = 1
DropdownLabel.Position = UDim2.new(0, 10, 0, 0)
DropdownLabel.Size = UDim2.new(1, -25, 1, 0)
DropdownLabel.Font = Enum.Font.Gotham
DropdownLabel.Text = "target mode"
DropdownLabel.TextColor3 = Color3.fromRGB(120, 130, 150)
DropdownLabel.TextSize = 11
DropdownLabel.TextXAlignment = Enum.TextXAlignment.Left
DropdownLabel.ZIndex = 6

local DropdownValue = Instance.new("TextLabel")
DropdownValue.Parent = TargetDropdown
DropdownValue.BackgroundTransparency = 1
DropdownValue.Position = UDim2.new(0, 10, 0, 0)
DropdownValue.Size = UDim2.new(1, -30, 1, 0)
DropdownValue.Font = Enum.Font.GothamBold
DropdownValue.Text = ArrestSettings.TargetMode
DropdownValue.TextColor3 = Color3.fromRGB(200, 210, 230)
DropdownValue.TextSize = 12
DropdownValue.TextXAlignment = Enum.TextXAlignment.Right
DropdownValue.ZIndex = 6

local DropdownArrow = Instance.new("TextLabel")
DropdownArrow.Parent = TargetDropdown
DropdownArrow.BackgroundTransparency = 1
DropdownArrow.Position = UDim2.new(1, -20, 0, 0)
DropdownArrow.Size = UDim2.new(0, 20, 1, 0)
DropdownArrow.Font = Enum.Font.GothamBold
DropdownArrow.Text = "▼"
DropdownArrow.TextColor3 = Color3.fromRGB(120, 130, 150)
DropdownArrow.TextSize = 10
DropdownArrow.ZIndex = 6

local DropdownOptions = Instance.new("Frame")
DropdownOptions.Parent = TargetDropdown
DropdownOptions.BackgroundColor3 = Color3.fromRGB(15, 18, 25)
DropdownOptions.BorderSizePixel = 0
DropdownOptions.Position = UDim2.new(0, 0, 1, 2)
DropdownOptions.Size = UDim2.new(1, 0, 0, 0)
DropdownOptions.ClipsDescendants = true
DropdownOptions.Visible = false
DropdownOptions.ZIndex = 10

local DropdownOptionsCorner = Instance.new("UICorner")
DropdownOptionsCorner.CornerRadius = UDim.new(0, 6)
DropdownOptionsCorner.Parent = DropdownOptions

local DropdownStroke = Instance.new("UIStroke")
DropdownStroke.Color = Color3.fromRGB(60, 70, 90)
DropdownStroke.Thickness = 1
DropdownStroke.Parent = DropdownOptions

local Option1 = Instance.new("TextButton")
Option1.Parent = DropdownOptions
Option1.BackgroundColor3 = Color3.fromRGB(20, 24, 32)
Option1.Position = UDim2.new(0, 0, 0, 0)
Option1.Size = UDim2.new(1, 0, 0, 32)
Option1.Font = Enum.Font.Gotham
Option1.Text = "  closest"
Option1.TextColor3 = Color3.fromRGB(200, 210, 230)
Option1.TextSize = 12
Option1.TextXAlignment = Enum.TextXAlignment.Left
Option1.BorderSizePixel = 0
Option1.AutoButtonColor = false
Option1.ZIndex = 11

local Option1Corner = Instance.new("UICorner")
Option1Corner.CornerRadius = UDim.new(0, 6)
Option1Corner.Parent = Option1

local Option2 = Instance.new("TextButton")
Option2.Parent = DropdownOptions
Option2.BackgroundColor3 = Color3.fromRGB(20, 24, 32)
Option2.Position = UDim2.new(0, 0, 0, 34)
Option2.Size = UDim2.new(1, 0, 0, 32)
Option2.Font = Enum.Font.Gotham
Option2.Text = "  highest bounty"
Option2.TextColor3 = Color3.fromRGB(200, 210, 230)
Option2.TextSize = 12
Option2.TextXAlignment = Enum.TextXAlignment.Left
Option2.BorderSizePixel = 0
Option2.AutoButtonColor = false
Option2.ZIndex = 11

local Option2Corner = Instance.new("UICorner")
Option2Corner.CornerRadius = UDim.new(0, 6)
Option2Corner.Parent = Option2

local dropdownOpen = false

TargetDropdown.MouseButton1Click:Connect(function()
    dropdownOpen = not dropdownOpen
    if dropdownOpen then
        DropdownOptions.Visible = true
        DropdownOptions:TweenSize(UDim2.new(1, 0, 0, 68), Enum.EasingDirection.Out, Enum.EasingStyle.Quad, 0.2, true)
        DropdownArrow.Text = "▲"
    else
        DropdownOptions:TweenSize(UDim2.new(1, 0, 0, 0), Enum.EasingDirection.Out, Enum.EasingStyle.Quad, 0.2, true, function()
            DropdownOptions.Visible = false
        end)
        DropdownArrow.Text = "▼"
    end
end)

Option1.MouseButton1Click:Connect(function()
    ArrestSettings.TargetMode = "closest"
    DropdownValue.Text = "closest"
    print("🎯 Target mode set to: CLOSEST")
    dropdownOpen = false
    DropdownOptions:TweenSize(UDim2.new(1, 0, 0, 0), Enum.EasingDirection.Out, Enum.EasingStyle.Quad, 0.2, true, function()
        DropdownOptions.Visible = false
    end)
    DropdownArrow.Text = "▼"
end)

Option2.MouseButton1Click:Connect(function()
    ArrestSettings.TargetMode = "highest bounty"
    DropdownValue.Text = "highest bounty"
    print("🎯 Target mode set to: HIGHEST BOUNTY")
    dropdownOpen = false
    DropdownOptions:TweenSize(UDim2.new(1, 0, 0, 0), Enum.EasingDirection.Out, Enum.EasingStyle.Quad, 0.2, true, function()
        DropdownOptions.Visible = false
    end)
    DropdownArrow.Text = "▼"
end)

Option1.MouseEnter:Connect(function()
    Option1.BackgroundColor3 = Color3.fromRGB(30, 35, 45)
end)

Option1.MouseLeave:Connect(function()
    Option1.BackgroundColor3 = Color3.fromRGB(20, 24, 32)
end)

Option2.MouseEnter:Connect(function()
    Option2.BackgroundColor3 = Color3.fromRGB(30, 35, 45)
end)

Option2.MouseLeave:Connect(function()
    Option2.BackgroundColor3 = Color3.fromRGB(20, 24, 32)
end)

local BountyLabel = Instance.new("TextLabel")
BountyLabel.Parent = MainSection
BountyLabel.BackgroundTransparency = 1
BountyLabel.Position = UDim2.new(0, 12, 0, 75)
BountyLabel.Size = UDim2.new(0.5, -6, 0, 14)
BountyLabel.Font = Enum.Font.Gotham
BountyLabel.Text = "min bounty (enabled)"
BountyLabel.TextColor3 = Color3.fromRGB(100, 200, 120)
BountyLabel.TextSize = 11
BountyLabel.TextXAlignment = Enum.TextXAlignment.Left

local BountyInput = Instance.new("TextBox")
BountyInput.Parent = MainSection
BountyInput.BackgroundColor3 = Color3.fromRGB(15, 18, 25)
BountyInput.Position = UDim2.new(0, 12, 0, 92)
BountyInput.Size = UDim2.new(1, -24, 0, 32)
BountyInput.Font = Enum.Font.Gotham
BountyInput.Text = tostring(ArrestSettings.BountyThreshold)
BountyInput.PlaceholderText = "2900"
BountyInput.TextColor3 = Color3.fromRGB(180, 190, 210)
BountyInput.TextSize = 12
BountyInput.BorderSizePixel = 0

local BountyCorner = Instance.new("UICorner")
BountyCorner.CornerRadius = UDim.new(0, 6)
BountyCorner.Parent = BountyInput

local CooldownLabel = Instance.new("TextLabel")
CooldownLabel.Parent = MainSection
CooldownLabel.BackgroundTransparency = 1
CooldownLabel.Position = UDim2.new(0, 12, 0, 132)
CooldownLabel.Size = UDim2.new(1, -24, 0, 14)
CooldownLabel.Font = Enum.Font.Gotham
CooldownLabel.Text = "arrest cooldown: 0.0"
CooldownLabel.TextColor3 = Color3.fromRGB(120, 130, 150)
CooldownLabel.TextSize = 11
CooldownLabel.TextXAlignment = Enum.TextXAlignment.Left

local SpeedSection = Instance.new("Frame")
SpeedSection.Parent = MainFrame
SpeedSection.BackgroundColor3 = Color3.fromRGB(20, 24, 32)
SpeedSection.BorderSizePixel = 0
SpeedSection.Position = UDim2.new(0, 15, 0, 282)
SpeedSection.Size = UDim2.new(1, -30, 0, 120)

local SpeedSectionCorner = Instance.new("UICorner")
SpeedSectionCorner.CornerRadius = UDim.new(0, 8)
SpeedSectionCorner.Parent = SpeedSection

local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Parent = SpeedSection
SpeedLabel.BackgroundTransparency = 1
SpeedLabel.Position = UDim2.new(0, 12, 0, 8)
SpeedLabel.Size = UDim2.new(1, -24, 0, 20)
SpeedLabel.Font = Enum.Font.GothamBold
SpeedLabel.Text = "speed"
SpeedLabel.TextColor3 = Color3.fromRGB(200, 210, 230)
SpeedLabel.TextSize = 13
SpeedLabel.TextXAlignment = Enum.TextXAlignment.Left

local FlightLabel = Instance.new("TextLabel")
FlightLabel.Parent = SpeedSection
FlightLabel.BackgroundTransparency = 1
FlightLabel.Position = UDim2.new(0, 12, 0, 35)
FlightLabel.Size = UDim2.new(1, -24, 0, 14)
FlightLabel.Font = Enum.Font.Gotham
FlightLabel.Text = "flight speed"
FlightLabel.TextColor3 = Color3.fromRGB(120, 130, 150)
FlightLabel.TextSize = 11
FlightLabel.TextXAlignment = Enum.TextXAlignment.Left

local CharSpeedInput = Instance.new("TextBox")
CharSpeedInput.Parent = SpeedSection
CharSpeedInput.BackgroundColor3 = Color3.fromRGB(15, 18, 25)
CharSpeedInput.Position = UDim2.new(0, 12, 0, 52)
CharSpeedInput.Size = UDim2.new(0.48, -8, 0, 32)
CharSpeedInput.Font = Enum.Font.Gotham
CharSpeedInput.Text = tostring(ArrestSettings.CharacterFlySpeed)
CharSpeedInput.PlaceholderText = "Char"
CharSpeedInput.TextColor3 = Color3.fromRGB(180, 190, 210)
CharSpeedInput.TextSize = 12
CharSpeedInput.BorderSizePixel = 0

local CharSpeedCorner = Instance.new("UICorner")
CharSpeedCorner.CornerRadius = UDim.new(0, 6)
CharSpeedCorner.Parent = CharSpeedInput

local VehSpeedInput = Instance.new("TextBox")
VehSpeedInput.Parent = SpeedSection
VehSpeedInput.BackgroundColor3 = Color3.fromRGB(15, 18, 25)
VehSpeedInput.Position = UDim2.new(0.52, 4, 0, 52)
VehSpeedInput.Size = UDim2.new(0.48, -8, 0, 32)
VehSpeedInput.Font = Enum.Font.Gotham
VehSpeedInput.Text = tostring(ArrestSettings.VehicleFlySpeed)
VehSpeedInput.PlaceholderText = "Veh"
VehSpeedInput.TextColor3 = Color3.fromRGB(180, 190, 210)
VehSpeedInput.TextSize = 12
VehSpeedInput.BorderSizePixel = 0

local VehSpeedCorner = Instance.new("UICorner")
VehSpeedCorner.CornerRadius = UDim.new(0, 6)
VehSpeedCorner.Parent = VehSpeedInput

local SpeedPercent = Instance.new("TextLabel")
SpeedPercent.Parent = SpeedSection
SpeedPercent.BackgroundTransparency = 1
SpeedPercent.Position = UDim2.new(0, 12, 0, 92)
SpeedPercent.Size = UDim2.new(1, -24, 0, 18)
SpeedPercent.Font = Enum.Font.GothamBold
SpeedPercent.Text = "50.00%"
SpeedPercent.TextColor3 = Color3.fromRGB(100, 160, 255)
SpeedPercent.TextSize = 16
SpeedPercent.TextXAlignment = Enum.TextXAlignment.Left

local OtherSection = Instance.new("Frame")
OtherSection.Parent = MainFrame
OtherSection.BackgroundColor3 = Color3.fromRGB(20, 24, 32)
OtherSection.BorderSizePixel = 0
OtherSection.Position = UDim2.new(0, 15, 0, 412)
OtherSection.Size = UDim2.new(1, -30, 0, 60)

local OtherSectionCorner = Instance.new("UICorner")
OtherSectionCorner.CornerRadius = UDim.new(0, 8)
OtherSectionCorner.Parent = OtherSection

local OtherLabel = Instance.new("TextLabel")
OtherLabel.Parent = OtherSection
OtherLabel.BackgroundTransparency = 1
OtherLabel.Position = UDim2.new(0, 12, 0, 8)
OtherLabel.Size = UDim2.new(1, -24, 0, 20)
OtherLabel.Font = Enum.Font.GothamBold
OtherLabel.Text = "other"
OtherLabel.TextColor3 = Color3.fromRGB(200, 210, 230)
OtherLabel.TextSize = 13
OtherLabel.TextXAlignment = Enum.TextXAlignment.Left

local ServerHopToggle = Instance.new("TextButton")
ServerHopToggle.Parent = OtherSection
ServerHopToggle.BackgroundColor3 = ArrestSettings.ServerHopEnabled and Color3.fromRGB(70, 180, 100) or Color3.fromRGB(60, 65, 80)
ServerHopToggle.Position = UDim2.new(0, 12, 0, 35)
ServerHopToggle.Size = UDim2.new(0, 14, 0, 14)
ServerHopToggle.Text = ""
ServerHopToggle.BorderSizePixel = 0
ServerHopToggle.AutoButtonColor = false

local ServerHopCorner = Instance.new("UICorner")
ServerHopCorner.CornerRadius = UDim.new(0, 3)
ServerHopCorner.Parent = ServerHopToggle

local ServerHopLabel = Instance.new("TextLabel")
ServerHopLabel.Parent = OtherSection
ServerHopLabel.BackgroundTransparency = 1
ServerHopLabel.Position = UDim2.new(0, 32, 0, 33)
ServerHopLabel.Size = UDim2.new(1, -44, 0, 18)
ServerHopLabel.Font = Enum.Font.Gotham
ServerHopLabel.Text = "serverhop (4-8 slots left, disabled)"
ServerHopLabel.TextColor3 = Color3.fromRGB(180, 190, 210)
ServerHopLabel.TextSize = 11
ServerHopLabel.TextXAlignment = Enum.TextXAlignment.Left

local dragging, dragInput, dragStart, startPos

MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
    end
end)

MainFrame.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UIS.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(
            startPos.X.Scale, 
            startPos.X.Offset + delta.X, 
            startPos.Y.Scale, 
            startPos.Y.Offset + delta.Y
        )
    end
end)

ToggleBtn.MouseButton1Click:Connect(function()
    ArrestSettings.Enabled = not ArrestSettings.Enabled
    if ArrestSettings.Enabled then
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(70, 180, 100)
        ToggleBtn.Text = "ACTIVE - Min: $" .. ArrestSettings.BountyThreshold
        arrestState.phase = "SCANNING"
        arrestState.startMoney = getMoney()
        arrestState.startTime = tick()
        initializeCharacterConnection()
        startArrestSystem()
        if ArrestSettings.EquipSpamEnabled then
            startEquipSpam()
        end
    else
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(220, 60, 70)
        ToggleBtn.Text = "STOPPED"
        stopArrestSystem()
    end
end)

BountyInput.FocusLost:Connect(function()
    local n = tonumber(BountyInput.Text)
    if n and n >= 0 then
        ArrestSettings.BountyThreshold = n
        BountyInput.Text = tostring(ArrestSettings.BountyThreshold)
        print("💰 Bounty threshold set to: $" .. ArrestSettings.BountyThreshold)
        if ArrestSettings.Enabled then
            ToggleBtn.Text = "ACTIVE - Min: $" .. ArrestSettings.BountyThreshold
        end
    else
        BountyInput.Text = tostring(ArrestSettings.BountyThreshold)
    end
end)

CharSpeedInput.FocusLost:Connect(function()
    local n = tonumber(CharSpeedInput.Text)
    if n and n > 0 then
        ArrestSettings.CharacterFlySpeed = n
    else
        CharSpeedInput.Text = tostring(ArrestSettings.CharacterFlySpeed)
    end
end)

VehSpeedInput.FocusLost:Connect(function()
    local n = tonumber(VehSpeedInput.Text)
    if n and n > 0 then
        ArrestSettings.VehicleFlySpeed = n
    else
        VehSpeedInput.Text = tostring(ArrestSettings.VehicleFlySpeed)
    end
end)

ServerHopToggle.MouseButton1Click:Connect(function()
    ArrestSettings.ServerHopEnabled = not ArrestSettings.ServerHopEnabled
    if ArrestSettings.ServerHopEnabled then
        ServerHopToggle.BackgroundColor3 = Color3.fromRGB(70, 180, 100)
        print("🔄 Server hop enabled - Will hop to populated servers (4-8 slots left)")
    else
        ServerHopToggle.BackgroundColor3 = Color3.fromRGB(60, 65, 80)
        arrestState.noTargetsStartTime = nil
        print("🔄 Server hop disabled")
    end
end)

local lastUIUpdate = 0
spawn(function()
    while true do
        task.wait(0.2)
        local currentTime = tick()
        if currentTime - lastUIUpdate < 0.2 then
            continue
        end
        lastUIUpdate = currentTime
        local cooldown = getSpawnCooldownRemaining()
        CooldownLabel.Text = "arrest cooldown: " .. string.format("%.1f", cooldown)
        local avgSpeed = (ArrestSettings.CharacterFlySpeed + ArrestSettings.VehicleFlySpeed) / 2
        local percent = (avgSpeed / 300) * 100
        SpeedPercent.Text = string.format("%.2f%%", percent)
        if ArrestSettings.ServerHopEnabled and arrestState.noTargetsStartTime then
            local timeWithoutTargets = tick() - arrestState.noTargetsStartTime
            local remaining = ArrestSettings.ServerHopDelay - timeWithoutTargets
            if remaining > 0 then
                ServerHopLabel.Text = "serverhop in " .. string.format("%.1f", remaining) .. "s"
                ServerHopLabel.TextColor3 = Color3.fromRGB(255, 180, 100)
            else
                ServerHopLabel.Text = "serverhop (hopping now...)"
                ServerHopLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
            end
        else
            ServerHopLabel.Text = "serverhop (disabled by default)"
            ServerHopLabel.TextColor3 = Color3.fromRGB(180, 190, 210)
        end
    end
end)

print("✅ SFAA V8.0 Loaded Successfully!")
print("📱 Modern clean GUI active")
print("⚡ All features operational")
print("💰 Bounty filter: ENABLED (Min: $" .. ArrestSettings.BountyThreshold .. ")")
print("🔄 Server hop: ENABLED (targets 4-8 slots remaining)")
print("🚔 Team switch: AUTO-SWITCHED TO POLICE")
print("🏭 Spawn location: AUTO-RESPAWNS TO MILITARY BASE")
print("🚀 Script: AUTO-ENABLED")
print("")

-- AUTO-START AFTER GUI LOADS
print("⚡ AUTO-STARTING ARREST SYSTEM...")
task.wait(1)
ArrestSettings.Enabled = true
arrestState.phase = "SCANNING"
arrestState.startMoney = getMoney()
arrestState.startTime = tick()
initializeCharacterConnection()
startArrestSystem()
if ArrestSettings.EquipSpamEnabled then
    startEquipSpam()
end

print("✅ SFAA IS NOW ACTIVE AND RUNNING!")
print("🎯 Searching for criminals...")
displayAllBounties()
