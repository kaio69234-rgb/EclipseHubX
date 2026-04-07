local Services = setmetatable({}, {
    __index = function(self, serviceName)
        local service = game:GetService(serviceName)
        rawset(self, serviceName, service)
        return service
    end
})


local HttpService = Services.HttpService
local FolderName = "Bear hub | Farm"
local FileName = "Settings.json"
local FullPath = FolderName .. "/" .. FileName

if makefolder and not isfolder(FolderName) then 
    makefolder(FolderName) 
end

_G.SaveData = _G.SaveData or {}

function SaveSettings()
    if not writefile then return false end
    local success = pcall(function()
        local json = HttpService:JSONEncode(_G.SaveData)
        writefile(FullPath, json)
    end)
    return success
end

function LoadSettings()
    if not (isfile and isfile(FullPath)) then return false end
    local success, result = pcall(function()
        local content = readfile(FullPath)
        return HttpService:JSONDecode(content)
    end)
    if success and result then 
        _G.SaveData = result
        return true
    end
    return false
end

function GetSetting(name, default)
    return _G.SaveData[name] ~= nil and _G.SaveData[name] or default
end

LoadSettings()

-- ========================================
-- AUTO KEN (Observation Haki)
-- ========================================
local Players = Services.Players
local CollectionService = Services.CollectionService
local ReplicatedStorage = Services.ReplicatedStorage

local player = Players.LocalPlayer
local commE = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("CommE")

_G.AutoKen = true

local function HasKen()
    local char = player.Character
    return char and CollectionService:HasTag(char, "Ken")
end

task.spawn(function()
    while _G.AutoKen do
        task.wait(0.2)
        if not HasKen() then
            pcall(function()
                commE:FireServer("Ken", true)
            end)
        end
    end
end)

-- ========================================
-- AUTO TEAM & LIGHTING
-- ========================================
local desiredTeam = "Marines"

if not player.Team or player.Team.Name ~= desiredTeam then
    pcall(function()
        ReplicatedStorage.Remotes.CommF_:InvokeServer("SetTeam", desiredTeam)
    end)
end

local Lighting = Services.Lighting

-- Full bright (optimized lighting)
Lighting.Ambient = Color3.new(0.695, 0.695, 0.695)
Lighting.ColorShift_Bottom = Color3.new(0.695, 0.695, 0.695)
Lighting.ColorShift_Top = Color3.new(0.695, 0.695, 0.695)
Lighting.Brightness = 2
Lighting.FogEnd = 1e10

-- ========================================
-- GLOBAL VARIABLES (Cached & Organized)
-- ========================================
do
    ply = Services.Players
    plr = ply.LocalPlayer
    Root = plr.Character.HumanoidRootPart
    replicated = Services.ReplicatedStorage
    Lv = plr.Data.Level.Value
    TeleportService = Services.TeleportService
    TW = Services.TweenService
    Lighting = Services.Lighting
    Enemies = workspace.Enemies
    vim1 = Services.VirtualInputManager
    vim2 = Services.VirtualUser
    TeamSelf = plr.Team
    RunSer = Services.RunService
    Stats = Services.Stats
    Energy = plr.Character.Energy.Value
    
    -- Tables
    Boss = {}
    BringConnections = {}
    MaterialList = {}
    NPCList = {}
    
    -- Flags
    shouldTween = false
    SoulGuitar = false
    KenTest = true
    debug = false
    Brazier1 = false
    Brazier2 = false
    Brazier3 = false
    Sec = 0.1
    ClickState = 0
    Num_self = 25
end

-- Wait for game to load
repeat
    local loading = plr.PlayerGui:FindFirstChild("Main")
    loading = loading and loading:FindFirstChild("Loading")
    task.wait()
until game:IsLoaded() and not (loading and loading.Visible)

-- World Detection (Optimized)
local placeId = game.PlaceId
if placeId == 2753915549 or placeId == 85211729168715 then
    World1 = true
elseif placeId == 4442272183 or placeId == 79091703265657 then
    World2 = true
elseif placeId == 7449423635 or placeId == 100117331123089 then
    World3 = true
else
    plr:Kick("âŒ Error Blox Fruits - World not recognized")
end

Sea = World1 or World2 or World3

Marines = function()
    replicated.Remotes.CommF_:InvokeServer("SetTeam", "Marines")
end

Pirates = function()
    replicated.Remotes.CommF_:InvokeServer("SetTeam", "Pirates")
end
if World1 then
	Boss = {
			"The Gorilla King",
			"Bobby",
			"The Saw",
			"Yeti",
			"Mob Leader",
			"Vice Admiral",
			"Saber Expert",
			"Warden",
			"Chief Warden",
			"Swan",
			"Magma Admiral",
			"Fishman Lord",
			"Wysper",
			"Thunder God",
			"Cyborg",
			"Ice Admiral",
			"Greybeard",
		};
elseif World2 then
	Boss = {
			"Diamond",
			"Jeremy",
			"Fajita",
			"Don Swan",
			"Smoke Admiral",
			"Awakened Ice Admiral",
			"Tide Keeper",
			"Darkbeard",
			"Cursed Captain",
			"Order",
		};
elseif World3 then
	Boss = {
			"Stone",
			"Hydra Leader",
			"Kilo Admiral",
			"Captain Elephant",
			"Beautiful Pirate",
			"Cake Queen",
			"Longma",
			"Soul Reaper",
		};
end;
if World1 then
	MaterialList = {
			"Leather + Scrap Metal",
			"Angel Wings",
			"Magma Ore",
			"Fish Tail",
		};
elseif World2 then
	MaterialList = {
			"Leather + Scrap Metal",
			"Radioactive Material",
			"Ectoplasm",
			"Mystic Droplet",
			"Magma Ore",
			"Vampire Fang",
		};
elseif World3 then
	MaterialList = {
			"Scrap Metal",
			"Demonic Wisp",
			"Conjured Cocoa",
			"Dragon Scale",
			"Gunpowder",
			"Fish Tail",
			"Mini Tusk",
		};
end;
local e = {
		"Flame",
		"Ice",
		"Quake",
		"Light",
		"Dark",
		"String",
		"Rumble",
		"Magma",
		"Human: Buddha",
		"Sand",
		"Bird: Phoenix",
		"Dough",
	};
local K = {
		"Snow Lurker",
		"Arctic Warrior",
		"Hidden Key",
		"Awakened Ice Admiral",
	};
local n = {
		Mob = "Mythological Pirate",
		Mob2 = "Cursed Skeleton",
		"Hell\'s Messenger",
		Mob3 = "Cursed Skeleton",
		"Heaven\'s Guardian",
	};
local d = {
		"Part",
		"SpawnLocation",
		"Terrain",
		"WedgePart",
		"MeshPart",
	};
local z = { "Swan Pirate", "Jeremy" };
local H = { "Forest Pirate", "Captain Elephant" };
local F = { "Fajita", "Jeremy", "Diamond" };
local Q = {
		"Beast Hunter",
		"Lantern",
		"Guardian",
		"Grand Brigade",
		"Dinghy",
		"Sloop",
		"The Sentinel",
	};
local X = { "Cookie Crafter", "Head Baker", "Baking Staff", "Cake Guard" };
local P = { "Reborn Skeleton", "Posessed Mummy", "Demonic Soul", "Living Zombie" };
local w = {
		["Pirate Millionaire"] = CFrame.new(-712.82727050781, 98.577049255371, 5711.9541015625),
		["Pistol Billionaire"] = CFrame.new(-723.43316650391, 147.42906188965, 5931.9931640625),
		["Dragon Crew Warrior"] = CFrame.new(7021.5043945312, 55.762702941895, -730.12908935547),
		["Dragon Crew Archer"] = CFrame.new(6625, 378, 244),
		["Female Islander"] = CFrame.new(4692.7939453125, 797.97668457031, 858.84802246094),
		["Venomous Assailant"] = CFrame.new(4902, 670, 39),
		["Marine Commodore"] = CFrame.new(2401, 123, -7589),
		["Marine Rear Admiral"] = CFrame.new(3588, 229, -7085),
		["Fishman Raider"] = CFrame.new(-10941, 332, -8760),
		["Fishman Captain"] = CFrame.new(-11035, 332, -9087),
		["Forest Pirate"] = CFrame.new(-13446, 413, -7760),
		["Mythological Pirate"] = CFrame.new(-13510, 584, -6987),
		["Jungle Pirate"] = CFrame.new(-11778, 426, -10592),
		["Musketeer Pirate"] = CFrame.new(-13282, 496, -9565),
		["Reborn Skeleton"] = CFrame.new(-8764, 142, 5963),
		["Living Zombie"] = CFrame.new(-10227, 421, 6161),
		["Demonic Soul"] = CFrame.new(-9579, 6, 6194),
		["Posessed Mummy"] = CFrame.new(-9579, 6, 6194),
		["Peanut Scout"] = CFrame.new(-1993, 187, -10103),
		["Peanut President"] = CFrame.new(-2215, 159, -10474),
		["Ice Cream Chef"] = CFrame.new(-877, 118, -11032),
		["Ice Cream Commander"] = CFrame.new(-877, 118, -11032),
		["Cookie Crafter"] = CFrame.new(-2021, 38, -12028),
		["Cake Guard"] = CFrame.new(-2024, 38, -12026),
		["Baking Staff"] = CFrame.new(-1932, 38, -12848),
		["Head Baker"] = CFrame.new(-1932, 38, -12848),
		["Cocoa Warrior"] = CFrame.new(95, 73, -12309),
		["Chocolate Bar Battler"] = CFrame.new(647, 42, -12401),
		["Sweet Thief"] = CFrame.new(116, 36, -12478),
		["Candy Rebel"] = CFrame.new(47, 61, -12889),
		Ghost = CFrame.new(5251, 5, 1111),
	};
EquipWeapon = function(I)
		if not I then
			return;
		end;
		if plr.Backpack:FindFirstChild(I) then
			plr.Character.Humanoid:EquipTool(plr.Backpack:FindFirstChild(I));
		end;
	end;
weaponSc = function(I)
		for e, K in pairs(plr.Backpack:GetChildren()) do
			if K:IsA("Tool") then
				if K.ToolTip == I then
					EquipWeapon(K.Name);
				end;
			end;
		end;
	end;
hookfunction(require((game:GetService("ReplicatedStorage")).Effect.Container.Death), function()
 
end);
hookfunction((require((game:GetService("ReplicatedStorage")):WaitForChild("GuideModule"))).ChangeDisplayedNPC, function()
 
end);
hookfunction(error, function()
 
end);
hookfunction(warn, function()
 
end);
local O = workspace:FindFirstChild("Rocks");
if O then
	O:Destroy();
end;
gay = (function()
    local I = game:GetService("Lighting");
    local e = I:FindFirstChild("LightingLayers");

    -- NÃƒO remover DarkFog

    local K = workspace._WorldOrigin["Foam;"];
    if K and workspace._WorldOrigin["Foam;"] then
        K:Destroy();
    end;
end)();

local G = {};
G.__index = G;
G.Alive = function(I)
		if not I then
			return;
		end;
		local e = I:FindFirstChild("Humanoid");
		return e and e.Health > 0;
	end;
G.Pos = function(I, e)
		return (Root.Position - mode.Position).Magnitude <= e;
	end;
G.Dist = function(I, e)
		return (Root.Position - (I:FindFirstChild("HumanoidRootPart")).Position).Magnitude <= e;
	end;
G.DistH = function(I, e)
		return (Root.Position - (I:FindFirstChild("HumanoidRootPart")).Position).Magnitude > e;
	end;
-- ALTURA ÃšNICA AJUSTÃVEL DO MOB
_G.MobHeight = _G.MobHeight or 20

G.Kill = function(I, e)
	if not (I and e) then return end

	local hrp = I:FindFirstChild("HumanoidRootPart")
	if not hrp then return end

	-- trava posiÃ§Ã£o do mob
	if not I:GetAttribute("Locked") then
		I:SetAttribute("Locked", hrp.CFrame)
	end

	-- posiÃ§Ã£o alvo do bring
	PosMon = (I:GetAttribute("Locked")).Position

	-- >>> FORÃ‡A O BRING <<<
	_B = true
	BringEnemy()

	-- equipa arma
	EquipWeapon(_G.SelectWeapon)

	local tool = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool")
	if not tool then return end

	-- TP acima do mob (altura Ãºnica)
	_tp(hrp.CFrame * CFrame.new(0, _G.MobHeight, 0))
end
G.Kill2 = function(I, e)
		if I and e then
			if not I:GetAttribute("Locked") then
				I:SetAttribute("Locked", I.HumanoidRootPart.CFrame);
			end;
			PosMon = (I:GetAttribute("Locked")).Position;
			BringEnemy();
			EquipWeapon(_G.SelectWeapon);
			local e = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool");
			local K = e.ToolTip;
			if K == "Blox Fruit" then
				_tp((I.HumanoidRootPart.CFrame * CFrame.new(0, 10, 0)) * CFrame.Angles(0, math.rad(90), 0));
			else
				_tp((I.HumanoidRootPart.CFrame * CFrame.new(0, 20, 8)) * CFrame.Angles(0, math.rad(180), 0));
			end;
			if RandomCFrame then
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 30, 25));
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(25, 30, 0));
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(-25, 30, 0));
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 30, 25));
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(-25, 30, 0));
			end;
		end;
	end;
G.KillSea = function(I, e)
		if I and e then
			if not I:GetAttribute("Locked") then
				I:SetAttribute("Locked", I.HumanoidRootPart.CFrame);
			end;
			PosMon = (I:GetAttribute("Locked")).Position;
			BringEnemy();
			EquipWeapon(_G.SelectWeapon);
			local e = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool");
			local K = e.ToolTip;
			if K == "Blox Fruit" then
				_tp((I.HumanoidRootPart.CFrame * CFrame.new(0, 10, 0)) * CFrame.Angles(0, math.rad(90), 0));
			else
				notween(I.HumanoidRootPart.CFrame * CFrame.new(0, 50, 8));
				wait(.85);
				notween(I.HumanoidRootPart.CFrame * CFrame.new(0, 400, 0));
				wait(1);
			end;
		end;
	end;
G.Sword = function(I, e)
		if I and e then
			if not I:GetAttribute("Locked") then
				I:SetAttribute("Locked", I.HumanoidRootPart.CFrame);
			end;
			PosMon = (I:GetAttribute("Locked")).Position;
			BringEnemy();
			weaponSc("Sword");
			_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 30, 0));
			if RandomCFrame then
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 30, 25));
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(25, 30, 0));
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(-25, 30, 0));
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 30, 25));
				wait(.1);
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(-25, 30, 0));
			end;
		end;
	end;
-- CONTROLE DE SKILLS DA FRUTA (MAESTRIA)
_G.FruitSkills = {
    Z = false,
    X = false,
    C = false,
    V = false,
    F = false
}

UseFruitSkills = function()
    weaponSc("Blox Fruit")

    if _G.FruitSkills.Z then
        Useskills("Blox Fruit", "Z")
    end
    if _G.FruitSkills.X then
        Useskills("Blox Fruit", "X")
    end
    if _G.FruitSkills.C then
        Useskills("Blox Fruit", "C")
    end
    if _G.FruitSkills.V then
        Useskills("Blox Fruit", "V")
    end
    if _G.FruitSkills.F then
        vim1:SendKeyEvent(true, "F", false, game)
        vim1:SendKeyEvent(false, "F", false, game)
    end
end

G.Mas = function(I, e)
		if I and e then
			if not I:GetAttribute("Locked") then
				I:SetAttribute("Locked", I.HumanoidRootPart.CFrame);
			end;
			PosMon = (I:GetAttribute("Locked")).Position;
			BringEnemy();
			if I.Humanoid.Health <= HealthM then
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 20, 0));
				UseFruitSkills()
			else
				weaponSc("Melee");
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 30, 0));
			end;
		end;
	end;
G.Masgun = function(I, e)
		if I and e then
			if not I:GetAttribute("Locked") then
				I:SetAttribute("Locked", I.HumanoidRootPart.CFrame);
			end;
			PosMon = (I:GetAttribute("Locked")).Position;
			BringEnemy();
			if I.Humanoid.Health <= HealthM then
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 35, 8));
				Useskills("Gun", "Z");
				Useskills("Gun", "X");
			else
				weaponSc("Melee");
				_tp(I.HumanoidRootPart.CFrame * CFrame.new(0, 30, 0));
			end;
		end;
	end;
statsSetings = function(I, e)
		if I == "Melee" then
			if plr.Data.Points.Value ~= 0 then
				replicated.Remotes.CommF_:InvokeServer("AddPoint", "Melee", e);
			end;
		elseif I == "Defense" then
			if plr.Data.Points.Value ~= 0 then
				replicated.Remotes.CommF_:InvokeServer("AddPoint", "Defense", e);
			end;
		elseif I == "Sword" then
			if plr.Data.Points.Value ~= 0 then
				replicated.Remotes.CommF_:InvokeServer("AddPoint", "Sword", e);
			end;
		elseif I == "Gun" then
			if plr.Data.Points.Value ~= 0 then
				replicated.Remotes.CommF_:InvokeServer("AddPoint", "Gun", e);
			end;
		elseif I == "Devil" then
			if plr.Data.Points.Value ~= 0 then
				replicated.Remotes.CommF_:InvokeServer("AddPoint", "Demon Fruit", e);
			end;
		end;
	end;




--==================================================
-- VARIÃVEIS DE CONTROLE NECESSÃRIAS
--==================================================
_G = _G or {}

_B = false
PosMon = nil

_G.BringRange = _G.BringRange or 235
_G.MaxBringMobs = _G.MaxBringMobs or 3 -- LIMITE DE MOBS

_G.FarmPriorityElf = _G.FarmPriorityElf or false
_G.FarmMastery_S   = _G.FarmMastery_S or false

local TweenService = game:GetService("TweenService")
local TweenInfoBring = TweenInfo.new(
    0.45, -- velocidade do tween
    Enum.EasingStyle.Linear,
    Enum.EasingDirection.Out
)

--==================================================
-- FUNÃ‡ÃƒO: VERIFICA SE QUALQUER FARM ESTÃ ATIVO
--==================================================
local function FarmAtivo()
    -- PRIORIDADE ABSOLUTA (ELF)
    if _G.FarmPriorityElf or _G.FarmElfLevelCustom then
        return true
    end

    -- AUTO MASTERY ALL SWORD (INDEPENDENTE DO START FARM)  
    if _G.FarmMastery_S then  
        return true  
    end  

    -- OUTROS FARMS (DEPENDENTES DO START FARM)  
    return _G.StartFarm and (
        _G.Level or  
        _G.AutoFarm_Bone or  
        _G.AutoFarm_Cake or  
        _G.FarmMastery_Dev or  
        _G.FarmMastery_G or  
        (getgenv()).AutoMaterial or  
        _G.AutoTyrant or
        _G.SailBoat_Hydra or _G.WardenBoss or _G.AutoFactory or _G.HighestMirage or _G.HCM or _G.PGB or _G.Leviathan1 or _G.UPGDrago or _G.Complete_Trials or _G.TpDrago_Prehis or _G.BuyDrago or _G.AutoFireFlowers or _G.DT_Uzoth or _G.AutoBerry or _G.Prehis_Find or _G.Prehis_Skills or _G.Prehis_DB or _G.Prehis_DE or _G.FarmBlazeEM or _G.Dojoo or _G.CollectPresent or _G.AutoLawKak or _G.TpLab or _G.AutoPhoenixF or _G.AutoFarmChest or _G.AutoHytHallow or _G.LongsWord or _G.BlackSpikey or _G.AutoHolyTorch or _G.TrainDrago or _G.AutoSaber or _G.FarmMastery_Dev or _G.CitizenQuest or _G.AutoEctoplasm or _G.KeysRen or _G.Auto_Rainbow_Haki or _G.obsFarm or _G.AutoBigmom or _G.Doughv2 or _G.AuraBoss or _G.Raiding or _G.Auto_Cavender or _G.TpPly or _G.Bartilo_Quest or _G.Level or _G.FarmEliteHunt or _G.AutoZou or _G.AutoFarm_Bone or (getgenv()).AutoMaterial or _G.CraftVM or _G.FrozenTP or _G.TPDoor or _G.AcientOne or _G.AutoFarmNear or _G.AutoRaidCastle or _G.DarkBladev3 or _G.AutoFarmRaid or _G.Auto_Cake_Prince or _G.Addealer or _G.TPNpc or _G.TwinHook or _G.FindMirage or _G.FarmChestM or _G.Shark or _G.TerrorShark or _G.Piranha or _G.MobCrew or _G.SeaBeast1 or _G.FishBoat or _G.Auto or _G.AutoPoleV2 or _G.Auto_SuperHuman or _G.AutoDeathStep or _G.Auto_SharkMan_Karate or _G.Auto_Electric_Claw or _G.AutoDragonTalon or _G.Auto_Def_DarkCoat or _G.Auto_God_Human or _G.Auto_Tushita or _G.AutoMatSoul or _G.AutoKenVTWO or _G.AutoSerpentBow or _G.AutoFMon or _G.Auto_Soul_Guitar or _G.TPGEAR or _G.AutoSaw or _G.AutoTridentW2 or _G.Auto_StartRaid or _G.AutoEvoRace or _G.AutoGetQuestBounty or _G.MarinesCoat or _G.TravelDres or _G.Defeating or _G.DummyMan or _G.Auto_Yama or _G.Auto_SwanGG or _G.SwanCoat or _G.AutoEcBoss or _G.Auto_Mink or _G.Auto_Human or _G.Auto_Skypiea or _G.Auto_Fish or _G.CDK_TS or _G.CDK_YM or _G.CDK or _G.AutoFarmGodChalice or _G.AutoFistDarkness or _G.AutoMiror or _G.Teleport or _G.AutoKilo or _G.AutoGetUsoap or _G.Praying or _G.TryLucky or _G.AutoColShad or _G.AutoUnHaki or _G.Auto_DonAcces or _G.AutoRipIngay or _G.DragoV3 or _G.DragoV1 or _G.SailBoats or NextIs or _G.FarmGodChalice or _G.IceBossRen or senth or senth2 or _G.Lvthan or _G.beasthunter or _G.DangerLV or _G.Relic123 or _G.tweenKitsune or _G.Collect_Ember or _G.AutofindKitIs or _G.snaguine or _G.TwFruits or _G.tweenKitShrine or _G.Tp_LgS or _G.Tp_MasterA or _G.tweenShrine or _G.FarmMastery_G or _G.FarmMastery_S
    )
end

--==================================================
-- FUNÃ‡ÃƒO: IGNORA MOBS INDESEJADOS
--==================================================
local function IsRaidMob(mob)
    local n = mob.Name:lower()

    if n:find("raid") or n:find("microchip") or n:find("island") then  
        return true  
    end  

    if mob:GetAttribute("IsRaid")  
        or mob:GetAttribute("RaidMob")  
        or mob:GetAttribute("IsBoss") then  
        return true  
    end  

    local hum = mob:FindFirstChild("Humanoid")  
    if hum and hum.WalkSpeed == 0 then  
        return true  
    end  

    if mob.Parent and tostring(mob.Parent):lower():find("_worldorigin") then  
        return true  
    end  

    return false
end

--==================================================
-- FUNÃ‡ÃƒO PRINCIPAL: BRING
--==================================================
BringEnemy = function()
    if not FarmAtivo() or not _B then return end

    local plr = game.Players.LocalPlayer  
    local char = plr.Character  
    local hrp = char and char:FindFirstChild("HumanoidRootPart")  
    if not hrp then return end  

    -- Simulation Radius  
    pcall(function()  
        sethiddenproperty(plr, "SimulationRadius", math.huge)  
    end)  

    lo
