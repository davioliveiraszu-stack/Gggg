// ============================================================================
// INCLUDES
// ============================================================================
#include <a_samp>
#include <streamer>
#include <sscanf2>
#include <DOF2>
#include <zcmd>

#pragma disablerecursion

// ============================================================================
// DEFINES
// ============================================================================

#if defined MAX_VEHICLES
    #undef MAX_VEHICLES
    #define MAX_VEHICLES (1500)
#endif

#if       defined MAX_PLAYERS
#undef    MAX_PLAYERS
#define   MAX_PLAYERS     (150)  
#endif

#define MAX_VEICULOS_ORG       5
#define MAX_SLOTS_COFRE        20
#define MAX_MEMBROS_ORG        50
#define MAX_ORGS               10
#define MATERIAL_INICIAL       100000

#define ORG_TIPO_CRIMINOSA     0
#define ORG_TIPO_CORPORACAO    1

// ============================================================================
// ENUMS
// ============================================================================

enum 
{
	DIALOG_REGISTRO,
	DIALOG_LOGAR,
    DIALOG_ORG_MAIN,
    DIALOG_ORG_CRIAR,
    DIALOG_ORG_LISTA,
    DIALOG_ORG_GERENCIAR,
    DIALOG_ORG_SETAR_ID,
    DIALOG_ORG_SETAR_SEL,
    DIALOG_ORG_COFRE_MENU,
    DIALOG_ORG_COFRE_ARMAS,
    DIALOG_ORG_COFRE_DEP_A,
    DIALOG_ORG_COFRE_RET_A,
    DIALOG_ORG_TIPO,
    DIALOG_ORG_MENU,
    DIALOG_ORG_CONVIDAR,
    DIALOG_ORG_MEMBRO_ACAO,
    DIALOG_ORG_VEICULO,
    DIALOG_ORG_CONFIG_VEIC,
    DIALOG_ORG_CONFIRMAR_RESET,
    DIALOG_ORG_CONFIRMAR_EXCLUIR,
    DIALOG_ORG_SAIR,
    DIALOG_ORG_COFRE_DEP_DIN,
    DIALOG_ORG_COFRE_SACAR_DIN,
    DIALOG_ORG_ARSENAL_ITENS,
    DIALOG_ORG_MATERIAL,
    DIALOG_ORG_COFRE_DEPOSITAR,
    DIALOG_ORG_COFRE_RETIRAR,
    DIALOG_ORG_ESCOLHER_COR,
    DIALOG_ORG_MEMBROS,
    DIALOG_ORG_FARDA_MENU,
    DIALOG_ORG_SKIN
};

enum pInfo {
    pAdmin,
    pDinheiro,
    pSkin,
    pSkinFardado,
    pInterior,
    pVirtualWorld,
    Float:pPosX,
    Float:pPosY,
    Float:pPosZ,
    Float:pPosR,
    Float:pVida,
    Float:pArmour,
    pOrgID,
    pOrgCargo,
    bool:pLogado,
    bool:pFardado
};

enum E_MEMBRO_DATA {
    MembroNome[MAX_PLAYER_NAME],
    MembroCargo,
    bool:MembroAtivo
};

enum E_ORG_DATA {
    bool:OrgCriada,
    OrgNome[50],
    OrgTipo,
    OrgCor,
    OrgDono[MAX_PLAYER_NAME],
    bool:TemDono,
    OrgDinheiro,
    OrgMaterial,
    bool:CofreStatus,
    Float:CofreX,
    Float:CofreY,
    Float:CofreZ,
    Text3D:CofreText,
    ObjCofre,
    SkinOrg,
    Float:VeicPickupX, 
    Float:VeicPickupY, 
    Float:VeicPickupZ,
    PickupVeiculo,
    Text3D:VeicText,
    Float:FardaX, 
    Float:FardaY, 
    Float:FardaZ,
    PickupFarda,
    Text3D:FardaText,
    Float:ArsenalX, 
    Float:ArsenalY, 
    Float:ArsenalZ,
    PickupArsenal,
    Text3D:ArsenalText,
    bool:ArsenalStatus
};

enum E_COFRE_SLOT {
    CofreArmaID,
    CofreArmaMunicao
};

enum E_VEICULO_DATA {
    vModelo,
    Float:vX, 
    Float:vY, 
    Float:vZ, 
    Float:vA,
    vID_Atual,
    bool:vSpawnado,
    NomeModelo[32]
};

new PlayerInfo[MAX_PLAYERS][pInfo];
new OrgMembros[MAX_ORGS][MAX_MEMBROS_ORG][E_MEMBRO_DATA];
new OrgMembrosCount[MAX_ORGS];
new OrgDados[MAX_ORGS][E_ORG_DATA];
new OrgArmas[MAX_ORGS][MAX_SLOTS_COFRE][E_COFRE_SLOT];
new OrgFrota[MAX_ORGS][MAX_VEICULOS_ORG][E_VEICULO_DATA];

new VeiculosCorporacao[] = {
    427,  // Police Car (LSPD)
    490,  // Police Car (FBI)
    596,  // Police Car (LVPD)
    528,  // Police Truck (SWAT)
    523   // HPV1000 (Police Motorcycle)
};

new VeiculosCriminosa[] = {
    550,  // Sunrise
    482,  // Burrito
    440,  // Rumpo
    463,  // Freeway (Moto)
    560   // Sultan
};

// ============================================================================
// VARIÁVEIS GLOBAIS
// ============================================================================

new PlayerVeiculoID[MAX_PLAYERS] = {0, ...};

new MenuOrgSelecionada[MAX_PLAYERS];
new MenuPlayerAlvoID[MAX_PLAYERS];
new MenuSlotSelecionado[MAX_PLAYERS];
new MenuCargoSelecionado[MAX_PLAYERS];
new MenuMembroIndex[MAX_PLAYERS];

new CoresOrg[][] = {
    {0x66CCFFAA, "Azul"},
    {0xFF3333AA, "Vermelho"},
    {0x33FF33AA, "Verde"},
    {0xFFCC00AA, "Amarelo"},
    {0xFF66FFAA, "Rosa"},
    {0xFF9933AA, "Laranja"},
    {0x9933FFAA, "Roxo"},
    {0xFFFFFFAA, "Branco"},
    {0x000000AA, "Preto"},
    {0x00FFFFAA, "Ciano"}
};

new NomesVeiculos[][] = {
    {"Landstalker"}, {"Bravura"}, {"Buffalo"}, {"Linerunner"}, {"Perrenial"}, {"Sentinel"},
    {"Dumper"}, {"Firetruck"}, {"Trashmaster"}, {"Stretch"}, {"Manana"}, {"Infernus"},
    {"Voodoo"}, {"Pony"}, {"Mule"}, {"Cheetah"}, {"Ambulance"}, {"Leviathan"},
    {"Moonbeam"}, {"Esperanto"}, {"Taxi"}, {"Washington"}, {"Bobcat"}, {"Mr Whoopee"},
    {"BF Injection"}, {"Hunter"}, {"Premier"}, {"Enforcer"}, {"Securicar"}, {"Banshee"},
    {"Predator"}, {"Bus"}, {"Rhino"}, {"Barracks"}, {"Hotknife"}, {"Trailer"},
    {"Previon"}, {"Coach"}, {"Cabbie"}, {"Stallion"}, {"Rumpo"}, {"RC Bandit"},
    {"Romero"}, {"Packer"}, {"Monster"}, {"Admiral"}, {"Squalo"}, {"Seasparrow"},
    {"Pizzaboy"}, {"Tram"}, {"Trailer"}, {"Turismo"}, {"Speeder"}, {"Reefer"},
    {"Tropic"}, {"Flatbed"}, {"Yankee"}, {"Caddy"}, {"Solair"}, {"Berkley's RC"},
    {"Skimmer"}, {"PCJ-600"}, {"Faggio"}, {"Freeway"}, {"RC Baron"}, {"RC Raider"},
    {"Glendale"}, {"Oceanic"}, {"Sanchez"}, {"Sparrow"}, {"Patriot"}, {"Quad"},
    {"Coastguard"}, {"Dinghy"}, {"Hermes"}, {"Sabre"}, {"Rustler"}, {"ZR-350"},
    {"Walton"}, {"Regina"}, {"Comet"}, {"BMX"}, {"Burrito"}, {"Camper"},
    {"Marquis"}, {"Baggage"}, {"Dozer"}, {"Maverick"}, {"News Chopper"}, {"Rancher"},
    {"FBI Rancher"}, {"Virgo"}, {"Greenwood"}, {"Jetmax"}, {"Hotring"}, {"Sandking"},
    {"Blista Compact"}, {"Police Maverick"}, {"Boxville"}, {"Benson"}, {"Mesa"},
    {"RC Goblin"}, {"Hotring Racer A"}, {"Hotring Racer B"}, {"Bloodring Banger"},
    {"Rancher"}, {"Super GT"}, {"Elegant"}, {"Journey"}, {"Bike"}, {"Mountain Bike"},
    {"Beagle"}, {"Cropduster"}, {"Stunt"}, {"Tanker"}, {"Roadtrain"}, {"Nebula"},
    {"Majestic"}, {"Buccaneer"}, {"Shamal"}, {"Hydra"}, {"FCR-900"}, {"NRG-500"},
    {"HPV1000"}, {"Cement Truck"}, {"Tow Truck"}, {"Fortune"}, {"Cadrona"}, {"FBI Truck"},
    {"Willard"}, {"Forklift"}, {"Tractor"}, {"Combine"}, {"Feltzer"}, {"Remington"},
    {"Slamvan"}, {"Blade"}, {"Freight"}, {"Streak"}, {"Vortex"}, {"Vincent"},
    {"Bullet"}, {"Clover"}, {"Sadler"}, {"Firetruck LA"}, {"Hustler"}, {"Intruder"},
    {"Primo"}, {"Cargobob"}, {"Tampa"}, {"Sunrise"}, {"Merit"}, {"Utility Van"},
    {"Nevada"}, {"Yosemite"}, {"Windsor"}, {"Monster A"}, {"Monster B"}, {"Uranus"},
    {"Jester"}, {"Sultan"}, {"Stratum"}, {"Elegy"}, {"Raindance"}, {"RC Tiger"},
    {"Flash"}, {"Tahoma"}, {"Savanna"}, {"Bandito"}, {"Freight Flat"}, {"Streak Carriage"},
    {"Kart"}, {"Mower"}, {"Dune"}, {"Sweeper"}, {"Broadway"}, {"Tornado"},
    {"AT400"}, {"DFT-30"}, {"Huntley"}, {"Stafford"}, {"BF-400"}, {"News Van"},
    {"Tug"}, {"Petrol Trailer"}, {"Emperor"}, {"Wayfarer"}, {"Euros"}, {"Hotdog"},
    {"Club"}, {"Freight Box"}, {"Trailer"}, {"Andromada"}, {"Dodo"}, {"RC Cam"},
    {"Launch"}, {"Police Car LSPD"}, {"Police Car SFPD"}, {"Police Car LVPD"},
    {"Police Ranger"}, {"Picador"}, {"S.W.A.T."}, {"Alpha"}, {"Phoenix"}, {"Glendale"},
    {"Sadler"}, {"Luggage Trailer A"}, {"Luggage Trailer B"}, {"Stair Trailer"},
    {"Boxville"}, {"Farm Plow"}, {"Utility Trailer"}
};

static const s_AnimationLibraries[][] = {
    "AIRPORT", "ATTRACTORS", "BAR", "BASEBALL", "BD_FIRE", "BEACH", "BENCHPRESS",
    "BF_INJECTION", "BIKED", "BIKEH", "BIKELEAP", "BIKES", "BIKEV", "BIKE_DBZ",
    "BMX", "BOMBER", "BOX", "BSKTBALL", "BUDDY", "BUS", "CAMERA", "CAR", "CARRY",
    "CAR_CHAT", "CASINO", "CHAINSAW", "CHOPPA", "CLOTHES", "COACH", "COLT45",
    "COP_AMBIENT", "COP_DVBYZ", "CRACK", "CRIB", "DAM_JUMP", "DANCING", "DEALER",
    "DILDO", "DODGE", "DOZER", "DRIVEBYS", "FAT", "FIGHT_B", "FIGHT_C", "FIGHT_D",
    "FIGHT_E", "FINALE", "FINALE2", "FLAME", "FLOWERS", "FOOD", "FREEWEIGHTS",
    "GANGS", "GHANDS", "GHETTO_DB", "GOGGLES", "GRAFFITI", "GRAVEYARD", "GRENADE",
    "GYMNASIUM", "HAIRCUTS", "HEIST9", "INT_HOUSE", "INT_OFFICE", "INT_SHOP",
    "JST_BUISNESS", "KART", "KISSING", "KNIFE", "LAPDAN1", "LAPDAN2", "LAPDAN3",
    "LOWRIDER", "MD_CHASE", "MD_END", "MEDIC", "MISC", "MTB", "MUSCULAR", "NEVADA",
    "ON_LOOKERS", "OTB", "PARACHUTE", "PARK", "PAULNMAC", "PED", "PLAYER_DVBYS",
    "PLAYIDLES", "POLICE", "POOL", "POOR", "PYTHON", "QUAD", "QUAD_DBZ", "RAPPING",
    "RIFLE", "RIOT", "ROB_BANK", "ROCKET", "RUSTLER", "RYDER", "SCRATCHING",
    "SHAMAL", "SHOP", "SHOTGUN", "SILENCED", "SKATE", "SMOKING", "SNIPER",
    "SPRAYCAN", "STRIP", "SUNBATHE", "SWAT", "SWEET", "SWIM", "SWORD", "TANK",
    "TATTOOS", "TEC", "TRAIN", "TRUCK", "UZI", "VAN", "VENDING", "VORTEX",
    "WAYFARER", "WEAPONS", "WUZI", "WOP", "GFUNK", "RUNNINGMAN"
};

stock PreloadPlayerAnimations(playerid)
{
    for(new i = 0; i < sizeof(s_AnimationLibraries); i++) {
        ApplyAnimation(playerid, s_AnimationLibraries[i], "null", 0.0, 0, 0, 0, 0, 0);
    }
    return 1;
}

// ============================================================================
// FORWARDS
// ============================================================================

forward CarregarOrgs();
forward SalvarOrgs();
forward SaveOrgMembers(orgid);
forward LoadOrgMembers(orgid);
forward AtualizarMembroOffline(orgid, const nome[], cargo);
forward GetCargoNome(org_tipo, nivel, output[], len);
forward IsPlayerInAnyOrg(playerid);
forward IsPlayerLeader(playerid);
forward IsPlayerSubLeader(playerid);
forward SendOrgMessage(orgid, color, const msg[]);
forward LogOrgAction(orgid, playerid, const action[]);
forward GetPlayerOrgRank(orgid, playerid);
forward GetOrgTipoNome(orgid, output[], len);
forward CanAccessCofre(playerid);
forward CanAccessArsenal(playerid);
forward GetVehicleModelName(modelid, output[], len);
forward AtualizarFardaPlayer(playerid);
forward RemoverPlayerWeapon(playerid, weaponid);
forward AtualizarSkinMembros(orgid);
forward AtualizarTextLabelsOrg(orgid);
forward ResetarOrganizacao(orgid);
forward ExcluirOrganizacao(orgid);

// ============================================================================
// FUNÇÕES UTILITÁRIAS
// ============================================================================

GetVehicleModelName(modelid, output[], len)
{
    if(modelid >= 400 && modelid <= 611) {
        format(output, len, NomesVeiculos[modelid - 400]);
    } else {
        format(output, len, "Desconhecido");
    }
    return 1;
}

GetCargoNome(org_tipo, nivel, output[], len)
{
    if(org_tipo == ORG_TIPO_CRIMINOSA) 
    {
        new cargos[5][15] = {"Fogueteiro", "Vapor", "Gerente", "Vice-lider", "Chefe"};
        if(nivel >= 0 && nivel < 5) format(output, len, cargos[nivel]);
    } 
    else if(org_tipo == ORG_TIPO_CORPORACAO) 
    {
        new cargos[5][20] = {"Soldado", "Sub Tenente", "Tenente", "Major", "Comandante Geral"};
        if(nivel >= 0 && nivel < 5) format(output, len, cargos[nivel]);
    }
    return 1;
}

IsPlayerInAnyOrg(playerid)
{
    if(PlayerInfo[playerid][pOrgID] != -1 && OrgDados[PlayerInfo[playerid][pOrgID]][OrgCriada]) {
        return 1;
    }
    return 0;
}

IsPlayerLeader(playerid)
{
    if(PlayerInfo[playerid][pOrgID] != -1 && PlayerInfo[playerid][pOrgCargo] == 4) {
        return 1;
    }
    return 0;
}

IsPlayerSubLeader(playerid)
{
    if(PlayerInfo[playerid][pOrgID] != -1 && PlayerInfo[playerid][pOrgCargo] == 3) {
        return 1;
    }
    return 0;
}

CanAccessCofre(playerid)
{
    new org = PlayerInfo[playerid][pOrgID];
    if(org == -1) return 0;
    if(OrgDados[org][OrgTipo] != ORG_TIPO_CRIMINOSA) return 0;
    if(OrgDados[org][CofreStatus] == false) return 1;
    if(IsPlayerLeader(playerid) || IsPlayerSubLeader(playerid)) return 1;
    return 0;
}

CanAccessArsenal(playerid)
{
    new org = PlayerInfo[playerid][pOrgID];
    if(org == -1) return 0;
    if(OrgDados[org][OrgTipo] != ORG_TIPO_CORPORACAO) return 0;
    if(OrgDados[org][ArsenalStatus] == false) return 1;
    if(IsPlayerLeader(playerid) || IsPlayerSubLeader(playerid)) return 1;
    return 0;
}

SendOrgMessage(orgid, color, const msg[])
{
    for(new i = 0; i < MAX_PLAYERS; i++) {
        if(IsPlayerConnected(i) && PlayerInfo[i][pOrgID] == orgid) {
            SendClientMessage(i, color, msg);
        }
    }
    return 1;
}

LogOrgAction(orgid, playerid, const action[])
{
    new nome[MAX_PLAYER_NAME], msg[200];
    GetPlayerName(playerid, nome, sizeof(nome));
    format(msg, sizeof(msg), "[%s] %s: %s", OrgDados[orgid][OrgNome], nome, action);
    SendOrgMessage(orgid, OrgDados[orgid][OrgCor], msg);
    return 1;
}

AtualizarFardaPlayer(playerid)
{
    new org = PlayerInfo[playerid][pOrgID];
    new skin;
    
    if(OrgDados[org][OrgTipo] == ORG_TIPO_CORPORACAO && PlayerInfo[playerid][pFardado] == true) {
        switch(PlayerInfo[playerid][pOrgCargo]){
            case 4: skin = 286;
            case 3: skin = 285;
            case 2: skin = 281;
            case 1: skin = 280;
            case 0: skin = 282;
        }
        SetPlayerSkin(playerid, skin);
    } else {
        SetPlayerSkin(playerid, PlayerInfo[playerid][pSkin]);
    }
}

AtualizarTextLabelsOrg(orgid)
{
    printf("AtualizarTextLabelsOrg(%d)", orgid);
    new txt[500];
    
    if(OrgDados[orgid][OrgTipo] == ORG_TIPO_CRIMINOSA && OrgDados[orgid][CofreX] != 0.0 && OrgDados[orgid][CofreY] != 0.0 && OrgDados[orgid][CofreZ] != 0.0) {
        if(OrgDados[orgid][CofreText] == Text3D:-1) {
            format(txt, sizeof(txt), "%s\nCofre da Organizacao\nPressione 'F' para interagir", OrgDados[orgid][OrgNome]);
            OrgDados[orgid][CofreText] = CreateDynamic3DTextLabel(txt, OrgDados[orgid][OrgCor], OrgDados[orgid][CofreX], OrgDados[orgid][CofreY], OrgDados[orgid][CofreZ]+0.5, 15.0);
        } else {
            format(txt, sizeof(txt), "%s\nCofre da Organizacao\nPressione 'F' para interagir", OrgDados[orgid][OrgNome]);
            UpdateDynamic3DTextLabelText(OrgDados[orgid][CofreText], OrgDados[orgid][OrgCor], txt);
        }
    }
    
    if(OrgDados[orgid][VeicPickupX] != 0.0 && OrgDados[orgid][VeicPickupY] != 0.0 && OrgDados[orgid][VeicPickupZ] != 0.0) {
        if(OrgDados[orgid][VeicText] == Text3D:-1) {
            format(txt, sizeof(txt), "%s\nGaragem da Organizacao\nPressione 'F' para interagir", OrgDados[orgid][OrgNome]);
            OrgDados[orgid][VeicText] = CreateDynamic3DTextLabel(txt, OrgDados[orgid][OrgCor], OrgDados[orgid][VeicPickupX], OrgDados[orgid][VeicPickupY], OrgDados[orgid][VeicPickupZ]+0.5, 15.0);
        } else {
            format(txt, sizeof(txt), "%s\nGaragem da Organizacao\nPressione 'F' para interagir", OrgDados[orgid][OrgNome]);
            UpdateDynamic3DTextLabelText(OrgDados[orgid][VeicText], OrgDados[orgid][OrgCor], txt);
        }
    }
    
	if(OrgDados[orgid][OrgTipo] == ORG_TIPO_CORPORACAO && OrgDados[orgid][FardaX] != 0.0 && OrgDados[orgid][FardaY] != 0.0 && OrgDados[orgid][FardaZ] != 0.0) {
        if(OrgDados[orgid][FardaText] == Text3D:-1) {
            format(txt, sizeof(txt), "%s\nFarda da Corporação\nPressione 'F' para interagir", OrgDados[orgid][OrgNome]);
            OrgDados[orgid][FardaText] = CreateDynamic3DTextLabel(txt, OrgDados[orgid][OrgCor], OrgDados[orgid][FardaX], OrgDados[orgid][FardaY], OrgDados[orgid][FardaZ]+0.5, 15.0);
        } else {
            format(txt, sizeof(txt), "%s\nFarda da Corporação\nPressione 'F' para interagir", OrgDados[orgid][OrgNome]);
            UpdateDynamic3DTextLabelText(OrgDados[orgid][FardaText], OrgDados[orgid][OrgCor], txt);
        }
    }
    
    if(OrgDados[orgid][OrgTipo] == ORG_TIPO_CORPORACAO && OrgDados[orgid][ArsenalX] != 0.0 && OrgDados[orgid][ArsenalY] != 0.0 && OrgDados[orgid][ArsenalZ] != 0.0) {
        if(OrgDados[orgid][ArsenalText] == Text3D:-1) {
            format(txt, sizeof(txt), "%s\nArsenal - Material: %d/%d\nPressione 'F' para interagir", OrgDados[orgid][OrgNome], OrgDados[orgid][OrgMaterial], MATERIAL_INICIAL);
            OrgDados[orgid][ArsenalText] = CreateDynamic3DTextLabel(txt, OrgDados[orgid][OrgCor], OrgDados[orgid][ArsenalX], OrgDados[orgid][ArsenalY], OrgDados[orgid][ArsenalZ]+0.5, 15.0);
        } else {
            format(txt, sizeof(txt), "%s\nArsenal - Material: %d/%d\nPressione 'F' para interagir", OrgDados[orgid][OrgNome], OrgDados[orgid][OrgMaterial], MATERIAL_INICIAL);
            UpdateDynamic3DTextLabelText(OrgDados[orgid][ArsenalText], OrgDados[orgid][OrgCor], txt);
        }
    }
}

AdicionarMembro(orgid, const nome[], cargo)
{
    for(new i = 0; i < MAX_MEMBROS_ORG; i++) {
        if(!OrgMembros[orgid][i][MembroAtivo]) {
            strmid(OrgMembros[orgid][i][MembroNome], nome, 0, strlen(nome), MAX_PLAYER_NAME);
            OrgMembros[orgid][i][MembroCargo] = cargo;
            OrgMembros[orgid][i][MembroAtivo] = true;
            OrgMembrosCount[orgid]++;
            SaveOrgMembers(orgid);
            return i;
        }
    }
    return -1;
}

RemoverMembro(orgid, const nome[])
{
    for(new i = 0; i < MAX_MEMBROS_ORG; i++) {
        if(OrgMembros[orgid][i][MembroAtivo] && strcmp(OrgMembros[orgid][i][MembroNome], nome) == 0) {
            OrgMembros[orgid][i][MembroAtivo] = false;
            OrgMembrosCount[orgid]--;
            SaveOrgMembers(orgid);
            return 1;
        }
    }
    return 0;
}

AtualizarMembroOffline(orgid, const nome[], cargo)
{
    for(new i = 0; i < MAX_MEMBROS_ORG; i++) {
        if(OrgMembros[orgid][i][MembroAtivo] && strcmp(OrgMembros[orgid][i][MembroNome], nome) == 0) {
            OrgMembros[orgid][i][MembroCargo] = cargo;
            SaveOrgMembers(orgid);
            return 1;
        }
    }
    return 0;
}

RemoverPlayerWeapon(playerid, weaponid)
{
    new wData[13][2];
    for(new slot = 0; slot < 13; slot++) {
        GetPlayerWeaponData(playerid, slot, wData[slot][0], wData[slot][1]);
    }
    ResetPlayerWeapons(playerid);
    for(new slot = 0; slot < 13; slot++) {
        if(wData[slot][0] != weaponid && wData[slot][0] != 0) {
            GivePlayerWeapon(playerid, wData[slot][0], wData[slot][1]);
        }
    }
    return 1;
}

AtualizarSkinMembros(orgid)
{
    for(new i = 0; i < MAX_PLAYERS; i++) {
        if(IsPlayerConnected(i) && PlayerInfo[i][pOrgID] == orgid && PlayerInfo[i][pFardado] == true) {
            AtualizarFardaPlayer(i);
        }
    }
}

ResetarOrganizacao(orgid)
{
    // Primeiro: Limpa TODOS os jogadores online que pertencem a esta org
    for(new p = 0; p < MAX_PLAYERS; p++) {
        if(IsPlayerConnected(p) && PlayerInfo[p][pOrgID] == orgid) {
            // Salva o nome para debug
            new nome[MAX_PLAYER_NAME];
            GetPlayerName(p, nome, sizeof(nome));
            printf("[RESET] Removendo jogador online: %s (ID: %d)", nome, p);
            
            // Limpa as variáveis do jogador
            PlayerInfo[p][pOrgID] = -1;
            PlayerInfo[p][pOrgCargo] = 0;
            PlayerInfo[p][pFardado] = false;
            SalvarConta(p);
            SetPlayerSkin(p, PlayerInfo[p][pSkin]);
            SendClientMessage(p, -1, "A organização foi resetada! Você foi removido.");
        }
    }
    
    // Segundo: Limpa todos os membros offline nos arquivos
    for(new i = 0; i < MAX_MEMBROS_ORG; i++) {
        if(OrgMembros[orgid][i][MembroAtivo]) {
            new nome_membro[MAX_PLAYER_NAME];
            strmid(nome_membro, OrgMembros[orgid][i][MembroNome], 0, strlen(OrgMembros[orgid][i][MembroNome]), MAX_PLAYER_NAME);
            
            // Limpa arquivo do jogador offline
            new file_path[64];
            format(file_path, sizeof(file_path), "Contas/%s.ini", nome_membro);
            if(DOF2_FileExists(file_path)) {
                printf("[RESET] Removendo membro offline: %s", nome_membro);
                DOF2_SetInt(file_path, "OrgID", -1);
                DOF2_SetInt(file_path, "OrgCargo", 0);
                DOF2_SetBool(file_path, "Fardado", false);
                DOF2_SaveFile();
            }
            
            // Limpa da memória
            format(OrgMembros[orgid][i][MembroNome], MAX_PLAYER_NAME, "");
            OrgMembros[orgid][i][MembroAtivo] = false;
            OrgMembros[orgid][i][MembroCargo] = 0;
        }
    }
    OrgMembrosCount[orgid] = 0;
    
    // Terceiro: Reseta os dados da organização
    if(OrgDados[orgid][OrgTipo] == ORG_TIPO_CRIMINOSA) {
        OrgDados[orgid][OrgDinheiro] = 0;
        for(new s = 0; s < MAX_SLOTS_COFRE; s++) {
            OrgArmas[orgid][s][CofreArmaID] = 0;
            OrgArmas[orgid][s][CofreArmaMunicao] = 0;
        }
    }
    
    OrgDados[orgid][TemDono] = false;
    format(OrgDados[orgid][OrgDono], MAX_PLAYER_NAME, "Nenhum");
    OrgDados[orgid][CofreStatus] = false;
    OrgDados[orgid][ArsenalStatus] = false;
    
    if(OrgDados[orgid][OrgTipo] == ORG_TIPO_CORPORACAO) {
        OrgDados[orgid][OrgMaterial] = MATERIAL_INICIAL;
    }
    
    // Quarto: Salva a organização sem dono
    SalvarOrgs();
    
    // Quinto: Limpa o arquivo de membros
    new path[40];
    format(path, sizeof(path), "orgs/Membros_%d.ini", orgid);
    if(DOF2_FileExists(path)) {
        DOF2_RemoveFile(path);
        DOF2_CreateFile(path);
        DOF2_SetInt(path, "TotalMembros", 0);
        DOF2_SaveFile();
    }
    
    AtualizarTextLabelsOrg(orgid);
    
    // Mensagem para todos os membros (se ainda tiver algum)
    SendOrgMessage(orgid, -1, "A organização foi resetada por um administrador!");
    
    printf("[RESET] Organização %d resetada com sucesso!", orgid);
}

ExcluirOrganizacao(orgid)
{
    // Primeiro: Remove TODOS os jogadores online que pertencem a esta org
    for(new p = 0; p < MAX_PLAYERS; p++) {
        if(IsPlayerConnected(p) && PlayerInfo[p][pOrgID] == orgid) {
            new nome[MAX_PLAYER_NAME];
            GetPlayerName(p, nome, sizeof(nome));
            printf("[EXCLUIR] Removendo jogador online: %s (ID: %d)", nome, p);
            
            PlayerInfo[p][pOrgID] = -1;
            PlayerInfo[p][pOrgCargo] = 0;
            PlayerInfo[p][pFardado] = false;
            SalvarConta(p);
            SetPlayerSkin(p, PlayerInfo[p][pSkin]);
            SendClientMessage(p, -1, "A organização foi excluída! Você foi removido.");
        }
    }
    
    // Segundo: Remove todos os membros offline dos arquivos
    for(new i = 0; i < MAX_MEMBROS_ORG; i++) {
        if(OrgMembros[orgid][i][MembroAtivo]) {
            new nome_membro[MAX_PLAYER_NAME];
            strmid(nome_membro, OrgMembros[orgid][i][MembroNome], 0, strlen(OrgMembros[orgid][i][MembroNome]), MAX_PLAYER_NAME);
            
            new file_path[64];
            format(file_path, sizeof(file_path), "Contas/%s.ini", nome_membro);
            if(DOF2_FileExists(file_path)) {
                printf("[EXCLUIR] Removendo membro offline: %s", nome_membro);
                DOF2_SetInt(file_path, "OrgID", -1);
                DOF2_SetInt(file_path, "OrgCargo", 0);
                DOF2_SetBool(file_path, "Fardado", false);
                DOF2_SaveFile();
            }
        }
    }
    
    // Terceiro: Destroi pickups e textlabels
    if(OrgDados[orgid][CofreText] != Text3D:-1) DestroyDynamic3DTextLabel(OrgDados[orgid][CofreText]);
    if(OrgDados[orgid][ObjCofre] != 0) DestroyDynamicObject(OrgDados[orgid][ObjCofre]);
    if(OrgDados[orgid][VeicText] != Text3D:-1) DestroyDynamic3DTextLabel(OrgDados[orgid][VeicText]);
    if(OrgDados[orgid][PickupVeiculo] != 0) DestroyDynamicPickup(OrgDados[orgid][PickupVeiculo]);
    if(OrgDados[orgid][FardaText] != Text3D:-1) DestroyDynamic3DTextLabel(OrgDados[orgid][FardaText]);
    if(OrgDados[orgid][PickupFarda] != 0) DestroyDynamicPickup(OrgDados[orgid][PickupFarda]);
    if(OrgDados[orgid][ArsenalText] != Text3D:-1) DestroyDynamic3DTextLabel(OrgDados[orgid][ArsenalText]);
    if(OrgDados[orgid][PickupArsenal] != 0) DestroyDynamicPickup(OrgDados[orgid][PickupArsenal]);
    
    for(new v = 0; v < MAX_VEICULOS_ORG; v++) {
        if(OrgFrota[orgid][v][vID_Atual] != 0) DestroyVehicle(OrgFrota[orgid][v][vID_Atual]);
    }
    
    // Quarto: Remove arquivos da organização
    new path[40];
    format(path, sizeof(path), "orgs/Org_%d.ini", orgid);
    if(DOF2_FileExists(path)) DOF2_RemoveFile(path);
    format(path, sizeof(path), "orgs/Membros_%d.ini", orgid);
    if(DOF2_FileExists(path)) DOF2_RemoveFile(path);
    
    // Quinto: Marca como não criada
    OrgDados[orgid][OrgCriada] = false;
    
    printf("[EXCLUIR] Organização %d excluída com sucesso!", orgid);
}

main(){}

public OnGameModeInit()
{
    SetGameModeText("Projeto RP");
    ShowNameTags(0);
    ShowPlayerMarkers(0);
    AllowInteriorWeapons(1);
    ManualVehicleEngineAndLights();
    UsePlayerPedAnims();
    DisableInteriorEnterExits();
    
    for(new i = 0; i < MAX_ORGS; i++)
	{
	    OrgDados[i][CofreText] = Text3D:-1;
	    OrgDados[i][VeicText] = Text3D:-1;
	    OrgDados[i][FardaText] = Text3D:-1;
	    OrgDados[i][ArsenalText] = Text3D:-1;
	}
    CarregarOrgs();
    return 1;
}

public OnGameModeExit()
{
    for(new i = 0; i < MAX_PLAYERS; i++) {
        if(IsPlayerConnected(i)) {
            SalvarConta(i);
        }
    }
    SalvarOrgs();
    DOF2_Exit();
    return 1;
}

public OnPlayerRequestClass(playerid, classid)
{
	TogglePlayerSpectating(playerid, true);
	InterpolateCameraPos(playerid, -1324.3650, 493.5515, 113.5469, -1324.3650, 493.5515, 113.5469, 10000);
	InterpolateCameraLookAt(playerid, -1423.6027, 464.3260, 16.9766, -1423.6027, 464.3260, 16.9766, 10000);
    
    if(DOF2_FileExists(Arquivo(playerid)))
    {
        ShowPlayerDialog(playerid, DIALOG_LOGAR, DIALOG_STYLE_PASSWORD, "Login", "Digite sua senha para logar-se", "Confirmar", "Cancelar");
    } else {
        ShowPlayerDialog(playerid, DIALOG_REGISTRO, DIALOG_STYLE_INPUT, "Registro", "Digite uma senha para se registrar", "Confirmar", "Cancelar");
    }
	return 1;
}

public OnPlayerConnect(playerid)
{
	PreloadPlayerAnimations(playerid);
	PlayerInfo[playerid][pLogado] = false;
    return 1;
}

public OnPlayerDisconnect(playerid, reason)
{
	if(PlayerInfo[playerid][pLogado] == true) {
        SalvarConta(playerid);
        PlayerInfo[playerid][pLogado] = false;
    }
    if(PlayerVeiculoID[playerid] != 0) {
        for(new o = 0; o < MAX_ORGS; o++) {
            if(OrgDados[o][OrgCriada]) {
                for(new v = 0; v < MAX_VEICULOS_ORG; v++) {
                    if(OrgFrota[o][v][vID_Atual] == PlayerVeiculoID[playerid]) {
                        DestroyVehicle(PlayerVeiculoID[playerid]);
                        OrgFrota[o][v][vID_Atual] = 0;
                        OrgFrota[o][v][vSpawnado] = false;
                        break;
                    }
                }
            }
        }
        PlayerVeiculoID[playerid] = 0;
    }
    if(PlayerInfo[playerid][pOrgID] != -1) {
        AtualizarMembroOffline(PlayerInfo[playerid][pOrgID], pName(playerid), PlayerInfo[playerid][pOrgCargo]);
    }
    return 1;
}

public OnPlayerSpawn(playerid)
{
    return 1;
}

public OnPlayerRequestSpawn(playerid)
{
    return 0;
}

public OnPlayerKeyStateChange(playerid, newkeys, oldkeys)
{
    if((newkeys & KEY_SECONDARY_ATTACK) && !(oldkeys & KEY_SECONDARY_ATTACK))
    {
        new org = PlayerInfo[playerid][pOrgID];
        if(org != -1 && OrgDados[org][OrgCriada]) {
            
            if(OrgDados[org][OrgTipo] == ORG_TIPO_CRIMINOSA && IsPlayerInRangeOfPoint(playerid, 2.0, OrgDados[org][CofreX], OrgDados[org][CofreY], OrgDados[org][CofreZ])) {
                if(CanAccessCofre(playerid)) {
                    MenuOrgSelecionada[playerid] = org;
                    ShowPlayerDialog(playerid, DIALOG_ORG_COFRE_MENU, DIALOG_STYLE_LIST, "Cofre da Organização", "1. Depositar Dinheiro\n2. Sacar Dinheiro\n3. Itens da Família", "Selecionar", "Fechar");
                } else {
                    SendClientMessage(playerid, -1, "O cofre está FECHADO! Apenas Líder e Sub-Líder podem acessar.");
                }
                return 1;
            }
            
            if(OrgDados[org][OrgTipo] == ORG_TIPO_CORPORACAO && IsPlayerInRangeOfPoint(playerid, 2.0, OrgDados[org][ArsenalX], OrgDados[org][ArsenalY], OrgDados[org][ArsenalZ])) {
                if(CanAccessArsenal(playerid)) {
                    MenuOrgSelecionada[playerid] = org;
                    new str[500];
                    strcat(str, "M4 (300 materiais / 100 balas)\n");
                    strcat(str, "MP5 (250 materiais / 100 balas)\n");
                    strcat(str, "Dose (150 materiais / 50 balas)\n");
                    strcat(str, "Desert Eagle (100 materiais / 100 balas)\n");
                    strcat(str, "Taser (80 materiais / 50 balas)\n");
                    strcat(str, "Colete (50 materiais)");
                    ShowPlayerDialog(playerid, DIALOG_ORG_ARSENAL_ITENS, DIALOG_STYLE_LIST, "Arsenal", str, "Equipar", "Fechar");
                } else {
                    SendClientMessage(playerid, -1, "O arsenal está FECHADO! Apenas Líder e Sub-Líder podem acessar.");
                }
                return 1;
            }
                        
			if(OrgDados[org][OrgTipo] == ORG_TIPO_CORPORACAO && IsPlayerInRangeOfPoint(playerid, 2.0, OrgDados[org][FardaX], OrgDados[org][FardaY], OrgDados[org][FardaZ])) {
			    ShowPlayerDialog(playerid, DIALOG_ORG_FARDA_MENU, DIALOG_STYLE_LIST, "Farda da Organização", "1. Vestir Farda\n2. Retirar Farda", "Selecionar", "Fechar");
			    return 1;
			}

            if(IsPlayerInRangeOfPoint(playerid, 3.0, OrgDados[org][VeicPickupX], OrgDados[org][VeicPickupY], OrgDados[org][VeicPickupZ])) {
			    MenuOrgSelecionada[playerid] = org;
			    new lista[800], item[100];
			    for(new v = 0; v < MAX_VEICULOS_ORG; v++) {
			        if(OrgFrota[org][v][vModelo] != 0) {
			            new nomeveic[32];
			            GetVehicleModelName(OrgFrota[org][v][vModelo], nomeveic, 32);
			            format(item, sizeof(item), "%s (%d)\n", nomeveic, OrgFrota[org][v][vModelo]);
			        } else {
			            format(item, sizeof(item), "Vazio\n");
			        }
			        strcat(lista, item);
			    }
			    strcat(lista, "Guardar Veículo\n");
			    ShowPlayerDialog(playerid, DIALOG_ORG_VEICULO, DIALOG_STYLE_LIST, "Garagem da Organização", lista, "Selecionar", "Fechar");
			    return 1;
			}
		}
	}
    return 1;
}

public OnDialogResponse(playerid, dialogid, response, listitem, inputtext[])
{
	if(dialogid == DIALOG_REGISTRO)
    {
        if(!response) return Kick(playerid);
        
        if(strlen(inputtext) < 4 || strlen(inputtext) < 10) {
            ShowPlayerDialog(playerid, DIALOG_REGISTRO, DIALOG_STYLE_INPUT, "Registro", "Senha deve ter no mínimo 4 a 10 caracteres", "Confirmar", "Cancelar");
            return 1;
        }
        DOF2_CreateFile(Arquivo(playerid));        
        DOF2_SetString(Arquivo(playerid), "Senha", inputtext);        
        DOF2_SetInt(Arquivo(playerid), "Admin", 0);
        DOF2_SetInt(Arquivo(playerid), "Dinheiro", 1000);
        DOF2_SetInt(Arquivo(playerid), "Skin", 8);
        DOF2_SetFloat(Arquivo(playerid), "PosX", 1685.7456);
        DOF2_SetFloat(Arquivo(playerid), "PosY", -13.5469);
        DOF2_SetFloat(Arquivo(playerid), "PosZ", 27.6875);
        DOF2_SetFloat(Arquivo(playerid), "PosR", 357.6325);
        DOF2_SetInt(Arquivo(playerid), "Interior", 0);
        DOF2_SetInt(Arquivo(playerid), "VirtualWorld", 0);
        DOF2_SetFloat(Arquivo(playerid), "Vida", 100.0);
        DOF2_SetFloat(Arquivo(playerid), "Armour", 0.0);
        DOF2_SetInt(Arquivo(playerid), "OrgID", -1);
        DOF2_SetInt(Arquivo(playerid), "OrgCargo", 0);
        DOF2_SetBool(Arquivo(playerid), "Fardado", false);
        DOF2_SaveFile();
        
        ShowPlayerDialog(playerid, DIALOG_LOGAR, DIALOG_STYLE_PASSWORD, "Login", "Digite sua senha para logar-se", "Confirmar", "Cancelar");
        SendClientMessage(playerid, -1, "{00FF00}Conta criada com sucesso! Bem-vindo ao servidor!");
        return 1;
    }
    if(dialogid == DIALOG_LOGAR)
    {
        if(!response) return Kick(playerid);
        
        if(strlen(inputtext) <= 0) return ShowPlayerDialog(playerid, DIALOG_LOGAR, DIALOG_STYLE_PASSWORD, "Login", "Peencha o campo! Tente novamente", "Confirmar", "Cancelar");
        
        if(strcmp(inputtext, DOF2_GetString(Arquivo(playerid), "Senha")) == 0) {       
	        CarregarConta(playerid);        
	        SendClientMessage(playerid, -1, "{00FF00}Login realizado com sucesso! Bem-vindo de volta!");
	    } else {
			ShowPlayerDialog(playerid, DIALOG_LOGAR, DIALOG_STYLE_PASSWORD, "Login", "Senha incorreta! Tente novamente", "Confirmar", "Cancelar");
		}
        return 1;
    }
    if(dialogid == DIALOG_ORG_MAIN && response)
    {
        switch(listitem)
        {
            case 0: 
            {
                ShowPlayerDialog(playerid, DIALOG_ORG_TIPO, DIALOG_STYLE_LIST, "Tipo de Organização", "1. Organização Criminosa\n2. Corporação", "Selecionar", "Voltar");
            }
            case 1: 
            {
                new encontrou = 0;
                new lista[2000], item[200];
                strcat(lista, "Nome\tLíder\tStatus\n");
                for(new i = 0; i < MAX_ORGS; i++) {
                    if(OrgDados[i][OrgCriada]) {
                        encontrou = 1;
                        if(OrgDados[i][TemDono]) {
                            format(item, sizeof(item), "%s\t%s\tOcupada\n", OrgDados[i][OrgNome], OrgDados[i][OrgDono]);
                        } else {
                            format(item, sizeof(item), "%s\tNenhum\tDesocupada\n", OrgDados[i][OrgNome]);
                        }
                        strcat(lista, item);
                    }
                }
                if(!encontrou) {
                    SendClientMessage(playerid, -1, "Nenhuma organização criada ainda!");
                    return 1;
                }
                ShowPlayerDialog(playerid, DIALOG_ORG_LISTA, DIALOG_STYLE_TABLIST_HEADERS, "Organizações Criadas", lista, "Gerenciar", "Voltar");
                return 1;
            }
            case 2:
            {
                ShowPlayerDialog(playerid, DIALOG_ORG_SETAR_ID, DIALOG_STYLE_INPUT, "Setar Líder", "Digite o ID do Jogador conectado:", "Próximo", "Voltar");
            }
        }
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_TIPO && response)
    {
        MenuCargoSelecionado[playerid] = listitem;
        new listaCores[300], item[40];
        for(new i = 0; i < sizeof(CoresOrg); i++) {
            format(item, sizeof(item), "%s\n", CoresOrg[i][1]);
            strcat(listaCores, item);
        }
        ShowPlayerDialog(playerid, DIALOG_ORG_ESCOLHER_COR, DIALOG_STYLE_LIST, "Escolha a Cor da Organização", listaCores, "Selecionar", "Voltar");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_ESCOLHER_COR && response)
    {
        new tipo = MenuCargoSelecionado[playerid];
        new cor = listitem;
        ShowPlayerDialog(playerid, DIALOG_ORG_CRIAR, DIALOG_STYLE_INPUT, "Criar Organização", "Digite o nome da nova Organização:", "Criar", "Voltar");
        MenuCargoSelecionado[playerid] = tipo;
        MenuSlotSelecionado[playerid] = cor;
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_CRIAR && response)
    {
        if(strlen(inputtext) < 4) return SendClientMessage(playerid, -1, "Nome muito curto!");
        
        new slot = -1;
        for(new i = 0; i < MAX_ORGS; i++) {
            if(!OrgDados[i][OrgCriada]) { slot = i; break; }
        }
        if(slot == -1) return SendClientMessage(playerid, -1, "Limite máximo de organizações atingido!");
        
        new tipo = MenuCargoSelecionado[playerid];
        new cor = MenuSlotSelecionado[playerid];
        
        OrgDados[slot][OrgCriada] = true;
        format(OrgDados[slot][OrgNome], 50, "%s", inputtext);
        OrgDados[slot][OrgTipo] = tipo;
        OrgDados[slot][OrgCor] = CoresOrg[cor][0];
        format(OrgDados[slot][OrgDono], MAX_PLAYER_NAME, "Nenhum");
        OrgDados[slot][TemDono] = false;
        OrgDados[slot][OrgDinheiro] = 0;
        OrgDados[slot][OrgMaterial] = MATERIAL_INICIAL;
        OrgDados[slot][CofreStatus] = false;
        OrgDados[slot][ArsenalStatus] = false;
        OrgDados[slot][SkinOrg] = 0;
        
        SalvarOrgs();
        SendClientMessage(playerid, -1, "Organização criada! Use /criarorg > Lista para gerenciar.");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_LISTA && response)
    {
        new encontrados = 0, id_real = -1;
        for(new i = 0; i < MAX_ORGS; i++) {
            if(OrgDados[i][OrgCriada]) {
                if(encontrados == listitem) { id_real = i; break; }
                encontrados++;
            }
        }
        if(id_real == -1 || !OrgDados[id_real][OrgCriada]) return SendClientMessage(playerid, -1, "Organização não encontrada!");
        
        MenuOrgSelecionada[playerid] = id_real;
        
        new str[1500], cabecalho[64];
        format(cabecalho, sizeof(cabecalho), "Config: %s", OrgDados[id_real][OrgNome]);
        
        if(OrgDados[id_real][OrgTipo] == ORG_TIPO_CRIMINOSA) {
            strcat(str, "1. Definir Posição do Cofre\n");
            strcat(str, "3. Definir Local da Garagem\n");
            if(OrgDados[id_real][CofreStatus] == false) strcat(str, "FECHAR Cofre\n");
            else strcat(str, "ABRIR Cofre\n");
            strcat(str, "7. {FF0000}Resetar Organização\n");
            strcat(str, "8. {FF0000}Excluir Organização");
        } else {
            strcat(str, "1. Definir Posição do Arsenal\n");
            strcat(str, "3. Definir Local da Garagem\n");
            strcat(str, "4. Definir Local da Farda\n");
            strcat(str, "5. Recarregar Material (Resetar para 100k)\n");
            if(OrgDados[id_real][ArsenalStatus] == false) strcat(str, "FECHAR Arsenal\n");
            else strcat(str, "ABRIR Arsenal\n");
            strcat(str, "7. {FF0000}Resetar Corporação\n");
            strcat(str, "8. {FF0000}Excluir Corporação");
        }
        
        ShowPlayerDialog(playerid, DIALOG_ORG_GERENCIAR, DIALOG_STYLE_LIST, cabecalho, str, "Selecionar", "Voltar");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_GERENCIAR && response)
	{
	    new org = MenuOrgSelecionada[playerid];
	    new Float:x, Float:y, Float:z;
	    GetPlayerPos(playerid, x, y, z);
	    
	    if(OrgDados[org][OrgTipo] == ORG_TIPO_CRIMINOSA) {
	        switch(listitem) {
	            case 0: // Cofre
	            {
	                OrgDados[org][CofreX] = x; OrgDados[org][CofreY] = y; OrgDados[org][CofreZ] = z;
	                if(OrgDados[org][ObjCofre] != 0) DestroyDynamicObject(OrgDados[org][ObjCofre]);
	                OrgDados[org][ObjCofre] = CreateDynamicObject(2332, x, y, z, 0, 0, 0);
	                AtualizarTextLabelsOrg(org);
	                SalvarOrgs();
	                SendClientMessage(playerid, -1, "Posicao do cofre definida!");
	            }
	            case 1: // Veículos
	            {
	                new lista[400], item[100];
	                strcat(lista, "Slot\t\tVeiculo\n");
	                for(new v = 0; v < MAX_VEICULOS_ORG; v++) {
	                    if(OrgFrota[org][v][vModelo] == 0) {
	                        format(item, sizeof(item), "Slot %d\t\t{FF0000}Nao Configurado\n", v+1);
	                    } else {
	                        new nomeveic[32];
	                        GetVehicleModelName(OrgFrota[org][v][vModelo], nomeveic, 32);
	                        format(item, sizeof(item), "Slot %d\t\t{00FF00}%s\n", v+1, nomeveic);
	                    }
	                    strcat(lista, item);
	                }
	                ShowPlayerDialog(playerid, DIALOG_ORG_SELECIONAR_SLOT, DIALOG_STYLE_TABLIST_HEADERS, "Frota da Organizacao", lista, "Selecionar", "Voltar");
	                MenuOrgSelecionada[playerid] = org;
	                return 1;
	            }
	            case 2: // Garagem
	            {
	                OrgDados[org][VeicPickupX] = x; OrgDados[org][VeicPickupY] = y; OrgDados[org][VeicPickupZ] = z;
	                if(OrgDados[org][PickupVeiculo] != 0) DestroyDynamicPickup(OrgDados[org][PickupVeiculo]);
	                OrgDados[org][PickupVeiculo] = CreateDynamicPickup(19134, 1, x, y, z, -1);
	                AtualizarTextLabelsOrg(org);
	                SalvarOrgs();
	                SendClientMessage(playerid, -1, "Local da garagem definido!");
	            }
	            case 5: // Abrir/Fechar Cofre
	            {
	                if(OrgDados[org][CofreStatus] == false) OrgDados[org][CofreStatus] = true;
	                else OrgDados[org][CofreStatus] = false;
	                AtualizarTextLabelsOrg(org);
	                SalvarOrgs();
	                SendClientMessage(playerid, -1, OrgDados[org][CofreStatus] == false ? "Cofre ABERTO!" : "Cofre FECHADO!");
	            }
	            case 6: // Resetar
				{
				    if(!OrgDados[org][TemDono]) {
				        SendClientMessage(playerid, -1, "Esta organizacao ja nao tem lider! Nao ha o que resetar.");
				        return 1;
				    }
				    ShowPlayerDialog(playerid, DIALOG_ORG_CONFIRMAR_RESET, DIALOG_STYLE_MSGBOX, "Resetar Organizacao", 
				        "ATENCAO! Isso ira:\n- Remover TODOS os membros\n- Demitir o lider atual\n- ZERAR o dinheiro do cofre\n- A organizacao ficara sem dono\n\nDeseja continuar?", "Sim", "Nao");
				    MenuOrgSelecionada[playerid] = org;
				}
				case 7: // Excluir
				{
				    ShowPlayerDialog(playerid, DIALOG_ORG_CONFIRMAR_EXCLUIR, DIALOG_STYLE_MSGBOX, "Excluir Organizacao", 
				        "ATENCAO! Isso ira DELETAR PERMANENTEMENTE a organizacao e todos os dados!\n\nDeseja continuar?", "Sim", "Nao");
				    MenuOrgSelecionada[playerid] = org;
				}
	        }
	    } else {
	        switch(listitem) {
	            case 0: // Arsenal
	            {
	                OrgDados[org][ArsenalX] = x; OrgDados[org][ArsenalY] = y; OrgDados[org][ArsenalZ] = z;
	                if(OrgDados[org][PickupArsenal] != 0) DestroyDynamicPickup(OrgDados[org][PickupArsenal]);
	                OrgDados[org][PickupArsenal] = CreateDynamicPickup(2061, 1, x, y, z, -1);
	                AtualizarTextLabelsOrg(org);
	                SalvarOrgs();
	                SendClientMessage(playerid, -1, "Posicao do arsenal definida!");
	            }
	            case 1: // Veículos (Corporação)
	            {
	                new lista[400], item[100];
	                strcat(lista, "Slot\t\tVeiculo\n");
	                for(new v = 0; v < MAX_VEICULOS_ORG; v++) {
	                    if(OrgFrota[org][v][vModelo] == 0) {
	                        format(item, sizeof(item), "Slot %d\t\t{FF0000}Nao Configurado\n", v+1);
	                    } else {
	                        new nomeveic[32];
	                        GetVehicleModelName(OrgFrota[org][v][vModelo], nomeveic, 32);
	                        format(item, sizeof(item), "Slot %d\t\t{00FF00}%s\n", v+1, nomeveic);
	                    }
	                    strcat(lista, item);
	                }
	                ShowPlayerDialog(playerid, DIALOG_ORG_SELECIONAR_SLOT, DIALOG_STYLE_TABLIST_HEADERS, "Frota da Organizacao", lista, "Selecionar", "Voltar");
	                MenuOrgSelecionada[playerid] = org;
	                return 1;
	            }
	            case 2: // Garagem
	            {
	                OrgDados[org][VeicPickupX] = x; OrgDados[org][VeicPickupY] = y; OrgDados[org][VeicPickupZ] = z;
	                if(OrgDados[org][PickupVeiculo] != 0) DestroyDynamicPickup(OrgDados[org][PickupVeiculo]);
	                OrgDados[org][PickupVeiculo] = CreateDynamicPickup(19134, 1, x, y, z, -1);
	                AtualizarTextLabelsOrg(org);
	                SalvarOrgs();
	                SendClientMessage(playerid, -1, "Local da garagem definido!");
	            }
	            case 3: // Farda
	            {
	                OrgDados[org][FardaX] = x; OrgDados[org][FardaY] = y; OrgDados[org][FardaZ] = z;
	                if(OrgDados[org][PickupFarda] != 0) DestroyDynamicPickup(OrgDados[org][PickupFarda]);
	                OrgDados[org][PickupFarda] = CreateDynamicPickup(1275, 1, x, y, z, -1);
	                AtualizarTextLabelsOrg(org);
	                SalvarOrgs();
	                SendClientMessage(playerid, -1, "Local da farda definido!");
	            }
	            case 4: // Recarregar Material
	            {
	                OrgDados[org][OrgMaterial] = MATERIAL_INICIAL;
	                AtualizarTextLabelsOrg(org);
	                SalvarOrgs();
	                SendClientMessage(playerid, -1, "Material recarregado! Estoque voltou para 100.000.");
	            }
	            case 5: // Abrir/Fechar Arsenal
	            {
	                if(OrgDados[org][ArsenalStatus] == false) OrgDados[org][ArsenalStatus] = true;
	                else OrgDados[org][ArsenalStatus] = false;
	                AtualizarTextLabelsOrg(org);
	                SalvarOrgs();
	                SendClientMessage(playerid, -1, OrgDados[org][ArsenalStatus] == false ? "Arsenal ABERTO!" : "Arsenal FECHADO!");
	            }
	            case 6: // Resetar Corporação
				{
				    if(!OrgDados[org][TemDono]) {
				        SendClientMessage(playerid, -1, "Esta corporacao ja nao tem lider! Nao ha o que resetar.");
				        return 1;
				    }
				    ShowPlayerDialog(playerid, DIALOG_ORG_CONFIRMAR_RESET, DIALOG_STYLE_MSGBOX, "Resetar Corporacao", 
				        "ATENCAO! Isso ira:\n- Remover TODOS os membros\n- Demitir o lider atual\n- A corporacao ficara sem dono\n\nDeseja continuar?", "Sim", "Nao");
				    MenuOrgSelecionada[playerid] = org;
				}
				case 7: // Excluir Corporação
				{
				    ShowPlayerDialog(playerid, DIALOG_ORG_CONFIRMAR_EXCLUIR, DIALOG_STYLE_MSGBOX, "Excluir Corporacao", 
				        "ATENCAO! Isso ira DELETAR PERMANENTEMENTE a corporacao e todos os dados!\n\nDeseja continuar?", "Sim", "Nao");
				    MenuOrgSelecionada[playerid] = org;
				}
	        }
	    }
	    return 1;
    }
    
    if(dialogid == DIALOG_ORG_SELECIONAR_SLOT && response)
	{
	    MenuSlotSelecionado[playerid] = listitem;
	    
	    new str[200];
	    format(str, sizeof(str), "Digite o ID do Modelo do veiculo para o Slot %d\n(Ex: 411 para Infernus):", listitem+1);
	    
	    ShowPlayerDialog(playerid, DIALOG_ORG_FROTA_MOD, DIALOG_STYLE_INPUT, "Configurar Veiculo", str, "Salvar", "Voltar");
	    return 1;
	}

    if(dialogid == DIALOG_ORG_SKIN && response)
    {
        new org = MenuOrgSelecionada[playerid];
        new skin = strval(inputtext);
        OrgDados[org][SkinOrg] = skin;
        AtualizarSkinMembros(org);
        SalvarOrgs();
        SendClientMessage(playerid, -1, skin > 0 ? "Skin da organização configurada!" : "Skin removida! Membros usarão skin padrão.");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_CONFIRMAR_RESET && response)
    {
        new org = MenuOrgSelecionada[playerid];
        ResetarOrganizacao(org);
        SendClientMessage(playerid, -1, "Organização resetada com sucesso!");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_CONFIRMAR_EXCLUIR && response)
    {
        new org = MenuOrgSelecionada[playerid];
        ExcluirOrganizacao(org);
        SendClientMessage(playerid, -1, "Organização excluída permanentemente!");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_FROTA_MENU && response)
    {
        MenuSlotSelecionado[playerid] = listitem;
        ShowPlayerDialog(playerid, DIALOG_ORG_FROTA_MOD, DIALOG_STYLE_INPUT, "Configurar Veículo", "Digite o ID do Modelo do veículo (Ex: 411 para Infernus):", "Salvar", "Voltar");
        return 1;
    }
    
	if(dialogid == DIALOG_ORG_FROTA_MOD && response)
	{
	    new modelo = strval(inputtext);
	    if(modelo < 400 || modelo > 611) return SendClientMessage(playerid, -1, "Modelo inválido de veículo!");
	    
	    new org = MenuOrgSelecionada[playerid];
	    new slot = MenuSlotSelecionado[playerid];
	    new Float:x, Float:y, Float:z, Float:a;
	    GetPlayerPos(playerid, x, y, z);
	    GetPlayerFacingAngle(playerid, a);
	    
	    OrgFrota[org][slot][vModelo] = modelo;
	    OrgFrota[org][slot][vX] = x + (2.0 * floatsin(-a, degrees));
	    OrgFrota[org][slot][vY] = y + (2.0 * floatcos(-a, degrees));
	    OrgFrota[org][slot][vZ] = z;
	    OrgFrota[org][slot][vA] = a;
	    OrgFrota[org][slot][vSpawnado] = false;
	    GetVehicleModelName(modelo, OrgFrota[org][slot][NomeModelo], 32);
	    
	    if(OrgFrota[org][slot][vID_Atual] != 0) DestroyVehicle(OrgFrota[org][slot][vID_Atual]);
	    OrgFrota[org][slot][vID_Atual] = 0;
	    
	    SalvarOrgs();
	    SendClientMessage(playerid, -1, "Veículo configurado! Use o pickup da garagem para spawnar.");
	    return 1;
	}
    
    if(dialogid == DIALOG_ORG_VEICULO && response)
	{
	    new org = MenuOrgSelecionada[playerid];
	    
	    if(PlayerVeiculoID[playerid] != 0 && listitem != 5) {
	        SendClientMessage(playerid, -1, "Você já tem um veículo ativo! Use a opção 'Guardar Veículo' primeiro.");
	        return 1;
	    }
	    
	    if(listitem == 5) { 
	        if(PlayerVeiculoID[playerid] == 0) {
	            SendClientMessage(playerid, -1, "Você não tem nenhum veículo para guardar!");
	            return 1;
	        }
	        
	        for(new v = 0; v < MAX_VEICULOS_ORG; v++) {
	            if(OrgFrota[org][v][vID_Atual] == PlayerVeiculoID[playerid]) {
	                DestroyVehicle(PlayerVeiculoID[playerid]);
	                OrgFrota[org][v][vID_Atual] = 0;
	                OrgFrota[org][v][vSpawnado] = false;
	                PlayerVeiculoID[playerid] = 0;
	                SendClientMessage(playerid, -1, "Veículo guardado na garagem!");
	                LogOrgAction(org, playerid, "Guardou o veículo");
	                break;
	            }
	        }
	        return 1;
	    }
	    
	    if(OrgFrota[org][listitem][vModelo] == 0) return SendClientMessage(playerid, -1, "Este slot está vazio!");
	    
	    new veiculo = CreateVehicle(OrgFrota[org][listitem][vModelo], OrgFrota[org][listitem][vX], OrgFrota[org][listitem][vY], OrgFrota[org][listitem][vZ], OrgFrota[org][listitem][vA], -1, -1, -1);
	    
	    OrgFrota[org][listitem][vID_Atual] = veiculo;
	    OrgFrota[org][listitem][vSpawnado] = true;
	    PlayerVeiculoID[playerid] = veiculo;
	    
	    PutPlayerInVehicle(playerid, veiculo, 0);
	    SendClientMessage(playerid, -1, "Veículo spawnado! Use a opção 'Guardar Veículo' antes de pegar outro.");
	    LogOrgAction(org, playerid, "Spawnou um veículo");
	    return 1;
	}
    
    if(dialogid == DIALOG_ORG_FARDA_MENU && response)
    {
        if(listitem == 0)
        {
        	if(PlayerInfo[playerid][pFardado] == true) return SendClientMessage(playerid, -1, "Voce ja esta fardado!");
            PlayerInfo[playerid][pSkin] = GetPlayerSkin(playerid);
            PlayerInfo[playerid][pFardado] = true;
            AtualizarFardaPlayer(playerid);
            SalvarConta(playerid);
        } 
        if(listitem == 1) 
        {
        	if(PlayerInfo[playerid][pFardado] == false) return SendClientMessage(playerid, -1, "Voce nao esta fardado!");
            SetPlayerSkin(playerid, PlayerInfo[playerid][pSkin]);
            PlayerInfo[playerid][pFardado] = false;
            SalvarConta(playerid);
        }
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_COFRE_MENU && response)
    {
        new org = MenuOrgSelecionada[playerid];
        switch(listitem) 
        {
            case 0:
            {
                ShowPlayerDialog(playerid, DIALOG_ORG_COFRE_DEPOSITAR, DIALOG_STYLE_INPUT, "Depositar Dinheiro", "Digite o valor que deseja depositar no cofre:", "Depositar", "Voltar");
            }
            case 1:
            {
                new str[100];
                format(str, sizeof(str), "Saldo do Cofre: R$ %d\nDigite o valor que deseja sacar:", OrgDados[org][OrgDinheiro]);
                ShowPlayerDialog(playerid, DIALOG_ORG_COFRE_RETIRAR, DIALOG_STYLE_INPUT, "Sacar Dinheiro", str, "Sacar", "Voltar");
            }
            case 2:
            {
                new lista[1500], item[100];
                strcat(lista, "Slot\tArma\tBalas\n");
                for(new i = 0; i < MAX_SLOTS_COFRE; i++) {
                    if(OrgArmas[org][i][CofreArmaID] == 0) format(item, sizeof(item), "%d\tLivre\t-\n", i+1);
                    else format(item, sizeof(item), "%d\tArma %d\t%d\n", i+1, OrgArmas[org][i][CofreArmaID], OrgArmas[org][i][CofreArmaMunicao]);
                    strcat(lista, item);
                }
                ShowPlayerDialog(playerid, DIALOG_ORG_COFRE_ARMAS, DIALOG_STYLE_TABLIST_HEADERS, "Itens da Família", lista, "Selecionar", "Fechar");
            }
        }
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_COFRE_DEPOSITAR && response)
    {
        new org = MenuOrgSelecionada[playerid];
        new valor = strval(inputtext);
        if(valor <= 0) return SendClientMessage(playerid, -1, "Valor inválido!");
        if(GetPlayerMoney(playerid) < valor) return SendClientMessage(playerid, -1, "Você não tem esse dinheiro!");
        
        GivePlayerMoney(playerid, -valor);
        OrgDados[org][OrgDinheiro] += valor;
        SalvarOrgs();
        
        new msg[100];
        format(msg, sizeof(msg), "Você depositou R$ %d no cofre da organização!", valor);
        SendClientMessage(playerid, -1, msg);
        LogOrgAction(org, playerid, msg);
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_COFRE_RETIRAR && response)
    {
        new org = MenuOrgSelecionada[playerid];
        new valor = strval(inputtext);
        if(valor <= 0) return SendClientMessage(playerid, -1, "Valor inválido!");
        if(OrgDados[org][OrgDinheiro] < valor) return SendClientMessage(playerid, -1, "Saldo insuficiente no cofre!");
        
        OrgDados[org][OrgDinheiro] -= valor;
        GivePlayerMoney(playerid, valor);
        SalvarOrgs();
        
        new msg[100];
        format(msg, sizeof(msg), "Você sacou R$ %d do cofre da organização!", valor);
        SendClientMessage(playerid, -1, msg);
        LogOrgAction(org, playerid, msg);
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_COFRE_ARMAS && response)
    {
        MenuSlotSelecionado[playerid] = listitem;
        new org = MenuOrgSelecionada[playerid];
        if(OrgArmas[org][listitem][CofreArmaID] == 0) {
            ShowPlayerDialog(playerid, DIALOG_ORG_COFRE_DEP_A, DIALOG_STYLE_MSGBOX, "Slot Vazio", "Deseja DEPOSITAR a arma que você está segurando neste slot?", "Depositar", "Voltar");
        } else {
            ShowPlayerDialog(playerid, DIALOG_ORG_COFRE_RET_A, DIALOG_STYLE_MSGBOX, "Slot Ocupado", "Deseja RETIRAR esta arma do cofre?", "Retirar", "Voltar");
        }
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_COFRE_DEP_A && response)
    {
        new org = MenuOrgSelecionada[playerid];
        new slot = MenuSlotSelecionado[playerid];
        new arma = GetPlayerWeapon(playerid);
        new balas = GetPlayerAmmo(playerid);
        
        if(arma == 0 || balas <= 0) return SendClientMessage(playerid, -1, "Você não está segurando nenhuma arma válida!");
        if(OrgArmas[org][slot][CofreArmaID] != 0) return SendClientMessage(playerid, -1, "Este slot já está ocupado!");
        
        OrgArmas[org][slot][CofreArmaID] = arma;
        OrgArmas[org][slot][CofreArmaMunicao] = balas;
        RemoverPlayerWeapon(playerid, arma);
        
        new msg[128];
        format(msg, sizeof(msg), "Depositou arma ID %d (%d balas) no slot %d", arma, balas, slot+1);
        LogOrgAction(org, playerid, msg);
        SalvarOrgs();
        SendClientMessage(playerid, -1, "Arma depositada!");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_COFRE_RET_A && response)
    {
        new org = MenuOrgSelecionada[playerid];
        new slot = MenuSlotSelecionado[playerid];
        
        GivePlayerWeapon(playerid, OrgArmas[org][slot][CofreArmaID], OrgArmas[org][slot][CofreArmaMunicao]);
        
        new msg[128];
        format(msg, sizeof(msg), "Retirou arma ID %d (%d balas) do slot %d", OrgArmas[org][slot][CofreArmaID], OrgArmas[org][slot][CofreArmaMunicao], slot+1);
        LogOrgAction(org, playerid, msg);
        
        OrgArmas[org][slot][CofreArmaID] = 0;
        OrgArmas[org][slot][CofreArmaMunicao] = 0;
        SalvarOrgs();
        SendClientMessage(playerid, -1, "Arma retirada!");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_ARSENAL_ITENS && response)
    {
        new org = MenuOrgSelecionada[playerid];
        new custos[6] = {300, 250, 150, 100, 80, 50};
        new armas[6] = {31, 29, 25, 24, 23, 0};
        new balas[6] = {100, 100, 50, 100, 50, 1};
        new nomes[6][20] = {"M4", "MP5", "Dose", "Desert Eagle", "Taser", "Colete"};
        
        if(OrgDados[org][OrgMaterial] < custos[listitem]) {
            SendClientMessage(playerid, -1, "Material insuficiente no arsenal!");
            return 1;
        }
        
        if(listitem == 0 || listitem == 1 || listitem == 2) {
            if(PlayerInfo[playerid][pOrgCargo] < 2) {
                SendClientMessage(playerid, -1, "Seu cargo não tem permissão para pegar esta arma!");
                return 1;
            }
        }
        
        if(listitem == 5) {
        	SetPlayerArmour(playerid, 100.0);
            SendClientMessage(playerid, -1, "Voce pegou um colete a prova de balas!");
            return 1;
        }
        
        OrgDados[org][OrgMaterial] -= custos[listitem];
        GivePlayerWeapon(playerid, armas[listitem], balas[listitem]);
        
        AtualizarTextLabelsOrg(org);
        SalvarOrgs();
        
        new msg[128];
        format(msg, sizeof(msg), "Pegou %s do arsenal (material restante: %d)", nomes[listitem], OrgDados[org][OrgMaterial]);
        LogOrgAction(org, playerid, msg);
        SendClientMessage(playerid, -1, msg);
        return 1;
    }
   
    if(dialogid == DIALOG_ORG_MENU && response)
    {
        new org = MenuOrgSelecionada[playerid];
        switch(listitem) {
            case 0: 
            {
                new lista[3000], item[300];
                strcat(lista, "Nome\tCargo\tStatus\n");
                
                for(new cargo = 4; cargo >= 0; cargo--) {
                    for(new m = 0; m < MAX_MEMBROS_ORG; m++) {
                        if(OrgMembros[org][m][MembroAtivo] && OrgMembros[org][m][MembroCargo] == cargo) {
                            new online = 0;
                            for(new p = 0; p < MAX_PLAYERS; p++) {
                                if(IsPlayerConnected(p)) {
                                    new nomep[MAX_PLAYER_NAME];
                                    GetPlayerName(p, nomep, sizeof(nomep));
                                    if(strcmp(nomep, OrgMembros[org][m][MembroNome]) == 0) {
                                        online = 1;
                                        break;
                                    }
                                }
                            }
                            
                            new cargonome[25];
                            GetCargoNome(OrgDados[org][OrgTipo], cargo, cargonome, 25);
                            
                            if(online) {
                                format(item, sizeof(item), "%s\t%s\tOnline\n", OrgMembros[org][m][MembroNome], cargonome);
                            } else {
                                format(item, sizeof(item), "%s\t%s\tOffline\n", OrgMembros[org][m][MembroNome], cargonome);
                            }
                            strcat(lista, item);
                        }
                    }
                }
                
                if(strlen(lista) <= 20) {
                    SendClientMessage(playerid, -1, "Nenhum membro encontrado!");
                    return 1;
                }
                
                ShowPlayerDialog(playerid, DIALOG_ORG_MEMBROS, DIALOG_STYLE_TABLIST_HEADERS, "Membros da Organização", lista, "Gerenciar", "Fechar");
                MenuOrgSelecionada[playerid] = org;
            }
            case 1:
            {
                ShowPlayerDialog(playerid, DIALOG_ORG_CONVIDAR, DIALOG_STYLE_INPUT, "Convidar Membro", "Digite o ID do jogador para convidar:", "Convidar", "Voltar");
            }
            case 2:
            {
                if(!IsPlayerLeader(playerid) && !IsPlayerSubLeader(playerid)) return SendClientMessage(playerid, -1, "Sem permissão!");
                if(OrgDados[org][OrgTipo] == ORG_TIPO_CRIMINOSA) {
                    if(OrgDados[org][CofreStatus] == false) OrgDados[org][CofreStatus] = true;
                    else OrgDados[org][CofreStatus] = false;
                    AtualizarTextLabelsOrg(org);
                    SendClientMessage(playerid, -1, OrgDados[org][CofreStatus] == false ? "Cofre ABERTO!" : "Cofre FECHADO!");
                } else {
                    if(OrgDados[org][ArsenalStatus] == false) OrgDados[org][ArsenalStatus] = true;
                    else OrgDados[org][ArsenalStatus] = false;
                    AtualizarTextLabelsOrg(org);
                    SendClientMessage(playerid, -1, OrgDados[org][ArsenalStatus] == false ? "Arsenal ABERTO!" : "Arsenal FECHADO!");
                }
                SalvarOrgs();
            }
            case 3:
            {
                ShowPlayerDialog(playerid, DIALOG_ORG_SAIR, DIALOG_STYLE_MSGBOX, "Sair da Organização", 
                    "Tem certeza que deseja sair da organização?\nVocê perderá todos os seus cargos e benefícios.", "Sim", "Não");
                MenuOrgSelecionada[playerid] = org;
            }
        }
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_SAIR && response)
	{
		new org = MenuOrgSelecionada[playerid];
		new nome[MAX_PLAYER_NAME];
		GetPlayerName(playerid, nome, sizeof(nome));
		
		RemoverMembro(org, nome);
		PlayerInfo[playerid][pOrgID] = -1;
		PlayerInfo[playerid][pOrgCargo] = 0;
		PlayerInfo[playerid][pFardado] = false;
		SetPlayerSkin(playerid, PlayerInfo[playerid][pSkin]);
		SalvarConta(playerid);
		
		if(IsPlayerLeader(playerid)) {
		    strmid(OrgDados[org][OrgDono], "Nenhum", 0, 7, MAX_PLAYER_NAME);
		    OrgDados[org][TemDono] = false;
		    SalvarOrgs();
		    SendClientMessage(playerid, -1, "Você era o líder e saiu! A organização agora está sem dono.");
		    LogOrgAction(org, playerid, "Líder saiu da organização (org agora sem dono)");
		} else {
		    SendClientMessage(playerid, -1, "Você saiu da organização!");
		    LogOrgAction(org, playerid, "Saiu da organização");
		}
		
		return 1;
	}
    
    if(dialogid == DIALOG_ORG_CONVIDAR && response)
    {
        new targetid = strval(inputtext);
        if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, -1, "Jogador não está online!");
        if(IsPlayerInAnyOrg(targetid)) return SendClientMessage(playerid, -1, "Este jogador já está em uma organização!");
        
        new org = MenuOrgSelecionada[playerid];
        new convidado[MAX_PLAYER_NAME];
        GetPlayerName(targetid, convidado, sizeof(convidado));
        
        AdicionarMembro(org, convidado, 0);
        PlayerInfo[targetid][pOrgID] = org;
        PlayerInfo[targetid][pOrgCargo] = 0;
        SalvarConta(targetid);
        
        new msg[128];
        format(msg, sizeof(msg), "Você entrou na organização %s!", OrgDados[org][OrgNome]);
        SendClientMessage(targetid, -1, msg);
        format(msg, sizeof(msg), "%s entrou na organização!", convidado);
        LogOrgAction(org, playerid, msg);
        
        SalvarOrgs();
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_MEMBROS && response)
    {
        new org = MenuOrgSelecionada[playerid];
        new linha[200];
        strmid(linha, inputtext, 0, strlen(inputtext), 200);
        
        new nome_membro[MAX_PLAYER_NAME];
        new i, j;
        
        while(linha[i] != '\0' && j < MAX_PLAYER_NAME-1) {
            if(linha[i] == '{') {
                while(linha[i] != '\0' && linha[i] != '}') i++;
                if(linha[i] == '}') i++;
                continue;
            }
            if(linha[i] == ' ' || linha[i] == '\n') break;
            nome_membro[j++] = linha[i++];
        }
        nome_membro[j] = '\0';
        
        if(strlen(nome_membro) == 0) {
            strmid(nome_membro, inputtext, 0, strlen(inputtext));
        }
        
        new membroIndex = -1;
        for(new m = 0; m < MAX_MEMBROS_ORG; m++) {
            if(OrgMembros[org][m][MembroAtivo] && strcmp(OrgMembros[org][m][MembroNome], nome_membro, true) == 0) {
                membroIndex = m;
                break;
            }
        }
        
        if(membroIndex == -1) return SendClientMessage(playerid, -1, "Membro não encontrado!");
        
        MenuMembroIndex[playerid] = membroIndex;
        MenuOrgSelecionada[playerid] = org;
        
        new str[300];
        strcat(str, "1. Promover\n");
        strcat(str, "2. Rebaixar\n");
        strcat(str, "3. Expulsar");
        ShowPlayerDialog(playerid, DIALOG_ORG_MEMBRO_ACAO, DIALOG_STYLE_LIST, "Ações do Membro", str, "Selecionar", "Fechar");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_MEMBRO_ACAO && response)
    {
        new org = MenuOrgSelecionada[playerid];
        new membroIndex = MenuMembroIndex[playerid];
        new nome[MAX_PLAYER_NAME];
        strmid(nome, OrgMembros[org][membroIndex][MembroNome], 0, strlen(OrgMembros[org][membroIndex][MembroNome]), MAX_PLAYER_NAME);
        new cargoAtual = OrgMembros[org][membroIndex][MembroCargo];
        
        new meuNome[MAX_PLAYER_NAME];
        GetPlayerName(playerid, meuNome, sizeof(meuNome));
        
        if(strcmp(nome, meuNome) == 0) {
            SendClientMessage(playerid, -1, "Você não pode fazer isso consigo mesmo!");
            return 1;
        }
        
        if(listitem == 0) {
            if(cargoAtual >= 4) return SendClientMessage(playerid, -1, "Este membro já está no cargo máximo!");
            
            new novoCargo = cargoAtual + 1;
            AtualizarMembroOffline(org, nome, novoCargo);
            
            for(new p = 0; p < MAX_PLAYERS; p++) {
                if(IsPlayerConnected(p)) {
                    new nomep[MAX_PLAYER_NAME];
                    GetPlayerName(p, nomep, sizeof(nomep));
                    if(strcmp(nomep, nome) == 0) {
                        PlayerInfo[p][pOrgCargo] = novoCargo;
                        SalvarConta(p);
                        new cargonome[25];
                        GetCargoNome(OrgDados[org][OrgTipo], novoCargo, cargonome, 25);
                        new msg[128];
                        format(msg, sizeof(msg), "Você foi promovido a %s!", cargonome);
                        SendClientMessage(p, -1, msg);
                        if(PlayerInfo[p][pFardado] == true) AtualizarFardaPlayer(p);
                        break;
                    }
                }
            }
            
            new cargonome[25];
            GetCargoNome(OrgDados[org][OrgTipo], novoCargo, cargonome, 25);
            new msg[128];
            format(msg, sizeof(msg), "%s foi promovido a %s", nome, cargonome);
            LogOrgAction(org, playerid, msg);
            SalvarOrgs();
            SendClientMessage(playerid, -1, "Membro promovido!");
            
        } else if(listitem == 1) {
            if(cargoAtual <= 0) return SendClientMessage(playerid, -1, "Este membro já está no cargo mínimo!");
            
            new novoCargo = cargoAtual - 1;
            AtualizarMembroOffline(org, nome, novoCargo);
            
            for(new p = 0; p < MAX_PLAYERS; p++) {
                if(IsPlayerConnected(p)) {
                    new nomep[MAX_PLAYER_NAME];
                    GetPlayerName(p, nomep, sizeof(nomep));
                    if(strcmp(nomep, nome) == 0) {
                        PlayerInfo[p][pOrgCargo] = novoCargo;
                        SalvarConta(p);
                        new cargonome[25];
                        GetCargoNome(OrgDados[org][OrgTipo], novoCargo, cargonome, 25);
                        new msg[128];
                        format(msg, sizeof(msg), "Você foi rebaixado a %s!", cargonome);
                        SendClientMessage(p, -1, msg);
                        if(PlayerInfo[p][pFardado] == true) AtualizarFardaPlayer(p);
                        break;
                    }
                }
            }
            
            new cargonome[25];
            GetCargoNome(OrgDados[org][OrgTipo], novoCargo, cargonome, 25);
            new msg[128];
            format(msg, sizeof(msg), "%s foi rebaixado a %s", nome, cargonome);
            LogOrgAction(org, playerid, msg);
            SalvarOrgs();
            SendClientMessage(playerid, -1, "Membro rebaixado!");
            
        } else if(listitem == 2) {
            RemoverMembro(org, nome);
            
            for(new p = 0; p < MAX_PLAYERS; p++) {
                if(IsPlayerConnected(p)) {
                    new nomep[MAX_PLAYER_NAME];
                    GetPlayerName(p, nomep, sizeof(nomep));
                    if(strcmp(nomep, nome) == 0) {
                        PlayerInfo[p][pOrgID] = -1;
                        PlayerInfo[p][pOrgCargo] = 0;
                        PlayerInfo[p][pFardado] = false;
                        SetPlayerSkin(p, PlayerInfo[p][pSkin]);
                        SalvarConta(p);
                        SendClientMessage(p, -1, "Você foi expulso da organização!");
                        break;
                    }
                }
            }
            
            new msg[128];
            format(msg, sizeof(msg), "%s foi expulso da organização!", nome);
            LogOrgAction(org, playerid, msg);
            SalvarOrgs();
            SendClientMessage(playerid, -1, "Membro expulso!");
        }
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_SETAR_ID && response)
    {
        new targetid = strval(inputtext);
        if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, -1, "Jogador ID offline.");
        if(IsPlayerInAnyOrg(targetid)) return SendClientMessage(playerid, -1, "Este jogador já está em uma organização!");
        
        MenuPlayerAlvoID[playerid] = targetid;
        
        new lista[2000], item[200];
        strcat(lista, "Nome\tStatus\n");
        for(new i = 0; i < MAX_ORGS; i++) {
            if(OrgDados[i][OrgCriada] && !OrgDados[i][TemDono]) {
                format(item, sizeof(item), "%s\tDisponível\n", OrgDados[i][OrgNome]);
                strcat(lista, item);
            }
        }
        if(strlen(lista) <= 20) {
            SendClientMessage(playerid, -1, "Nenhuma organização disponível para receber líder!");
            return 1;
        }
        ShowPlayerDialog(playerid, DIALOG_ORG_SETAR_SEL, DIALOG_STYLE_TABLIST_HEADERS, "Selecione a Organização", lista, "Setar", "Cancelar");
        return 1;
    }
    
    if(dialogid == DIALOG_ORG_SETAR_SEL && response)
    {
        new encontrados = 0, id_real = -1;
        for(new i = 0; i < MAX_ORGS; i++) {
            if(OrgDados[i][OrgCriada] && !OrgDados[i][TemDono]) {
                if(encontrados == listitem) { id_real = i; break; }
                encontrados++;
            }
        }
        if(id_real == -1 || !OrgDados[id_real][OrgCriada]) return SendClientMessage(playerid, -1, "Organização inválida!");
        
        new target = MenuPlayerAlvoID[playerid];

        PlayerInfo[target][pOrgID] = id_real;
        PlayerInfo[target][pOrgCargo] = 4;
        SalvarConta(target);
        
        format(OrgDados[id_real][OrgDono], MAX_PLAYER_NAME, "%s", pName(target));
        OrgDados[id_real][TemDono] = true;
        
        AdicionarMembro(id_real, pName(target), 4);
        SalvarOrgs();
        
        new msg[128];
        format(msg, sizeof(msg), "Você foi promovido a Líder da organização: %s", OrgDados[id_real][OrgNome]);
        SendClientMessage(target, -1, msg);
        format(msg, sizeof(msg), "%s agora é Líder de %s", pName(target), OrgDados[id_real][OrgNome]);
        SendClientMessage(playerid, -1, msg);
        return 1;
    }
    return 0;
}

public OnPlayerClickMap(playerid, Float:fX, Float:fY, Float:fZ)
{
    SetPlayerPos(playerid, fX, fY, fZ);
    return 1;
}

// ============================================================================
// SAVES
// ============================================================================

stock pName(playerid)
{
	new name[MAX_PLAYER_NAME];
	GetPlayerName(playerid, name, sizeof(name));
	return name;
}

stock Arquivo(playerid)
{
	new file[64];
	format(file, sizeof(file), "Contas/%s.ini", pName(playerid));
	return file;
}

stock SalvarConta(playerid)
{
	if(DOF2_FileExists(Arquivo(playerid)))
	{
	    DOF2_SetInt(Arquivo(playerid), "Admin", PlayerInfo[playerid][pAdmin]);
	    DOF2_SetInt(Arquivo(playerid), "Dinheiro", GetPlayerMoney(playerid));
	    DOF2_SetInt(Arquivo(playerid), "Skin", PlayerInfo[playerid][pSkin]);
	    DOF2_SetBool(Arquivo(playerid), "Fardado", PlayerInfo[playerid][pFardado]);
	    DOF2_SetInt(Arquivo(playerid), "OrgID", PlayerInfo[playerid][pOrgID]);
	    DOF2_SetInt(Arquivo(playerid), "OrgCargo", PlayerInfo[playerid][pOrgCargo]);
	    
	    if(PlayerInfo[playerid][pFardado] == true) {
	        DOF2_SetInt(Arquivo(playerid), "SkinFardado", GetPlayerSkin(playerid));
	    }
	    
	    GetPlayerPos(playerid, PlayerInfo[playerid][pPosX], PlayerInfo[playerid][pPosY], PlayerInfo[playerid][pPosZ]);
	    GetPlayerFacingAngle(playerid, PlayerInfo[playerid][pPosR]);
	    PlayerInfo[playerid][pInterior] = GetPlayerInterior(playerid);
	    PlayerInfo[playerid][pVirtualWorld] = GetPlayerVirtualWorld(playerid);
	    
	    DOF2_SetFloat(Arquivo(playerid), "PosX", PlayerInfo[playerid][pPosX]);
	    DOF2_SetFloat(Arquivo(playerid), "PosY", PlayerInfo[playerid][pPosY]);
	    DOF2_SetFloat(Arquivo(playerid), "PosZ", PlayerInfo[playerid][pPosZ]);
	    DOF2_SetFloat(Arquivo(playerid), "PosR", PlayerInfo[playerid][pPosR]);
	    DOF2_SetInt(Arquivo(playerid), "Interior", PlayerInfo[playerid][pInterior]);
	    DOF2_SetInt(Arquivo(playerid), "VirtualWorld", PlayerInfo[playerid][pVirtualWorld]);
	    
	    GetPlayerHealth(playerid, PlayerInfo[playerid][pVida]);
	    GetPlayerArmour(playerid, PlayerInfo[playerid][pArmour]);
	    
	    DOF2_SetFloat(Arquivo(playerid), "Vida", PlayerInfo[playerid][pVida]);
	    DOF2_SetFloat(Arquivo(playerid), "Armour", PlayerInfo[playerid][pArmour]);
	    DOF2_SaveFile();
	
	    SalvarArmas(playerid);
	}
    return 1;
}

stock CarregarConta(playerid)
{
    if(DOF2_FileExists(Arquivo(playerid)))
    {
        PlayerInfo[playerid][pAdmin] = DOF2_GetInt(Arquivo(playerid), "Admin");
        PlayerInfo[playerid][pDinheiro] = DOF2_GetInt(Arquivo(playerid), "Dinheiro");
        PlayerInfo[playerid][pSkin] = DOF2_GetInt(Arquivo(playerid), "Skin");
        PlayerInfo[playerid][pFardado] = DOF2_GetBool(Arquivo(playerid), "Fardado");
        PlayerInfo[playerid][pOrgID] = DOF2_GetInt(Arquivo(playerid), "OrgID");
        PlayerInfo[playerid][pOrgCargo] = DOF2_GetInt(Arquivo(playerid), "OrgCargo");
    
        if(PlayerInfo[playerid][pOrgID] != -1 && !OrgDados[PlayerInfo[playerid][pOrgID]][OrgCriada]) {
            PlayerInfo[playerid][pOrgID] = -1;
            PlayerInfo[playerid][pOrgCargo] = 0;
            PlayerInfo[playerid][pFardado] = false;
            SalvarConta(playerid);
        }
        
        if(PlayerInfo[playerid][pOrgID] != -1 && OrgDados[PlayerInfo[playerid][pOrgID]][OrgCriada]) {
            new org = PlayerInfo[playerid][pOrgID];
            new nome[MAX_PLAYER_NAME];
            GetPlayerName(playerid, nome, sizeof(nome));
            
            new jaExiste = 0;
            for(new i = 0; i < MAX_MEMBROS_ORG; i++) {
                if(OrgMembros[org][i][MembroAtivo] && strcmp(OrgMembros[org][i][MembroNome], nome) == 0) {
                    jaExiste = 1;
                    // Atualiza o cargo se necessário
                    if(OrgMembros[org][i][MembroCargo] != PlayerInfo[playerid][pOrgCargo]) {
                        OrgMembros[org][i][MembroCargo] = PlayerInfo[playerid][pOrgCargo];
                        SaveOrgMembers(org);
                    }
                    break;
                }
            }
            
            if(!jaExiste) {
                printf("[FIX] Adicionando %s como membro da org %d (cargo %d)", nome, org, PlayerInfo[playerid][pOrgCargo]);
                AdicionarMembro(org, nome, PlayerInfo[playerid][pOrgCargo]);
            }
            
            if(OrgMembrosCount[org] == 0) {
                printf("[FIX] Recarregando membros da org %d para o jogador %s", org, nome);
                LoadOrgMembers(org);
            }
        }
        
        if(PlayerInfo[playerid][pFardado] == true) {
            PlayerInfo[playerid][pSkinFardado] = DOF2_GetInt(Arquivo(playerid), "SkinFardado");
        }
        
        PlayerInfo[playerid][pPosX] = DOF2_GetFloat(Arquivo(playerid), "PosX");
        PlayerInfo[playerid][pPosY] = DOF2_GetFloat(Arquivo(playerid), "PosY");
        PlayerInfo[playerid][pPosZ] = DOF2_GetFloat(Arquivo(playerid), "PosZ");
        PlayerInfo[playerid][pPosR] = DOF2_GetFloat(Arquivo(playerid), "PosR");
        PlayerInfo[playerid][pInterior] = DOF2_GetInt(Arquivo(playerid), "Interior");
        PlayerInfo[playerid][pVirtualWorld] = DOF2_GetInt(Arquivo(playerid), "VirtualWorld");
        PlayerInfo[playerid][pVida] = DOF2_GetFloat(Arquivo(playerid), "Vida");
        PlayerInfo[playerid][pArmour] = DOF2_GetFloat(Arquivo(playerid), "Armour");
        
        SetSpawnPlayer(playerid);
    }
    return 1;
}

SetSpawnPlayer(playerid)
{
	new skin;
    if(PlayerInfo[playerid][pFardado] == true && PlayerInfo[playerid][pOrgID] != -1) {
        skin = PlayerInfo[playerid][pSkinFardado];
    } else {
        skin = PlayerInfo[playerid][pSkin];
    }
    ResetPlayerMoney(playerid);
    GivePlayerMoney(playerid, PlayerInfo[playerid][pDinheiro]);
    SetPlayerInterior(playerid, PlayerInfo[playerid][pInterior]);
    SetPlayerVirtualWorld(playerid, PlayerInfo[playerid][pVirtualWorld]);    
    SetPlayerHealth(playerid, PlayerInfo[playerid][pVida]);
    SetPlayerArmour(playerid, PlayerInfo[playerid][pArmour]);
    SetCameraBehindPlayer(playerid);
	TogglePlayerSpectating(playerid, false);

	PlayerInfo[playerid][pLogado] = true;
    SetSpawnInfo(playerid, NO_TEAM, skin, PlayerInfo[playerid][pPosX], PlayerInfo[playerid][pPosY], PlayerInfo[playerid][pPosZ], PlayerInfo[playerid][pPosR], 0, 0, 0, 0, 0, 0);
    SpawnPlayer(playerid);        
    CarregarArmas(playerid);
}

stock SalvarArmas(playerid)
{
    new path[64];
    format(path, sizeof(path), "Armas/%s.ini", pName(playerid));
    
    new arma, municao;
    new key[24];
    for(new i = 0; i < 13; i++)
    {
        GetPlayerWeaponData(playerid, i, arma, municao);        
        format(key, sizeof(key), "Arma_%d", i);
        DOF2_SetInt(path, key, arma);        
        format(key, sizeof(key), "Municao_%d", i);
        DOF2_SetInt(path, key, municao);
    }
    DOF2_SaveFile();
    return 1;
}

stock CarregarArmas(playerid)
{
    new path[64];
    format(path, sizeof(path), "Armas/%s.ini", pName(playerid));
    
    if(!DOF2_FileExists(path)) return 0;
    
    new arma, municao;
    new key[24];    
    ResetPlayerWeapons(playerid);        
    for(new i = 0; i < 13; i++)
    {
        format(key, sizeof(key), "Arma_%d", i);
        arma = DOF2_GetInt(path, key);        
        format(key, sizeof(key), "Municao_%d", i);
        municao = DOF2_GetInt(path, key);                
        if(arma != 0 && municao > 0) {
            GivePlayerWeapon(playerid, arma, municao);
        }
    }
    return 1;
}

public SalvarOrgs()
{
    new path[40], key[50];
    for(new i = 0; i < MAX_ORGS; i++) {
        if(!OrgDados[i][OrgCriada]) continue;
        
        format(path, sizeof(path), "orgs/Org_%d.ini", i);
        if(!DOF2_FileExists(path)) DOF2_CreateFile(path);
        
        DOF2_SetString(path, "Nome", OrgDados[i][OrgNome]);
        DOF2_SetInt(path, "Tipo", OrgDados[i][OrgTipo]);
        DOF2_SetInt(path, "Cor", OrgDados[i][OrgCor]);
        DOF2_SetString(path, "Dono", OrgDados[i][OrgDono]);
        DOF2_SetBool(path, "TemDono", OrgDados[i][TemDono]);
        DOF2_SetInt(path, "Dinheiro", OrgDados[i][OrgDinheiro]);
        DOF2_SetInt(path, "Material", OrgDados[i][OrgMaterial]);
        DOF2_SetBool(path, "CofreStatus", OrgDados[i][CofreStatus]);
        DOF2_SetBool(path, "ArsenalStatus", OrgDados[i][ArsenalStatus]);
        DOF2_SetFloat(path, "CofreX", OrgDados[i][CofreX]);
        DOF2_SetFloat(path, "CofreY", OrgDados[i][CofreY]);
        DOF2_SetFloat(path, "CofreZ", OrgDados[i][CofreZ]);
        DOF2_SetFloat(path, "VeicPickupX", OrgDados[i][VeicPickupX]);
        DOF2_SetFloat(path, "VeicPickupY", OrgDados[i][VeicPickupY]);
        DOF2_SetFloat(path, "VeicPickupZ", OrgDados[i][VeicPickupZ]);
        DOF2_SetFloat(path, "FardaX", OrgDados[i][FardaX]);
        DOF2_SetFloat(path, "FardaY", OrgDados[i][FardaY]);
        DOF2_SetFloat(path, "FardaZ", OrgDados[i][FardaZ]);
        DOF2_SetFloat(path, "ArsenalX", OrgDados[i][ArsenalX]);
        DOF2_SetFloat(path, "ArsenalY", OrgDados[i][ArsenalY]);
        DOF2_SetFloat(path, "ArsenalZ", OrgDados[i][ArsenalZ]);
        DOF2_SetInt(path, "SkinOrg", OrgDados[i][SkinOrg]);
        
        for(new s = 0; s < MAX_SLOTS_COFRE; s++) {
            format(key, sizeof(key), "ArmaID_Slot_%d", s); DOF2_SetInt(path, key, OrgArmas[i][s][CofreArmaID]);
            format(key, sizeof(key), "ArmaBalas_Slot_%d", s); DOF2_SetInt(path, key, OrgArmas[i][s][CofreArmaMunicao]);
        }
        
        for(new v = 0; v < MAX_VEICULOS_ORG; v++) {
            format(key, sizeof(key), "VeicMod_Slot_%d", v); DOF2_SetInt(path, key, OrgFrota[i][v][vModelo]);
            format(key, sizeof(key), "VeicX_Slot_%d", v); DOF2_SetFloat(path, key, OrgFrota[i][v][vX]);
            format(key, sizeof(key), "VeicY_Slot_%d", v); DOF2_SetFloat(path, key, OrgFrota[i][v][vY]);
            format(key, sizeof(key), "VeicZ_Slot_%d", v); DOF2_SetFloat(path, key, OrgFrota[i][v][vZ]);
            format(key, sizeof(key), "VeicA_Slot_%d", v); DOF2_SetFloat(path, key, OrgFrota[i][v][vA]);
            format(key, sizeof(key), "VeicNome_Slot_%d", v); DOF2_SetString(path, key, OrgFrota[i][v][NomeModelo]);
        }
        
        SaveOrgMembers(i);
    }
    DOF2_SaveFile();
    return 1;
}

public CarregarOrgs()
{
    new path[40], key[50];
    new carregadas = 0;
    
    for(new i = 0; i < MAX_ORGS; i++) {
        format(path, sizeof(path), "orgs/Org_%d.ini", i);
        if(!DOF2_FileExists(path)) {
            OrgDados[i][OrgCriada] = false;
            continue;
        }
        carregadas++;
        
        OrgDados[i][OrgCriada] = true;
        format(OrgDados[i][OrgNome], 50, "%s", DOF2_GetString(path, "Nome"));
        OrgDados[i][OrgTipo] = DOF2_GetInt(path, "Tipo");
        OrgDados[i][OrgCor] = DOF2_GetInt(path, "Cor");        
		format(OrgDados[i][OrgDono], MAX_PLAYER_NAME, "%s", DOF2_GetString(path, "Dono"));
        OrgDados[i][TemDono] = DOF2_GetBool(path, "TemDono");
        OrgDados[i][OrgDinheiro] = DOF2_GetInt(path, "Dinheiro");
        OrgDados[i][OrgMaterial] = DOF2_GetInt(path, "Material");
        if(OrgDados[i][OrgMaterial] == 0) OrgDados[i][OrgMaterial] = MATERIAL_INICIAL;
        OrgDados[i][CofreStatus] = DOF2_GetBool(path, "CofreStatus");
        OrgDados[i][ArsenalStatus] = DOF2_GetBool(path, "ArsenalStatus");
        OrgDados[i][CofreX] = DOF2_GetFloat(path, "CofreX");
        OrgDados[i][CofreY] = DOF2_GetFloat(path, "CofreY");
        OrgDados[i][CofreZ] = DOF2_GetFloat(path, "CofreZ");
        OrgDados[i][VeicPickupX] = DOF2_GetFloat(path, "VeicPickupX");
        OrgDados[i][VeicPickupY] = DOF2_GetFloat(path, "VeicPickupY");
        OrgDados[i][VeicPickupZ] = DOF2_GetFloat(path, "VeicPickupZ");
        OrgDados[i][FardaX] = DOF2_GetFloat(path, "FardaX");
        OrgDados[i][FardaY] = DOF2_GetFloat(path, "FardaY");
        OrgDados[i][FardaZ] = DOF2_GetFloat(path, "FardaZ");
        OrgDados[i][ArsenalX] = DOF2_GetFloat(path, "ArsenalX");
        OrgDados[i][ArsenalY] = DOF2_GetFloat(path, "ArsenalY");
        OrgDados[i][ArsenalZ] = DOF2_GetFloat(path, "ArsenalZ");
        OrgDados[i][SkinOrg] = DOF2_GetInt(path, "SkinOrg");
        
        for(new s = 0; s < MAX_SLOTS_COFRE; s++) {
            format(key, sizeof(key), "ArmaID_Slot_%d", s); OrgArmas[i][s][CofreArmaID] = DOF2_GetInt(path, key);
            format(key, sizeof(key), "ArmaBalas_Slot_%d", s); OrgArmas[i][s][CofreArmaMunicao] = DOF2_GetInt(path, key);
        }
        
        for(new v = 0; v < MAX_VEICULOS_ORG; v++) {
            if(OrgDados[i][OrgTipo] == ORG_TIPO_CORPORACAO) {
                OrgFrota[i][v][vModelo] = VeiculosCorporacao[v];
            } else {
                OrgFrota[i][v][vModelo] = VeiculosCriminosa[v];
            }
            GetVehicleModelName(OrgFrota[i][v][vModelo], OrgFrota[i][v][NomeModelo], 32);
            OrgFrota[i][v][vSpawnado] = false;
            OrgFrota[i][v][vID_Atual] = 0;
        }
        
        if(OrgDados[i][CofreX] != 0.0 && OrgDados[i][OrgTipo] == ORG_TIPO_CRIMINOSA) {
            OrgDados[i][ObjCofre] = CreateDynamicObject(2332, OrgDados[i][CofreX], OrgDados[i][CofreY], OrgDados[i][CofreZ], 0, 0, 0);
        }
        
        if(OrgDados[i][VeicPickupX] != 0.0) {
            OrgDados[i][PickupVeiculo] = CreateDynamicPickup(19134, 1, OrgDados[i][VeicPickupX], OrgDados[i][VeicPickupY], OrgDados[i][VeicPickupZ]);
        }
        
		if(OrgDados[i][OrgTipo] == ORG_TIPO_CORPORACAO && OrgDados[i][FardaX] != 0.0) {
		    OrgDados[i][PickupFarda] = CreateDynamicPickup(1275, 1, OrgDados[i][FardaX], OrgDados[i][FardaY], OrgDados[i][FardaZ]);
		}
        
        if(OrgDados[i][ArsenalX] != 0.0 && OrgDados[i][OrgTipo] == ORG_TIPO_CORPORACAO) {
            OrgDados[i][PickupArsenal] = CreateDynamicPickup(2061, 1, OrgDados[i][ArsenalX], OrgDados[i][ArsenalY], OrgDados[i][ArsenalZ]);
        }
        
        printf("[LOAD] Org %d: %s | Dono: %s | Tipo: %d", i, OrgDados[i][OrgNome], OrgDados[i][OrgDono], OrgDados[i][OrgTipo]);
        
        AtualizarTextLabelsOrg(i);
        LoadOrgMembers(i);
    }
    
    printf("Carregadas %d organizacoes", carregadas);
    return 1;
}

public SaveOrgMembers(orgid)
{
    new path[40];
    format(path, sizeof(path), "orgs/Membros_%d.ini", orgid);
    if(!DOF2_FileExists(path)) DOF2_CreateFile(path);
    
    new key[32];
    for(new m = 0; m < MAX_MEMBROS_ORG; m++) {
        if(OrgMembros[orgid][m][MembroAtivo]) {
            format(key, sizeof(key), "Membro_%d", m);
            DOF2_SetString(path, key, OrgMembros[orgid][m][MembroNome]);
            format(key, sizeof(key), "Cargo_%d", m);
            DOF2_SetInt(path, key, OrgMembros[orgid][m][MembroCargo]);
        }
    }
    DOF2_SetInt(path, "TotalMembros", OrgMembrosCount[orgid]);
    DOF2_SaveFile();
}

public LoadOrgMembers(orgid)
{
    new path[40], key[32];
    format(path, sizeof(path), "orgs/Membros_%d.ini", orgid);
    
    if(!DOF2_FileExists(path)) {
        printf("[LOADMEMBRO] Arquivo NAO existe: %s", path);
        return 0;
    }
    
    printf("[LOADMEMBRO] Carregando membros de: %s", path);
    
    OrgMembrosCount[orgid] = DOF2_GetInt(path, "TotalMembros");
    printf("[LOADMEMBRO] TotalMembros esperado: %d", OrgMembrosCount[orgid]);
    
    for(new m = 0; m < MAX_MEMBROS_ORG; m++) {
        format(key, sizeof(key), "Membro_%d", m);
        DOF2_GetString(path, key, OrgMembros[orgid][m][MembroNome]);
        if(strlen(OrgMembros[orgid][m][MembroNome]) > 0) {
            OrgMembros[orgid][m][MembroAtivo] = true;
            format(key, sizeof(key), "Cargo_%d", m);
            OrgMembros[orgid][m][MembroCargo] = DOF2_GetInt(path, key);
            printf("LOADMEMBRO Org:%d Slot:%d Nome:%s Cargo:%d", orgid, m, OrgMembros[orgid][m][MembroNome], OrgMembros[orgid][m][MembroCargo]);
        }
    }
    return 1;
}

// ============================================================================
// COMANDOS
// ============================================================================

CMD:criarorg(playerid)
{
    new str[500];
    strcat(str, "1. Criar Nova Organização\n");
    strcat(str, "2. Lista de Organizações Criadas\n");
    strcat(str, "3. Setar Líder em uma Org");
    ShowPlayerDialog(playerid, DIALOG_ORG_MAIN, DIALOG_STYLE_LIST, "Gerenciador de Organizações", str, "Selecionar", "Fechar");
    return 1;
}
    
CMD:mm(playerid)
{
    if(PlayerInfo[playerid][pOrgID] == -1) return SendClientMessage(playerid, -1, "Você não está em nenhuma organização!");
    
    new org = PlayerInfo[playerid][pOrgID];
    MenuOrgSelecionada[playerid] = org;
    
    new str[500];
    strcat(str, "1. Lista de Membros\n");
    strcat(str, "2. Convidar Jogador\n");
    if(IsPlayerLeader(playerid) || IsPlayerSubLeader(playerid)) {
        if(OrgDados[org][OrgTipo] == ORG_TIPO_CRIMINOSA) {
            if(OrgDados[org][CofreStatus] == false) strcat(str, "FECHAR Cofre\n");
            else strcat(str, "ABRIR Cofre\n");
        } else {
            if(OrgDados[org][ArsenalStatus] == false) strcat(str, "FECHAR Arsenal\n");
            else strcat(str, "ABRIR Arsenal\n");
        }
    }
    strcat(str, "4. Sair da ");
    if(OrgDados[org][OrgTipo] == ORG_TIPO_CRIMINOSA) strcat(str, "Organização");
    else strcat(str, "Corporação");
    
    ShowPlayerDialog(playerid, DIALOG_ORG_MENU, DIALOG_STYLE_LIST, "Menu da Organização", str, "Selecionar", "Fechar");
    return 1;
}
