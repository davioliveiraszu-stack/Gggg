#define SSCANF_NO_NICE_FEATURES

#include <a_samp>
#include <DOF2>
#include <streamer>
#include <sscanf2>
#include <zcmd>
#include <foreach>

#define MENUSTORE_PTBR
#include <MenuStore>

#include <modulos/maps>
#include <modulos/textdraw_player>

#if defined MAX_PLAYERS
#undef MAX_PLAYERS
#define MAX_PLAYERS 100
#endif

#if defined MAX_VEHICLES
#undef MAX_VEHICLES 
#define MAX_VEHICLES 1500
#endif

#define function%0(%1) forward%0(%1); public%0(%1)
#define RandomEx(%0,%1) (random((%1)-(%0)+1)+(%0))
#define GivePlayerCash(%0,%1) SetPVarInt(%0,"Money",GetPlayerCash(%0)+%1),GivePlayerMoney(%0,%1)
#define ResetPlayerCash(%0) SetPVarInt(%0,"Money",0),ResetPlayerMoney(%0)
#define GetPlayerCash(%0) GetPVarInt(%0,"Money")

#define MAX_CASAS 500
#define MAX_MORADORES_CASA 5
#define CASA_CLASSES 4
#define CASA_VW_BASE 20000

#define MAX_EMPRESAS 100
#define MAX_ESTOQUE_ITEM 100     
#define EMPRESA_VW_BASE 10000

#define EMPRESA_24_7 1
#define EMPRESA_ROUPAS 2
#define EMPRESA_ARMAS 3
#define EMPRESA_RESTAURANTE 4
#define EMPRESA_BAR 5
#define EMPRESA_POSTO 6
#define EMPRESA_TIPOS 6      

#define PROD_CELULAR 0
#define PROD_JBL 1
#define PROD_KITREPARO 2
#define PROD_GALAO 3
#define PROD_AK 4
#define PROD_M4 5
#define PROD_DESERT 6
#define PROD_MP5 7
#define PROD_AGUA 8
#define PROD_REFRIGERANTE 9
#define PROD_HAMBURGUER 10
#define PROD_PIZZA 11
#define PROD_CERVEJA 12
#define PROD_VODKA 13
#define PROD_WHISKY 14
#define PROD_ROUPA 15

#define MAX_PRODUTOS 16
#define MAX_PRODUTOS_TIPO 4

#define INV_CATEGORIA_COMIDAS 0
#define INV_CATEGORIA_ROUPAS 1
#define INV_CATEGORIA_ARMAS 2
#define INV_CATEGORIA_OUTROS 3

#define INV_CATEGORIAS 4
#define INV_SLOTS 20

#define INV_ITEM_AGUA 1
#define INV_ITEM_REFRIGERANTE 2
#define INV_ITEM_HAMBURGUER 3
#define INV_ITEM_PIZZA 4
#define INV_ITEM_CERVEJA 5
#define INV_ITEM_VODKA 6
#define INV_ITEM_WHISKY 7
#define INV_ITEM_KIT_REPARO 8
#define INV_ITEM_GALAO 9

#define MAX_JBLS 100
#define MAX_PLAYLISTS 20

#define ADM_SUPORTE 1
#define ADM_MODERADOR 2
#define ADM_ADMINISTRADOR 3
#define ADM_GERENTE 4
#define ADM_DIRETOR 5
#define ADM_FUNDADOR 6

#define DESEMPREGADO 0
#define GARI 1

#define COR_ERRO 0xFF4500AA
#define COR_VERDE 0x00FF00AA
#define COR_AMARELO 0xFFD700AA
#define COR_LARANJA 0xFF8C00AA
#define COR_SUCESSO 0xADFF2FAA

enum
{
	DIALOG_INICIO,
	DIALOG_INFO_CONTA,
    DIALOG_REGISTRO, 
    DIALOG_LOGAR, 
    DIALOG_GENERO, 
    DIALOG_IDADE, 
    DIALOG_TPTODOS, 
    DIALOG_TELEMAP, 
    DIALOG_AJUDA_ADM,
    DIALOG_ADMINS,
	DIALOG_ADM_LISTA,
	DIALOG_ADM_ACOES,
	DIALOG_ADM_AVALIACOES,
	DIALOG_ADM_SAIR,
	DIALOG_REPORT_LISTA,
	DIALOG_REPORT_RESPOSTA,
	DIALOG_REPORT_AVALIACAO,
	DIALOG_REPORT_ENVIAR,
    DIALOG_ESCOLHER_SPAWN, 
    DIALOG_ANIMS_CATEGORIAS, 
    DIALOG_ANIMS_DANCAS, 
    DIALOG_ANIMS_EMOCOES, 
    DIALOG_ANIMS_BAFORADA, 
    DIALOG_ANIMS_INTERACAO, 
    DIALOG_ANIMS_ACOES, 
    DIALOG_ANIMS_SENTADO, 
    DIALOG_ANIMS_LUTA, 
    DIALOG_ANIMS_OUTROS, 
    DIALOG_CASA_INTERACAO, 
    DIALOG_CASA_COMPRAR, 
    DIALOG_CASA_MENU, 
    DIALOG_CASA_INFO, 
    DIALOG_CASA_VENDER_ESTADO, 
    DIALOG_CASA_VENDER_JOGADOR, 
    DIALOG_CASA_VENDER_CONFIRMA, 
    DIALOG_CASA_MORADORES, 
    DIALOG_CASA_MORADOR_ADD, 
    DIALOG_CASA_MORADOR_CONVITE, 
    DIALOG_CASA_MORADOR_OPCOES, 
    DIALOG_EMPRESA_INTERACAO, 
    DIALOG_EMPRESA_COMPRAR, 
    DIALOG_EMPRESA_MENU, 
    DIALOG_EMPRESA_INFO, 
    DIALOG_EMPRESA_FINANCEIRO, 
    DIALOG_EMPRESA_ESTOQUE, 
    DIALOG_EMPRESA_REABASTECER, 
    DIALOG_EMPRESA_REABASTECERT, 
    DIALOG_EMPRESA_SACAR, 
    DIALOG_EMPRESA_VENDER, 
    DIALOG_EMPRESA_VENDER_JOGADOR, 
	DIALOG_EMPRESA_VENDER_CONFIRMA, 
    DIALOG_EMPRESA_PRECO_GASOLINA, 
    DIALOG_EMPRESA_ROUPA,
    DIALOG_INV_CONFIRMAR_EXCLUSAO,
	DIALOG_INV_QUANTIDADE_EXCLUIR,
	DIALOG_INV_TRANSFERIR,
	DIALOG_INV_QUANTIA_TRANSFERIR,
    DIALOG_JBL_MENU, 
	DIALOG_JBL_LINK, 
	DIALOG_JBL_PLAYLISTS, 
	DIALOG_JBL_PLAYLIST_ACAO, 
	DIALOG_JBL_PLAYLIST_NOME
};

new const NomeClasseCasa[CASA_CLASSES][] = {"Popular", "Media", "Alta", "Luxo"};
new const InteriorClasseCasa[CASA_CLASSES] = {11, 12, 9, 5};
new const Float:PosIntClasseCasa[CASA_CLASSES][4] =
{
    {2282.97, -1140.28, 1050.89, 357.7650}, 
    {2324.38, -1148.48, 1050.71, 349.2345}, 
    {2319.12, -1023.95, 1050.21, 352.9356}, 
    {1261.8472, -785.6419, 1091.9062, 359.3935}
};
new const ValorClasseCasa[CASA_CLASSES] = {100000, 150000, 350000, 1000000};

enum E_CASA
{
    casaClasse, 
    Float:casaExtX, 
    Float:casaExtY, 
    Float:casaExtZ, 
    Float:casaExtA, 
    casaValor, 
    casaDono[MAX_PLAYER_NAME], 
    bool:casaOcupada, 
    bool:casaTrancada, 
    casaTotalMoradores, 
    casaPickup, 
    casaIcon, 
    Text3D:casaLabel
};
new CasaInfo[MAX_CASAS][E_CASA];
new CasaMorador[MAX_CASAS][MAX_MORADORES_CASA][MAX_PLAYER_NAME];
new TotalCasas;

new const NomeEmpresa[EMPRESA_TIPOS][] =
{
    "Loja 24/7", 
    "Loja de Roupas", 
    "Loja de Armas", 
    "Restaurante", 
    "Bar", 
    "Posto de Gasolina"
};

new const ValorEmpresa[EMPRESA_TIPOS] = {80000, 100000, 180000, 120000, 100000, 250000};
new const InteriorEmpresa[EMPRESA_TIPOS] = {10, 14, 6, 5, 11, 6};
new const Float:PosInteriorEmpresa[EMPRESA_TIPOS][4] =
{
    {6.3704, -31.0050, 1003.5494, 352.5428}, 
    {204.5840, -168.1813, 1000.5234, 1.1874}, 
    {296.7463, -110.9906, 1001.5156, 1.9001}, 
    {372.4030, -132.5051, 1001.4922, 349.9338}, 
    {501.9992, -69.0285, 998.7578, 180.1709}, 
    {-2240.1000, 136.9700, 1035.4100, 252.2888}
};

new const Float:PosCompraEmpresa[EMPRESA_TIPOS][3] =
{
    {2.4654, -28.9052, 1003.5494}, 
    {204.4163, -159.6073, 1000.5234}, 
    {290.3599, -106.4931, 1001.5156}, 
    {374.0173, -119.0841, 1001.4922}, 
    {497.5566, -75.6739, 998.7578}, 
    {-2237.1702, 130.5831, 1035.4141}
};

new const ProdutoNome[MAX_PRODUTOS][24] =
{
    "Celular", 
    "JBL", 
    "Kit de Reparo", 
    "Galao 20L", 
    "AK-47", 
    "M4", 
    "Desert Eagle", 
    "MP5", 
    "Agua", 
    "Refrigerante", 
    "Hamburguer", 
    "Pizza", 
    "Cerveja", 
    "Vodka", 
    "Whisky", 
    "Roupa"
};

new const ProdutoKey[MAX_PRODUTOS][16] =
{
    "Celular", "JBL", "KitReparo", "Galao", 
    "AK", "M4", "Desert", "MP5", 
    "Agua", "Refrigerante", "Hamburguer", "Pizza", 
    "Cerveja", "Vodka", "Whisky", "Roupa"
};

new const ProdutoPrecoPadrao[MAX_PRODUTOS] =
{
    3000, 2500, 750, 1200, 
    50, 50, 40, 35, 
    20, 30, 60, 120, 
    80, 150, 250, 1500
};

new const ProdutoCusto[MAX_PRODUTOS] =
{
    1200, 1000, 300, 500, 
    2500, 2500, 1500, 1500, 
    8, 12, 25, 50, 
    30, 60, 100, 600
};

new const EmpresaCatalogo[EMPRESA_TIPOS][MAX_PRODUTOS_TIPO] =
{
    {PROD_CELULAR, PROD_JBL, -1, -1}, // Loja 24/7
    {PROD_ROUPA, -1, -1, -1}, // Roupas
    {PROD_AK, PROD_M4, PROD_DESERT, PROD_MP5}, // Armas
    {PROD_AGUA, PROD_REFRIGERANTE, PROD_HAMBURGUER, PROD_PIZZA}, // Restaurante
    {PROD_CERVEJA, PROD_VODKA, PROD_WHISKY, -1}, // Bar
    {PROD_KITREPARO, PROD_GALAO, -1, -1} // Posto de Gasolina
};

enum E_EMPRESA
{
    empresaTipo, 
    Float:empresaExtX, 
    Float:empresaExtY, 
    Float:empresaExtZ, 
    Float:empresaExtA, 
    empresaValor, 
    empresaDono[MAX_PLAYER_NAME], 
    bool:empresaOcupada, 
    empresaSaldo, 
    empresaPickup, 
    empresaPickupBomba, 
    Text3D:empresaLabel, 
    Text3D:empresaLabelBomba, 
    empresaIcon,
    empresaCP, 
    Float:empresaBombaX, 
    Float:empresaBombaY, 
    Float:empresaBombaZ, 
    empresaPrecoGasolina, 
    empresaSemana, 
    empresaVendasSemana, 
    empresaVendasDia[7]
};
new EmpresaInfo[MAX_EMPRESAS][E_EMPRESA];
new EmpresaEstoque[MAX_EMPRESAS][MAX_PRODUTOS];
new TotalEmpresas;

enum E_INVENTARIO
{
    invID,
    invQuantidade,
    invExtra
};
new Inventario[MAX_PLAYERS][INV_CATEGORIAS][INV_SLOTS][E_INVENTARIO];
new InventarioCategoria[MAX_PLAYERS];
new InventarioSelecionado[MAX_PLAYERS];
new InventarioTransferirID[MAX_PLAYERS];
new InventarioTransferirQuantidade[MAX_PLAYERS];
new bool:InventarioAberto[MAX_PLAYERS];

new const InventarioSlotTD[INV_SLOTS] =
{
    10, 11, 12, 13, 14, 15, 16, 17, 18, 19,
    20, 21, 22, 23, 24, 25, 26, 27, 28, 39
};

enum E_JBL
{
    bool:jblAtiva, 
    jblDono, 
    jblDonoNome[MAX_PLAYER_NAME], 
    Float:jblX, 
    Float:jblY, 
    Float:jblZ, 
    Float:jblA, 
    jblInterior, 
    jblVW, 
    jblObjeto, 
    Text3D:jblLabel, 
    bool:jblTocando, 
    jblLink[192]                       
};
new JBLInfo[MAX_JBLS][E_JBL];

enum pInfo
{
    pIDFixo, 
    pTemRg, 
    pAdmin, 
    pLevel, 
    pExp, 
    pVip, 
    pEmprego, 
    pCoins, 
    pSkin, 
    pGenero, 
    pIdade, 
    pProcurado, 
    pFome,
    pSede,
    pSono,
    pPreso, 
    pTempoCadeia, 
    pMutado, 
    pTempoMuted, 
    pInterior, 
    pVirtualWorld, 
    Float:pPosX, 
    Float:pPosY, 
    Float:pPosZ, 
    Float:pPosR, 
    Float:pVida, 
    Float:pArmour, 
    pFerido, 
    pTempoFerido, 
    pCelular, 
    pJBL
};
new Player[MAX_PLAYERS][pInfo];

new AdminRegistroID[MAX_PLAYERS];
new AdminRegistroCargo[MAX_PLAYERS];
new AdminRegistroNome[MAX_PLAYERS][MAX_PLAYER_NAME];
new bool:AdminRegistroOnline[MAX_PLAYERS];
new AdminRegistroPlayer[MAX_PLAYERS];
new AdminRegistroPositivo[MAX_PLAYERS];
new AdminRegistroNegativo[MAX_PLAYERS];
new AdminListaSlot[MAX_PLAYERS][MAX_PLAYERS];
new AdmSelecionado[MAX_PLAYERS];
new bool:ReportAtivo[MAX_PLAYERS];
new ReportRespondendo[MAX_PLAYERS];
new ReportTexto[MAX_PLAYERS][256];
new ReportListaSlot[MAX_PLAYERS][MAX_PLAYERS];
new bool:ReportAguardandoAvaliacao[MAX_PLAYERS];
new ReportAvaliacaoAdmin[MAX_PLAYERS];
new ReportAvaliacaoAdminNome[MAX_PLAYERS][MAX_PLAYER_NAME];
new ReportAvaliacaoAdminIDFixo[MAX_PLAYERS];

new gPlayerAnimLibReloaded[MAX_PLAYERS];
new bool:PlayerLogado[MAX_PLAYERS];
new bool:AdmTrabalhando[MAX_PLAYERS];
new TentativasLogin[MAX_PLAYERS];
new VehPublico[MAX_PLAYERS];
new Text3D:LabelIDFixo[MAX_PLAYERS];

new TimerCadeia[MAX_PLAYERS];
new TimerMuted[MAX_PLAYERS];
new TimerFerido[MAX_PLAYERS];
new TimerFome[MAX_PLAYERS];
new TimerSede[MAX_PLAYERS];
new TimerSono[MAX_PLAYERS];

new JBLOuvindo[MAX_PLAYERS];        
new JBLCooldown[MAX_PLAYERS];    
new JBLPlaylistNome[MAX_PLAYERS][MAX_PLAYLISTS][64];
new JBLPlaylistLink[MAX_PLAYERS][MAX_PLAYLISTS][92];
new JBLPlaylistPagina[MAX_PLAYERS];
new JBLPlaylistSelecionada[MAX_PLAYERS];

new T_ChamadaUmSegundo;
new T_ChamadaUmMinuto;

new NomesVeiculos[212][18] =
{
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

stock Float:GetPlayerHealthEx(playerid)
{
    new Float:playerhealth;
    GetPlayerHealth(playerid, playerhealth);
    return playerhealth;
}

stock Float:GetPlayerArmourEx(playerid)
{
    new Float:playerarmor;
    GetPlayerArmour(playerid, playerarmor);
    return playerarmor;
}

stock Float:JBLDistancia(playerid, jblid)
{
    if(!IsPlayerConnected(playerid) || !JBLValida(jblid)) return -1.0;
    if(GetPlayerInterior(playerid) != JBLInfo[jblid][jblInterior]) return -1.0;
    if(GetPlayerVirtualWorld(playerid) != JBLInfo[jblid][jblVW]) return -1.0;
    return GetPlayerDistanceFromPoint(playerid, JBLInfo[jblid][jblX], JBLInfo[jblid][jblY], JBLInfo[jblid][jblZ]);
}

PreloadAnimLib(playerid, animlib[])
{
	ApplyAnimation(playerid, animlib, "null", 0.0, 0, 0, 0, 0, 0, 1);
}

main(){}

public OnGameModeInit()
{
    SetGameModeText("Tropical City v1.0");
    ShowNameTags(0);
    ShowPlayerMarkers(PLAYER_MARKERS_MODE_OFF);
    AllowInteriorWeapons(1);
    UsePlayerPedAnims();
    DisableInteriorEnterExits();
    ManualVehicleEngineAndLights();
    EnableStuntBonusForAll(0);
    
    T_ChamadaUmSegundo = SetTimer("ChamadaUmSegundo", 1000, true);
	T_ChamadaUmMinuto = SetTimer("ChamadaUmMinuto", 60000, true);
	
	CreateDynamicPickup(1318, 1, 1481.0408, -1771.6692, 18.7891);
	CreateDynamicPickup(1254, 1, 870.3837, -25.1340, 63.9656);
	CreateDynamicPickup(1239, 1, 1676.4017, -2309.8730, 13.5472);
	
	CreateDynamic3DTextLabel("{DAA520}Spawn de Motinha\n{FFFFFF}Use o menu de interacao para interagir", -1, 1676.4017, -2309.8730, 13.5472 + 0.50, 15.0);
	CreateDynamic3DTextLabel("{1E90FF}Prefeitura\n{FFFFFF}Use o menu de interacao para entrar", -1, 1481.0408, -1771.6692, 18.7891, 15.0);
	CreateDynamic3DTextLabel("{1E90FF}Mercado Negro\n{FFFFFF}Use o menu de interacao para entrar", -1, 870.3837, -25.1340, 63.9656, 15.0);
	
	CreateDynamicActor(30, 515.9908, -2334.5146, 508.6938, 359.2471);
	CreateDynamicActor(17, 359.7102, 173.5314, 1008.3828, 266.0310);
	
    CasaCarregarTodas();
    EmpresaCarregarTodas();
    AdminRegistroCarregar();
    AtualizarDataHora();
    CarregarMaps();
    return 1;
}

public OnGameModeExit()
{
    sdados();
    DOF2_Exit();
    return 1;
}

public OnPlayerRequestClass(playerid, classid)
{
	for(new i = 0; i < 30; i++)
	{
		SendClientMessage(playerid, -1, "");
	}
    GameTextForPlayer(playerid, "CARREGANDO SERVIDOR...", 3000, 3);
	SetTimerEx("TelaDeCarregamento", 3000, false, "i", playerid);
    TogglePlayerSpectating(playerid, true);
    SetPlayerVirtualWorld(playerid, -555);
    return 1;
}

public OnPlayerConnect(playerid)
{
    ResetarJogador(playerid);
    RemovesObjs(playerid);
    CarregarTextdrawPlayer(playerid);
    return 1;
}

public OnPlayerDisconnect(playerid, reason)
{
	if(AdmTrabalhando[playerid] == true)
	{
	    SetPlayerHealth(playerid, Player[playerid][pVida]);
	    SetPlayerArmour(playerid, Player[playerid][pArmour]);
	}
    if(PlayerLogado[playerid] == true)
	{
	    SalvarConta(playerid);
	    InventarioSalvar(playerid);
	}
    if(TimerCadeia[playerid] != -1)
    {
        KillTimer(TimerCadeia[playerid]);
        TimerCadeia[playerid] = -1;
    }
    if(TimerMuted[playerid] != -1)
    {
        KillTimer(TimerMuted[playerid]);
        TimerMuted[playerid] = -1;
    }
    if(TimerFerido[playerid] != -1)
    {
        KillTimer(TimerFerido[playerid]);
        TimerFerido[playerid] = -1;
    }
    if(MsgTD[playerid] != PlayerText:INVALID_TEXT_DRAW)
	{
		PlayerTextDrawHide(playerid, MsgTD[playerid]);
	    PlayerTextDrawDestroy(playerid, MsgTD[playerid]);
	    MsgTD[playerid] = PlayerText:INVALID_TEXT_DRAW;
	}
	if(VehPublico[playerid] != -1)
    {
        DestroyVehicle(VehPublico[playerid]);
        VehPublico[playerid] = -1;
    }
    if(TimerFome[playerid] != -1)
	{
	    KillTimer(TimerFome[playerid]);
	    TimerFome[playerid] = -1;
	}
	if(TimerSede[playerid] != -1)
	{
	    KillTimer(TimerSede[playerid]);
	    TimerSede[playerid] = -1;
	}
	if(TimerSono[playerid] != -1)
	{
	    KillTimer(TimerSono[playerid]);
	    TimerSono[playerid] = -1;
	}
	AdminRegistroOffline(playerid);
	for(new i = 0; i < MAX_PLAYERS; i++)
	{
	    if(ReportAtivo[i] && ReportRespondendo[i] == playerid)
	        ReportRespondendo[i] = INVALID_PLAYER_ID;
	}
    new jblSaindo = JBLDoDono(playerid);
    if(jblSaindo != -1) JBLGuardar(jblSaindo);
    
    DestruirLabelIDFixo(playerid);
    ResetarJogador(playerid);
    return 1;
}

public OnPlayerSpawn(playerid)
{
	if(!gPlayerAnimLibReloaded[playerid])
	{
	    PreloadAnimLib(playerid, "AIRPORT");
	    PreloadAnimLib(playerid, "Attractors");
	    PreloadAnimLib(playerid, "BAR");
	    PreloadAnimLib(playerid, "BASEBALL");
	    PreloadAnimLib(playerid, "BD_FIRE");
	    PreloadAnimLib(playerid, "benchpress");
	    PreloadAnimLib(playerid, "BF_injection");
	    PreloadAnimLib(playerid, "BIKED");
	    PreloadAnimLib(playerid, "BIKEH");
	    PreloadAnimLib(playerid, "BIKELEAP");
	    PreloadAnimLib(playerid, "BIKES");
	    PreloadAnimLib(playerid, "BIKEV");
	    PreloadAnimLib(playerid, "BIKE_DBZ");
	    PreloadAnimLib(playerid, "BMX");
	    PreloadAnimLib(playerid, "BOX");
	    PreloadAnimLib(playerid, "BSKTBALL");
	    PreloadAnimLib(playerid, "BUDDY");
	    PreloadAnimLib(playerid, "BUS");
	    PreloadAnimLib(playerid, "CAMERA");
	    PreloadAnimLib(playerid, "CAR");
	    PreloadAnimLib(playerid, "CAR_CHAT");
	    PreloadAnimLib(playerid, "CASINO");
	    PreloadAnimLib(playerid, "CHAINSAW");
	    PreloadAnimLib(playerid, "CHOPPA");
	    PreloadAnimLib(playerid, "CLOTHES");
	    PreloadAnimLib(playerid, "COACH");
	    PreloadAnimLib(playerid, "COLT45");
	    PreloadAnimLib(playerid, "COP_DVBYZ");
	    PreloadAnimLib(playerid, "CRIB");
	    PreloadAnimLib(playerid, "DAM_JUMP");
	    PreloadAnimLib(playerid, "DANCING");
	    PreloadAnimLib(playerid, "DILDO");
	    PreloadAnimLib(playerid, "DODGE");
	    PreloadAnimLib(playerid, "DOZER");
	    PreloadAnimLib(playerid, "DRIVEBYS");
	    PreloadAnimLib(playerid, "FAT");
	    PreloadAnimLib(playerid, "FIGHT_B");
	    PreloadAnimLib(playerid, "FIGHT_C");
	    PreloadAnimLib(playerid, "FIGHT_D");
	    PreloadAnimLib(playerid, "FIGHT_E");
	    PreloadAnimLib(playerid, "FINALE");
	    PreloadAnimLib(playerid, "FINALE2");
	    PreloadAnimLib(playerid, "Flowers");
	    PreloadAnimLib(playerid, "FOOD");
	    PreloadAnimLib(playerid, "Freeweights");
	    PreloadAnimLib(playerid, "GANGS");
	    PreloadAnimLib(playerid, "GHANDS");
	    PreloadAnimLib(playerid, "GHETTO_DB");
	    PreloadAnimLib(playerid, "goggles");
	    PreloadAnimLib(playerid, "GRAFFITI");
	    PreloadAnimLib(playerid, "GRAVEYARD");
	    PreloadAnimLib(playerid, "GRENADE");
	    PreloadAnimLib(playerid, "GYMNASIUM");
	    PreloadAnimLib(playerid, "HAIRCUTS");
	    PreloadAnimLib(playerid, "HEIST9");
	    PreloadAnimLib(playerid, "INT_HOUSE");
	    PreloadAnimLib(playerid, "INT_OFFICE");
	    PreloadAnimLib(playerid, "INT_SHOP");
	    PreloadAnimLib(playerid, "JST_BUISNESS");
	    PreloadAnimLib(playerid, "KART");
	    PreloadAnimLib(playerid, "KISSING");
	    PreloadAnimLib(playerid, "KNIFE");
	    PreloadAnimLib(playerid, "LAPDAN1");
	    PreloadAnimLib(playerid, "LAPDAN2");
	    PreloadAnimLib(playerid, "LAPDAN3");
	    PreloadAnimLib(playerid, "LOWRIDER");
	    PreloadAnimLib(playerid, "MD_CHASE");
	    PreloadAnimLib(playerid, "MEDIC");
	    PreloadAnimLib(playerid, "MD_END");
	    PreloadAnimLib(playerid, "MISC");
	    PreloadAnimLib(playerid, "MTB");
	    PreloadAnimLib(playerid, "MUSCULAR");
	    PreloadAnimLib(playerid, "NEVADA");
	    PreloadAnimLib(playerid, "ON_LOOKERS");
	    PreloadAnimLib(playerid, "OTB");
	    PreloadAnimLib(playerid, "PARACHUTE");
	    PreloadAnimLib(playerid, "PARK");
	    PreloadAnimLib(playerid, "PAULNMAC");
	    PreloadAnimLib(playerid, "PED");
	    PreloadAnimLib(playerid, "PLAYER_DVBYS");
	    PreloadAnimLib(playerid, "PLAYIDLES");
	    PreloadAnimLib(playerid, "POLICE");
	    PreloadAnimLib(playerid, "POOL");
	    PreloadAnimLib(playerid, "POOR");
	    PreloadAnimLib(playerid, "PYTHON");
	    PreloadAnimLib(playerid, "QUAD");
	    PreloadAnimLib(playerid, "QUAD_DBZ");
	    PreloadAnimLib(playerid, "RIFLE");
	    PreloadAnimLib(playerid, "RIOT");
	    PreloadAnimLib(playerid, "ROB_BANK");
	    PreloadAnimLib(playerid, "ROCKET");
	    PreloadAnimLib(playerid, "RUSTLER");
	    PreloadAnimLib(playerid, "RYDER");
	    PreloadAnimLib(playerid, "SCRATCHING");
	    PreloadAnimLib(playerid, "SHAMAL");
	    PreloadAnimLib(playerid, "SHOTGUN");
	    PreloadAnimLib(playerid, "SILENCED");
	    PreloadAnimLib(playerid, "SKATE");
	    PreloadAnimLib(playerid, "SPRAYCAN");
	    PreloadAnimLib(playerid, "STRIP");
	    PreloadAnimLib(playerid, "SUNBATHE");
	    PreloadAnimLib(playerid, "SWAT");
	    PreloadAnimLib(playerid, "SWEET");
	    PreloadAnimLib(playerid, "SWIM");
	    PreloadAnimLib(playerid, "SWORD");
	    PreloadAnimLib(playerid, "TANK");
	    PreloadAnimLib(playerid, "TATTOOS");
	    PreloadAnimLib(playerid, "TEC");
	    PreloadAnimLib(playerid, "TRAIN");
	    PreloadAnimLib(playerid, "TRUCK");
	    PreloadAnimLib(playerid, "UZI");
	    PreloadAnimLib(playerid, "VAN");
	    PreloadAnimLib(playerid, "VENDING");
	    PreloadAnimLib(playerid, "VORTEX");
	    PreloadAnimLib(playerid, "WAYFARER");
	    PreloadAnimLib(playerid, "WEAPONS");
	    PreloadAnimLib(playerid, "WUZI");
	    PreloadAnimLib(playerid, "SNM");
	    PreloadAnimLib(playerid, "BLOWJOBZ");
	    PreloadAnimLib(playerid, "SEX");
	    PreloadAnimLib(playerid, "BOMBER");
	    PreloadAnimLib(playerid, "RAPPING");
	    PreloadAnimLib(playerid, "SHOP");
	    PreloadAnimLib(playerid, "BEACH");
	    PreloadAnimLib(playerid, "SMOKING");
	    PreloadAnimLib(playerid, "DEALER");
	    PreloadAnimLib(playerid, "CRACK");
	    PreloadAnimLib(playerid, "CARRY");
	    PreloadAnimLib(playerid, "COP_AMBIENT");
	    gPlayerAnimLibReloaded[playerid] = 1;
	}
    if(Player[playerid][pPreso] == 1)
    {
        TeleportarParaCadeia(playerid);
        return 1;
    }
    if(Player[playerid][pFerido] == 1)
    {
        SetPlayerInterior(playerid, Player[playerid][pInterior]);
        SetPlayerVirtualWorld(playerid, Player[playerid][pVirtualWorld]);
        SetPlayerPos(playerid, Player[playerid][pPosX], Player[playerid][pPosY], Player[playerid][pPosZ]);
        SetPlayerFacingAngle(playerid, Player[playerid][pPosR]);
        SetCameraBehindPlayer(playerid);
        SetPlayerHealth(playerid, 20.0);
        SetPlayerArmour(playerid, 0.0);
        TogglePlayerControllable(playerid, false);

        if(TimerFerido[playerid] != -1)
        {
            KillTimer(TimerFerido[playerid]);
            TimerFerido[playerid] = -1;
        }
        TimerFerido[playerid] = SetTimerEx("CronometroFerido", 1000, true, "i", playerid);
        return 1;
    }
    return 1;
}

public OnPlayerDeath(playerid, killerid, reason)
{
	Player[playerid][pInterior] = GetPlayerInterior(playerid);
    Player[playerid][pVirtualWorld] = GetPlayerVirtualWorld(playerid);
    GetPlayerPos(playerid, Player[playerid][pPosX], Player[playerid][pPosY], Player[playerid][pPosZ]);
    GetPlayerFacingAngle(playerid, Player[playerid][pPosR]);

    if(Player[playerid][pPreso] == 1)
    {
        Player[playerid][pFerido] = 0;
        Player[playerid][pTempoFerido] = 0;
        return 1;
    }
    Player[playerid][pFerido] = 1;
    Player[playerid][pTempoFerido] = 300;
    return 1;
}

public OnPlayerText(playerid, text[])
{
    if(PlayerLogado[playerid] == false || Player[playerid][pFerido] == 1) return 0;
    
    if(Player[playerid][pMutado] == 1)
    {
        new minutos = Player[playerid][pTempoMuted] / 60;
        new segundos = Player[playerid][pTempoMuted] % 60;
        new aviso[96];
        format(aviso, sizeof(aviso), "[MUTE] Voce esta mutado. Tempo restante: %02d:%02d", minutos, segundos);
        SendClientMessage(playerid, COR_ERRO, aviso);
        return 0;
    }
    new string[220];
    if(Player[playerid][pAdmin] > 0 && AdmTrabalhando[playerid] == true)
    {
        format(string, sizeof(string), "{5DADE2}[%s] {FFFFFF}%s: %s", GetCargoAdmName(Player[playerid][pAdmin]), pName(playerid), text);
    }
    else
    {
        format(string, sizeof(string), "{FFFFFF}[%d] %s", Player[playerid][pIDFixo], text);
    }
    ProxDetector(25, playerid, string, -1, -1, -1, -1, -1);
    ApplyAnimation(playerid, "PED", "IDLE_CHAT", 4.1, 0, 1, 1, 1, 1, 1);
    return 0;
}

public OnPlayerEnterVehicle(playerid, vehicleid, ispassenger)
{
    if(!ispassenger)
    {
        foreach(Player, i)
        {
            if(i == playerid) continue;
            if(GetPlayerVehicleID(i) == vehicleid && GetPlayerVehicleSeat(i) == 0)
            {
                new Float:x, Float:y, Float:z;
                GetPlayerPos(playerid, x, y, z);
                SetPlayerPos(playerid, x, y - 0.50, z);
                ClearAnimations(playerid);
                return 1;
            }
        }
    }
    return 1;
}

public OnPlayerStateChange(playerid, newstate, oldstate)
{
    foreach(Player, i)
    {
        if(GetPVarInt(i, "SpecTarget") != playerid) continue;
        AtualizarTVAlvo(i, playerid);
    }
    return 1;
}

public OnPlayerRequestSpawn(playerid)
{
    return 0;
}

public OnPlayerInteriorChange(playerid, newinteriorid, oldinteriorid)
{
    foreach(Player, i)
    {
        if(GetPVarInt(i, "SpecTarget") != playerid) continue;
        SetPlayerInterior(i, newinteriorid);
        SetPlayerVirtualWorld(i, GetPlayerVirtualWorld(playerid));
        AtualizarTVInfo(i, playerid);
    }
    return 1;
}

public OnPlayerKeyStateChange(playerid, newkeys, oldkeys)
{
    if(newkeys & KEY_YES)
    {
        if(GetPlayerState(playerid) == PLAYER_STATE_DRIVER)
        {
            AlternarMotor(playerid, GetPlayerVehicleID(playerid));
        }
        else if(PlayerLogado[playerid] && !InventarioAberto[playerid])
        {
            InventarioAbrir(playerid);
            return 1;
        }
    }
    if(newkeys & KEY_CTRL_BACK)
    {
        if(InteracaoTeclaH(playerid)) return 1;
        else if(EmpresaTeclaH(playerid)) return 1;
        else if(CasaTeclaH(playerid)) return 1;
    }
    return 1;
}

public OnPlayerUpdate(playerid)
{
    static armaTick[MAX_PLAYERS];
    if(GetTickCount() - armaTick[playerid] < 180) return 1;
    armaTick[playerid] = GetTickCount();

    new armaAtual = GetPlayerWeapon(playerid);
    new temAK = 0;
    new temM4 = 0;
    new weaponid, ammo;

    for(new slot = 0; slot < 13; slot++)
    {
        GetPlayerWeaponData(playerid, slot, weaponid, ammo);
        if(ammo <= 0) continue;
        if(weaponid == 30) temAK = 1;
        else if(weaponid == 31) temM4 = 1;
    }
    if(armaAtual == 30 || armaAtual == 31)
    {
        if(IsPlayerAttachedObjectSlotUsed(playerid, 2))
        {
            RemovePlayerAttachedObject(playerid, 2);
        }
        return 1;
    }
    if(temAK || temM4)
    {
        if(!IsPlayerAttachedObjectSlotUsed(playerid, 2))
        {
            if(temAK)
            {
                SetPlayerAttachedObject(playerid, 2, 355, 1, 0.11, 0.26, -0.18, 11.0, -327.0, 174.0, 1.00, 1.00, 1.00);
            }
            else
            {
                SetPlayerAttachedObject(playerid, 2, 356, 1, 0.11, 0.26, -0.18, 11.0, -327.0, 174.0, 1.00, 1.00, 1.00);
            }
        }
    }
    else
    {
        if(IsPlayerAttachedObjectSlotUsed(playerid, 2))
        {
            RemovePlayerAttachedObject(playerid, 2);
        }
    }
    return 1;
}

public OnDialogResponse(playerid, dialogid, response, listitem, inputtext[])
{
    switch(dialogid)
    {
    	case DIALOG_INICIO:
        {
        	if(response)
            {
	        	new str[256];
	        	switch(listitem)
	            {
	                case 0: 
	                {
	                	if(!DOF2_FileExists(Arquivo(playerid))) return ShowPlayerDialog(playerid, DIALOG_INFO_CONTA, DIALOG_STYLE_MSGBOX, "Registre-se", "{FFFFFF}Voce ainda {FF0000}nao possui uma conta registrada{FFFFFF}.\n\nCrie sua conta para poder entrar no servidor.\n\nClique em {00FF00}Voltar{FFFFFF} para retornar e criar sua conta primeiro.", "Voltar", "");
	                    format(str, sizeof(str), "{FFFFFF}Status: {00FF00}Registrado\n{FFFFFF}Nome: {FFFF00}%s\n\n{FFFFFF}Digite sua senha para logar-se:", pName(playerid));
			            ShowPlayerDialog(playerid, DIALOG_LOGAR, DIALOG_STYLE_PASSWORD, "Login", str, "Confirmar", "Cancelar");
	                }
	                case 1: 
	                {
	                	if(DOF2_FileExists(Arquivo(playerid))) return ShowPlayerDialog(playerid, DIALOG_INFO_CONTA, DIALOG_STYLE_MSGBOX, "Logar-se", "{FFFFFF}Voce ja possui uma {00FF00}conta registrada{FFFFFF}.\n\nVoce precisa fazer login para entrar no servidor.\n\nClique em {00FF00}Voltar{FFFFFF} para retornar e logar-se.", "Voltar", "");
	                    format(str, sizeof(str), "{FFFFFF}Status: {FF0000}Nao registrado\n{FFFFFF}Nome: {FFFF00}%s\n\n{FFFFFF}Digite sua senha para logar-se", pName(playerid));
	                    ShowPlayerDialog(playerid, DIALOG_REGISTRO, DIALOG_STYLE_INPUT, "Registro", str, "Confirmar", "Cancelar");
	                }
	            }
	        }
	    }
        case DIALOG_INFO_CONTA:
        {
        	if(response) ShowPlayerDialog(playerid, DIALOG_INICIO, DIALOG_STYLE_LIST, "Tropical City", "{FF0000}[1] {FFFFFF}Logar\n{FF0000}[2] {FFFFFF}Registrar", "Confirmar", "Cancelar");
            if(!response) ShowPlayerDialog(playerid, DIALOG_INICIO, DIALOG_STYLE_LIST, "Tropical City", "{FF0000}[1] {FFFFFF}Logar\n{FF0000}[2] {FFFFFF}Registrar", "Confirmar", "Cancelar");
        }
        case DIALOG_REGISTRO:
        {
            if(!response) return Kick(playerid);

            new str[256];
            if(strlen(inputtext) < 5 || strlen(inputtext) > 16)
            {
                format(str, sizeof(str), "{FFFFFF}Status: {FF0000}Nao registrado\n{FFFFFF}Nome: {FFFF00}%s\n\n{FF0000}Senha deve ter no minimo 5 a 16 caracteres", pName(playerid));
                return ShowPlayerDialog(playerid, DIALOG_REGISTRO, DIALOG_STYLE_INPUT, "Registro", str, "Confirmar", "Cancelar");
            }
            else
            {
                new idFixo = GerarID(playerid);
				if(idFixo <= 0)
				{
					format(str, sizeof(str), "{FF0000}Nao foi possivel gerar seu ID fixo. Tente novamente.\n\n{FFFFFF}Status: {FF0000}Nao registrado\n{FFFFFF}Nome: {FFFF00}%s\n\n{FFFFFF}Digite sua senha para logar-se", pName(playerid));
                    ShowPlayerDialog(playerid, DIALOG_REGISTRO, DIALOG_STYLE_INPUT, "Registro", str, "Confirmar", "Cancelar");
				    return 1;
				}
				SetPVarInt(playerid, "AcabouDeRegistrar", 1);

                new arquivo[64], senhaHash[65];
			    format(arquivo, sizeof(arquivo), "Contas/%s.ini", pName(playerid));		
				SHA256_PassHash(inputtext, "78sdjs86d2h", senhaHash, sizeof(senhaHash));	
			
				DOF2_CreateFile(arquivo);
				DOF2_SetString(arquivo, "SenhaHash", senhaHash);
				DOF2_SetInt(arquivo, "IDFixo", idFixo);
                DOF2_SetInt(arquivo, "Admin", 0);
                DOF2_SetInt(arquivo, "Dinheiro", 1000);
                DOF2_SetInt(arquivo, "Level", 0);
                DOF2_SetInt(arquivo, "Exp", 0);
                DOF2_SetInt(arquivo, "Vip", 0);
                DOF2_SetInt(arquivo, "Coins", 0);
                DOF2_SetInt(arquivo, "TemRg", 0);
                DOF2_SetInt(arquivo, "Emprego", 0);
                DOF2_SetInt(arquivo, "Procurado", 0);
                DOF2_SetInt(arquivo, "Fome", 100);
                DOF2_SetInt(arquivo, "Sede", 100);
                DOF2_SetInt(arquivo, "Sono", 100);
                DOF2_SetInt(arquivo, "Skin", 0);
                DOF2_SetInt(arquivo, "Genero", 0);
                DOF2_SetInt(arquivo, "Idade", 0);
                DOF2_SetInt(arquivo, "Preso", 0);
                DOF2_SetInt(arquivo, "TempoCadeia", 0);
                DOF2_SetInt(arquivo, "Mutado", 0);
                DOF2_SetInt(arquivo, "TempoMuted", 0);
                DOF2_SetInt(arquivo, "Ferido", 0);
                DOF2_SetInt(arquivo, "TempoFerido", 0);
                DOF2_SetFloat(arquivo, "PosX", 1682.8490);
                DOF2_SetFloat(arquivo, "PosY", -2333.3352);
                DOF2_SetFloat(arquivo, "PosZ", 13.5469);
                DOF2_SetFloat(arquivo, "Angulo", 0.0);
                DOF2_SetInt(arquivo, "Interior", 0);
                DOF2_SetInt(arquivo, "VirtualWorld", 0);
                DOF2_SetFloat(arquivo, "Vida", 100.0);
                DOF2_SetFloat(arquivo, "Colete", 0.0);
                DOF2_SetInt(arquivo, "Celular", 0);
                DOF2_SetInt(arquivo, "JBL", 0);
                DOF2_SetInt(arquivo, "KitReparo", 0);
                DOF2_SetInt(arquivo, "Galao", 0);
                DOF2_SetInt(arquivo, "Agua", 0);
                DOF2_SetInt(arquivo, "Refrigerante", 0);
                DOF2_SetInt(arquivo, "Hamburguer", 0);
                DOF2_SetInt(arquivo, "Pizza", 0);
                DOF2_SetInt(arquivo, "Cerveja", 0);
                DOF2_SetInt(arquivo, "Vodka", 0);
                DOF2_SetInt(arquivo, "Whisky", 0);
                DOF2_SaveFile();

                format(str, sizeof(str), "{FFFFFF}Nome: {FFFF00}%s\n\n{FFFFFF}Selecione o genero do seu personagem abaixo:", pName(playerid));
                ShowPlayerDialog(playerid, DIALOG_GENERO, DIALOG_STYLE_MSGBOX, "Genero do Personagem", str, "{00BFFF}Masculino", "{FF00FF}Feminino");
            }
        }
        case DIALOG_GENERO:
        {
            if(response)
            {
                Player[playerid][pGenero] = 1;
                Player[playerid][pSkin] = 23;
            }
            else
            {
                Player[playerid][pGenero] = 2;
                Player[playerid][pSkin] = 41;
            }
            new str[128];
            format(str, sizeof(str), "{FFFFFF}Nome: {FFFF00}%s\n\n{FFFFFF}Digite a idade do seu personagem (18 a 90 anos):", pName(playerid));
            ShowPlayerDialog(playerid, DIALOG_IDADE, DIALOG_STYLE_INPUT, "Idade do Personagem", str, "Confirmar", "Cancelar");
        }
        case DIALOG_IDADE:
        {
            if(!response) return Kick(playerid);

            new str[256];
            if(strval(inputtext) < 18 || strval(inputtext) > 90)
            {
                format(str, sizeof(str), "{FFFFFF}Nome: {FFFF00}%s\n\n{FF0000}ERRO: Idade inválida! Digite um valor entre 18 e 90 anos.\n\n{FFFFFF}Digite a idade do seu personagem:", pName(playerid));
                return ShowPlayerDialog(playerid, DIALOG_IDADE, DIALOG_STYLE_INPUT, "Idade do Personagem", str, "Confirmar", "Cancelar");
            }
            Player[playerid][pIdade] = strval(inputtext);
            DOF2_SetInt(Arquivo(playerid), "Skin", Player[playerid][pSkin]);
            DOF2_SetInt(Arquivo(playerid), "Genero", Player[playerid][pGenero]);
            DOF2_SetInt(Arquivo(playerid), "Idade", Player[playerid][pIdade]);
            DOF2_SaveFile();

            format(str, sizeof(str), "{FFFFFF}Status: {00FF00}Registrado\n{FFFFFF}Nome: {FFFF00}%s\n\n{FFFFFF}Digite sua senha para logar-se:", pName(playerid));
            ShowPlayerDialog(playerid, DIALOG_LOGAR, DIALOG_STYLE_PASSWORD, "Login", str, "Confirmar", "Cancelar");
        }
        case DIALOG_LOGAR:
		{
		    if(!response)
		    {
		        Kick(playerid);
		        return 1;
		    }
		    new str[256];
		    if(strlen(inputtext) < 5 || strlen(inputtext) > 16)
		    {
		        format(str, sizeof(str), "{FFFFFF}Status: {00FF00}Registrado\n{FFFFFF}Nome: {FFFF00}%s\n\n{FF0000}A senha deve possuir entre 5 e 16 caracteres.\n{FFFFFF}Digite sua senha:", pName(playerid));
		        ShowPlayerDialog(playerid, DIALOG_LOGAR, DIALOG_STYLE_PASSWORD, "Login", str, "Confirmar", "Cancelar");
		        return 1;
		    }
		    new senhaHash[65];
			SHA256_PassHash(inputtext, "78sdjs86d2h", senhaHash, sizeof(senhaHash));			
			if(DOF2_IsSet(Arquivo(playerid), "SenhaHash"))
			{
			    new hashSalvo[65];
			    format(hashSalvo, sizeof(hashSalvo), "%s", DOF2_GetString(Arquivo(playerid), "SenhaHash"));			
			    if(strcmp(senhaHash, hashSalvo, false) == 0)
			    {
			        TentativasLogin[playerid] = 0;			        
					new arquivo[64];
				    format(arquivo, sizeof(arquivo), "Contas/%s.ini", pName(playerid));
				    
				    for(new i = 0; i < MAX_PLAYLISTS; i++)
					{
					    new chave[32];
					    format(chave, sizeof(chave), "JBLPlaylistNome_%d", i);
					    format(JBLPlaylistNome[playerid][i], 64, "%s", DOF2_GetString(arquivo, chave));
					    format(chave, sizeof(chave), "JBLPlaylistLink_%d", i);
					    format(JBLPlaylistLink[playerid][i], 92, "%s", DOF2_GetString(arquivo, chave));
					}
				    Player[playerid][pIDFixo] = DOF2_GetInt(arquivo, "IDFixo");
				    Player[playerid][pAdmin] = DOF2_GetInt(arquivo, "Admin");
				    Player[playerid][pLevel] = DOF2_GetInt(arquivo, "Level");
				    Player[playerid][pExp] = DOF2_GetInt(arquivo, "Exp");
				    Player[playerid][pVip] = DOF2_GetInt(arquivo, "Vip");
				    Player[playerid][pCoins] = DOF2_GetInt(arquivo, "Coins");
				    Player[playerid][pTemRg] = DOF2_GetInt(arquivo, "TemRg");
				    Player[playerid][pEmprego] = DOF2_GetInt(arquivo, "Emprego");
				    Player[playerid][pProcurado] = DOF2_GetInt(arquivo, "Procurado");
				    Player[playerid][pFome] = DOF2_GetInt(arquivo, "Fome");
				    Player[playerid][pSede] = DOF2_GetInt(arquivo, "Sede");
				    Player[playerid][pSono] = DOF2_GetInt(arquivo, "Sono");
				    Player[playerid][pSkin] = DOF2_GetInt(arquivo, "Skin");
				    Player[playerid][pGenero] = DOF2_GetInt(arquivo, "Genero");
				    Player[playerid][pIdade] = DOF2_GetInt(arquivo, "Idade");
				    Player[playerid][pPreso] = DOF2_GetInt(arquivo, "Preso");
				    Player[playerid][pTempoCadeia] = DOF2_GetInt(arquivo, "TempoCadeia");
				    Player[playerid][pMutado] = DOF2_GetInt(arquivo, "Mutado");
				    Player[playerid][pTempoMuted] = DOF2_GetInt(arquivo, "TempoMuted");
				    Player[playerid][pFerido] = DOF2_GetInt(arquivo, "Ferido");
				    Player[playerid][pTempoFerido] = DOF2_GetInt(arquivo, "TempoFerido");
				    Player[playerid][pPosX] = DOF2_GetFloat(arquivo, "PosX");
				    Player[playerid][pPosY] = DOF2_GetFloat(arquivo, "PosY");
				    Player[playerid][pPosZ] = DOF2_GetFloat(arquivo, "PosZ");
				    Player[playerid][pPosR] = DOF2_GetFloat(arquivo, "Angulo");
				    Player[playerid][pInterior] = DOF2_GetInt(arquivo, "Interior");
				    Player[playerid][pVirtualWorld] = DOF2_GetInt(arquivo, "VirtualWorld");
				    Player[playerid][pVida] = DOF2_GetFloat(arquivo, "Vida");
				    Player[playerid][pArmour] = DOF2_GetFloat(arquivo, "Colete");
				    Player[playerid][pCelular] = DOF2_GetInt(arquivo, "Celular");
				    Player[playerid][pJBL] = DOF2_GetInt(arquivo, "JBL");
								    
				    if(GetPVarInt(playerid, "AcabouDeRegistrar") == 0)
				    {
				        ShowPlayerDialog(playerid, DIALOG_ESCOLHER_SPAWN, DIALOG_STYLE_LIST, "Local de nascimento", "{FF8C00}1. {FFFFFF}Spawn Civil\n{FF8C00}2. {FFFFFF}Residencia\n{FF8C00}3. {FFFFFF}Ultima posicao", "Selecionar", "Cancelar");
				    }
				    else
				    {
				        SetSpawnPlayer(playerid);
				    }			        
			        return 1;
			    }
			}
			TentativasLogin[playerid]++;
			if(TentativasLogin[playerid] >= 3)
			{
			    SendClientMessage(playerid, COR_ERRO, "[LOGIN] Voce errou a senha 3 vezes. Conexao encerrada.");
			    SetTimerEx("KickSeguro", 100, false, "i", playerid);
			    return 1;
			}
			format(str, sizeof(str), "{FFFFFF}Status: {00FF00}Registrado\n{FFFFFF}Nome: {FFFF00}%s\n\n{FF0000}Senha incorreta!\n{FFFFFF}Tentativa: {FFFF00}%d/3\n\nDigite sua senha novamente:", pName(playerid), TentativasLogin[playerid]);
			ShowPlayerDialog(playerid, DIALOG_LOGAR, DIALOG_STYLE_PASSWORD, "Login", str, "Confirmar", "Cancelar");
		}
        case DIALOG_TPTODOS:
        {
            if(!response)
            {
                DeletePVar(playerid, "ID_Adm_Tp");
                return 1;
            }
            else
            {
                new admid = GetPVarInt(playerid, "ID_Adm_Tp");
                if(!IsPlayerConnected(admid)) return SendClientMessage(playerid, COR_ERRO, "[ERRO] O Administrador deslogou.");
                new Float:Pos[3];
                GetPlayerPos(admid, Pos[0], Pos[1], Pos[2]);
                SetPlayerPos(playerid, Pos[0]+1.0, Pos[1]+1.0, Pos[2]);
                SetPlayerInterior(playerid, GetPlayerInterior(admid));
                SetPlayerVirtualWorld(playerid, GetPlayerVirtualWorld(admid));
                DeletePVar(playerid, "ID_Adm_Tp");
                return 1;
            }
        }
        case DIALOG_TELEMAP:
        {
            if(!response) return 1;
            if(response)
            {
                new Float:Pos[3];
                Pos[0] = GetPVarFloat(playerid, "TeleX");
                Pos[1] = GetPVarFloat(playerid, "TeleY");
                Pos[2] = GetPVarFloat(playerid, "TeleZ");

                if(IsPlayerInAnyVehicle(playerid))
                {
                    SetVehiclePos(GetPlayerVehicleID(playerid), Pos[0], Pos[1], Pos[2]);
                }
                else
                {
                    SetPlayerPos(playerid, Pos[0], Pos[1], Pos[2]);
                }
                return 1;
            }
        }
        case DIALOG_CASA_INTERACAO:
        {
            if(!response) return 1;
            new casaid = GetPVarInt(playerid, "CasaProxima");
            if(!CasaValida(casaid)) return 1;
            if(!CasaPertoDaEntrada(playerid, casaid, 3.0)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce se afastou da residencia.");
            if(CasaInfo[casaid][casaOcupada] == true) return SendClientMessage(playerid, COR_ERRO, "[CASA] Esta residencia ja foi vendida.");

            switch(listitem)
            {
                case 0: CasaMostrarInfo(playerid, casaid);
                case 1: CasaEntrar(playerid, casaid);
                case 2:
                {
                    if(CasaJogadorPossuiCasa(playerid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja possui uma residencia.");
                    if(CasaJogadorMorador(playerid) != -1) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja e morador de outra residencia. Saia dela primeiro.");

                    new string[256];
                    format(string, sizeof(string), "{FFFFFF}Residencia: {DC143C}#%d\n{FFFFFF}Classe: {DC143C}%s\n{FFFFFF}Valor: {00FF00}$%d\n{FFFFFF}Seu dinheiro: {00FF00}$%d\n\n{FFFFFF}Confirmar a compra?", 
                        casaid, NomeClasseCasa[CasaInfo[casaid][casaClasse]], CasaInfo[casaid][casaValor], GetPlayerCash(playerid));
                    ShowPlayerDialog(playerid, DIALOG_CASA_COMPRAR, DIALOG_STYLE_MSGBOX, "Confirmar compra", string, "Comprar", "Cancelar");
                }
            }
            return 1;
        }
        case DIALOG_CASA_COMPRAR:
        {
            if(!response) return CasaMostrarInteracao(playerid, GetPVarInt(playerid, "CasaProxima"));
            new casaid = GetPVarInt(playerid, "CasaProxima");
            if(!CasaValida(casaid)) return 1;
            CasaComprar(playerid, casaid);
            return 1;
        }
        case DIALOG_CASA_MENU:
        {
            if(!response) return 1;
            new casaid = CasaNoInterior(playerid);
            if(casaid == -1) return 1;
            if(!CasaEhDono(playerid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce nao e o proprietario desta residencia.");

            new string[256];
            switch(listitem)
            {
                case 0: CasaMostrarInfo(playerid, casaid);
                case 1:
                {
                    CasaInfo[casaid][casaTrancada] = !CasaInfo[casaid][casaTrancada];
                    CasaAtualizarVisual(casaid);
                    CasaSalvar(casaid);
                    SendClientMessage(playerid, COR_SUCESSO, (CasaInfo[casaid][casaTrancada]) ? ("[CASA] Residencia trancada.") : ("[CASA] Residencia destrancada."));
                    CasaMostrarMenu(playerid, casaid);
                }
                case 2: CasaMostrarMoradores(playerid, casaid);
                case 3:
                {
                    format(string, sizeof(string), "{FFFFFF}Voce vai vender a residencia {DC143C}#%d {FFFFFF}para o Estado.\n\n{FFFFFF}Voce recebe: {00FF00}$%d\n{FFFFFF}Valor de mercado: {FFFF00}$%d\n\n{FF0000}Todos os moradores serao removidos.\n{FFFFFF}Deseja confirmar?", 
                        casaid, CasaInfo[casaid][casaValor] / 2, CasaInfo[casaid][casaValor]);
                    ShowPlayerDialog(playerid, DIALOG_CASA_VENDER_ESTADO, DIALOG_STYLE_MSGBOX, "Vender para o Estado", string, "Vender", "Cancelar");
                }
                case 4:
                {
                    ShowPlayerDialog(playerid, DIALOG_CASA_VENDER_JOGADOR, DIALOG_STYLE_INPUT, "Vender para um jogador", "{FFFFFF}Digite o {DC143C}ID fixo {FFFFFF}do jogador que vai comprar sua residencia.\n{AFAFAF}Ele precisa estar online e aceitar a oferta em 60 segundos.", "Enviar", "Voltar");
                }
            }
            return 1;
        }
        case DIALOG_CASA_INFO:
        {
            if(!response) return 1;
            new casaid = CasaNoInterior(playerid);
            if(casaid != -1 && CasaEhDono(playerid, casaid)) return CasaMostrarMenu(playerid, casaid);
            casaid = GetPVarInt(playerid, "CasaProxima");
            if(CasaValida(casaid) && !CasaInfo[casaid][casaOcupada] && CasaPertoDaEntrada(playerid, casaid, 5.0))
                return CasaMostrarInteracao(playerid, casaid);
            return 1;
        }
        case DIALOG_CASA_VENDER_ESTADO:
        {
            if(!response) return CasaMostrarMenu(playerid, CasaNoInterior(playerid));
            new casaid = CasaNoInterior(playerid);
            if(casaid == -1) return 1;
            if(!CasaEhDono(playerid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce nao e o proprietario desta residencia.");

            new valorVenda = CasaInfo[casaid][casaValor] / 2;
            new Float:x = CasaInfo[casaid][casaExtX];
            new Float:y = CasaInfo[casaid][casaExtY];
            new Float:z = CasaInfo[casaid][casaExtZ];
            new Float:a = CasaInfo[casaid][casaExtA];

            sGivePlayerCash(playerid, valorVenda);
			CasaLiberar(casaid);

            SetPlayerInterior(playerid, 0);
            SetPlayerVirtualWorld(playerid, 0);
            SetPlayerPos(playerid, x, y, z);
            SetPlayerFacingAngle(playerid, a);

            new string[128];
            format(string, sizeof(string), "[CASA] Residencia vendida ao Estado. Voce recebeu $%d.", valorVenda);
            SendClientMessage(playerid, COR_SUCESSO, string);
            return 1;
        }
        case DIALOG_CASA_VENDER_JOGADOR:
        {
            if(!response) return CasaMostrarMenu(playerid, CasaNoInterior(playerid));
            new casaid = CasaNoInterior(playerid);
            if(casaid == -1) return 1;
            if(!CasaEhDono(playerid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce nao e o proprietario desta residencia.");
            new idFixoAlvo;
            if(sscanf(inputtext, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[CASA] ID fixo invalido.");
            new targetid = ProcurarPorIDFixo(idFixoAlvo);
            if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[CASA] Nenhum jogador online com esse ID fixo.");
            if(!IsPlayerConnected(targetid) || !PlayerLogado[targetid]) return SendClientMessage(playerid, COR_ERRO, "[CASA] Jogador nao encontrado.");
            if(targetid == playerid) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce nao pode vender para si mesmo.");
            if(CasaJogadorPossuiCasa(targetid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este jogador ja possui uma residencia.");
            if(CasaJogadorMorador(targetid) != -1) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este jogador ja e morador de outra residencia.");
            if(GetPVarInt(targetid, "CasaOfertaTempo") > gettime()) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este jogador ja esta analisando outra oferta.");

            SetPVarInt(targetid, "CasaOfertaID", casaid);
            SetPVarInt(targetid, "CasaOfertaVendedor", playerid);
            SetPVarInt(targetid, "CasaOfertaValor", CasaInfo[casaid][casaValor]);
            SetPVarInt(targetid, "CasaOfertaTempo", gettime() + 60);
            SetPVarString(targetid, "CasaOfertaVendedorNome", CasaInfo[casaid][casaDono]);

            new str[400];
            format(str, sizeof(str), "{FFFFFF}Vendedor: {FFFF00}%s\n\n{FFFFFF}Residencia: {DC143C}#%d\n{FFFFFF}Classe: {DC143C}%s\n{FFFFFF}Valor: {00FF00}$%d\n{FFFFFF}Seu dinheiro: {00FF00}$%d\n\n{AFAFAF}A oferta expira em %d segundos.\n{FFFFFF}Deseja aceitar?", 
                pName(playerid), casaid, NomeClasseCasa[CasaInfo[casaid][casaClasse]], CasaInfo[casaid][casaValor], GetPlayerCash(targetid), 60);
            ShowPlayerDialog(targetid, DIALOG_CASA_VENDER_CONFIRMA, DIALOG_STYLE_MSGBOX, "Oferta de residencia", str, "Aceitar", "Recusar");
            SendClientMessage(playerid, COR_SUCESSO, "[CASA] Oferta enviada. Aguarde a resposta do jogador.");
            return 1;
        }
        case DIALOG_CASA_VENDER_CONFIRMA:
        {
            new casaid = GetPVarInt(playerid, "CasaOfertaID");
            new vendedorid = GetPVarInt(playerid, "CasaOfertaVendedor");
            new valor = GetPVarInt(playerid, "CasaOfertaValor");
            new tempo = GetPVarInt(playerid, "CasaOfertaTempo");
            new vendedorNome[MAX_PLAYER_NAME];
            GetPVarString(playerid, "CasaOfertaVendedorNome", vendedorNome, sizeof(vendedorNome));

            DeletePVar(playerid, "CasaOfertaID");
            DeletePVar(playerid, "CasaOfertaVendedor");
            DeletePVar(playerid, "CasaOfertaValor");
            DeletePVar(playerid, "CasaOfertaTempo");
            DeletePVar(playerid, "CasaOfertaVendedorNome");

            if(!response)
            {
                if(IsPlayerConnected(vendedorid)) SendClientMessage(vendedorid, COR_ERRO, "[CASA] O jogador recusou a sua oferta.");
                return 1;
            }
            if(gettime() > tempo) return SendClientMessage(playerid, COR_ERRO, "[CASA] Esta oferta expirou.");
            if(!CasaValida(casaid)) return 1;
            if(!IsPlayerConnected(vendedorid) || !PlayerLogado[vendedorid]) return SendClientMessage(playerid, COR_ERRO, "[CASA] O vendedor nao esta mais disponivel.");
            if(strcmp(CasaInfo[casaid][casaDono], vendedorNome, true) != 0) return SendClientMessage(playerid, COR_ERRO, "[CASA] Esta oferta nao e mais valida.");
            if(!CasaEhDono(vendedorid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] O vendedor nao e mais o proprietario.");
            if(CasaNoInterior(vendedorid) != casaid) return SendClientMessage(playerid, COR_ERRO, "[CASA] O vendedor saiu da residencia. Negocio cancelado.");
            if(!EstaPertoDoJogador(playerid, vendedorid, 3.0)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce esta muito longe do vendedor.");
            if(valor != CasaInfo[casaid][casaValor]) return SendClientMessage(playerid, COR_ERRO, "[CASA] O valor da residencia mudou. Negocio cancelado.");
            if(CasaJogadorPossuiCasa(playerid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja possui uma residencia.");
            if(CasaJogadorMorador(playerid) != -1) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja e morador de outra residencia.");
            if(GetPlayerCash(playerid) < valor)
            {
                SendClientMessage(playerid, COR_ERRO, "[CASA] Dinheiro insuficiente.");
                return SendClientMessage(vendedorid, COR_ERRO, "[CASA] O jogador nao tinha dinheiro suficiente.");
            }
            sGivePlayerCash(playerid, -valor);
            sGivePlayerCash(vendedorid, valor);

            CasaExpulsarTodos(casaid, playerid);
            format(CasaInfo[casaid][casaDono], MAX_PLAYER_NAME, "%s", pName(playerid));
            for(new i = 0;i < MAX_MORADORES_CASA;i++) CasaMorador[casaid][i][0] = EOS;
            CasaInfo[casaid][casaTrancada] = false;
            CasaRecontarMoradores(casaid);
            CasaAtualizarVisual(casaid);
            CasaSalvar(casaid);

            new string[144];
            format(string, sizeof(string), "[CASA] Voce adquiriu a residencia #%d por $%d.", casaid, valor);
            SendClientMessage(playerid, COR_SUCESSO, string);
            format(string, sizeof(string), "[CASA] %s aceitou a oferta. Voce recebeu $%d.", pName(playerid), valor);
            SendClientMessage(vendedorid, COR_SUCESSO, string);
            return 1;
        }
        case DIALOG_CASA_MORADORES:
        {
            new casaid = CasaNoInterior(playerid);
            if(casaid == -1) return 1;
            if(!response) return CasaMostrarMenu(playerid, casaid);
            if(!CasaEhDono(playerid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce nao e o proprietario desta residencia.");
            if(listitem < 0 || listitem >= MAX_MORADORES_CASA) return 1;

            SetPVarInt(playerid, "CasaSlotMorador", listitem);
            if(CasaMorador[casaid][listitem][0] == EOS)
            {
                ShowPlayerDialog(playerid, DIALOG_CASA_MORADOR_ADD, DIALOG_STYLE_INPUT, "Adicionar morador", "{FFFFFF}Digite o {DC143C}ID fixo {FFFFFF}do jogador que voce quer convidar.\n{AFAFAF}Ele precisa estar online e aceitar em 60 segundos.", "Convidar", "Voltar");
            }
            else
            {
                new str[256];
                format(str, sizeof(str), "{FFFFFF}Slot: {DC143C}%d\n{FFFFFF}Morador: {FFFF00}%s\n\n{FF0000}Deseja expulsar este morador da residencia?", listitem + 1, CasaMorador[casaid][listitem]);
                ShowPlayerDialog(playerid, DIALOG_CASA_MORADOR_OPCOES, DIALOG_STYLE_MSGBOX, "Opcoes do morador", str, "Expulsar", "Voltar");
            }
            return 1;
        }
        case DIALOG_CASA_MORADOR_ADD:
        {
            new casaid = CasaNoInterior(playerid);
            if(casaid == -1) return 1;
            if(!response) return CasaMostrarMoradores(playerid, casaid);
            if(!CasaEhDono(playerid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce nao e o proprietario desta residencia.");
            CasaRecontarMoradores(casaid);
            if(CasaInfo[casaid][casaTotalMoradores] >= MAX_MORADORES_CASA) return SendClientMessage(playerid, COR_ERRO, "[CASA] Limite de moradores atingido.");
            new slot = GetPVarInt(playerid, "CasaSlotMorador");
            if(slot < 0 || slot >= MAX_MORADORES_CASA) return 1;
            if(CasaMorador[casaid][slot][0] != EOS) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este slot ja esta ocupado.");
            new idFixoAlvo;
            if(sscanf(inputtext, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[CASA] ID fixo invalido.");
            new targetid = ProcurarPorIDFixo(idFixoAlvo);
            if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[CASA] Nenhum jogador online com esse ID fixo.");
            if(!IsPlayerConnected(targetid) || !PlayerLogado[targetid]) return SendClientMessage(playerid, COR_ERRO, "[CASA] Jogador nao encontrado.");
            if(targetid == playerid) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja e o proprietario.");
            if(CasaJogadorPossuiCasa(targetid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este jogador ja possui uma residencia.");
            if(CasaJogadorMorador(targetid) != -1) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este jogador ja e morador de outra residencia.");
            if(GetPVarInt(targetid, "CasaConviteTempo") > gettime()) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este jogador ja tem um convite pendente.");

            SetPVarInt(targetid, "CasaConviteID", casaid);
            SetPVarInt(targetid, "CasaConviteSlot", slot);
            SetPVarInt(targetid, "CasaConviteTempo", gettime() + 60);
            SetPVarString(targetid, "CasaConviteDono", CasaInfo[casaid][casaDono]);

            new str[320];
            format(str, sizeof(str), "{FFFFFF}Proprietario: {FFFF00}%s\n{FFFFFF}Residencia: {DC143C}#%d\n{FFFFFF}Classe: {DC143C}%s\n\n{AFAFAF}O convite expira em %d segundos.\n{FFFFFF}Deseja ser morador desta residencia?", 
                pName(playerid), casaid, NomeClasseCasa[CasaInfo[casaid][casaClasse]], 60);
            ShowPlayerDialog(targetid, DIALOG_CASA_MORADOR_CONVITE, DIALOG_STYLE_MSGBOX, "Convite de residencia", str, "Aceitar", "Recusar");
            SendClientMessage(playerid, COR_SUCESSO, "[CASA] Convite enviado ao jogador.");
            return 1;
        }
        case DIALOG_CASA_MORADOR_CONVITE:
        {
            new casaid = GetPVarInt(playerid, "CasaConviteID");
            new slot = GetPVarInt(playerid, "CasaConviteSlot");
            new tempo = GetPVarInt(playerid, "CasaConviteTempo");
            new donoConvite[MAX_PLAYER_NAME];
            GetPVarString(playerid, "CasaConviteDono", donoConvite, sizeof(donoConvite));

            DeletePVar(playerid, "CasaConviteID");
            DeletePVar(playerid, "CasaConviteSlot");
            DeletePVar(playerid, "CasaConviteTempo");
            DeletePVar(playerid, "CasaConviteDono");

            if(!response) return 1;
            if(gettime() > tempo) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este convite expirou.");
            if(!CasaValida(casaid)) return 1;
            if(slot < 0 || slot >= MAX_MORADORES_CASA) return 1;
            if(!CasaInfo[casaid][casaOcupada]) return SendClientMessage(playerid, COR_ERRO, "[CASA] Esta residencia nao tem mais proprietario.");
            if(strcmp(CasaInfo[casaid][casaDono], donoConvite, true) != 0) return SendClientMessage(playerid, COR_ERRO, "[CASA] O proprietario mudou. Convite cancelado.");
            if(CasaMorador[casaid][slot][0] != EOS) return SendClientMessage(playerid, COR_ERRO, "[CASA] Este slot ja foi ocupado.");
            if(CasaJogadorPossuiCasa(playerid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja possui uma residencia.");
            if(CasaJogadorMorador(playerid) != -1) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja e morador de outra residencia.");
            CasaRecontarMoradores(casaid);
            if(CasaInfo[casaid][casaTotalMoradores] >= MAX_MORADORES_CASA) return SendClientMessage(playerid, COR_ERRO, "[CASA] Limite de moradores atingido.");

            format(CasaMorador[casaid][slot], MAX_PLAYER_NAME, "%s", pName(playerid));
            CasaRecontarMoradores(casaid);
            CasaSalvar(casaid);

            new string[144];
            format(string, sizeof(string), "[CASA] Voce agora e morador da residencia #%d.", casaid);
            SendClientMessage(playerid, COR_SUCESSO, string);

            new donoid = ProcurarPorNomeDono(casaid);
            if(donoid != INVALID_PLAYER_ID)
            {
                format(string, sizeof(string), "[CASA] %s aceitou o convite e agora e morador da sua residencia.", pName(playerid));
                SendClientMessage(donoid, COR_SUCESSO, string);
            }
            return 1;
        }
        case DIALOG_CASA_MORADOR_OPCOES:
        {
            new casaid = CasaNoInterior(playerid);
            if(casaid == -1) return 1;
            if(!response) return CasaMostrarMoradores(playerid, casaid);
            if(!CasaEhDono(playerid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce nao e o proprietario desta residencia.");

            new slot = GetPVarInt(playerid, "CasaSlotMorador");
            if(slot < 0 || slot >= MAX_MORADORES_CASA) return 1;
            if(CasaMorador[casaid][slot][0] == EOS) return CasaMostrarMoradores(playerid, casaid);

            new nomeMorador[MAX_PLAYER_NAME];
            format(nomeMorador, sizeof(nomeMorador), "%s", CasaMorador[casaid][slot]);
            CasaMorador[casaid][slot][0] = EOS;
            CasaRecontarMoradores(casaid);
            CasaSalvar(casaid);

            new string[144];
            format(string, sizeof(string), "[CASA] %s foi removido dos moradores da residencia.", nomeMorador);
            SendClientMessage(playerid, COR_SUCESSO, string);

            for(new i = 0, j = GetPlayerPoolSize();i <= j;i++)
            {
                if(!IsPlayerConnected(i)) continue;
                if(strcmp(pName(i), nomeMorador, true) != 0) continue;
                SendClientMessage(i, COR_ERRO, "[CASA] Voce foi removido dos moradores da residencia.");
                if(CasaNoInterior(i) == casaid) CasaSair(i, casaid);
                break;
            }
            CasaMostrarMoradores(playerid, casaid);
            return 1;
        }
        case DIALOG_ESCOLHER_SPAWN:
        {
            if(!response) return Kick(playerid);            
            switch(listitem)
            {
                case 0:
                {
                    Player[playerid][pInterior] = 0;
                    Player[playerid][pVirtualWorld] = 0;
                    Player[playerid][pPosX] = 1682.8490;
                    Player[playerid][pPosY] = -2333.3352;
                    Player[playerid][pPosZ] = 13.5469;
                    Player[playerid][pPosR] = 0.0;
                }
                case 1:
                {
                    new casaid = CasaBuscarPorDono(playerid);
                    if(casaid == -1) casaid = CasaJogadorMorador(playerid);
                    if(casaid == -1)
                    {
                        SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao possui uma residencia e nao e morador de nenhuma casa!");
                        return ShowPlayerDialog(playerid, DIALOG_ESCOLHER_SPAWN, DIALOG_STYLE_LIST, "Local de nascimento", "{FF8C00}1. {FFFFFF}Spawn Civil\n{FF8C00}2. {FFFFFF}Residencia\n{FF8C00}3. {FFFFFF}Ultima posicao", "Selecionar", "Cancelar");
                    }
                    new classe = CasaInfo[casaid][casaClasse];
                    if(!CasaClasseValida(classe)) classe = 0;
                    Player[playerid][pInterior] = InteriorClasseCasa[classe];
                    Player[playerid][pVirtualWorld] = CASA_VW_BASE + casaid;
                    Player[playerid][pPosX] = PosIntClasseCasa[classe][0];
                    Player[playerid][pPosY] = PosIntClasseCasa[classe][1];
                    Player[playerid][pPosZ] = PosIntClasseCasa[classe][2];
                    Player[playerid][pPosR] = 0.0;
                }
            }
            SetSpawnPlayer(playerid);
            return 1;
        }
        case DIALOG_EMPRESA_INTERACAO:
        {
            if(!response) return 1;
            new empresaid = GetPVarInt(playerid, "EmpresaProxima");
            if(!EmpresaValida(empresaid)) return 1;
            if(!IsPlayerInRangeOfPoint(playerid, 3.0, EmpresaInfo[empresaid][empresaExtX], EmpresaInfo[empresaid][empresaExtY], EmpresaInfo[empresaid][empresaExtZ])) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce esta longe da empresa.");
            if(listitem == 0) return EmpresaEntrar(playerid, empresaid);
            if(listitem == 1)
            {
                if(!EmpresaInfo[empresaid][empresaOcupada])
                {
                    new texto[300];
                    format(texto, sizeof(texto), "{FFFFFF}Empresa: {DC143C}%s\n{FFFFFF}Valor: {00FF00}$%d\n{FFFFFF}Seu dinheiro: {00FF00}$%d\n\n{FFFFFF}Deseja comprar esta empresa?", 
                        EmpresaNomeTipo(EmpresaInfo[empresaid][empresaTipo]), EmpresaInfo[empresaid][empresaValor], GetPlayerCash(playerid));
                    return ShowPlayerDialog(playerid, DIALOG_EMPRESA_COMPRAR, DIALOG_STYLE_MSGBOX, "Comprar Empresa", texto, "Comprar", "Cancelar");
                }
                if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao e o proprietario.");
                return EmpresaAbrirMenu(playerid, empresaid);
            }
            return 1;
        }
        case DIALOG_EMPRESA_COMPRAR:
        {
            if(!response) return 1;
            new empresaid = GetPVarInt(playerid, "EmpresaProxima");
            if(!EmpresaValida(empresaid)) return 1;
            if(EmpresaInfo[empresaid][empresaOcupada]) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Esta empresa ja foi comprada.");
            if(!IsPlayerInRangeOfPoint(playerid, 5.0, EmpresaInfo[empresaid][empresaExtX], EmpresaInfo[empresaid][empresaExtY], EmpresaInfo[empresaid][empresaExtZ])) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce esta longe da empresa.");
            if(EmpresaBuscarPorDono(playerid) != -1) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce ja possui uma empresa.");
            if(GetPlayerCash(playerid) < EmpresaInfo[empresaid][empresaValor]) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Dinheiro insuficiente.");

            sGivePlayerCash(playerid, -EmpresaInfo[empresaid][empresaValor]);
            EmpresaInfo[empresaid][empresaOcupada] = true;
            EmpresaInfo[empresaid][empresaSaldo] = 0;
            format(EmpresaInfo[empresaid][empresaDono], MAX_PLAYER_NAME, "%s", pName(playerid));
            EmpresaInicializarEstoque(empresaid);
            EmpresaInfo[empresaid][empresaSemana] = EmpresaSemanaAtual();
            EmpresaInfo[empresaid][empresaVendasSemana] = 0;
            for(new i = 0; i < 7; i++) EmpresaInfo[empresaid][empresaVendasDia][i] = 0;
            
            EmpresaAtualizarVisual(empresaid);
            EmpresaSalvar(empresaid);

            SendClientMessage(playerid, COR_SUCESSO, "[EMPRESA] Empresa comprada com sucesso. Entre e use /menuempresa.");
            return 1;
        }
        case DIALOG_EMPRESA_MENU:
        {
            if(!response) return 1;
            new empresaid = GetPVarInt(playerid, "EmpresaMenuID");
            if(!EmpresaValida(empresaid)) return 1;
            if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao esta mais dentro desta empresa.");
            if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao e o proprietario.");
            switch(listitem)
            {
                case 0: 
                {
                    new texto[600];
                    format(texto, sizeof(texto), 
                        "{C8C8C8}Campo\t{C8C8C8}Valor\n\
                        {FFFFFF}Empresa\t{DC143C}%s\n\
                        {FFFFFF}ID\t{FFFFFF}#%d\n\
                        {FFFFFF}Proprietario\t{FFFFFF}%s\n\
                        {FFFFFF}Valor de compra\t{00FF00}$%d\n\
                        {FFFFFF}Cofre\t{00FF00}$%d\n\
                        {FFFFFF}Faturamento semanal\t{00FF00}$%d\n\
                        {FFFFFF}Produtos\t{FFFFFF}%d itens", 
                        EmpresaNomeTipo(EmpresaInfo[empresaid][empresaTipo]), empresaid, EmpresaInfo[empresaid][empresaDono], 
                        EmpresaInfo[empresaid][empresaValor], EmpresaInfo[empresaid][empresaSaldo], 
                        EmpresaInfo[empresaid][empresaVendasSemana], EmpresaTotalProdutos(EmpresaInfo[empresaid][empresaTipo]));

                    ShowPlayerDialog(playerid, DIALOG_EMPRESA_INFO, DIALOG_STYLE_TABLIST_HEADERS, "Informacoes da Empresa", texto, "Voltar", "Fechar");
                }
                case 1: 
                {
                    new texto[700];
                    format(texto, sizeof(texto), 
                        "{C8C8C8}Dia\t{C8C8C8}Faturamento\n\
                        {FFFFFF}Segunda\t{00FF00}$%d\n{FFFFFF}Terca\t{00FF00}$%d\n{FFFFFF}Quarta\t{00FF00}$%d\n\
                        {FFFFFF}Quinta\t{00FF00}$%d\n{FFFFFF}Sexta\t{00FF00}$%d\n{FFFFFF}Sabado\t{00FF00}$%d\n{FFFFFF}Domingo\t{00FF00}$%d\n\
                        {DC143C}Total da semana\t{00FF00}$%d\n{DC143C}Cofre atual\t{00FF00}$%d", 
                        EmpresaInfo[empresaid][empresaVendasDia][0], EmpresaInfo[empresaid][empresaVendasDia][1], 
                        EmpresaInfo[empresaid][empresaVendasDia][2], EmpresaInfo[empresaid][empresaVendasDia][3], 
                        EmpresaInfo[empresaid][empresaVendasDia][4], EmpresaInfo[empresaid][empresaVendasDia][5], 
                        EmpresaInfo[empresaid][empresaVendasDia][6], EmpresaInfo[empresaid][empresaVendasSemana], 
                        EmpresaInfo[empresaid][empresaSaldo]);

                    ShowPlayerDialog(playerid, DIALOG_EMPRESA_FINANCEIRO, DIALOG_STYLE_TABLIST_HEADERS, "Financeiro", texto, "Voltar", "Fechar");
                }
                case 2: EmpresaAbrirEstoque(playerid, empresaid);      
                case 3: EmpresaAbrirReabastecer(playerid, empresaid);
                case 4: 
                {
                    new texto[300];
                    format(texto, sizeof(texto), "{FFFFFF}Disponivel no cofre: {00FF00}$%d\n\n{FFFFFF}Digite o valor que deseja sacar:", EmpresaInfo[empresaid][empresaSaldo]);
                    ShowPlayerDialog(playerid, DIALOG_EMPRESA_SACAR, DIALOG_STYLE_INPUT, "Sacar da Empresa", texto, "Sacar", "Voltar");
                }
                case 5: 
                {
                    new texto[320];
                    format(texto, sizeof(texto), "{FFFFFF}Empresa: {DC143C}%s\n{FFFFFF}Voce recebera {00FF00}$%d{FFFFFF} pela venda ao Estado.\n{FF6347}Todo o estoque sera perdido.\n\n{FFFFFF}Deseja continuar?", 
                        EmpresaNomeTipo(EmpresaInfo[empresaid][empresaTipo]), (EmpresaInfo[empresaid][empresaValor] / 2) + EmpresaInfo[empresaid][empresaSaldo]);
                    ShowPlayerDialog(playerid, DIALOG_EMPRESA_VENDER, DIALOG_STYLE_MSGBOX, "Vender Empresa", texto, "Vender", "Voltar");
                }
                case 6:
				{
				    ShowPlayerDialog(playerid, DIALOG_EMPRESA_VENDER_JOGADOR, DIALOG_STYLE_INPUT, "Vender Empresa", "{FFFFFF}Digite o ID fixo do jogador que deseja comprar sua empresa:", "Enviar", "Voltar");
				}
            }
            return 1;
        }
        case DIALOG_EMPRESA_INFO, DIALOG_EMPRESA_FINANCEIRO, DIALOG_EMPRESA_ESTOQUE:
        {
            if(!response) return 1;
            new empresaid = GetPVarInt(playerid, "EmpresaMenuID");
            if(EmpresaValida(empresaid) && EmpresaEhDono(playerid, empresaid) && EmpresaNoInterior(playerid) == empresaid) EmpresaAbrirMenu(playerid, empresaid);
            return 1;
        }
        case DIALOG_EMPRESA_ROUPA:
		{
		    new empresaid = GetPVarInt(playerid, "EmpresaLojaID");
		    if(!response)
		    {
		        DeletePVar(playerid, "EmpresaProduto");
		        return 1;
		    }
		    if(!EmpresaValida(empresaid)) return 1;
		    if(EmpresaNoInterior(playerid) != empresaid || !EmpresaNoBalcao(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Acao invalida.");
		
		    new skin = strval(inputtext);
		    if(skin < 0 || skin > 311)
		    {
		        return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Skin invalida (0 a 311).");
		    }
		    return EmpresaProcessarCompra(playerid, empresaid, PROD_ROUPA, 1, skin);
		}
        case DIALOG_EMPRESA_REABASTECER:
		{
		    new empresaid = GetPVarInt(playerid, "EmpresaMenuID");
		    if(!response) return EmpresaAbrirMenu(playerid, empresaid);
		    if(!EmpresaValida(empresaid)) return 1;
		    if(EmpresaNoInterior(playerid) != empresaid || !EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Acao invalida.");
		
		    new tipo = EmpresaInfo[empresaid][empresaTipo];
		    if(!EmpresaTipoValido(tipo)) return 1;
		
		    new total = EmpresaTotalProdutos(tipo);
		    new totalFalta = 0;
		    new custoTotal = 0;
		
		    for(new i = 0; i < total; i++)
		    {
		        new produto = EmpresaCatalogo[tipo][i];
		        new falta = MAX_ESTOQUE_ITEM - EmpresaEstoque[empresaid][produto];
		
		        if(falta > 0)
		        {
		            totalFalta += falta;
		            custoTotal += falta * ProdutoCusto[produto];
		        }
		    }
		    if(totalFalta < 50)
		    {
		        new msg[144];
		        format(msg, sizeof(msg), "[EMPRESA] O estoque ainda nao precisa de reabastecimento. Faltam apenas %d unidades no total.", totalFalta);
		        return SendClientMessage(playerid, COR_ERRO, msg);
		    }
		    if(custoTotal > EmpresaInfo[empresaid][empresaSaldo])
		    {
		        new msg[160];
		        format(msg, sizeof(msg), "[EMPRESA] O cofre nao possui dinheiro suficiente. Custo: $%d | Cofre: $%d.", custoTotal, EmpresaInfo[empresaid][empresaSaldo]);
		        return SendClientMessage(playerid, COR_ERRO, msg);
		    }
			SetPVarInt(playerid, "EmpresaReabastecerCusto", custoTotal);
		    SetPVarInt(playerid, "EmpresaReabastecerFalta", totalFalta);
		
		    new texto[512];
		    format(texto, sizeof(texto), "{FFFFFF}Reabastecimento geral da empresa.\n\n{C8C8C8}Unidades para repor: {FFFFFF}%d\n{C8C8C8}Custo total: {00FF00}$%d\n{C8C8C8}Cofre atual: {00FF00}$%d\n\n{FFFFFF}Todos os produtos serao reabastecidos para {00FF00}%d unidades{FFFFFF}.\n\nDeseja continuar?", totalFalta, custoTotal, EmpresaInfo[empresaid][empresaSaldo], MAX_ESTOQUE_ITEM);				    		
		    ShowPlayerDialog(playerid, DIALOG_EMPRESA_REABASTECERT, DIALOG_STYLE_MSGBOX, "Reabastecer Estoque", texto, "Reabastecer", "Cancelar");
		    return 1;
		}
		case DIALOG_EMPRESA_REABASTECERT:
		{
		    new empresaid = GetPVarInt(playerid, "EmpresaMenuID");
		    if(!response) return EmpresaAbrirMenu(playerid, empresaid);
		    if(!EmpresaValida(empresaid)) return 1;
		    if(EmpresaNoInterior(playerid) != empresaid || !EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Acao invalida.");
		
		    new custoTotal = GetPVarInt(playerid, "EmpresaReabastecerCusto");
		    new totalFalta = GetPVarInt(playerid, "EmpresaReabastecerFalta");
		
		    if(totalFalta < 50 || custoTotal <= 0) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Reabastecimento invalido.");
		    if(custoTotal > EmpresaInfo[empresaid][empresaSaldo]) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] O cofre da empresa nao possui dinheiro suficiente.");
		
		    new tipo = EmpresaInfo[empresaid][empresaTipo];
		    if(!EmpresaTipoValido(tipo)) return 1;
		
		    new total = EmpresaTotalProdutos(tipo);		
		    EmpresaInfo[empresaid][empresaSaldo] -= custoTotal;
		
		    for(new i = 0; i < total; i++)
		    {
		        new produto = EmpresaCatalogo[tipo][i];
		        EmpresaEstoque[empresaid][produto] = MAX_ESTOQUE_ITEM;
		    }
		    EmpresaSalvar(empresaid);
		
		    DeletePVar(playerid, "EmpresaReabastecerCusto");
		    DeletePVar(playerid, "EmpresaReabastecerFalta");
		
		    new msg[160];
		    format(msg, sizeof(msg), "[EMPRESA] Estoque reabastecido! %d unidades repostas por $%d. Cofre: $%d.", totalFalta, custoTotal, EmpresaInfo[empresaid][empresaSaldo]);
		    SendClientMessage(playerid, COR_SUCESSO, msg);		
		    return EmpresaAbrirReabastecer(playerid, empresaid);
		}
        case DIALOG_EMPRESA_SACAR:
        {
            new empresaid = GetPVarInt(playerid, "EmpresaMenuID");
            if(!response) return EmpresaAbrirMenu(playerid, empresaid);
            if(!EmpresaValida(empresaid)) return 1;
            if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao esta mais dentro desta empresa.");
            if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao e o proprietario.");
            new valor = strval(inputtext);
            if(valor <= 0) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Valor invalido.");
            if(valor > EmpresaInfo[empresaid][empresaSaldo]) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] A empresa nao possui esse valor.");

            EmpresaInfo[empresaid][empresaSaldo] -= valor;
            sGivePlayerCash(playerid, valor);
            
            EmpresaSalvar(empresaid);

            new msg[128];
            format(msg, sizeof(msg), "[EMPRESA] Voce sacou $%d. Cofre: $%d.", valor, EmpresaInfo[empresaid][empresaSaldo]);
            SendClientMessage(playerid, COR_SUCESSO, msg);
            return 1;
        }
        case DIALOG_EMPRESA_VENDER_JOGADOR:
		{
			new idFixoAlvo;
		    new empresaid = GetPVarInt(playerid, "EmpresaMenuID");
		    if(!response) return EmpresaAbrirMenu(playerid, empresaid);
		    if(!EmpresaValida(empresaid)) return 1;
		    if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao esta dentro da empresa.");
		    if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao e o proprietario.");
		    if(sscanf(inputtext, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] ID fixo invalido.");
			new targetid = ProcurarPorIDFixo(idFixoAlvo);
		    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Nenhum jogador online com esse ID fixo.");
		    if(!PlayerLogado[targetid]) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Jogador nao encontrado.");
		    if(targetid == playerid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao pode vender para si mesmo.");
		    if(EmpresaBuscarPorDono(targetid) != -1) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Este jogador ja possui uma empresa.");
		    if(GetPVarInt(targetid, "EmpresaOfertaTempo") > gettime()) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Este jogador ja possui uma oferta pendente.");
		
		    SetPVarInt(targetid, "EmpresaOfertaID", empresaid);
		    SetPVarInt(targetid, "EmpresaOfertaVendedor", playerid);
		    SetPVarInt(targetid, "EmpresaOfertaValor", EmpresaInfo[empresaid][empresaValor]);
		    SetPVarInt(targetid, "EmpresaOfertaTempo", gettime() + 60);
		    SetPVarString(targetid, "EmpresaOfertaVendedorNome", EmpresaInfo[empresaid][empresaDono]);
		
		    new texto[400];
		    format(texto, sizeof(texto), "{FFFFFF}Vendedor: {FFFF00}%s\n\n{FFFFFF}Empresa: {DC143C}%s #%d\n{FFFFFF}Valor: {00FF00}$%d\n{FFFFFF}Seu dinheiro: {00FF00}$%d\n\n{AFAFAF}A oferta expira em 60 segundos.\n{FFFFFF}Deseja aceitar?", 
		        pName(playerid), EmpresaNomeTipo(EmpresaInfo[empresaid][empresaTipo]), empresaid, EmpresaInfo[empresaid][empresaValor], GetPlayerCash(targetid));
		    ShowPlayerDialog(targetid, DIALOG_EMPRESA_VENDER_CONFIRMA, DIALOG_STYLE_MSGBOX, "Oferta de empresa", texto, "Aceitar", "Recusar");
		    SendClientMessage(playerid, COR_SUCESSO, "[EMPRESA] Oferta enviada. Aguarde a resposta do jogador.");
		    return 1;
		}
		case DIALOG_EMPRESA_VENDER_CONFIRMA:
		{
		    new empresaid = GetPVarInt(playerid, "EmpresaOfertaID");
		    new vendedorid = GetPVarInt(playerid, "EmpresaOfertaVendedor");
		    new valor = GetPVarInt(playerid, "EmpresaOfertaValor");
		    new tempo = GetPVarInt(playerid, "EmpresaOfertaTempo");
		    new vendedorNome[MAX_PLAYER_NAME];
		
		    GetPVarString(playerid, "EmpresaOfertaVendedorNome", vendedorNome, sizeof(vendedorNome));
		    DeletePVar(playerid, "EmpresaOfertaID");
		    DeletePVar(playerid, "EmpresaOfertaVendedor");
		    DeletePVar(playerid, "EmpresaOfertaValor");
		    DeletePVar(playerid, "EmpresaOfertaTempo");
		    DeletePVar(playerid, "EmpresaOfertaVendedorNome");
		
		    if(!response)
		    {
		        if(IsPlayerConnected(vendedorid)) SendClientMessage(vendedorid, COR_ERRO, "[EMPRESA] O jogador recusou sua oferta.");
		        return 1;
		    }
		    if(gettime() > tempo) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Esta oferta expirou.");
		    if(!EmpresaValida(empresaid)) return 1;
		    if(!IsPlayerConnected(vendedorid) || !PlayerLogado[vendedorid]) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] O vendedor nao esta mais disponivel.");
		    if(strcmp(EmpresaInfo[empresaid][empresaDono], vendedorNome, true) != 0) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Esta oferta nao e mais valida.");
		    if(!EmpresaEhDono(vendedorid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] O vendedor nao e mais o proprietario.");
		    if(EmpresaNoInterior(vendedorid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] O vendedor saiu da empresa. Negocio cancelado.");
			if(!EstaPertoDoJogador(playerid, vendedorid, 3.0)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce esta muito longe do vendedor.");
		    if(EmpresaBuscarPorDono(playerid) != -1) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce ja possui uma empresa.");
		    if(GetPlayerCash(playerid) < valor)
		    {
		        SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Dinheiro insuficiente.");
		        SendClientMessage(vendedorid, COR_ERRO, "[EMPRESA] O jogador nao possui dinheiro suficiente.");
		        return 1;
		    }
		    sGivePlayerCash(playerid, -valor);
		    sGivePlayerCash(vendedorid, valor);
		    format(EmpresaInfo[empresaid][empresaDono], MAX_PLAYER_NAME, "%s", pName(playerid));
		    EmpresaInfo[empresaid][empresaOcupada] = true;
		    EmpresaAtualizarVisual(empresaid);
		    EmpresaSalvar(empresaid);
		
		    new texto[144];
		    format(texto, sizeof(texto), "[EMPRESA] Voce adquiriu a empresa #%d por $%d.", empresaid, valor);
		    SendClientMessage(playerid, COR_SUCESSO, texto);
		    format(texto, sizeof(texto), "[EMPRESA] %s aceitou sua oferta. Voce recebeu $%d.", pName(playerid), valor);
		    SendClientMessage(vendedorid, COR_SUCESSO, texto);
		    return 1;
		}
        case DIALOG_EMPRESA_VENDER:
        {
            new empresaid = GetPVarInt(playerid, "EmpresaMenuID");
            if(!response) return EmpresaAbrirMenu(playerid, empresaid);
            if(!EmpresaValida(empresaid)) return 1;
            if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao esta mais dentro desta empresa.");
            if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao e o proprietario.");

            new valor = (EmpresaInfo[empresaid][empresaValor] / 2) + EmpresaInfo[empresaid][empresaSaldo];
            sGivePlayerCash(playerid, valor);
            EmpresaInfo[empresaid][empresaOcupada] = false;
            EmpresaInfo[empresaid][empresaDono][0] = EOS;
            EmpresaInfo[empresaid][empresaSaldo] = 0;
            EmpresaInfo[empresaid][empresaPrecoGasolina] = 5;
            EmpresaInfo[empresaid][empresaVendasSemana] = 0;
            for(new i = 0; i < 7; i++) EmpresaInfo[empresaid][empresaVendasDia][i] = 0;
            for(new p = 0; p < MAX_PRODUTOS; p++) EmpresaEstoque[empresaid][p] = 0;
            
            EmpresaAtualizarVisual(empresaid);
            EmpresaSalvar(empresaid);

            SetPlayerInterior(playerid, 0);
            SetPlayerVirtualWorld(playerid, 0);
            SetPlayerPos(playerid, EmpresaInfo[empresaid][empresaExtX], EmpresaInfo[empresaid][empresaExtY], EmpresaInfo[empresaid][empresaExtZ]);

            new msg[128];
            format(msg, sizeof(msg), "[EMPRESA] Empresa vendida ao Estado. Voce recebeu $%d.", valor);
            SendClientMessage(playerid, COR_SUCESSO, msg);
            return 1;
        }
        case DIALOG_EMPRESA_PRECO_GASOLINA:
        {
            if(!response) return 1;
            new empresaid = GetPVarInt(playerid, "EmpresaMenuID");
            if(!EmpresaValida(empresaid)) return 1;
            if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao esta mais dentro desta empresa.");
            if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao e o proprietario.");
            if(EmpresaInfo[empresaid][empresaTipo] != EMPRESA_POSTO) return 1;
            new preco = strval(inputtext);
            if(preco < 1 || preco > 20) return SendClientMessage(playerid, COR_ERRO, "[POSTO] O preco deve ser entre $1 e $20 por litro.");
            
            EmpresaInfo[empresaid][empresaPrecoGasolina] = preco;
            EmpresaAtualizarVisual(empresaid); 
            EmpresaSalvar(empresaid);
            SendClientMessage(playerid, COR_SUCESSO, "[POSTO] Preco por litro atualizado.");
            return 1;
        }
        case DIALOG_ANIMS_CATEGORIAS:
		{
		    if(!response) return 1;
		    switch(listitem)
		    {
		        case 0:
		        {
		            ShowPlayerDialog(playerid, DIALOG_ANIMS_DANCAS, DIALOG_STYLE_LIST, "Animacoes - Dancas", "{FF8C00}1. {FFFFFF}Danca 1\n{FF8C00}2. {FFFFFF}Danca 2\n{FF8C00}3. {FFFFFF}Danca 3\n{FF8C00}4. {FFFFFF}Danca 4", "Usar", "Voltar");
		        }
		        case 1:
		        {
		            ShowPlayerDialog(playerid, DIALOG_ANIMS_EMOCOES, DIALOG_STYLE_LIST, "Animacoes - Emocoes", "{FF8C00}1. {FFFFFF}Acenar\n{FF8C00}2. {FFFFFF}Aplaudir\n{FF8C00}3. {FFFFFF}Raiva\n{FF8C00}4. {FFFFFF}Medo\n{FF8C00}5. {FFFFFF}Render-se", "Usar", "Voltar");
		        }
		        case 2:
		        {
		            ShowPlayerDialog(playerid, DIALOG_ANIMS_BAFORADA, DIALOG_STYLE_LIST, "Animacoes - Baforada", "{FF8C00}1. {FFFFFF}Fumar\n{FF8C00}2. {FFFFFF}Fumar 2\n{FF8C00}3. {FFFFFF}Fumar 3\n{FF8C00}4. {FFFFFF}Beber\n{FF8C00}5. {FFFFFF}Comer", "Usar", "Voltar");
		        }
		        case 3:
		        {
		            ShowPlayerDialog(playerid, DIALOG_ANIMS_INTERACAO, DIALOG_STYLE_LIST, "Animacoes - Interacoes", "{FF8C00}1. {FFFFFF}Mandar beijo\n{FF8C00}2. {FFFFFF}Cumprimentar\n{FF8C00}3. {FFFFFF}Cumprimentar 2\n{FF8C00}4. {FFFFFF}Abrir os bracos\n{FF8C00}5. {FFFFFF}Dar presente", "Usar", "Voltar");
		        }
		        case 4:
		        {
		            ShowPlayerDialog(playerid, DIALOG_ANIMS_ACOES, DIALOG_STYLE_LIST, "Animacoes - Acoes", "{FF8C00}1. {FFFFFF}Sentar\n{FF8C00}2. {FFFFFF}Deitar\n{FF8C00}3. {FFFFFF}Dormir\n{FF8C00}4. {FFFFFF}Apontar\n{FF8C00}5. {FFFFFF}Maos na cabeca\n{FF8C00}6. {FFFFFF}Ajoelhar", "Usar", "Voltar");
		        }
		        case 5:
		        {
		            ShowPlayerDialog(playerid, DIALOG_ANIMS_SENTADO, DIALOG_STYLE_LIST, "Animacoes - Sentado", "{FF8C00}1. {FFFFFF}Sentar 1\n{FF8C00}2. {FFFFFF}Sentar 2\n{FF8C00}3. {FFFFFF}Sentar 3\n{FF8C00}4. {FFFFFF}Sentar na praia", "Usar", "Voltar");
		        }
		        case 6:
		        {
		            ShowPlayerDialog(playerid, DIALOG_ANIMS_LUTA, DIALOG_STYLE_LIST, "Animacoes - Luta", "{FF8C00}1. {FFFFFF}Soco\n{FF8C00}2. {FFFFFF}Soco 2\n{FF8C00}3. {FFFFFF}Soco 3\n{FF8C00}4. {FFFFFF}Chute", "Usar", "Voltar");
		        }
		        case 7:
		        {
		            ShowPlayerDialog(playerid, DIALOG_ANIMS_OUTROS, DIALOG_STYLE_LIST, "Animacoes - Outros", "{FF8C00}1. {FFFFFF}Cair\n{FF8C00}2. {FFFFFF}Levantar\n{FF8C00}3. {FFFFFF}Olhar relogio\n{FF8C00}4. {FFFFFF}Fotografar\n{FF8C00}5. {FFFFFF}Digitar\n{FF8C00}6. {FFFFFF}Telefone", "Usar", "Voltar");
		        }
		    }
		    return 1;
		}
        case DIALOG_ANIMS_DANCAS:
        {
            if(!response)
            {
                return ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
            }
            switch(listitem)
			{
			    case 0: UsarAnimacao(playerid, "DANCING", "dance_loop");
			    case 1: UsarAnimacao(playerid, "DANCING", "dnce_M_a");
			    case 2: UsarAnimacao(playerid, "DANCING", "dnce_M_b");
			    case 3: UsarAnimacao(playerid, "DANCING", "dnce_M_c");
			}
			return 1;
        }
        case DIALOG_ANIMS_EMOCOES:
        {
            if(!response)
            {
                return ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
            }
            switch(listitem)
			{
			    case 0: UsarAnimacao(playerid, "GREETINGS", "greet1");
			    case 1: UsarAnimacao(playerid, "RIOT", "riot_shout");
			    case 2: UsarAnimacao(playerid, "PED", "facanger");
			    case 3: UsarAnimacao(playerid, "PED", "cower");
			    case 4: UsarAnimacao(playerid, "PED", "handsup");
			}
            return 1;
        }
        case DIALOG_ANIMS_BAFORADA:
        {
            if(!response)
            {
                return ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
            }
            switch(listitem)
			{
			    case 0: UsarAnimacao(playerid, "SMOKING", "M_smkstnd_loop");
			    case 1: UsarAnimacao(playerid, "SMOKING", "M_smk_drag");
			    case 2: UsarAnimacao(playerid, "SMOKING", "M_smklean_loop");
			    case 3: UsarAnimacao(playerid, "VENDING", "vend_drink_p");
			    case 4: UsarAnimacao(playerid, "VENDING", "vend_eat_p");
			}
            return 1;
        }
        case DIALOG_ANIMS_INTERACAO:
        {
            if(!response)
            {
                return ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
            }
            switch(listitem)
			{
			    case 0: UsarAnimacao(playerid, "KISSING", "f_kiss");
			    case 1: UsarAnimacao(playerid, "GREETINGS", "greet1");
			    case 2: UsarAnimacao(playerid, "GREETINGS", "greet2");
			    case 3: UsarAnimacao(playerid, "GIFT", "gift_give");
			    case 4: UsarAnimacao(playerid, "GIFT", "gift_get");
			}
            return 1;
        }
        case DIALOG_ANIMS_ACOES:
        {
            if(!response)
            {
                return ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
            }
            switch(listitem)
			{
			    case 0: UsarAnimacao(playerid, "SITTING", "sit_idle");
			    case 1: UsarAnimacao(playerid, "CRACK", "crckidle1");
			    case 2: UsarAnimacao(playerid, "CRACK", "crckidle4");
			    case 3: UsarAnimacao(playerid, "PED", "gang_gunstand");
			    case 4: UsarAnimacao(playerid, "PED", "handsup");
			    case 5: UsarAnimacao(playerid, "PED", "cower");
			}
            return 1;
        }
        case DIALOG_ANIMS_SENTADO:
        {
            if(!response)
            {
                return ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
            }
            switch(listitem)
			{
			    case 0: UsarAnimacao(playerid, "SITTING", "sit_idle");
			    case 1: UsarAnimacao(playerid, "SITTING", "sit_in_p");
			    case 2: UsarAnimacao(playerid, "SITTING", "sit_out_p");
			    case 3: UsarAnimacao(playerid, "BEACH", "parksit_m_loop");
			}
            return 1;
        }
        case DIALOG_ANIMS_LUTA:
        {
            if(!response)
            {
                return ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
            }
            switch(listitem)
			{
			    case 0: UsarAnimacao(playerid, "FIGHT_B", "fightB_1");
			    case 1: UsarAnimacao(playerid, "FIGHT_B", "fightB_2");
			    case 2: UsarAnimacao(playerid, "FIGHT_B", "fightB_3");
			    case 3: UsarAnimacao(playerid, "FIGHT_C", "fightC_1");
			}
            return 1;
        }
        case DIALOG_ANIMS_OUTROS:
        {
            if(!response)
            {
                return ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
            }
            switch(listitem)
			{
			    case 0: UsarAnimacao(playerid, "PED", "FALL_FALL");
			    case 1: UsarAnimacao(playerid, "PED", "getup");
			    case 2: UsarAnimacao(playerid, "COP_AMBIENT", "coplook_watch");
			    case 3: UsarAnimacao(playerid, "CAMERA", "camstnd_cmon");
			    case 4: UsarAnimacao(playerid, "PED", "typing");
			    case 5: UsarAnimacao(playerid, "PED", "phone_in");
			}
            return 1;
        }
        case DIALOG_INV_CONFIRMAR_EXCLUSAO:
		{
		    if(!response)
		        return 1;
		
		    InventarioExcluirQuantidade(playerid, 1);
		    return 1;
		}
		case DIALOG_INV_QUANTIDADE_EXCLUIR:
		{
		    if(!response)
		        return 1;
		
		    new quantidade = strval(inputtext);
		
		    if(quantidade < 1)
		        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Digite uma quantidade valida.");
		
		    InventarioExcluirQuantidade(playerid, quantidade);
		    return 1;
		}
		case DIALOG_INV_TRANSFERIR:
		{
		    if(!response)
		        return 1;
		
		    new id = strval(inputtext);
		
		    if(!IsPlayerConnected(id))
		        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Jogador nao encontrado.");
		
		    if(id == playerid)
		        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Voce nao pode transferir para si mesmo.");
		
		    new Float:px, Float:py, Float:pz;
		    GetPlayerPos(id, px, py, pz);
		
		    if(!IsPlayerInRangeOfPoint(playerid, 3.0, px, py, pz))
		        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] O jogador precisa estar a ate 3 metros de voce.");
		
		    InventarioTransferirID[playerid] = id;
		
		    new categoria = GetPVarInt(playerid, "InvTransferCategoria");
		    new slot = GetPVarInt(playerid, "InvTransferSlot");
		    new itemid = Inventario[playerid][categoria][slot][invID];
		    new quantidade = Inventario[playerid][categoria][slot][invQuantidade];
		
		    if(itemid <= 0 || quantidade <= 0)
		        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Item invalido.");
		
		    if(categoria == INV_CATEGORIA_ARMAS || categoria == INV_CATEGORIA_ROUPAS)
		        return InventarioTransferirQuantidade(playerid, 1);
		
		    new limite = 30;
		
		    if(itemid == INV_ITEM_GALAO || itemid == INV_ITEM_KIT_REPARO)
		        limite = 5;
		
		    if(limite > quantidade)
		        limite = quantidade;
		
		    new texto[160];
		    format(texto, sizeof(texto), "{FFFFFF}Digite a quantidade que deseja transferir.\n\nDisponivel: {F1C40F}%d\n{FFFFFF}Maximo: {F1C40F}%d", quantidade, limite);
		
		    ShowPlayerDialog(playerid, DIALOG_INV_QUANTIA_TRANSFERIR, DIALOG_STYLE_INPUT, "Transferir item", texto, "Enviar", "Voltar");
		    return 1;
		}
		case DIALOG_INV_QUANTIA_TRANSFERIR:
		{
		    if(!response)
		        return 1;
		
		    new quantidade = strval(inputtext);
		
		    if(quantidade < 1)
		        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Digite uma quantidade valida.");
		
		    InventarioTransferirQuantidade(playerid, quantidade);
		    return 1;
		}
        case DIALOG_JBL_MENU:
		{
		    if(!response) return 1;
		    new jblid = JBLDoDono(playerid);
		    switch(listitem)
		    {
		        case 0:
		        {
		            JBLPlaylistPagina[playerid] = 0;
		            return JBLMostrarPlaylists(playerid);
		        }
		        case 1:
				{
				    if(jblid == -1) return SendClientMessage(playerid, COR_ERRO, "[JBL] Voce nao tem uma JBL colocada no chao.");
				    new Float:dist = JBLDistancia(playerid, jblid);
				    if(dist < 0.0 || dist > 3.0) return SendClientMessage(playerid, COR_ERRO, "[JBL] Voce precisa estar perto da JBL para guarda-la.");
				    ApplyAnimation(playerid, "BOMBER", "BOM_Plant", 4.1, 0, 0, 0, 0, 0);
				    JBLGuardar(jblid);
				    return SendClientMessage(playerid, COR_SUCESSO, "[JBL] JBL guardada.");
				}
		        case 2:
		        {
		            if(jblid == -1) return SendClientMessage(playerid, COR_ERRO, "[JBL] Voce nao tem uma JBL colocada no chao.");		
		            if(!JBLInfo[jblid][jblTocando]) return SendClientMessage(playerid, COR_ERRO, "[JBL] Nao existe nenhuma musica tocando.");		
		            JBLDesligar(jblid);
		            return SendClientMessage(playerid, COR_SUCESSO, "[JBL] Musica parada.");
		        }
		    }
		    return 1;
		}
        case DIALOG_JBL_LINK:
		{
		    if(!response) return JBLMostrarPlaylists(playerid);
		    if(!Player[playerid][pJBL]) return SendClientMessage(playerid, COR_ERRO, "[JBL] Voce nao possui uma caixa de som.");		
		    new playlistid = JBLPlaylistSelecionada[playerid];
		    if(playlistid < 0 || playlistid >= MAX_PLAYLISTS) return JBLMostrarPlaylists(playerid);
		    if(!JBLLinkValido(inputtext)) return JBLPlaylistPedirLink(playerid);
		
		    format(JBLPlaylistLink[playerid][playlistid], 92, "%s", inputtext);
		    new texto[300];
		    format(texto, sizeof(texto), "{FFFFFF}Link aceito!\n\nDigite o nome que deseja salvar para esta musica.");
		    return ShowPlayerDialog(playerid, DIALOG_JBL_PLAYLIST_NOME, DIALOG_STYLE_INPUT, "{F1C40F}Nome da musica", texto, "Salvar", "Cancelar");
		}
		case DIALOG_JBL_PLAYLIST_NOME:
		{
		    if(!response)
		    {
		        new playlistid = JBLPlaylistSelecionada[playerid];
		        if(playlistid >= 0 && playlistid < MAX_PLAYLISTS) JBLPlaylistLink[playerid][playlistid][0] = EOS;
		        return JBLMostrarPlaylists(playerid);
		    }
		    new playlistid = JBLPlaylistSelecionada[playerid];
		    if(playlistid < 0 || playlistid >= MAX_PLAYLISTS) return JBLMostrarPlaylists(playerid);		
		
		    if(strlen(inputtext) < 1)
		    {
		        return ShowPlayerDialog(playerid, DIALOG_JBL_PLAYLIST_NOME, DIALOG_STYLE_INPUT, "{F1C40F}Nome da musica", "{E74C3C}Digite um nome valido.\n\n{FFFFFF}Digite o nome da musica:", "Salvar", "Cancelar");
		    }
		    format(JBLPlaylistNome[playerid][playlistid], 64, "%s", inputtext);
		    SendClientMessage(playerid, COR_SUCESSO, "[JBL] Musica salva na playlist.");
		    return JBLMostrarPlaylists(playerid);
		}
        case DIALOG_JBL_PLAYLIST_ACAO:
		{
		    if(!response) return JBLMostrarPlaylists(playerid);		
		    new playlistid = JBLPlaylistSelecionada[playerid];
		    if(playlistid < 0 || playlistid >= MAX_PLAYLISTS) return JBLMostrarPlaylists(playerid);		
		    if(strlen(JBLPlaylistNome[playerid][playlistid]) == 0)
		    {
		        if(listitem == 0) return JBLPlaylistPedirLink(playerid);
		        return JBLMostrarPlaylists(playerid);
		    }
		    if(listitem == 0)
		    {
		        new jblid = JBLDoDono(playerid);		
		        if(jblid == -1)
		        {
		            JBLColocar(playerid);
		            jblid = JBLDoDono(playerid);
		        }
		        if(jblid == -1) return JBLMostrarPlaylists(playerid);
		        JBLTocar(jblid, JBLPlaylistLink[playerid][playlistid]);
		        SendClientMessage(playerid, COR_SUCESSO, "[JBL] Musica iniciada.");
		        return 1;
		    }
		    if(listitem == 1)
		    {
		        new jblid = JBLDoDono(playerid);
		        if(jblid != -1 && JBLInfo[jblid][jblTocando])
		        {
		            if(!strcmp(JBLInfo[jblid][jblLink], JBLPlaylistLink[playerid][playlistid], true)) JBLDesligar(jblid);
		        }
		        JBLPlaylistNome[playerid][playlistid][0] = EOS;
		        JBLPlaylistLink[playerid][playlistid][0] = EOS;
		        SendClientMessage(playerid, COR_SUCESSO, "[JBL] Musica excluida da playlist.");
		        return JBLMostrarPlaylists(playerid);
		    }
		    return 1;
		}
        case DIALOG_JBL_PLAYLISTS:
		{
		    if(!response) return 1;
		    new pagina = JBLPlaylistPagina[playerid];
		    new inicio = pagina * 10;
		
		    if(pagina == 0 && listitem == 10)
		    {
		        JBLPlaylistPagina[playerid] = 1;
		        return JBLMostrarPlaylists(playerid);
		    }
		    if(pagina > 0)
		    {
		        if(inicio + 10 < MAX_PLAYLISTS && listitem == 10)
		        {
		            JBLPlaylistPagina[playerid] = pagina - 1;
		            return JBLMostrarPlaylists(playerid);
		        }
		        if(inicio + 10 < MAX_PLAYLISTS && listitem == 11)
		        {
		            JBLPlaylistPagina[playerid] = pagina + 1;
		            return JBLMostrarPlaylists(playerid);
		        }
		        if(inicio + 10 >= MAX_PLAYLISTS && listitem == 10)
		        {
		            JBLPlaylistPagina[playerid] = pagina - 1;
		            return JBLMostrarPlaylists(playerid);
		        }
		    }
		    new playlistid = inicio + listitem;
		    if(playlistid < 0 || playlistid >= MAX_PLAYLISTS) return 1;
		    return JBLAbrirPlaylist(playerid, playlistid);
		}
		case DIALOG_ADMINS:
		{
		    if(!response) return 1;		
		    switch(listitem)
		    {
		        case 0:
		        {
		            new string[4096], linha[160];
		            new total;		
		            format(string, sizeof(string), "Nome\tCargo\tStatus\n");		
		            for(new i = 0; i < MAX_PLAYERS; i++)
		            {
		                if(AdminRegistroID[i] == -1) continue;		
		                AdminListaSlot[playerid][total] = i;		
		                format(linha, sizeof(linha), "%s\t%s\t%s\n", AdminRegistroNome[i], GetCargoAdmName(AdminRegistroCargo[i]), AdminRegistroOnline[i] ? ("{00FF00}Online") : ("{FF0000}Offline"));
		                strcat(string, linha, sizeof(string));		
		                total++;
		            }		
		            if(total == 0) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Nenhum administrador encontrado.");		
		            ShowPlayerDialog(playerid, DIALOG_ADM_LISTA, DIALOG_STYLE_TABLIST_HEADERS, "Administradores", string, "Selecionar", "Voltar");
		        }
		        case 1:
		        {
		            new string[2048];
				    if(Player[playerid][pAdmin] < ADM_SUPORTE) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
				    strcat(string, "{FF8C00}1. {FFFFFF}/tra - entrar em servico\n");
				    strcat(string, "{FF8C00}2. {FFFFFF}/ca - chat da administracao\n");
				    strcat(string, "{FF8C00}3. {FFFFFF}/admins - ver administradores online\n");
				    
				    if(Player[playerid][pAdmin] >= ADM_MODERADOR)
				    {
				        strcat(string, "{FF8C00}4. {FFFFFF}/ir - ir ate um jogador\n");
				        strcat(string, "{FF8C00}5. {FFFFFF}/trazer - trazer um jogador ate voce\n");
				        strcat(string, "{FF8C00}6. {FFFFFF}/kick - expulsar um jogador\n");
				        strcat(string, "{FF8C00}7. {FFFFFF}/congelar - congelar um jogador\n");
				        strcat(string, "{FF8C00}8. {FFFFFF}/descongelar - descongelar um jogador\n");
				        strcat(string, "{FF8C00}9. {FFFFFF}/reviver - reviver um jogador\n");
				        strcat(string, "{FF8C00}10. {FFFFFF}/cadeia - prender um jogador\n");
				        strcat(string, "{FF8C00}11. {FFFFFF}/mutar - mutar um jogador\n");
				        strcat(string, "{FF8C00}12. {FFFFFF}/desmutar - remover o mute\n");
				        strcat(string, "{FF8C00}13. {FFFFFF}/spec - espectar um jogador\n");
				    }
				    if(Player[playerid][pAdmin] >= ADM_ADMINISTRADOR)
				    {
				        strcat(string, "{FF8C00}14. {FFFFFF}/explodir - explodir um jogador\n");
				        strcat(string, "{FF8C00}15. {FFFFFF}/car - criar um veiculo\n");
				        strcat(string, "{FF8C00}16. {FFFFFF}/dararma - dar arma a um jogador\n");
				        strcat(string, "{FF8C00}17. {FFFFFF}/darvida - alterar a vida de um jogador\n");
				        strcat(string, "{FF8C00}18. {FFFFFF}/darcolete - alterar o colete de um jogador\n");
				        strcat(string, "{FF8C00}19. {FFFFFF}/repararveh - reparar seu veiculo\n");
				        strcat(string, "{FF8C00}20. {FFFFFF}/puxarveh - puxar um veiculo\n");
				        strcat(string, "{FF8C00}21. {FFFFFF}/soltarcadeia - soltar um jogador da cadeia\n");
				        strcat(string, "{FF8C00}22. {FFFFFF}/tirararma - remover armas de um jogador\n");
				        strcat(string, "{FF8C00}23. {FFFFFF}/criarcasa - criar uma residencia\n");
				        strcat(string, "{FF8C00}24. {FFFFFF}/criarempresa - criar uma empresa\n");
				        strcat(string, "{FF8C00}25. {FFFFFF}/definibomba - definir bomba de combustivel\n");
				    }
				    if(Player[playerid][pAdmin] >= ADM_GERENTE)
				    {
				    }
				    if(Player[playerid][pAdmin] >= ADM_DIRETOR)
				    {
				        strcat(string, "{FF8C00}26. {FFFFFF}/trazertodos - enviar convite para trazer todos\n");
				        strcat(string, "{FF8C00}27. {FFFFFF}/dargrana - dar dinheiro a um jogador\n");
				    }
				    if(Player[playerid][pAdmin] >= ADM_FUNDADOR)
				    {
				        strcat(string, "{FF8C00}28. {FFFFFF}/daradmin - alterar cargo administrativo\n");
				    }
				    ShowPlayerDialog(playerid, 5000, DIALOG_STYLE_MSGBOX, "Comandos administrativos", string, "Fechar", "");
		        }
		        case 2:
		        {
		            new string[4096], linha[220];
		            new total;		
		            format(string, sizeof(string), "Jogador\tReport\n");		
		            for(new i = 0; i < MAX_PLAYERS; i++)
		            {
		                if(!ReportAtivo[i]) continue;		
		                ReportListaSlot[playerid][total] = i;		
		                format(linha, sizeof(linha), "%s\t%s\n", pName(i), ReportTexto[i]);
		                strcat(string, linha, sizeof(string));		
		                total++;
		            }		
		            if(total == 0) return SendClientMessage(playerid, COR_INFO, "[REPORT] Nao existem reports pendentes.");		
		            ShowPlayerDialog(playerid, DIALOG_REPORT_LISTA, DIALOG_STYLE_TABLIST_HEADERS, "Lista de Report", string, "Responder", "Voltar");
		        }
				case 3:
				{
				    new string[4096], linha[180];
				    new total;
				    format(string, sizeof(string), "Administrador\tCargo\tPositivo\tNegativo\n");
				    for(new i = 0; i < MAX_PLAYERS; i++)
				    {
				        if(AdminRegistroID[i] == -1) continue;
				        format(linha, sizeof(linha), "%s\t%s\t{00FF00}%d\t{FF0000}%d\n",
				            AdminRegistroNome[i],
				            GetCargoAdmName(AdminRegistroCargo[i]),
				            AdminRegistroPositivo[i],
				            AdminRegistroNegativo[i]
				        );
				        strcat(string, linha, sizeof(string));
				        total++;
				    }
				    if(total == 0) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Nenhum administrador encontrado.");
				    ShowPlayerDialog(playerid, DIALOG_ADM_AVALIACOES, DIALOG_STYLE_TABLIST_HEADERS, "Avaliacoes dos Administradores", string, "Fechar", "");
				}
				case 4:
				{
				    ShowPlayerDialog(playerid, DIALOG_ADM_SAIR, DIALOG_STYLE_MSGBOX, "Sair da Administracao", "Tem certeza que deseja sair da equipe Staff?\n\nAo confirmar, seu cargo administrativo sera removido.", "Confirmar", "Cancelar");
				}
		    }
		    return 1;
		}
		case DIALOG_ADM_LISTA:
		{
		    if(!response) return ShowPlayerDialog(playerid, DIALOG_ADMINS, DIALOG_STYLE_LIST, "Administrador", "Administradores\nComandos Administrativos\nLista de Report\nSair da Administracao", "Selecionar", "Fechar");
		    new slot = AdminListaSlot[playerid][listitem];
			if(slot < 0 || slot >= MAX_PLAYERS || AdminRegistroID[slot] == -1) return 1;
			if(AdminRegistroID[slot] == Player[playerid][pIDFixo]) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Voce nao pode interagir com seu proprio administrador.");
			if(Player[playerid][pAdmin] < ADM_DIRETOR) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Apenas Diretor ou superior pode gerenciar administradores.");
			if(AdminRegistroCargo[slot] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Voce nao pode gerenciar um cargo igual ou superior ao seu.");
		    AdmSelecionado[playerid] = slot;
		    ShowPlayerDialog(playerid, DIALOG_ADM_ACOES, DIALOG_STYLE_LIST, "Gerenciar Administrador", "Promover\nRebaixar\nRemover", "Selecionar", "Voltar");
		    return 1;
		}
		case DIALOG_ADM_ACOES:
		{
		    new slot = AdmSelecionado[playerid];
		    if(!response) return ShowPlayerDialog(playerid, DIALOG_ADM_LISTA, DIALOG_STYLE_MSGBOX, "Administradores", "Selecione um administrador na lista anterior.", "Voltar", "");
		    if(slot < 0 || slot >= MAX_PLAYERS || AdminRegistroID[slot] == -1) return 1;
		    if(Player[playerid][pAdmin] < ADM_DIRETOR) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Apenas Diretor ou superior pode gerenciar administradores.");
		    if(AdminRegistroCargo[slot] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Voce nao pode gerenciar um cargo igual ou superior ao seu.");
		
		    switch(listitem)
		    {
		        case 0:
		        {
		            if(AdminRegistroCargo[slot] >= ADM_FUNDADOR) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Este administrador ja possui o maior cargo.");
		            AdminAtualizarCargo(slot, AdminRegistroCargo[slot] + 1, playerid);
		            SendClientMessage(playerid, COR_SUCESSO, "[ADMIN] Administrador promovido com sucesso.");
		        }
		        case 1:
		        {
		            if(AdminRegistroCargo[slot] <= ADM_SUPORTE) return SendClientMessage(playerid, COR_ERRO, "[ADMIN] Este administrador ja possui o menor cargo.");
		            AdminAtualizarCargo(slot, AdminRegistroCargo[slot] - 1, playerid);
		            SendClientMessage(playerid, COR_SUCESSO, "[ADMIN] Administrador rebaixado com sucesso.");
		        }
		        case 2:
		        {
		            AdminAtualizarCargo(slot, 0, playerid);
		            SendClientMessage(playerid, COR_SUCESSO, "[ADMIN] Administracao removida com sucesso.");
		        }
		    }
		    return 1;
		}
		case DIALOG_ADM_SAIR:
		{
		    if(!response) return ShowPlayerDialog(playerid, DIALOG_ADMINS, DIALOG_STYLE_LIST, "Administrador", "Administradores\nComandos Administrativos\nLista de Report\nSair da Administracao", "Selecionar", "Fechar");
		    new cargoAntigo = Player[playerid][pAdmin];
		    Player[playerid][pAdmin] = 0;
		
		    if(AdmTrabalhando[playerid])
		    {
		        AdmTrabalhando[playerid] = false;
		        SetPlayerHealth(playerid, Player[playerid][pVida]);
		        SetPlayerArmour(playerid, Player[playerid][pArmour]);
		        SetPlayerSkin(playerid, Player[playerid][pSkin]);
		        SetPlayerColor(playerid, -1);
		    }
		    new arquivo[64];
		    format(arquivo, sizeof(arquivo), "Contas/%s.ini", pName(playerid));
		    DOF2_SetInt(arquivo, "Admin", 0);
		    DOF2_SaveFile();
		
		    new slot = AdminRegistroEncontrarID(Player[playerid][pIDFixo]);
		    if(slot != -1)
		    {
		        AdminRegistroID[slot] = -1;
		        AdminRegistroCargo[slot] = 0;
		        AdminRegistroNome[slot][0] = EOS;
		        AdminRegistroOnline[slot] = false;
		        AdminRegistroPlayer[slot] = INVALID_PLAYER_ID;
		        AdminRegistroSalvar(slot);
		    }
		    AdminLog("SAIDA DA ADMINISTRACAO", playerid, pName(playerid), GetCargoAdmName(cargoAntigo));
		    SendClientMessage(playerid, COR_SUCESSO, "[ADMIN] Voce saiu da equipe Staff.");
		    return 1;
		}
		case DIALOG_REPORT_ENVIAR:
		{
		    if(!response) return 1;
		    if(ReportAtivo[playerid]) return SendClientMessage(playerid, COR_ERRO, "[REPORT] Voce ja possui um report em aberto.");
		    if(inputtext[0] == EOS) return SendClientMessage(playerid, COR_ERRO, "[REPORT] Escreva o motivo do report.");
		
		    format(ReportTexto[playerid], sizeof(ReportTexto[]), "%s", inputtext);
		    ReportAtivo[playerid] = true;
		    ReportRespondendo[playerid] = INVALID_PLAYER_ID;
		    SendClientMessage(playerid, COR_SUCESSO, "[REPORT] Seu report foi enviado para a administracao.");
		    AdminLog("NOVO REPORT", playerid, pName(playerid), ReportTexto[playerid]);
		
		    foreach(Player, i)
		    {
		        if(Player[i][pAdmin] >= ADM_SUPORTE && AdmPodeUsar(i))
		        {
		            new msg[256];
		            format(msg, sizeof(msg), "[REPORT] %s abriu um report. Use /admins para visualizar.", pName(playerid));
		            SendClientMessage(i, COR_ADM, msg);
		        }
		    }
		    return 1;
		}
		case DIALOG_REPORT_LISTA:
		{
		    if(!response) return ShowPlayerDialog(playerid, DIALOG_ADMINS, DIALOG_STYLE_LIST, "Administrador", "Administradores\nComandos Administrativos\nLista de Report\nSair da Administracao", "Selecionar", "Fechar");
		    if(Player[playerid][pAdmin] < ADM_SUPORTE) return 1;
		    new reporterid = ReportListaSlot[playerid][listitem];
		    if(reporterid < 0 || reporterid >= MAX_PLAYERS || !ReportAtivo[reporterid]) return SendClientMessage(playerid, COR_ERRO, "[REPORT] Este report nao esta mais disponivel.");
		    if(ReportRespondendo[reporterid] != INVALID_PLAYER_ID && ReportRespondendo[reporterid] != playerid)
		    {
		        if(IsPlayerConnected(ReportRespondendo[reporterid])) return SendClientMessage(playerid, COR_ERRO, "[REPORT] Este report ja esta sendo atendido por outro administrador.");
		        ReportRespondendo[reporterid] = INVALID_PLAYER_ID;
		    }
		    ReportRespondendo[reporterid] = playerid;
		    SetPVarInt(playerid, "ReportSelecionado", reporterid);
		    new texto[500];
		    format(texto, sizeof(texto), "Jogador: %s\n\nReport:\n%s\n\nDigite sua resposta:", pName(reporterid), ReportTexto[reporterid]);
		    ShowPlayerDialog(playerid, DIALOG_REPORT_RESPOSTA, DIALOG_STYLE_INPUT, "Responder Report", texto, "Responder", "Cancelar");
		    return 1;
		}
		case DIALOG_REPORT_RESPOSTA:
		{
		    new reporterid = GetPVarInt(playerid, "ReportSelecionado");
		    if(reporterid != INVALID_PLAYER_ID && reporterid >= 0 && reporterid < MAX_PLAYERS && ReportAtivo[reporterid])
		    {
		        if(ReportRespondendo[reporterid] != playerid) return SendClientMessage(playerid, COR_ERRO, "[REPORT] Este report nao esta mais reservado para voce.");
		        if(!response)
		        {
		            ReportRespondendo[reporterid] = INVALID_PLAYER_ID;
		            DeletePVar(playerid, "ReportSelecionado");
		            return 1;
		        }
		        if(inputtext[0] == EOS) return SendClientMessage(playerid, COR_ERRO, "[REPORT] Digite uma resposta.");
		        new nomeAdmin[MAX_PLAYER_NAME];
		        format(nomeAdmin, sizeof(nomeAdmin), "%s", pName(playerid));
		
		        if(IsPlayerConnected(reporterid))
		        {
		            new msg[300];
		            format(msg, sizeof(msg), "[REPORT] Administrador %s respondeu seu report: %s", nomeAdmin, inputtext);
		            SendClientMessage(reporterid, COR_ADM, msg);		
		            ReportAguardandoAvaliacao[reporterid] = true;
					ReportAvaliacaoAdmin[reporterid] = playerid;
					ReportAvaliacaoAdminIDFixo[reporterid] = Player[playerid][pIDFixo];
					format(ReportAvaliacaoAdminNome[reporterid], MAX_PLAYER_NAME, "%s", nomeAdmin);		
		            ShowPlayerDialog(reporterid, DIALOG_REPORT_AVALIACAO, DIALOG_STYLE_MSGBOX, "Avaliacao do Atendimento", "Como voce avalia o atendimento recebido pelo administrador?", "Positivo", "Negativo");
		        }
		        AdminLog("RESPOSTA REPORT", playerid, pName(reporterid), inputtext);
		        ReportAtivo[reporterid] = false;
		        ReportRespondendo[reporterid] = INVALID_PLAYER_ID;
		        ReportTexto[reporterid][0] = EOS;
		        DeletePVar(playerid, "ReportSelecionado");
		        return 1;
		    }
		    return 1;
		}
		case DIALOG_REPORT_AVALIACAO:
		{
		    if(!ReportAguardandoAvaliacao[playerid]) return 1;
		    new avaliacao[16];
			new slot = AdminRegistroEncontrarID(ReportAvaliacaoAdminIDFixo[playerid]);
			
			if(slot != -1)
			{
			    if(response)
			    {
			        AdminRegistroPositivo[slot]++;
			        format(avaliacao, sizeof(avaliacao), "Positiva");
			    }
			    else
			    {
			        AdminRegistroNegativo[slot]++;
			        format(avaliacao, sizeof(avaliacao), "Negativa");
			    }
			    AdminRegistroSalvar(slot);
			}
			else
			{
			    format(avaliacao, sizeof(avaliacao), response ? ("Positiva") : ("Negativa"));
			}
		    AdminLog("AVALIACAO REPORT", playerid, ReportAvaliacaoAdminNome[playerid], avaliacao);
		
		    if(ReportAvaliacaoAdmin[playerid] != INVALID_PLAYER_ID && IsPlayerConnected(ReportAvaliacaoAdmin[playerid]))
		    {
		        new msg[160];
		        format(msg, sizeof(msg), "[REPORT] %s avaliou seu atendimento como %s.", pName(playerid), avaliacao);
		        SendClientMessage(ReportAvaliacaoAdmin[playerid], COR_ADM, msg);
		    }
		    ReportAguardandoAvaliacao[playerid] = false;
			ReportAvaliacaoAdmin[playerid] = INVALID_PLAYER_ID;
			ReportAvaliacaoAdminIDFixo[playerid] = -1;
			ReportAvaliacaoAdminNome[playerid][0] = EOS;
		    return 1;
		}
    }
    return 1;
}

public OnPlayerTakeDamage(playerid, issuerid, Float:amount, weaponid, bodypart)
{
    if(issuerid != INVALID_PLAYER_ID)
    {
		if(Player[playerid][pTempoFerido] > 0)
		{
		    SetPlayerHealth(playerid, 20.0);
		}
		else if(bodypart == 9)
		{
		    SetPlayerHealth(playerid, 0.0);
		}		
	}
	return 1;
}

public OnPlayerClickMap(playerid, Float:fX, Float:fY, Float:fZ)
{
    if(Player[playerid][pAdmin] < 1) return 1;
    if(Player[playerid][pAdmin] != ADM_FUNDADOR && !AdmPodeUsar(playerid)) return 1;

    SetPVarFloat(playerid, "TeleX", fX);
    SetPVarFloat(playerid, "TeleY", fY);
    SetPVarFloat(playerid, "TeleZ", fZ + 3.0);
    ShowPlayerDialog(playerid, DIALOG_TELEMAP, DIALOG_STYLE_MSGBOX, 
        "Teleporte pelo mapa", 
        "Voce deseja teleportar no local em que voce marcou no mapa?", "SIM", "NAO");
    return 1;
}

public OnPlayerClickPlayerTextDraw(playerid, PlayerText:playertextid)
{
    if(!InventarioAberto[playerid])
        return 0;

    if(playertextid == InvPlayer[playerid][1])
    {
        if(InventarioSelecionado[playerid] == -1)
            return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Selecione um item.");
        return InventarioUsarSelecionado(playerid);
    }

    if(playertextid == InvPlayer[playerid][2])
    {
        if(InventarioSelecionado[playerid] == -1)
            return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Selecione um item.");
        return InventarioTransferirSelecionado(playerid);
    }

    if(playertextid == InvPlayer[playerid][3])
    {
        if(InventarioSelecionado[playerid] == -1)
            return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Selecione um item.");
        return InventarioExcluirSelecionado(playerid);
    }

    if(playertextid == InvPlayer[playerid][29])
    {
        InventarioFechar(playerid);
        return 1;
    }

    if(playertextid == InvPlayer[playerid][31])
        return InventarioAbrirCategoria(playerid, INV_CATEGORIA_COMIDAS);

    if(playertextid == InvPlayer[playerid][33])
        return InventarioAbrirCategoria(playerid, INV_CATEGORIA_ROUPAS);

    if(playertextid == InvPlayer[playerid][35])
        return InventarioAbrirCategoria(playerid, INV_CATEGORIA_ARMAS);

    if(playertextid == InvPlayer[playerid][37])
        return InventarioAbrirCategoria(playerid, INV_CATEGORIA_OUTROS);

    for(new slot = 0; slot < INV_SLOTS; slot++)
    {
        if(playertextid == InvPlayer[playerid][InventarioSlotTD[slot]])
        {
            InventarioSelecionar(playerid, slot);
            return 1;
        }
    }

    return 0;
}

public OnPlayerCommandReceived(playerid, cmdtext[])
{
    if(PlayerLogado[playerid] == false)
    {
        SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce precisa estar logado para usar comandos.");
        return 0;
    }
    return 1;
}

public OnPlayerEnterDynamicCP(playerid, checkpointid)
{
    new empresaid = EmpresaNoInterior(playerid);
    if(empresaid != -1 && checkpointid == EmpresaInfo[empresaid][empresaCP])
    {
        EmpresaAbrirProdutos(playerid, empresaid);
        return 1;
    }
    return 1;
}

public OnPlayerLeaveDynamicCP(playerid, checkpointid)
{
    new empresaid = EmpresaNoInterior(playerid);
    if(empresaid != -1 && checkpointid == EmpresaInfo[empresaid][empresaCP])
    {
        MenuStore_Close(playerid);
        DeletePVar(playerid, "EmpresaLojaID");
        return 1;
    }
    return 1;
}

function TelaDeCarregamento(playerid)
{
	ShowPlayerDialog(playerid, DIALOG_INICIO, DIALOG_STYLE_LIST, "Tropical City", "{FF0000}[1] {FFFFFF}Logar\n{FF0000}[2] {FFFFFF}Registrar", "Confirmar", "Cancelar");
	return 1;
}

function KickSeguro(playerid)
{
    Kick(playerid);
    return 1;
}

stock GerarID(playerid)
{
    new file[64], id, tentativas = 0;
    do
    {
        id = RandomEx(1000, 99999);
        format(file, sizeof(file), "IDsFixo/%d.ini", id);
        tentativas++;
    }
    while((DOF2_FileExists(file) || ProcurarPorIDFixo(id) != INVALID_PLAYER_ID) && tentativas < 200);

    if(tentativas >= 200) return 0; 

    DOF2_CreateFile(file);
    DOF2_SetString(file, "Nome", pName(playerid));
    DOF2_SetInt(file, "IDFixo", id);
    DOF2_SaveFile();
    return id;
}

stock ProcurarPorIDFixo(uid)
{
    if(uid <= 0) return INVALID_PLAYER_ID;
    foreach(Player, i)
    {
        if(!IsPlayerConnected(i)) continue;
        if(Player[i][pIDFixo] == uid) return i;
    }
    return INVALID_PLAYER_ID;
}

stock CriarLabelIDFixo(playerid)
{
    new texto[64];
    DestruirLabelIDFixo(playerid);
    format(texto, sizeof(texto), "[%d]", Player[playerid][pIDFixo]);
    LabelIDFixo[playerid] = CreateDynamic3DTextLabel(texto, 0xFFFFFFFF, 0.0, 0.0, 0.30, 15.0, playerid);
    return 1;
}

stock DestruirLabelIDFixo(playerid)
{
    if(LabelIDFixo[playerid] != Text3D:INVALID_3DTEXT_ID)
    {
        DestroyDynamic3DTextLabel(LabelIDFixo[playerid]);
        LabelIDFixo[playerid] = Text3D:INVALID_3DTEXT_ID;
    }
    return 1;
}

stock PararAnimacao(playerid)
{
    ClearAnimations(playerid);
    SetPlayerSpecialAction(playerid, SPECIAL_ACTION_NONE);
    return 1;
}

stock UsarAnimacao(playerid, biblioteca[], animacao[], Float:velocidade = 4.1, loop = 1)
{
    ApplyAnimation(playerid, biblioteca, animacao, velocidade, loop, 1, 1, loop, 0, 1);
    return 1;
}

stock AdmPodeUsar(playerid)
{
    if(Player[playerid][pAdmin] >= ADM_DIRETOR) return 1;
    if(AdmTrabalhando[playerid] == true) return 1;
    return 0;
}

stock TeleportarParaCadeia(playerid)
{
    SetPlayerInterior(playerid, 5);
    SetPlayerVirtualWorld(playerid, 0);
    SetPlayerPos(playerid, 322.72, 306.43, 999.15);
    SetCameraBehindPlayer(playerid);
    SetPlayerHealth(playerid, 100);
	SetPlayerSpecialAction(playerid, SPECIAL_ACTION_NONE);
    ClearAnimations(playerid);
    ResetPlayerWeapons(playerid);

    if(TimerCadeia[playerid] != -1)
    {
        KillTimer(TimerCadeia[playerid]);
        TimerCadeia[playerid] = -1;
    }
    TimerCadeia[playerid] = SetTimerEx("CronometroCadeia", 1000, true, "i", playerid);
    PlayerTextDrawShow(playerid, MsgTD[playerid]);
    return 1;
}

stock EstaPertoDoJogador(playerid, targetid, Float:distancia)
{
    if(!IsPlayerConnected(targetid)) return 0;
    if(GetPlayerVirtualWorld(playerid) != GetPlayerVirtualWorld(targetid)) return 0;
    if(GetPlayerInterior(playerid) != GetPlayerInterior(targetid)) return 0;

    new Float:x, Float:y, Float:z;
    GetPlayerPos(targetid, x, y, z);
    return IsPlayerInRangeOfPoint(playerid, distancia, x, y, z);
}

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

stock GetCargoAdmName(nivel)
{
    new str[32];
    switch(nivel)
    {
        case ADM_SUPORTE: format(str, sizeof(str), "Suporte");
        case ADM_MODERADOR: format(str, sizeof(str), "Moderador");
        case ADM_ADMINISTRADOR: format(str, sizeof(str), "Administrador");
        case ADM_GERENTE: format(str, sizeof(str), "Gerente");
        case ADM_DIRETOR: format(str, sizeof(str), "Diretor");
        case ADM_FUNDADOR: format(str, sizeof(str), "Fundador");
        default: format(str, sizeof(str), "Jogador");
    }
    return str;
}

stock sGivePlayerCash(playerid, quantia)
{
    GivePlayerCash(playerid, quantia);
    DOF2_SetInt(Arquivo(playerid), "Dinheiro", GetPlayerCash(playerid));
    DOF2_SaveFile();
}

stock sResetPlayerCash(playerid)
{
    ResetPlayerCash(playerid);
}

stock SalvarConta(playerid)
{
    GetPlayerPos(playerid, Player[playerid][pPosX], Player[playerid][pPosY], Player[playerid][pPosZ]);
    GetPlayerFacingAngle(playerid, Player[playerid][pPosR]);
    Player[playerid][pLevel] = GetPlayerScore(playerid);
    Player[playerid][pInterior] = GetPlayerInterior(playerid);
    Player[playerid][pVirtualWorld] = GetPlayerVirtualWorld(playerid);
    Player[playerid][pVida] = GetPlayerHealthEx(playerid);
    Player[playerid][pArmour] = GetPlayerArmourEx(playerid);

	new arquivo[64];
	format(arquivo, sizeof(arquivo), "Contas/%s.ini", pName(playerid));
	if(DOF2_FileExists(arquivo))
	{
	    new key[128];
	    new arma, municao;
	    for(new i = 0; i < 13; i++)
	    {
	        GetPlayerWeaponData(playerid, i, arma, municao);
	        format(key, sizeof(key), "Arma_%d", i);
	        DOF2_SetInt(arquivo, key, arma);
	        format(key, sizeof(key), "Municao_%d", i);
	        DOF2_SetInt(arquivo, key, municao);
	    }
	    for(new i = 0; i < MAX_PLAYLISTS; i++)
		{
		    new chave[32];
		    format(chave, sizeof(chave), "JBLPlaylistNome_%d", i);
		    DOF2_SetString(arquivo, chave, JBLPlaylistNome[playerid][i]);
		    format(chave, sizeof(chave), "JBLPlaylistLink_%d", i);
		    DOF2_SetString(arquivo, chave, JBLPlaylistLink[playerid][i]);
		}
	    DOF2_SetInt(arquivo, "IDFixo", Player[playerid][pIDFixo]);
	    DOF2_SetInt(arquivo, "Admin", Player[playerid][pAdmin]);
	    DOF2_SetInt(arquivo, "Level", Player[playerid][pLevel]);
	    DOF2_SetInt(arquivo, "Exp", Player[playerid][pExp]);
	    DOF2_SetInt(arquivo, "Vip", Player[playerid][pVip]);
	    DOF2_SetInt(arquivo, "Coins", Player[playerid][pCoins]);
	    DOF2_SetInt(arquivo, "TemRg", Player[playerid][pTemRg]);
	    DOF2_SetInt(arquivo, "Emprego", Player[playerid][pEmprego]);
	    DOF2_SetInt(arquivo, "Procurado", Player[playerid][pProcurado]);
	    DOF2_SetInt(arquivo, "Fome", Player[playerid][pFome]);
	    DOF2_SetInt(arquivo, "Sede", Player[playerid][pSede]);
	    DOF2_SetInt(arquivo, "Sono", Player[playerid][pSono]);
	    DOF2_SetInt(arquivo, "Skin", Player[playerid][pSkin]);
	    DOF2_SetInt(arquivo, "Genero", Player[playerid][pGenero]);
	    DOF2_SetInt(arquivo, "Idade", Player[playerid][pIdade]);
	    DOF2_SetInt(arquivo, "Preso", Player[playerid][pPreso]);
	    DOF2_SetInt(arquivo, "TempoCadeia", Player[playerid][pTempoCadeia]);
	    DOF2_SetInt(arquivo, "Mutado", Player[playerid][pMutado]);
	    DOF2_SetInt(arquivo, "TempoMuted", Player[playerid][pTempoMuted]);
	    DOF2_SetInt(arquivo, "Ferido", Player[playerid][pFerido]);
	    DOF2_SetInt(arquivo, "TempoFerido", Player[playerid][pTempoFerido]);
	    DOF2_SetFloat(arquivo, "PosX", Player[playerid][pPosX]);
	    DOF2_SetFloat(arquivo, "PosY", Player[playerid][pPosY]);
	    DOF2_SetFloat(arquivo, "PosZ", Player[playerid][pPosZ]);
	    DOF2_SetFloat(arquivo, "Angulo", Player[playerid][pPosR]);
	    DOF2_SetInt(arquivo, "Interior", Player[playerid][pInterior]);
	    DOF2_SetInt(arquivo, "VirtualWorld", Player[playerid][pVirtualWorld]);
	    DOF2_SetFloat(arquivo, "Vida", Player[playerid][pVida]);
	    DOF2_SetFloat(arquivo, "Colete", Player[playerid][pArmour]);
	    DOF2_SetInt(arquivo, "Celular", Player[playerid][pCelular]);
	    DOF2_SetInt(arquivo, "JBL", Player[playerid][pJBL]);
	    DOF2_SaveFile();
	}
}

stock SetSpawnPlayer(playerid)
{
	AdminRegistroAtualizar(playerid);
	InventarioCarregar(playerid);
	
    SetPlayerScore(playerid, Player[playerid][pLevel]);
    ResetPlayerCash(playerid);
    sGivePlayerCash(playerid, DOF2_GetInt(Arquivo(playerid), "Dinheiro"));
    SetPlayerInterior(playerid, Player[playerid][pInterior]);
    SetPlayerVirtualWorld(playerid, Player[playerid][pVirtualWorld]);
    SetPlayerHealth(playerid, Player[playerid][pVida]);
    SetPlayerArmour(playerid, Player[playerid][pArmour]);
    
    TogglePlayerSpectating(playerid, false);
    SetSpawnInfo(playerid, NO_TEAM, Player[playerid][pSkin], Player[playerid][pPosX], Player[playerid][pPosY], Player[playerid][pPosZ], Player[playerid][pPosR], 0, 0, 0, 0, 0, 0);
    SpawnPlayer(playerid);
    PlayerLogado[playerid] = true;
    SetCameraBehindPlayer(playerid);
    SetPlayerColor(playerid, -1);
    
    new arquivo[64], key[128];
	format(arquivo, sizeof(arquivo), "Contas/%s.ini", pName(playerid));
	new arma, municao;	
	for(new i = 0; i < 13; i++)
	{
	    format(key, sizeof(key), "Arma_%d", i);
	    arma = DOF2_GetInt(arquivo, key);
	    format(key, sizeof(key), "Municao_%d", i);
	    municao = DOF2_GetInt(arquivo, key);
	    if(arma > 0 && municao > 0) GivePlayerWeapon(playerid, arma, municao);
	}
    for(new i = 0; i < 29; i++)
    {
        PlayerTextDrawShow(playerid, Necessidades[playerid][i]);
    }
    if(Player[playerid][pTempoMuted] > 0)
    {
        TimerMuted[playerid] = SetTimerEx("CronometroMuted", 1000, true, "i", playerid);
    }
    TimerFome[playerid] = SetTimerEx("Fome", 80000, true, "i", playerid);
	TimerSede[playerid] = SetTimerEx("Sede", 95000, true, "i", playerid);
	TimerSono[playerid] = SetTimerEx("Sono", 120000, true, "i", playerid);
	
    CriarLabelIDFixo(playerid);
    return 1;
}

stock E_VeiculoSemMotor(modelid)
{
    if(modelid == 481 || modelid == 509 || modelid == 510) return 1;
    return 0;
}

stock AlternarMotor(playerid, vehicleid)
{
    if(vehicleid == 0) return 0;
    new modelid = GetVehicleModel(vehicleid);
    if(E_VeiculoSemMotor(modelid)) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Este veiculo nao possui motor!");
    new engine, lights, alarm, doors, bonnet, boot, objective;
    GetVehicleParamsEx(vehicleid, engine, lights, alarm, doors, bonnet, boot, objective);
    if(engine <= 0)
    {
        SetVehicleParamsEx(vehicleid, VEHICLE_PARAMS_ON, lights, alarm, doors, bonnet, boot, objective);
        GameTextForPlayer(playerid, "~g~MOTOR LIGADO!", 2000, 3);
    }
    else
    {
        SetVehicleParamsEx(vehicleid, VEHICLE_PARAMS_OFF, lights, alarm, doors, bonnet, boot, objective);
        GameTextForPlayer(playerid, "~r~MOTOR DESLIGADO!", 2000, 3);
    }
    return 1;
}

function CronometroCadeia(playerid)
{
    if(Player[playerid][pTempoCadeia] > 0)
    {
        Player[playerid][pTempoCadeia]--;
        new minutos = Player[playerid][pTempoCadeia] / 60;
        new segundos = Player[playerid][pTempoCadeia] % 60;
        new string[128];
        format(string, sizeof(string), "Cadeia Administrativa~n~~r~Tempo restante: ~w~%02d:%02d", minutos, segundos);
        PlayerTextDrawSetString(playerid, MsgTD[playerid], string);
    }
    else
    {
        if(TimerCadeia[playerid] != -1)
        {
            KillTimer(TimerCadeia[playerid]);
            TimerCadeia[playerid] = -1;
        }
        PlayerTextDrawHide(playerid, MsgTD[playerid]);
        Player[playerid][pPreso] = 0;
        Player[playerid][pTempoCadeia] = 0;
        SetPlayerInterior(playerid, 0);
        SetPlayerVirtualWorld(playerid, 0);
        SetPlayerHealth(playerid, 100.0);
        SetPlayerPos(playerid, 1544.2446, -1675.0531, 13.5550);
        SetCameraBehindPlayer(playerid);
        SendClientMessage(playerid, COR_SUCESSO, "[Cadeia] Voce cumpriu sua pena e foi solto automaticamente.");
    }
    return 1;
}

function CronometroMuted(playerid)
{
    if(Player[playerid][pTempoMuted] > 0)
    {
        Player[playerid][pTempoMuted]--;
    }
    else
    {
        if(TimerMuted[playerid] != -1)
        {
            KillTimer(TimerMuted[playerid]);
            TimerMuted[playerid] = -1;
        }
        Player[playerid][pMutado] = 0;
        Player[playerid][pTempoMuted] = 0;
        SendClientMessage(playerid, COR_SUCESSO, "[MUTE] Seu tempo mutado acabou, voce ja pode falar no chat novamente.");
    }
    return 1;
}

stock ReviverJogador(playerid)
{
    if(TimerFerido[playerid] != -1)
    {
        KillTimer(TimerFerido[playerid]);
        TimerFerido[playerid] = -1;
    }
    Player[playerid][pFerido] = 0;
    Player[playerid][pTempoFerido] = 0;
    ClearAnimations(playerid);
    TogglePlayerControllable(playerid, true);
    SetPlayerInterior(playerid, Player[playerid][pInterior]);
    SetPlayerVirtualWorld(playerid, Player[playerid][pVirtualWorld]);
    SetPlayerPos(playerid, Player[playerid][pPosX], Player[playerid][pPosY], Player[playerid][pPosZ]);
    SetPlayerFacingAngle(playerid, Player[playerid][pPosR]);
    SetCameraBehindPlayer(playerid);
    SetPlayerHealth(playerid, 100.0);
    SetPlayerArmour(playerid, 0.0);
    return 1;
}

function CronometroFerido(playerid)
{
    if(Player[playerid][pTempoFerido] > 0)
    {
        Player[playerid][pTempoFerido]--;
        new minutos = Player[playerid][pTempoFerido] / 60;
        new segundos = Player[playerid][pTempoFerido] % 60;
        new string[128];
        format(string, sizeof(string), "~r~VOCE ESTA FERIDO~n~~w~Aguardando atendimento: ~y~%02d:%02d", minutos, segundos);
        GameTextForPlayer(playerid, string, 1000, 3);
        ApplyAnimation(playerid, "CRACK", "CRCKIDLE4", 4.1, 1, 0, 0, 0, 0, 1);
    }
    else
    {
        if(TimerFerido[playerid] != -1)
	    {
	        KillTimer(TimerFerido[playerid]);
	        TimerFerido[playerid] = -1;
	    }
	    Player[playerid][pFerido] = 0;
	    Player[playerid][pTempoFerido] = 0;
	    ClearAnimations(playerid);
	    TogglePlayerControllable(playerid, true);
	    SetPlayerInterior(playerid, Player[playerid][pInterior]);
	    SetPlayerVirtualWorld(playerid, Player[playerid][pVirtualWorld]);
	    SetPlayerPos(playerid, Player[playerid][pPosX], Player[playerid][pPosY], Player[playerid][pPosZ]);
	    SetPlayerFacingAngle(playerid, Player[playerid][pPosR]);
	    SetCameraBehindPlayer(playerid);
	    SetPlayerHealth(playerid, 20.0);
	    SetPlayerArmour(playerid, 0.0);
    }
    return 1;
}

function Fome(playerid)
{
    if(Player[playerid][pFerido] == 1) return 1;
    if(Player[playerid][pFome] > 0)
    {
        Player[playerid][pFome]--;
    }
    else
    {
        new Float:vida = GetPlayerHealthEx(playerid);
        if(vida > 10.0)
        {
            SetPlayerHealth(playerid, vida - 5.0);
        }
        else
        {
            SetPlayerHealth(playerid, 0.0);
        }
    }
    new texto[10];
    format(texto, sizeof(texto), "%d", Player[playerid][pFome]);
    PlayerTextDrawSetString(playerid, Necessidades[playerid][9], texto);
    return 1;
}

function Sede(playerid)
{
	if(Player[playerid][pFerido] == 1) return 1;
    if(Player[playerid][pSede] > 0)
    {
        Player[playerid][pSede]--;
    }
    else
    {
        new Float:vida = GetPlayerHealthEx(playerid);
        if(vida > 10.0)
        {
            SetPlayerHealth(playerid, vida - 5.0);
        }
        else
        {
            SetPlayerHealth(playerid, 0.0);
        }
    }
    new texto[10];
    format(texto, sizeof(texto), "%d", Player[playerid][pSede]);
    PlayerTextDrawSetString(playerid, Necessidades[playerid][13], texto);
    return 1;
}

function Sono(playerid)
{
    if(Player[playerid][pFerido] == 1) return 1;
    if(Player[playerid][pSono] > 0)
    {
        Player[playerid][pSono]--;
    }
    else
    {
        new Float:vida = GetPlayerHealthEx(playerid);
        if(vida > 10.0)
        {
            SetPlayerHealth(playerid, vida - 5.0);
        }
        else
        {
            SetPlayerHealth(playerid, 0.0);
        }
    }
    new texto[10];
    format(texto, sizeof(texto), "%d", Player[playerid][pSono]);
    PlayerTextDrawSetString(playerid, Necessidades[playerid][18], texto);
    return 1;
}

stock AdminLog(tipo[], adminid, alvo[], detalhe[])
{
    new File:arquivo;
    new string[512];
    new data[16], hora[16];
    new ano, mes, dia, horaAtual, minuto, segundo;

    getdate(ano, mes, dia);
    gettime(horaAtual, minuto, segundo);

    format(data, sizeof(data), "%02d-%02d-%04d", dia, mes, ano);
    format(hora, sizeof(hora), "%02d:%02d:%02d", horaAtual, minuto, segundo);

    if(IsPlayerConnected(adminid))
    {
        format(string, sizeof(string), "[%s %s] [%s] Admin: %s | Alvo: %s | %s\r\n", data, hora, tipo, pName(adminid), alvo, detalhe);
    }
    else
    {
        format(string, sizeof(string), "[%s %s] [%s] Admin: %s | Alvo: %s | %s\r\n", data, hora, tipo, "Desconhecido", alvo, detalhe);
    }
    arquivo = fopen("Logs/Administracao.log", io_append);
    if(arquivo)
    {
        fwrite(arquivo, string);
        fclose(arquivo);
    }
    return 1;
}

stock AdminRegistroInicializar()
{
    for(new i = 0; i < MAX_PLAYERS; i++)
    {
        AdminRegistroID[i] = -1;
        AdminRegistroCargo[i] = 0;
        AdminRegistroNome[i][0] = EOS;
        AdminRegistroOnline[i] = false;
        AdminRegistroPlayer[i] = INVALID_PLAYER_ID;
        AdminRegistroPositivo[i] = 0;
		AdminRegistroNegativo[i] = 0;
    }
    return 1;
}

stock AdminRegistroCarregar()
{
    AdminRegistroInicializar();

	new arquivo[32], chave[64];
    format(arquivo, sizeof(arquivo), "Administradores.ini");

    if(!DOF2_FileExists(arquivo))
    {
        DOF2_CreateFile(arquivo);
        DOF2_SaveFile();
        return 1;
    }
    for(new i = 0; i < MAX_PLAYERS; i++)
    {
        format(chave, sizeof(chave), "ID_%d", i);
        AdminRegistroID[i] = DOF2_GetInt(arquivo, chave);

        if(AdminRegistroID[i] <= 0)
        {
            AdminRegistroID[i] = -1;
            continue;
        }
        format(chave, sizeof(chave), "Nome_%d", i);
        format(AdminRegistroNome[i], MAX_PLAYER_NAME, "%s", DOF2_GetString(arquivo, chave));
        format(chave, sizeof(chave), "Cargo_%d", i);
		AdminRegistroCargo[i] = DOF2_GetInt(arquivo, chave);
		format(chave, sizeof(chave), "Positivo_%d", i);
		AdminRegistroPositivo[i] = DOF2_GetInt(arquivo, chave);
		format(chave, sizeof(chave), "Negativo_%d", i);
		AdminRegistroNegativo[i] = DOF2_GetInt(arquivo, chave);
		AdminRegistroOnline[i] = false;
		AdminRegistroPlayer[i] = INVALID_PLAYER_ID;
    }
    return 1;
}

stock AdminRegistroSalvar(slot)
{
    if(slot < 0 || slot >= MAX_PLAYERS) return 0;

	new arquivo[32], chave[64];
    format(arquivo, sizeof(arquivo), "Administradores.ini");

    if(!DOF2_FileExists(arquivo)) DOF2_CreateFile(arquivo);
    format(chave, sizeof(chave), "ID_%d", slot);
    DOF2_SetInt(arquivo, chave, AdminRegistroID[slot]);
    format(chave, sizeof(chave), "Nome_%d", slot);
    DOF2_SetString(arquivo, chave, AdminRegistroNome[slot]);
    format(chave, sizeof(chave), "Cargo_%d", slot);
	DOF2_SetInt(arquivo, chave, AdminRegistroCargo[slot]);
	format(chave, sizeof(chave), "Positivo_%d", slot);
	DOF2_SetInt(arquivo, chave, AdminRegistroPositivo[slot]);
	format(chave, sizeof(chave), "Negativo_%d", slot);
	DOF2_SetInt(arquivo, chave, AdminRegistroNegativo[slot]);
	DOF2_SaveFile();
    return 1;
}

stock AdminRegistroEncontrarID(idfixo)
{
    for(new i = 0; i < MAX_PLAYERS; i++)
    {
        if(AdminRegistroID[i] == idfixo)
            return i;
    }
    return -1;
}

stock AdminRegistroSlotLivre()
{
    for(new i = 0; i < MAX_PLAYERS; i++)
    {
        if(AdminRegistroID[i] == -1)
            return i;
    }
    return -1;
}

stock AdminRegistroAtualizar(playerid)
{
    new slot;
    if(Player[playerid][pAdmin] < ADM_SUPORTE)
    {
        slot = AdminRegistroEncontrarID(Player[playerid][pIDFixo]);

        if(slot != -1)
        {
            AdminRegistroID[slot] = -1;
            AdminRegistroCargo[slot] = 0;
            AdminRegistroNome[slot][0] = EOS;
            AdminRegistroOnline[slot] = false;
            AdminRegistroPlayer[slot] = INVALID_PLAYER_ID;
            AdminRegistroSalvar(slot);
        }
        return 1;
    }
    slot = AdminRegistroEncontrarID(Player[playerid][pIDFixo]);
    if(slot == -1) slot = AdminRegistroSlotLivre();
    if(slot == -1) return 0;
    
    if(AdminRegistroID[slot] == -1)
	{
	    AdminRegistroPositivo[slot] = 0;
	    AdminRegistroNegativo[slot] = 0;
	}
    AdminRegistroID[slot] = Player[playerid][pIDFixo];
    AdminRegistroCargo[slot] = Player[playerid][pAdmin];
    format(AdminRegistroNome[slot], MAX_PLAYER_NAME, "%s", pName(playerid));
    AdminRegistroOnline[slot] = true;
    AdminRegistroPlayer[slot] = playerid;

    AdminRegistroSalvar(slot);
    return 1;
}

stock AdminRegistroOffline(playerid)
{
    new slot = AdminRegistroEncontrarID(Player[playerid][pIDFixo]);

    if(slot == -1) return 1;

    AdminRegistroOnline[slot] = false;
    AdminRegistroPlayer[slot] = INVALID_PLAYER_ID;
    AdminRegistroSalvar(slot);
    return 1;
}

stock AdminAtualizarCargo(slot, novoCargo, adminid)
{
    if(slot < 0 || slot >= MAX_PLAYERS) return 0;
    if(AdminRegistroID[slot] == -1) return 0;

    new alvoid = AdminRegistroPlayer[slot];
    new antigoCargo = AdminRegistroCargo[slot];
    new nomeAlvo[MAX_PLAYER_NAME];
    new arquivo[64];

    format(nomeAlvo, sizeof(nomeAlvo), "%s", AdminRegistroNome[slot]);

    if(novoCargo < 0 || novoCargo > ADM_FUNDADOR) return 0;

    if(AdminRegistroOnline[slot] && IsPlayerConnected(alvoid))
    {
        Player[alvoid][pAdmin] = novoCargo;
        if(novoCargo == 0 && AdmTrabalhando[alvoid])
        {
            AdmTrabalhando[alvoid] = false;
            SetPlayerHealth(alvoid, Player[alvoid][pVida]);
            SetPlayerArmour(alvoid, Player[alvoid][pArmour]);
            SetPlayerSkin(alvoid, Player[alvoid][pSkin]);
            SetPlayerColor(alvoid, -1);
        }
        format(arquivo, sizeof(arquivo), "Contas/%s.ini", pName(alvoid));
        DOF2_SetInt(arquivo, "Admin", novoCargo);
        DOF2_SaveFile();
    }
    else
    {
        format(arquivo, sizeof(arquivo), "Contas/%s.ini", nomeAlvo);
        if(DOF2_FileExists(arquivo))
        {
            DOF2_SetInt(arquivo, "Admin", novoCargo);
            DOF2_SaveFile();
        }
    }
    if(novoCargo == 0)
    {
        AdminLog("REMOVER ADM", adminid, nomeAlvo, "Administracao removida");
        AdminRegistroID[slot] = -1;
        AdminRegistroCargo[slot] = 0;
        AdminRegistroNome[slot][0] = EOS;
        AdminRegistroOnline[slot] = false;
        AdminRegistroPlayer[slot] = INVALID_PLAYER_ID;
        AdminRegistroPositivo[slot] = 0;
		AdminRegistroNegativo[slot] = 0;
        AdminRegistroSalvar(slot);
        if(IsPlayerConnected(alvoid)) SendClientMessage(alvoid, COR_ERRO, "[ADMIN] Sua administracao foi removida.");
    }
    else
    {
        AdminRegistroCargo[slot] = novoCargo;
        AdminRegistroSalvar(slot);

        new detalhe[128];
        format(detalhe, sizeof(detalhe), "%s -> %s", GetCargoAdmName(antigoCargo), GetCargoAdmName(novoCargo));
        AdminLog("ALTERAR ADM", adminid, nomeAlvo, detalhe);

        if(IsPlayerConnected(alvoid))
        {
            new msg[128];
            format(msg, sizeof(msg), "[ADMIN] Seu cargo foi alterado para %s.", GetCargoAdmName(novoCargo));
            SendClientMessage(alvoid, COR_ADM, msg);
        }
    }
    return 1;
}

stock AtualizarTVInfo(playerid, targetid)
{
    if(!IsPlayerConnected(targetid)) return 0;
    new string[128];
    format(string, sizeof(string), "~r~Telando Jogador~n~~s~Nome: %s~n~ID Fixo: %d", pName(targetid), Player[targetid][pIDFixo]);
    PlayerTextDrawSetString(playerid, MsgTD[playerid], string);
    PlayerTextDrawShow(playerid, MsgTD[playerid]);
    return 1;
}

stock AtualizarTVAlvo(playerid, targetid)
{
    if(!IsPlayerConnected(targetid)) return 0;
    SetPlayerInterior(playerid, GetPlayerInterior(targetid));
    SetPlayerVirtualWorld(playerid, GetPlayerVirtualWorld(targetid));
    if(IsPlayerInAnyVehicle(targetid))
    {
        PlayerSpectateVehicle(playerid, GetPlayerVehicleID(targetid));
    }
    else
    {
        PlayerSpectatePlayer(playerid, targetid);
    }
    return 1;
}

CMD:tra(playerid)
{
    if(Player[playerid][pAdmin] < ADM_SUPORTE) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao");
    if(AdmTrabalhando[playerid] == false)
    {
        AdmTrabalhando[playerid] = true;
        Player[playerid][pVida] = GetPlayerHealthEx(playerid);
        Player[playerid][pArmour] = GetPlayerArmourEx(playerid);
        SetPlayerHealth(playerid, 100000.0);
        SetPlayerArmour(playerid, 100000.0);
        if(Player[playerid][pGenero] == 1)
        {
            SetPlayerSkin(playerid, 217);
            SetPlayerColor(playerid, 0x00BFFFFF);
        }
        else if(Player[playerid][pGenero] == 2)
        {
            SetPlayerSkin(playerid, 211);
            SetPlayerColor(playerid, 0xFF69B4FF);
        }
    }
    else
    {
        AdmTrabalhando[playerid] = false;
        SetPlayerHealth(playerid, Player[playerid][pVida]);
        SetPlayerArmour(playerid, Player[playerid][pArmour]);
        SetPlayerSkin(playerid, Player[playerid][pSkin]);
        SetPlayerColor(playerid, -1);
    }
    return 1;
}

CMD:ca(playerid, params[])
{
    new texto[128], string[270];
    if(Player[playerid][pAdmin] < ADM_SUPORTE) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce precisa estar em modo de trabalho. Use /tra");
    if(sscanf(params, "s[128]", texto)) return SendClientMessage(playerid, COR_INFO, "[USO] /ca [mensagem]");

    format(string, sizeof(string), "{FFA500}[CHAT ADM] %s %s(%d): %s", GetCargoAdmName(Player[playerid][pAdmin]), pName(playerid), Player[playerid][pIDFixo], texto);
    foreach(Player, i)
    {
        if(Player[i][pAdmin] >= ADM_SUPORTE && AdmPodeUsar(i))
        {
            SendClientMessage(i, -1, string);
        }
    }
    return 1;
}

CMD:admins(playerid, params[])
{
    if(Player[playerid][pAdmin] < ADM_SUPORTE) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao.");
    ShowPlayerDialog(playerid, DIALOG_ADMINS, DIALOG_STYLE_LIST, "Administrador", "Administradores\nComandos Administrativos\nLista de Report\nAvaliacoes\nSair da Administracao", "Selecionar", "Fechar");
    return 1;
}

CMD:report(playerid, params[])
{
    if(ReportAtivo[playerid] == true) return SendClientMessage(playerid, COR_ERRO, "[REPORT] Voce ja possui um report em aberto.");
    ShowPlayerDialog(playerid, DIALOG_REPORT_ENVIAR, DIALOG_STYLE_INPUT, "Report", "Digite sua duvida ou informe o problema que deseja reportar aos administradores.", "Enviar", "Cancelar");
    return 1;
}

CMD:ir(playerid, params[])
{
	new targetid, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /ir [ID Fixo]");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");

    new Float:x, Float:y, Float:z;
    GetPlayerPos(targetid, x, y, z);
    SetPlayerPos(playerid, x + 1.0, y + 1.0, z);
    SetCameraBehindPlayer(playerid);
    SetPlayerInterior(playerid, GetPlayerInterior(targetid));
    SetPlayerVirtualWorld(playerid, GetPlayerVirtualWorld(targetid));

    new msg[96];
    format(msg, sizeof(msg), "[TP] Voce foi teleportado ate %s.", pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:trazer(playerid, params[])
{
	new targetid, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /trazer [ID Fixo]");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(Player[targetid][pAdmin] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce nao pode fazer isso com um superior.");

    new Float:x, Float:y, Float:z;
    GetPlayerPos(playerid, x, y, z);
    SetPlayerPos(targetid, x + 1.0, y + 1.0, z);
    SetCameraBehindPlayer(targetid);
    SetPlayerInterior(targetid, GetPlayerInterior(playerid));
    SetPlayerVirtualWorld(targetid, GetPlayerVirtualWorld(playerid));

    new msg[96];
    format(msg, sizeof(msg), "[TP] Voce foi trazido ate %s.", pName(playerid));
    SendClientMessage(targetid, COR_ADM, msg);
    format(msg, sizeof(msg), "[TP] Voce trouxe %s ate voce.", pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:kick(playerid, params[])
{
	new targetid, motivo[64], idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "is[64]", idFixoAlvo, motivo)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /kick [ID Fixo] [Motivo]");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(Player[targetid][pAdmin] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce nao pode kickar um superior ou igual.");

    new msg[128];
    format(msg, sizeof(msg), "[KICK] %s (ID fixo %d) foi kickado pelo %s %s. Motivo: %s", pName(targetid), Player[targetid][pIDFixo], GetCargoAdmName(Player[playerid][pAdmin]), pName(playerid), motivo);
    SendClientMessageToAll(COR_ERRO, msg);
    SetTimerEx("KickSeguro", 100, false, "i", targetid);
    return 1;
}

CMD:congelar(playerid, params[])
{
	new targetid, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /congelar [ID Fixo]");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(Player[targetid][pAdmin] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[Erro] Superior imune.");

    TogglePlayerControllable(targetid, false);

    new msg[96];
    format(msg, sizeof(msg), "Voce foi congelado por %s.", pName(playerid));
    SendClientMessage(targetid, COR_ADM, msg);
    format(msg, sizeof(msg), "[CONGELAR] Voce congelou %s com sucesso.", pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:descongelar(playerid, params[])
{
	new targetid, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /descongelar [ID Fixo]");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(Player[targetid][pFerido] == 1) return SendClientMessage(playerid, COR_ERRO, "[Erro] Este jogador esta ferido, use /reviver.");

    TogglePlayerControllable(targetid, true);

    new msg[96];
    format(msg, sizeof(msg), "Voce foi descongelado por %s.", pName(playerid));
    SendClientMessage(targetid, COR_SUCESSO, msg);
    format(msg, sizeof(msg), "[DESCONGELAR] Voce descongelou %s com sucesso.", pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:explodir(playerid, params[])
{
	new targetid, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /explodir [ID Fixo]");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(Player[targetid][pAdmin] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[Erro] Superior imune.");

    new Float:x, Float:y, Float:z;
    GetPlayerPos(targetid, x, y, z);
    CreateExplosion(x, y, z, 12, 5.0);

    new msg[96];
    format(msg, sizeof(msg), "Voce foi explodido por %s.", pName(playerid));
    SendClientMessage(targetid, COR_ERRO, msg);
    format(msg, sizeof(msg), "[EXPLODIR] Voce explodiu %s com sucesso.", pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:car(playerid, params[])
{
	new modelo[32], cor1, cor2, vehid = 0, tempNome[32];
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "s[32]ii", modelo, cor1, cor2)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /car [ID/Nome] [Cor 1] [Cor 2]");
    if(cor1 < 0 || cor1 > 255 || cor2 < 0 || cor2 > 255) return SendClientMessage(playerid, COR_ERRO, "[Erro] As cores devem estar entre 0 e 255.");
    if(modelo[0] >= '0' && modelo[0] <= '9')
    {
        vehid = strval(modelo);
    }
    else
    {
        for(new i = 400; i <= 611; i++)
        {
            GetVehicleModelName(i, tempNome, sizeof(tempNome));
            if(strfind(tempNome, modelo, true) != -1)
            {
                vehid = i;
                break;
            }
        }
    }
    if(vehid < 400 || vehid > 611) return SendClientMessage(playerid, COR_ERRO, "[Erro] Modelo de veiculo invalido. Use o ID 400-611 ou o nome do veiculo.");
    if(VehPublico[playerid] != -1)
    {
        DestroyVehicle(VehPublico[playerid]);
        VehPublico[playerid] = -1;
    }
    new Float:x, Float:y, Float:z, Float:a;
    GetPlayerPos(playerid, x, y, z);
    GetPlayerFacingAngle(playerid, a);

    VehPublico[playerid] = CreateVehicle(vehid, x + 2.0, y, z, a, cor1, cor2, -1);
    LinkVehicleToInterior(VehPublico[playerid], GetPlayerInterior(playerid));
    SetVehicleVirtualWorld(VehPublico[playerid], GetPlayerVirtualWorld(playerid));
    PutPlayerInVehicle(playerid, VehPublico[playerid], 0);
    
    new nomeVeh[32], msg[96];
    GetVehicleModelName(vehid, nomeVeh, sizeof(nomeVeh));
    format(msg, sizeof(msg), "[VEICULO] %s criado (ID interno: %d).", nomeVeh, VehPublico[playerid]);
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:dararma(playerid, params[])
{
	new targetid, armaid, muns;
    new idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "iii", idFixoAlvo, armaid, muns)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /dararma [ID Fixo] [ID Arma 1-46] [Balas]");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(armaid < 1 || armaid > 46) return SendClientMessage(playerid, COR_ERRO, "[Erro] ID de arma invalido (1-46).");
    if(muns < 1 || muns > 60000) return SendClientMessage(playerid, COR_ERRO, "[Erro] Quantidade de balas invalida.");

    GivePlayerWeapon(targetid, armaid, muns);
    
    new nomeArma[32], msg[128];
    GetWeaponName(armaid, nomeArma, sizeof(nomeArma));
    format(msg, sizeof(msg), "[ARMA] Voce recebeu %s (%d balas) de %s.", nomeArma, muns, pName(playerid));
    SendClientMessage(targetid, COR_SUCESSO, msg);
    format(msg, sizeof(msg), "[ARMA] Voce deu %s (%d balas) para %s.", nomeArma, muns, pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:reviver(playerid, params[])
{
    new id;
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce precisa estar em modo de trabalho. Digite /tra");
    new idFixoAlvo;
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_INFO, "[USO] /reviver [ID Fixo]");
    id = ProcurarPorIDFixo(idFixoAlvo);
    if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(id)) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Jogador offline.");
    if(Player[id][pFerido] == 0) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Este jogador nao esta ferido/morto.");

    ReviverJogador(id);

    new msg[96];
    format(msg, sizeof(msg), "[REVIVER] Voce foi revivido por %s.", pName(playerid));
    SendClientMessage(id, COR_SUCESSO, msg);
    format(msg, sizeof(msg), "[REVIVER] Voce reviveu %s com sucesso.", pName(id));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:darvida(playerid, params[])
{
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    new targetid, Float:vida;
    new idFixoAlvo;
    if(sscanf(params, "if", idFixoAlvo, vida)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /darvida [ID Fixo] [Quantidade]");
    if(vida < 0.0 || vida > 100.0) return SendClientMessage(playerid, COR_ERRO, "[ERRO] A vida deve estar entre 1 e 100.");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(Player[targetid][pFerido] == 1) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador ferido, use /reviver.");

    SetPlayerHealth(targetid, vida);

    new msg[96];
    format(msg, sizeof(msg), "[VIDA] Voce recebeu %.0f de vida de %s.", vida, pName(playerid));
    SendClientMessage(targetid, COR_SUCESSO, msg);
    format(msg, sizeof(msg), "[VIDA] Voce deu %.0f de vida para %s.", vida, pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:darcolete(playerid, params[])
{
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    new targetid, Float:colete;
    new idFixoAlvo;
    if(sscanf(params, "if", idFixoAlvo, colete)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /darcolete [ID Fixo] [Quantidade]");
    if(colete < 0.0 || colete > 100.0) return SendClientMessage(playerid, COR_ERRO, "[ERRO] O colete deve estar entre 0 e 100.");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");

    SetPlayerArmour(targetid, colete);

    new msg[96];
    format(msg, sizeof(msg), "[COLETE] Voce recebeu %.0f de colete de %s.", colete, pName(playerid));
    SendClientMessage(targetid, COR_SUCESSO, msg);
    format(msg, sizeof(msg), "[COLETE] Voce deu %.0f de colete para %s.", colete, pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:repararveh(playerid, params[])
{
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(!IsPlayerInAnyVehicle(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce nao esta em um veiculo.");

    RepairVehicle(GetPlayerVehicleID(playerid));
    SendClientMessage(playerid, COR_SUCESSO, "[INFO] Veiculo reparado.");
    return 1;
}

CMD:puxarveh(playerid, params[])
{
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    new vehid;
    if(sscanf(params, "i", vehid)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /puxarveh [ID do carro criado]");
    if(vehid < 1 || vehid >= MAX_VEHICLES) return SendClientMessage(playerid, COR_ERRO, "[Erro] ID de veiculo invalido.");
    if(GetVehicleModel(vehid) == 0) return SendClientMessage(playerid, COR_ERRO, "[Erro] Este veiculo nao existe.");

    new Float:x, Float:y, Float:z;
    GetPlayerPos(playerid, x, y, z);
    SetVehiclePos(vehid, x + 2.0, y, z);
    LinkVehicleToInterior(vehid, GetPlayerInterior(playerid));
    SetVehicleVirtualWorld(vehid, GetPlayerVirtualWorld(playerid));

    new msg[96];
    format(msg, sizeof(msg), "[VEICULO] Voce puxou o veiculo ID %d para sua posicao.", vehid);
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:trazertodos(playerid, params[])
{
    if(Player[playerid][pAdmin] < ADM_DIRETOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");

    foreach(Player, i)
    {
        if(i != playerid)
        {
            SetPVarInt(i, "ID_Adm_Tp", playerid);
            ShowPlayerDialog(i, DIALOG_TPTODOS, DIALOG_STYLE_MSGBOX, "Teletransporte Coletivo", "O administrador deseja puxar todos ate ele. Aceita ir?", "Sim", "Nao");
        }
    }
    SendClientMessage(playerid, COR_SUCESSO, "Convite de teletransporte enviado para todos online.");
    return 1;
}

CMD:dargrana(playerid, params[])
{
	new targetid, quantia;
    new idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_DIRETOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "ii", idFixoAlvo, quantia)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /dargrana [ID Fixo] [Quantia]");
    if(quantia <= 0) return SendClientMessage(playerid, COR_ERRO, "[ERRO] A quantia deve ser maior que zero.");
    targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(targetid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Offline.");

    sGivePlayerCash(targetid, quantia);

    new msg[96];
    format(msg, sizeof(msg), "[GRANA] Voce recebeu $%d de %s.", quantia, pName(playerid));
    SendClientMessage(targetid, COR_SUCESSO, msg);
    format(msg, sizeof(msg), "[GRANA] Voce deu $%d para %s.", quantia, pName(targetid));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:daradmin(playerid, params[])
{
    if(Player[playerid][pAdmin] < ADM_FUNDADOR) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao.");
    new idFixoAlvo, nivel;
    if(sscanf(params, "ii", idFixoAlvo, nivel)) return SendClientMessage(playerid, COR_INFO, "[USO] /daradmin [ID Fixo] [Nivel]");
    if(nivel < 0 || nivel > ADM_FUNDADOR) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nivel deve ser entre 0 e 6.");
    new targetid = ProcurarPorIDFixo(idFixoAlvo);
    if(targetid == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(Player[targetid][pAdmin] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao pode alterar um administrador igual ou superior.");
    
    Player[targetid][pAdmin] = nivel;
	AdminRegistroAtualizar(targetid);
	SalvarConta(targetid);
    
    new msg[128];
    format(msg, sizeof(msg), "[INFO] %s %s definiu seu cargo administrativo para %s.", GetCargoAdmName(Player[playerid][pAdmin]), pName(playerid), GetCargoAdmName(nivel));
    SendClientMessage(targetid, COR_ADM, msg);
    format(msg, sizeof(msg), "[INFO] Voce definiu o cargo de %s para %s.", pName(targetid), GetCargoAdmName(nivel));
    SendClientMessage(playerid, COR_SUCESSO, msg);
    return 1;
}

CMD:cadeia(playerid, params[])
{
    new id, minutos, motivo[128], idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_SUPORTE) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "iis[128]", idFixoAlvo, minutos, motivo)) return SendClientMessage(playerid, COR_INFO, "[USO] /cadeia [ID Fixo] [Minutos] [Motivo]");
    if(minutos <= 0 || minutos > 1440) return SendClientMessage(playerid, COR_ERRO, "[ERRO] O tempo deve ser entre 1 e 1440 minutos.");
    id = ProcurarPorIDFixo(idFixoAlvo);
    if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(id)) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Jogador off-line");
    if(Player[id][pAdmin] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Este adm possui o cargo igual ou superior");
    if(Player[id][pPreso] == 1) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Este jogador ja esta preso");

    if(Player[id][pFerido] == 1) ReviverJogador(id);
    Player[id][pPreso] = 1;
    Player[id][pTempoCadeia] = minutos * 60;
    TeleportarParaCadeia(id);

    new string[200];
    format(string, sizeof(string), "[CADEIA] O Administrador %s prendeu %s por %d minutos. Motivo: %s", pName(playerid), pName(id), minutos, motivo);
    SendClientMessageToAll(COR_ERRO, string);

    new cadeia[64];
    format(cadeia, sizeof(cadeia), "Cadeias/%s.ini", pName(id));
    DOF2_CreateFile(cadeia);
    DOF2_SetString(cadeia, "Jogador", pName(id));
    DOF2_SetString(cadeia, "Responsavel", pName(playerid));
    DOF2_SetString(cadeia, "Motivo", motivo);
    DOF2_SetInt(cadeia, "Tempo", minutos);
    DOF2_SaveFile();
    return 1;
}

CMD:soltarcadeia(playerid, params[])
{
    new id, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_ERRO, "[Uso] /soltarcadeia [ID Fixo]");
    id = ProcurarPorIDFixo(idFixoAlvo);
    if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(Player[id][pPreso] == 0) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador nao esta preso.");

    if(TimerCadeia[id] != -1)
    {
        KillTimer(TimerCadeia[id]);
        TimerCadeia[id] = -1;
    }
    PlayerTextDrawHide(id, MsgTD[id]);
    Player[id][pPreso] = 0;
    Player[id][pTempoCadeia] = 0;
    SetPlayerInterior(id, 0);
    SetPlayerVirtualWorld(id, 0);
    SetPlayerHealth(id, 100.0);
    SetPlayerPos(id, 1544.2446, -1675.0531, 13.5550);
	SetCameraBehindPlayer(id);
	
    new string[128];
    format(string, sizeof(string), "[CADEIA] O Administrador %s soltou %s da cadeia.", pName(playerid), pName(id));
    SendClientMessageToAll(COR_ERRO, string);
    return 1;
}

CMD:mutar(playerid, params[])
{
    new id, minutos, idFixoAlvo, motivo[128];
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "iis[128]", idFixoAlvo, minutos, motivo)) return SendClientMessage(playerid, COR_INFO, "[Uso] /mutar [ID Fixo] [Minutos] [Motivo]");
    if(minutos <= 0 || minutos > 1440) return SendClientMessage(playerid, COR_ERRO, "[ERRO] O tempo deve ser entre 1 e 1440 minutos.");
    id = ProcurarPorIDFixo(idFixoAlvo);
    if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(id)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(Player[id][pAdmin] >= Player[playerid][pAdmin]) return SendClientMessage(playerid, COR_ERRO, "[Erro] Superior imune.");

    Player[id][pMutado] = 1;
    Player[id][pTempoMuted] = minutos * 60;

    if(TimerMuted[id] != -1)
    {
        KillTimer(TimerMuted[id]);
        TimerMuted[id] = -1;
    }
    TimerMuted[id] = SetTimerEx("CronometroMuted", 1000, true, "i", id);

    new msg[128];
    format(msg, sizeof(msg), "[MUTE] O Administrador %s mutou %s por %d minutos. Motivo: %s", pName(playerid), pName(id), minutos, motivo);
    SendClientMessageToAll(COR_ERRO, msg);
    return 1;
}

CMD:desmutar(playerid, params[])
{
    new id, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_INFO, "[Uso] /desmutar [ID Fixo]");
    id = ProcurarPorIDFixo(idFixoAlvo);
    if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(id)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(Player[id][pMutado] == 0) return SendClientMessage(playerid, COR_ERRO, "[Erro] Este jogador nao esta mutado.");

    if(TimerMuted[id] != -1)
    {
        KillTimer(TimerMuted[id]);
        TimerMuted[id] = -1;
    }
    Player[id][pMutado] = 0;
    Player[id][pTempoMuted] = 0;

    new str[128];
    format(str, sizeof(str), "[MUTE] O administrador %s desmultou %s", pName(playerid), pName(id));
    SendClientMessageToAll(COR_ERRO, str);
    return 1;
}

CMD:tv(playerid, params[])
{
    new id, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_MODERADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_INFO, "[Uso] /tv [ID Fixo]");
    id = ProcurarPorIDFixo(idFixoAlvo);
    if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(id)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");
    if(id == playerid) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce nao pode telar a si mesmo.");

    if(GetPVarInt(playerid, "SpecTarget") == -1)
    {
        new Float:x, Float:y, Float:z;
        GetPlayerPos(playerid, x, y, z);
        SetPVarFloat(playerid, "SpecPosX", x);
        SetPVarFloat(playerid, "SpecPosY", y);
        SetPVarFloat(playerid, "SpecPosZ", z);
        SetPVarInt(playerid, "SpecInterior", GetPlayerInterior(playerid));
        SetPVarInt(playerid, "SpecVirtualWorld", GetPlayerVirtualWorld(playerid));
    }
    SetPVarInt(playerid, "SpecTarget", id);
    TogglePlayerSpectating(playerid, true);
    SetPlayerInterior(playerid, GetPlayerInterior(id));
    SetPlayerVirtualWorld(playerid, GetPlayerVirtualWorld(id));

    if(IsPlayerInAnyVehicle(id))
    {
        PlayerSpectateVehicle(playerid, GetPlayerVehicleID(id));
    }
    else
    {
        PlayerSpectatePlayer(playerid, id);
    }
    AtualizarTVInfo(playerid, id);
    SendClientMessage(playerid, COR_SUCESSO, "[TV] Voce esta telando o jogador. Use /tvoff para sair.");
    return 1;
}

CMD:tvoff(playerid)
{
    if(GetPVarInt(playerid, "SpecTarget") == -1) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce nao esta telando ninguem.");
    TogglePlayerSpectating(playerid, false);
    SetPVarInt(playerid, "SpecTarget", -1);
    SetPlayerInterior(playerid, GetPVarInt(playerid, "SpecInterior"));
    SetPlayerVirtualWorld(playerid, GetPVarInt(playerid, "SpecVirtualWorld"));
    SetPlayerPos(playerid, GetPVarFloat(playerid, "SpecPosX"), GetPVarFloat(playerid, "SpecPosY"), GetPVarFloat(playerid, "SpecPosZ"));
    SetCameraBehindPlayer(playerid);
    PlayerTextDrawHide(playerid, MsgTD[playerid]);
    SendClientMessage(playerid, COR_SUCESSO, "[TV] Voce saiu do modo de televisao.");
    return 1;
}

CMD:tirararma(playerid, params[])
{
    new id, idFixoAlvo;
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", idFixoAlvo)) return SendClientMessage(playerid, COR_INFO, "[Uso] /tirararma [ID Fixo]");
    id = ProcurarPorIDFixo(idFixoAlvo);
    if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Nenhum jogador online com esse ID fixo.");
    if(!IsPlayerConnected(id)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Jogador offline.");

    ResetPlayerWeapons(id);
    
	new str[128];
    format(str, sizeof(str), "[ARMA] O administrador %s removeu suas armas.", pName(playerid));
    SendClientMessage(id, COR_ERRO, str);
    format(str, sizeof(str), "[ARMA] Voce removeu as armas de %s com sucesso.", pName(id));
    SendClientMessage(playerid, COR_SUCESSO, str);
    return 1;
}

CMD:criarcasa(playerid, params[])
{
	new classeTexto[16], classe = -1;
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "s[16]", classeTexto)) return SendClientMessage(playerid, COR_INFO, "[Uso] /criarcasa [Popular/Media/Alta/Luxo]");
    for(new i = 0;i < CASA_CLASSES;i++)
    {
        if(strcmp(classeTexto, NomeClasseCasa[i], true) == 0)
        {
            classe = i;
            break;
        }
    }
    if(classe == -1) return SendClientMessage(playerid, COR_ERRO, "[CASA] Classe invalida. Use: Popular, Media, Alta ou Luxo.");
    if(TotalCasas >= MAX_CASAS) return SendClientMessage(playerid, COR_ERRO, "[CASA] Limite maximo de residencias atingido.");
    if(GetPlayerInterior(playerid) != 0 || GetPlayerVirtualWorld(playerid) != 0) return SendClientMessage(playerid, COR_ERRO, "[CASA] Crie a residencia no mundo exterior (interior 0 / virtual world 0).");

    new casaid = TotalCasas;
    new Float:x, Float:y, Float:z, Float:a;
    GetPlayerPos(playerid, x, y, z);
    GetPlayerFacingAngle(playerid, a);

    CasaInfo[casaid][casaClasse] = classe;
    CasaInfo[casaid][casaExtX] = x;
    CasaInfo[casaid][casaExtY] = y;
    CasaInfo[casaid][casaExtZ] = z;
    CasaInfo[casaid][casaExtA] = a;
    CasaInfo[casaid][casaValor] = ValorClasseCasa[classe];
    CasaInfo[casaid][casaPickup] = -1;
    CasaInfo[casaid][casaIcon] = -1;
    CasaInfo[casaid][casaLabel] = Text3D:INVALID_3DTEXT_ID;
    TotalCasas++;
    
    CasaResetar(casaid);
    CasaAtualizarVisual(casaid);
    CasaSalvar(casaid);
    CasaSalvarConfig();

    new string[128];
    format(string, sizeof(string), "[CASA] Residencia #%d [Classe: %s] [Valor: $%d] criada com sucesso.", casaid, NomeClasseCasa[classe], ValorClasseCasa[classe]);
    SendClientMessage(playerid, COR_SUCESSO, string);
    return 1;
}

CMD:criarempresa(playerid, params[])
{
    new tipoTexto[24], tipo = 0;
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "s[24]", tipoTexto)) return SendClientMessage(playerid, COR_INFO, "[Uso] /criarempresa [247/Roupas/Armas/Restaurante/Bar/Posto]");
    if(!strcmp(tipoTexto, "247", true) || !strcmp(tipoTexto, "24/7", true)) tipo = EMPRESA_24_7;
    else if(!strcmp(tipoTexto, "roupas", true)) tipo = EMPRESA_ROUPAS;
    else if(!strcmp(tipoTexto, "armas", true)) tipo = EMPRESA_ARMAS;
    else if(!strcmp(tipoTexto, "restaurante", true)) tipo = EMPRESA_RESTAURANTE;
    else if(!strcmp(tipoTexto, "bar", true)) tipo = EMPRESA_BAR;
    else if(!strcmp(tipoTexto, "posto", true)) tipo = EMPRESA_POSTO;
    if(tipo == 0) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Tipo invalido.");
    if(TotalEmpresas >= MAX_EMPRESAS) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Limite maximo atingido.");
    if(GetPlayerInterior(playerid) != 0 || GetPlayerVirtualWorld(playerid) != 0) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Crie a empresa no mundo exterior (interior 0 / vw 0).");

    new empresaid = TotalEmpresas;
    new Float:x, Float:y, Float:z, Float:a;
    GetPlayerPos(playerid, x, y, z);
    GetPlayerFacingAngle(playerid, a);
    
    EmpresaInfo[empresaid][empresaTipo] = tipo;
    EmpresaInfo[empresaid][empresaExtX] = x;
    EmpresaInfo[empresaid][empresaExtY] = y;
    EmpresaInfo[empresaid][empresaExtZ] = z;
    EmpresaInfo[empresaid][empresaExtA] = a;
    EmpresaInfo[empresaid][empresaValor] = ValorEmpresa[tipo];
    EmpresaInfo[empresaid][empresaOcupada] = false;
    EmpresaInfo[empresaid][empresaSaldo] = 0;
    EmpresaInfo[empresaid][empresaDono][0] = EOS;
    EmpresaInfo[empresaid][empresaBombaX] = 0.0;
    EmpresaInfo[empresaid][empresaBombaY] = 0.0;
    EmpresaInfo[empresaid][empresaBombaZ] = 0.0;
    EmpresaInfo[empresaid][empresaPrecoGasolina] = 5;
    EmpresaInfo[empresaid][empresaSemana] = EmpresaSemanaAtual();
    EmpresaInfo[empresaid][empresaVendasSemana] = 0;
    for(new i = 0; i < 7; i++) EmpresaInfo[empresaid][empresaVendasDia][i] = 0;
    for(new p = 0; p < MAX_PRODUTOS; p++) EmpresaEstoque[empresaid][p] = 0;
    EmpresaInfo[empresaid][empresaPickup] = -1;
    EmpresaInfo[empresaid][empresaPickupBomba] = -1;
    EmpresaInfo[empresaid][empresaLabel] = Text3D:INVALID_3DTEXT_ID;
    EmpresaInfo[empresaid][empresaLabelBomba] = Text3D:INVALID_3DTEXT_ID;
    EmpresaInfo[empresaid][empresaCP] = -1;
    EmpresaInfo[empresaid][empresaIcon] = -1;
    TotalEmpresas++;
    
    EmpresaAtualizarVisual(empresaid);
    EmpresaSalvar(empresaid);
    EmpresaSalvarConfig();

    new texto[220];
    format(texto, sizeof(texto), "[EMPRESA] %s criada com ID %d. Valor: $%d.", EmpresaNomeTipo(tipo), empresaid, EmpresaInfo[empresaid][empresaValor]);
    SendClientMessage(playerid, COR_SUCESSO, texto);

    if(tipo == EMPRESA_POSTO)
    {
        format(texto, sizeof(texto), "[POSTO] Agora va ate o local da bomba e use /definibomba %d.", empresaid);
        SendClientMessage(playerid, COR_INFO, texto);
    }
    return 1;
}

CMD:definibomba(playerid, params[])
{
	new empresaid;
    if(Player[playerid][pAdmin] < ADM_ADMINISTRADOR) return SendClientMessage(playerid, COR_ERRO, "[Erro] Sem permissao.");
    if(!AdmPodeUsar(playerid)) return SendClientMessage(playerid, COR_ERRO, "[Erro] Voce precisa estar em modo de trabalho. Digite /tra");
    if(sscanf(params, "i", empresaid)) return SendClientMessage(playerid, COR_INFO, "[Uso] /definibomba [ID Empresa]");
    if(!EmpresaValida(empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] ID invalido.");
    if(EmpresaInfo[empresaid][empresaTipo] != EMPRESA_POSTO) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Essa empresa nao e um posto.");

    new Float:x, Float:y, Float:z;
    GetPlayerPos(playerid, x, y, z);
    EmpresaInfo[empresaid][empresaBombaX] = x;
    EmpresaInfo[empresaid][empresaBombaY] = y;
    EmpresaInfo[empresaid][empresaBombaZ] = z;
    
    EmpresaAtualizarVisual(empresaid);
    EmpresaSalvar(empresaid);
    SendClientMessage(playerid, COR_SUCESSO, "[EMPRESA] Bomba de combustivel definida neste local.");
    return 1;
}

CMD:precobomba(playerid)
{
    new empresaid = EmpresaNoInterior(playerid);
    if(empresaid == -1) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce precisa estar dentro da empresa.");
    if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Apenas o proprietario pode alterar precos.");
    if(EmpresaInfo[empresaid][empresaTipo] != EMPRESA_POSTO) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Esta empresa nao e um posto. Use /menuempresa > Definir precos.");
    new texto[200];
    format(texto, sizeof(texto), "{FFFFFF}Preco atual: {00FF00}$%d {FFFFFF}por litro\n\n{FFFFFF}Digite o novo valor.\n{FFFFFF}Minimo: {00FF00}$1  {FFFFFF}Maximo: {00FF00}$20", EmpresaInfo[empresaid][empresaPrecoGasolina]);
    SetPVarInt(playerid, "EmpresaMenuID", empresaid);
    ShowPlayerDialog(playerid, DIALOG_EMPRESA_PRECO_GASOLINA, DIALOG_STYLE_INPUT, "Preco da Gasolina", texto, "Confirmar", "Cancelar");
    return 1;
}

CMD:menucasa(playerid, params[])
{
    new casaid = CasaNoInterior(playerid);
    if(casaid == -1) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce precisa estar dentro da sua residencia.");
    if(CasaInfo[casaid][casaOcupada] == false) return SendClientMessage(playerid, COR_ERRO, "[CASA] Esta residencia esta a venda.");
    if(!CasaEhDono(playerid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Apenas o proprietario pode usar este menu.");
    return CasaMostrarMenu(playerid, casaid);
}

CMD:menuempresa(playerid)
{
    new empresaid = EmpresaNoInterior(playerid);
    if(empresaid == -1) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce precisa estar dentro da empresa.");
    if(EmpresaInfo[empresaid][empresaOcupada] == false) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Esta empresa esta a venda.");
    if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Apenas o proprietario pode usar este menu.");
    return EmpresaAbrirMenu(playerid, empresaid);
}

CMD:anims(playerid)
{
    PararAnimacao(playerid);
    ShowPlayerDialog(playerid, DIALOG_ANIMS_CATEGORIAS, DIALOG_STYLE_LIST, "Animacoes", "{FF8C00}1. {FFFFFF}Dancas\n{FF8C00}2. {FFFFFF}Emocoes\n{FF8C00}3. {FFFFFF}Baforada\n{FF8C00}4. {FFFFFF}Beijos\n{FF8C00}5. {FFFFFF}Acoes\n{FF8C00}6. {FFFFFF}Sentado\n{FF8C00}7. {FFFFFF}Luta\n{FF8C00}8. {FFFFFF}Outros", "Selecionar", "Fechar");
    return 1;
}

CMD:jbl(playerid)
{
    return JBLMenu(playerid);
}

CMD:presidio(playerid)
{
    if(Player[playerid][pAdmin] < ADM_FUNDADOR) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao.");
    SetPlayerInterior(playerid, 0);
    SetPlayerVirtualWorld(playerid, 0);
    SetPlayerPos(playerid, 203.1500, 1391.1500, 551.1900);
    SetPlayerFacingAngle(playerid, 90.0);
    SetCameraBehindPlayer(playerid);
    return 1;
}
CMD:bar(playerid)
{
    if(Player[playerid][pAdmin] < ADM_FUNDADOR) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao.");
    SetPlayerInterior(playerid, 0);
    SetPlayerVirtualWorld(playerid, 0);
    SetPlayerPos(playerid, 2082.2981, 2343.8398, -89.1932);
    SetPlayerFacingAngle(playerid, 180.0);
    SetCameraBehindPlayer(playerid);
    return 1;
}
CMD:ilha(playerid)
{
    if(Player[playerid][pAdmin] < ADM_FUNDADOR) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao.");
    SetPlayerInterior(playerid, 0);
    SetPlayerVirtualWorld(playerid, 0);
    SetPlayerPos(playerid, 976.4410, -3189.6360, -28.2820);
    SetPlayerFacingAngle(playerid, 0.0);
    SetCameraBehindPlayer(playerid);
    return 1;
}
CMD:delegacia(playerid)
{
    if(Player[playerid][pAdmin] < ADM_FUNDADOR) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao.");
    SetPlayerInterior(playerid, 0);
    SetPlayerVirtualWorld(playerid, 0);
    SetPlayerPos(playerid, 651.2797, 2537.4397, -89.4550);
    SetPlayerFacingAngle(playerid, 90.0);
    SetCameraBehindPlayer(playerid);
    return 1;
}
CMD:prefeitura(playerid)
{
    if(Player[playerid][pAdmin] < ADM_FUNDADOR) return SendClientMessage(playerid, COR_ERRO, "[ERRO] Voce nao tem permissao.");
    SetPlayerInterior(playerid, 0);
    SetPlayerVirtualWorld(playerid, 0);
    SetPlayerPos(playerid, 1482.1810, -1780.9090, 1816.0000);
    SetPlayerFacingAngle(playerid, 180.0);
    SetCameraBehindPlayer(playerid);
    return 1;
}

stock InteracaoTeclaH(playerid)
{
    if(IsPlayerInRangeOfPoint(playerid, 2.0, 1676.4017, -2309.8730, 13.5472))
    {
        ShowPlayerDialog(playerid, DIALOG_INFO_SPAWN, DIALOG_STYLE_LIST, "Iniciante", "[1] Alugar Motinha", "Selecionar", "Cancelar");
        return 1;
    }
    else if(IsPlayerInRangeOfPoint(playerid, 2.0, 870.3837, -25.1340, 63.9656))
    {
        SetPlayerInterior(playerid, 0);
        SetPlayerPos(playerid, 504.8922, -2318.2366, 512.7908);
        SetPlayerFacingAngle(playerid, 183.5712);
        SetCameraBehindPlayer(playerid);
        return 1;
    }
    else if(IsPlayerInRangeOfPoint(playerid, 2.0, 504.8922, -2318.2366, 512.7908))
    {
        SetPlayerInterior(playerid, 0);
        SetPlayerPos(playerid, 870.3837, -25.1340, 63.9656);
        SetCameraBehindPlayer(playerid);
        return 1;
    }
    else if(IsPlayerInRangeOfPoint(playerid, 2.0, 1481.0408, -1771.6692, 18.7891))
    {
        SetPlayerInterior(playerid, 3);
        SetPlayerPos(playerid, 388.5677, 173.8163, 1008.3828);
        SetCameraBehindPlayer(playerid);
        return 1;
    }
    else if(IsPlayerInRangeOfPoint(playerid, 2.0, 388.5677, 173.8163, 1008.3828))
    {
        SetPlayerInterior(playerid, 0);
        SetPlayerPos(playerid, 1481.0408, -1771.6692, 18.7891);
        SetCameraBehindPlayer(playerid);
        return 1;
    }
    return 0;
}

stock CasaValida(casaid)
{
    return (casaid >= 0 && casaid < TotalCasas);
}

stock CasaClasseValida(classe)
{
    return (classe >= 0 && classe < CASA_CLASSES);
}

stock CasaEhDono(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;
    if(!CasaInfo[casaid][casaOcupada]) return 0;
    if(CasaInfo[casaid][casaDono][0] == EOS) return 0;
    return (strcmp(CasaInfo[casaid][casaDono], pName(playerid), true) == 0);
}

stock CasaEhMorador(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;
    for(new i = 0;i < MAX_MORADORES_CASA;i++)
    {
        if(CasaMorador[casaid][i][0] == EOS) continue;
        if(strcmp(CasaMorador[casaid][i], pName(playerid), true) == 0) return 1;
    }
    return 0;
}

stock CasaBuscarPorDono(playerid)
{
    for(new i = 0;i < TotalCasas;i++)
    {
        if(CasaEhDono(playerid, i)) return i;
    }
    return -1;
}

stock ProcurarPorNomeDono(casaid)
{
    if(!CasaValida(casaid)) return INVALID_PLAYER_ID;
    if(CasaInfo[casaid][casaDono][0] == EOS) return INVALID_PLAYER_ID;
    foreach(Player, i)
    {
        if(strcmp(pName(i), CasaInfo[casaid][casaDono], true) == 0) return i;
    }
    return INVALID_PLAYER_ID;
}

stock CasaJogadorPossuiCasa(playerid)
{
    return (CasaBuscarPorDono(playerid) != -1);
}

stock CasaJogadorMorador(playerid)
{
    for(new i = 0;i < TotalCasas;i++)
    {
        if(!CasaInfo[i][casaOcupada]) continue;
        if(CasaEhMorador(playerid, i)) return i;
    }
    return -1;
}

stock CasaJogadorAutorizado(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;
    if(!CasaInfo[casaid][casaOcupada]) return 0;
    if(!CasaInfo[casaid][casaTrancada]) return 1;
    if(CasaEhDono(playerid, casaid)) return 1;
    if(CasaEhMorador(playerid, casaid)) return 1;
    return 0;
}

stock CasaRecontarMoradores(casaid)
{
    if(!CasaValida(casaid)) return 0;
    new total = 0;
    for(new i = 0;i < MAX_MORADORES_CASA;i++)
    {
        if(CasaMorador[casaid][i][0] != EOS) total++;
    }
    CasaInfo[casaid][casaTotalMoradores] = total;
    return total;
}

stock CasaNoInterior(playerid)
{
    new vw = GetPlayerVirtualWorld(playerid);
    if(vw < CASA_VW_BASE || vw >= CASA_VW_BASE + MAX_CASAS) return -1;
    new casaid = vw - CASA_VW_BASE;
    if(!CasaValida(casaid)) return -1;
    new classe = CasaInfo[casaid][casaClasse];
    if(!CasaClasseValida(classe)) return -1;
    if(GetPlayerInterior(playerid) != InteriorClasseCasa[classe]) return -1;
    return casaid;
}

stock CasaMaisProxima(playerid, Float:raio)
{
    if(GetPlayerVirtualWorld(playerid) != 0 || GetPlayerInterior(playerid) != 0) return -1;
    new encontrada = -1;
    new Float:menor = raio + 1.0, Float:dist;
    for(new i = 0;i < TotalCasas;i++)
    {
        if(!IsPlayerInRangeOfPoint(playerid, raio, CasaInfo[i][casaExtX], CasaInfo[i][casaExtY], CasaInfo[i][casaExtZ])) continue;
        dist = GetPlayerDistanceFromPoint(playerid, CasaInfo[i][casaExtX], CasaInfo[i][casaExtY], CasaInfo[i][casaExtZ]);
        if(dist < menor)
        {
            menor = dist;
            encontrada = i;
        }
    }
    return encontrada;
}

stock CasaPertoDaEntrada(playerid, casaid, Float:raio)
{
    if(!CasaValida(casaid)) return 0;
    return IsPlayerInRangeOfPoint(playerid, raio, CasaInfo[casaid][casaExtX], CasaInfo[casaid][casaExtY], CasaInfo[casaid][casaExtZ]);
}

stock CasaDestruirVisual(casaid)
{
    if(casaid < 0 || casaid >= MAX_CASAS) return 0;
    if(CasaInfo[casaid][casaPickup] != -1)
    {
        DestroyDynamicPickup(CasaInfo[casaid][casaPickup]);
        CasaInfo[casaid][casaPickup] = -1;
    }
    if(CasaInfo[casaid][casaLabel] != Text3D:INVALID_3DTEXT_ID)
    {
        DestroyDynamic3DTextLabel(CasaInfo[casaid][casaLabel]);
        CasaInfo[casaid][casaLabel] = Text3D:INVALID_3DTEXT_ID;
    }
    if(CasaInfo[casaid][casaIcon] != -1)
    {
        DestroyDynamicMapIcon(CasaInfo[casaid][casaIcon]);
        CasaInfo[casaid][casaIcon] = -1;
    }
    return 1;
}

stock CasaAtualizarVisual(casaid)
{
    if(!CasaValida(casaid)) return 0;
    
    CasaDestruirVisual(casaid);

    new string[192];
    new classe = CasaInfo[casaid][casaClasse];
    if(!CasaClasseValida(classe)) classe = 0;
    if(CasaInfo[casaid][casaOcupada])
    {
        CasaInfo[casaid][casaPickup] = CreateDynamicPickup(1272, 1, CasaInfo[casaid][casaExtX], CasaInfo[casaid][casaExtY], CasaInfo[casaid][casaExtZ], 0);
        CasaInfo[casaid][casaIcon] = CreateDynamicMapIcon(CasaInfo[casaid][casaExtX], CasaInfo[casaid][casaExtY], CasaInfo[casaid][casaExtZ], 32, -1);
        format(string, sizeof(string), "{FFFFFF}Residencia {DC143C}#%d\n{FFFFFF}Classe: {DC143C}%s\n{FFFFFF}Proprietario: {FFFF00}%s\n{FFFFFF}Estado: %s\n{FFFFFF}Pressione {DC143C}'H' {FFFFFF}para entrar", 
            casaid, NomeClasseCasa[classe], CasaInfo[casaid][casaDono], 
            (CasaInfo[casaid][casaTrancada]) ? ("{FF0000}Trancada") : ("{00FF00}Destrancada"));
    }
    else
    {
        CasaInfo[casaid][casaPickup] = CreateDynamicPickup(1273, 1, CasaInfo[casaid][casaExtX], CasaInfo[casaid][casaExtY], CasaInfo[casaid][casaExtZ], 0);
        CasaInfo[casaid][casaIcon] = CreateDynamicMapIcon(CasaInfo[casaid][casaExtX], CasaInfo[casaid][casaExtY], CasaInfo[casaid][casaExtZ], 31, -1);
        format(string, sizeof(string), "{FFFFFF}Residencia {DC143C}#%d\n{FFFFFF}Classe: {DC143C}%s\n{FFFFFF}Valor: {00FF00}$%d\n{00FF00}A VENDA\n{FFFFFF}Pressione {DC143C}'H' {FFFFFF}para interagir", 
            casaid, NomeClasseCasa[classe], CasaInfo[casaid][casaValor]);
    }
    CasaInfo[casaid][casaLabel] = CreateDynamic3DTextLabel(string, COR_INFO, CasaInfo[casaid][casaExtX], CasaInfo[casaid][casaExtY], CasaInfo[casaid][casaExtZ] + 0.5, 15.0);
    return 1;
}

stock CasaResetar(casaid)
{
    if(casaid < 0 || casaid >= MAX_CASAS) return 0;
    CasaInfo[casaid][casaOcupada] = false;
    CasaInfo[casaid][casaTrancada] = false;
    CasaInfo[casaid][casaDono][0] = EOS;
    for(new i = 0;i < MAX_MORADORES_CASA;i++) CasaMorador[casaid][i][0] = EOS;
    CasaInfo[casaid][casaTotalMoradores] = 0;
    return 1;
}

stock CasaLiberar(casaid)
{
    if(!CasaValida(casaid)) return 0;
    CasaExpulsarTodos(casaid);
    CasaInfo[casaid][casaOcupada] = false;
    CasaInfo[casaid][casaTrancada] = false;
    CasaInfo[casaid][casaDono][0] = EOS;
    for(new i = 0; i < MAX_MORADORES_CASA; i++) CasaMorador[casaid][i][0] = EOS;
    CasaInfo[casaid][casaTotalMoradores] = 0;
    CasaAtualizarVisual(casaid);
    CasaSalvar(casaid);
    return 1;
}

stock CasaExpulsarTodos(casaid, exceto = INVALID_PLAYER_ID)
{
    if(!CasaValida(casaid)) return 0;
    foreach(Player, i)
    {
        if(i == exceto) continue;
        if(CasaNoInterior(i) != casaid) continue;
        
        SetPlayerInterior(i, 0);
        SetPlayerVirtualWorld(i, 0);
        SetPlayerPos(i, CasaInfo[casaid][casaExtX], CasaInfo[casaid][casaExtY], CasaInfo[casaid][casaExtZ]);
        SetPlayerFacingAngle(i, CasaInfo[casaid][casaExtA]);
        SetCameraBehindPlayer(i);
        SendClientMessage(i, COR_ERRO, "[CASA] A residencia mudou de proprietario, voce foi retirado.");
    }
    return 1;
}

stock CasaSalvar(casaid)
{
    if(!CasaValida(casaid)) return 0;
    
    new file[32];
    format(file, sizeof(file), "Casas/%d.ini", casaid);
    if(!DOF2_FileExists(file)) DOF2_CreateFile(file);
    DOF2_SetInt(file, "Classe", CasaInfo[casaid][casaClasse]);
    DOF2_SetFloat(file, "ExtX", CasaInfo[casaid][casaExtX]);
    DOF2_SetFloat(file, "ExtY", CasaInfo[casaid][casaExtY]);
    DOF2_SetFloat(file, "ExtZ", CasaInfo[casaid][casaExtZ]);
    DOF2_SetFloat(file, "ExtA", CasaInfo[casaid][casaExtA]);
    DOF2_SetInt(file, "Valor", CasaInfo[casaid][casaValor]);
    if(CasaInfo[casaid][casaOcupada] == true)
    {
	    DOF2_SetString(file, "Dono", CasaInfo[casaid][casaDono]);
	}
	else
	{
	    DOF2_SetString(file, "Dono", "Ninguem");
	}
    DOF2_SetInt(file, "Ocupada", (CasaInfo[casaid][casaOcupada]) ? (1) : (0));
    DOF2_SetInt(file, "Trancada", (CasaInfo[casaid][casaTrancada]) ? (1) : (0));
    CasaRecontarMoradores(casaid);
    DOF2_SetInt(file, "TotalMoradores", CasaInfo[casaid][casaTotalMoradores]);
    new key[24];
    for(new i = 0;i < MAX_MORADORES_CASA;i++)
    {
        format(key, sizeof(key), "Morador%d", i);
        DOF2_SetString(file, key, CasaMorador[casaid][i]);
    }
    DOF2_SaveFile();
    return 1;
}

stock CasaCarregar(casaid)
{
    if(casaid < 0 || casaid >= MAX_CASAS) return 0;
    
    new file[32];
    format(file, sizeof(file), "Casas/%d.ini", casaid);
    if(!DOF2_FileExists(file)) return 0;
    CasaInfo[casaid][casaClasse] = DOF2_GetInt(file, "Classe");
    if(!CasaClasseValida(CasaInfo[casaid][casaClasse])) CasaInfo[casaid][casaClasse] = 0;

    CasaInfo[casaid][casaExtX] = DOF2_GetFloat(file, "ExtX");
    CasaInfo[casaid][casaExtY] = DOF2_GetFloat(file, "ExtY");
    CasaInfo[casaid][casaExtZ] = DOF2_GetFloat(file, "ExtZ");
    CasaInfo[casaid][casaExtA] = DOF2_GetFloat(file, "ExtA");
    CasaInfo[casaid][casaValor] = DOF2_GetInt(file, "Valor");
    if(CasaInfo[casaid][casaValor] <= 0) CasaInfo[casaid][casaValor] = ValorClasseCasa[CasaInfo[casaid][casaClasse]];
    format(CasaInfo[casaid][casaDono], MAX_PLAYER_NAME, "%s", DOF2_GetString(file, "Dono"));
    CasaInfo[casaid][casaOcupada] = (DOF2_GetInt(file, "Ocupada") == 1);
    CasaInfo[casaid][casaTrancada] = (DOF2_GetInt(file, "Trancada") == 1);

    new key[24];
    for(new i = 0;i < MAX_MORADORES_CASA;i++)
    {
        format(key, sizeof(key), "Morador%d", i);
        format(CasaMorador[casaid][i], MAX_PLAYER_NAME, "%s", DOF2_GetString(file, key));
    }
    if(CasaInfo[casaid][casaOcupada] && CasaInfo[casaid][casaDono][0] == EOS) CasaInfo[casaid][casaOcupada] = false;
    if(CasaInfo[casaid][casaOcupada] == false)
    {
        CasaInfo[casaid][casaDono][0] = EOS;
        CasaInfo[casaid][casaTrancada] = false;
        for(new i = 0;i < MAX_MORADORES_CASA;i++) CasaMorador[casaid][i][0] = EOS;
    }
    CasaRecontarMoradores(casaid);

    CasaInfo[casaid][casaPickup] = -1;
    CasaInfo[casaid][casaIcon] = -1;
    CasaInfo[casaid][casaLabel] = Text3D:INVALID_3DTEXT_ID;
    CasaAtualizarVisual(casaid);
    return 1;
}

stock CasaSalvarConfig()
{
    if(!DOF2_FileExists("Casas/config.ini")) DOF2_CreateFile("Casas/config.ini");
    DOF2_SetInt("Casas/config.ini", "Total", TotalCasas);
    DOF2_SaveFile();
    return 1;
}

stock CasaCarregarTodas()
{
    for(new i = 0;i < MAX_CASAS;i++)
    {
        CasaInfo[i][casaPickup] = -1;
        CasaInfo[i][casaIcon] = -1;
        CasaInfo[i][casaLabel] = Text3D:INVALID_3DTEXT_ID;
    }
    if(!DOF2_FileExists("Casas/config.ini"))
    {
        TotalCasas = 0;
        return 1;
    }
    TotalCasas = DOF2_GetInt("Casas/config.ini", "Total");
    if(TotalCasas < 0) TotalCasas = 0;
    if(TotalCasas > MAX_CASAS) TotalCasas = MAX_CASAS;
    for(new i = 0;i < TotalCasas;i++) CasaCarregar(i);
    return 1;
}

stock CasaMostrarInteracao(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;
    new string[512], classe = CasaInfo[casaid][casaClasse];
    if(!CasaClasseValida(classe)) classe = 0;

    format(string, sizeof(string), "{FFFFFF}Opcao\t{FFFFFF}Detalhe\n\
    {FF8C00}Ver informacoes\t{FFFFFF}Residencia #%d\n\
    {FF8C00}Entrar para visitar\t{FFFFFF}Classe %s\n\
    {FF8C00}Comprar residencia\t{00FF00}$%d", 
        casaid, NomeClasseCasa[classe], CasaInfo[casaid][casaValor]);

    SetPVarInt(playerid, "CasaProxima", casaid);
    ShowPlayerDialog(playerid, DIALOG_CASA_INTERACAO, DIALOG_STYLE_TABLIST_HEADERS, "Residencia a venda", string, "Selecionar", "Sair");
    return 1;
}

stock CasaMostrarMenu(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;
    
    CasaRecontarMoradores(casaid);

	new string[600];
    format(string, sizeof(string), "{FFFFFF}Opcao\t{FFFFFF}Detalhe\n\
    {FF8C00}Informacoes\t{FFFFFF}Residencia #%d\n\
    {FF8C00}Trancar / Destrancar\t%s\n\
    {FF8C00}Gerenciar moradores\t{FFFFFF}%d/%d\n\
    {FF8C00}Vender para o Estado\t{00FF00}$%d\n\
    {FF8C00}Vender para um jogador\t{00FF00}$%d", 
        casaid, 
        (CasaInfo[casaid][casaTrancada]) ? ("{FF0000}Trancada") : ("{00FF00}Destrancada"), 
        CasaInfo[casaid][casaTotalMoradores], MAX_MORADORES_CASA, 
        CasaInfo[casaid][casaValor] / 2, 
        CasaInfo[casaid][casaValor]
    );
    ShowPlayerDialog(playerid, DIALOG_CASA_MENU, DIALOG_STYLE_TABLIST_HEADERS, "Minha Residencia", string, "Selecionar", "Fechar");
    return 1;
}

stock CasaMostrarInfo(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;

    new classe = CasaInfo[casaid][casaClasse];
    if(!CasaClasseValida(classe)) classe = 0;
    
    new string[400];
    format(string, sizeof(string), "{FFFFFF}Dado\t{FFFFFF}Valor\n\
    {FF8C00}Numero\t{FFFFFF}#%d\n\
    {FF8C00}Classe\t{FFFFFF}%s\n\
    {FF8C00}Valor de mercado\t{00FF00}$%d\n\
    {FF8C00}Venda ao Estado\t{00FF00}$%d", 
        casaid, 
        NomeClasseCasa[classe], 
        CasaInfo[casaid][casaValor], 
        CasaInfo[casaid][casaValor] / 2
    );
    ShowPlayerDialog(playerid, DIALOG_CASA_INFO, DIALOG_STYLE_TABLIST_HEADERS, "Informacoes da residencia", string, "Voltar", "Fechar");
    return 1;
}

stock CasaMostrarMoradores(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;
    
    CasaRecontarMoradores(casaid);

	new string[600], linha[96];
    format(string, sizeof(string), "{FFFFFF}Slot\t{FFFFFF}Morador\t{FFFFFF}Status\n");
    for(new i = 0;i < MAX_MORADORES_CASA;i++)
    {
        if(CasaMorador[casaid][i][0] != EOS)
        {
            format(linha, sizeof(linha), "{FF8C00}%d\t{FFFF00}%s\t{00FF00}Ocupado\n", i + 1, CasaMorador[casaid][i]);
        }
        else
        {
            format(linha, sizeof(linha), "{FF8C00}%d\t{AFAFAF}---\t{FF0000}Vazio\n", i + 1);
        }
        strcat(string, linha, sizeof(string));
    }
    ShowPlayerDialog(playerid, DIALOG_CASA_MORADORES, DIALOG_STYLE_TABLIST_HEADERS, "Gerenciar moradores", string, "Selecionar", "Voltar");
    return 1;
}

stock CasaEntrar(playerid, casaid, bool:validarDistancia = true)
{
    if(!CasaValida(casaid)) return 0;
    if(validarDistancia && !CasaPertoDaEntrada(playerid, casaid, 2.0)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce precisa estar na entrada da residencia.");
    if(CasaInfo[casaid][casaOcupada] && !CasaJogadorAutorizado(playerid, casaid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Esta residencia esta trancada.");

    new classe = CasaInfo[casaid][casaClasse];
    if(!CasaClasseValida(classe)) classe = 0;

    SetPlayerInterior(playerid, InteriorClasseCasa[classe]);
    SetPlayerVirtualWorld(playerid, CASA_VW_BASE + casaid);
    SetPlayerPos(playerid, PosIntClasseCasa[classe][0], PosIntClasseCasa[classe][1], PosIntClasseCasa[classe][2]);
    SetPlayerFacingAngle(playerid, PosIntClasseCasa[classe][3]);
    SetCameraBehindPlayer(playerid);
    return 1;
}

stock CasaSair(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;
    SetPlayerInterior(playerid, 0);
    SetPlayerVirtualWorld(playerid, 0);
    SetPlayerPos(playerid, CasaInfo[casaid][casaExtX], CasaInfo[casaid][casaExtY], CasaInfo[casaid][casaExtZ]);
    SetPlayerFacingAngle(playerid, CasaInfo[casaid][casaExtA]);
    SetCameraBehindPlayer(playerid);
    return 1;
}

stock CasaComprar(playerid, casaid)
{
    if(!CasaValida(casaid)) return 0;
    if(CasaInfo[casaid][casaOcupada]) return SendClientMessage(playerid, COR_ERRO, "[CASA] Esta residencia ja foi vendida.");
    if(!CasaPertoDaEntrada(playerid, casaid, 2.0)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce precisa estar perto da residencia.");
    if(CasaJogadorPossuiCasa(playerid)) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja possui uma residencia.");
    if(CasaJogadorMorador(playerid) != -1) return SendClientMessage(playerid, COR_ERRO, "[CASA] Voce ja e morador de outra residencia. Saia dela primeiro.");
    if(GetPlayerCash(playerid) < CasaInfo[casaid][casaValor]) return SendClientMessage(playerid, COR_ERRO, "[CASA] Dinheiro insuficiente.");

    sGivePlayerCash(playerid, -CasaInfo[casaid][casaValor]);
    CasaInfo[casaid][casaOcupada] = true;
    CasaInfo[casaid][casaTrancada] = false;
    format(CasaInfo[casaid][casaDono], MAX_PLAYER_NAME, "%s", pName(playerid));
    for(new i = 0;i < MAX_MORADORES_CASA;i++) CasaMorador[casaid][i][0] = EOS;
    
    CasaRecontarMoradores(casaid);
    CasaAtualizarVisual(casaid);
    CasaSalvar(casaid);

    new string[128];
    format(string, sizeof(string), "[CASA] Parabens! Voce comprou a residencia #%d por $%d.", casaid, CasaInfo[casaid][casaValor]);
    SendClientMessage(playerid, COR_SUCESSO, string);
    return 1;
}

stock CasaTeclaH(playerid)
{
    new casaid = CasaNoInterior(playerid);
    if(casaid != -1)
    {
        new classe = CasaInfo[casaid][casaClasse];
        if(!CasaClasseValida(classe)) classe = 0;
        if(!IsPlayerInRangeOfPoint(playerid, 2.0, PosIntClasseCasa[classe][0], PosIntClasseCasa[classe][1], PosIntClasseCasa[classe][2])) return 0;
        CasaSair(playerid, casaid);
        return 1;
    }
    casaid = CasaMaisProxima(playerid, 2.0);
    if(casaid == -1) return 0;
    
    if(CasaInfo[casaid][casaOcupada] == false)
    {
        CasaMostrarInteracao(playerid, casaid);
        return 1;
    }
    if(!CasaJogadorAutorizado(playerid, casaid))
    {
        SendClientMessage(playerid, COR_ERRO, "[CASA] Esta residencia esta trancada.");
        return 1;
    }
    CasaEntrar(playerid, casaid);
    return 1;
}

stock EmpresaValida(empresaid)
{
    return (empresaid >= 0 && empresaid < TotalEmpresas);
}

stock EmpresaTipoValido(tipo)
{
    return (tipo >= EMPRESA_24_7 && tipo <= EMPRESA_POSTO);
}

stock EmpresaNomeTipo(tipo)
{
    new nomeEmp[24];
    if(!EmpresaTipoValido(tipo)) format(nomeEmp, sizeof(nomeEmp), "Empresa");
    else format(nomeEmp, sizeof(nomeEmp), "%s", NomeEmpresa[tipo]);
    return nomeEmp;
}

stock EmpresaEhDono(playerid, empresaid)
{
    if(!EmpresaValida(empresaid)) return false;
    if(!EmpresaInfo[empresaid][empresaOcupada]) return false;
    if(EmpresaInfo[empresaid][empresaDono][0] == EOS) return false;
    return (strcmp(EmpresaInfo[empresaid][empresaDono], pName(playerid), true) == 0);
}

stock EmpresaBuscarPorDono(playerid)
{
    new nome[MAX_PLAYER_NAME];
    format(nome, sizeof(nome), "%s", pName(playerid));
    for(new i = 0; i < TotalEmpresas; i++)
    {
        if(EmpresaInfo[i][empresaOcupada] == true && strcmp(EmpresaInfo[i][empresaDono], nome, true) == 0)
            return i;
    }
    return -1;
}

stock EmpresaMaisProxima(playerid, Float:raio)
{
	new id = -1;
    new Float:menor = raio;
    if(GetPlayerInterior(playerid) != 0 || GetPlayerVirtualWorld(playerid) != 0) return -1;
    for(new i = 0; i < TotalEmpresas; i++)
    {
        new Float:dist = GetPlayerDistanceFromPoint(playerid, EmpresaInfo[i][empresaExtX], EmpresaInfo[i][empresaExtY], EmpresaInfo[i][empresaExtZ]);
        if(dist <= menor)
        {
            menor = dist;
            id = i;
        }
    }
    return id;
}

stock EmpresaNoInterior(playerid)
{
    new vw = GetPlayerVirtualWorld(playerid);
    if(vw < EMPRESA_VW_BASE || vw >= EMPRESA_VW_BASE + MAX_EMPRESAS) return -1;
    new empresaid = vw - EMPRESA_VW_BASE;
    if(!EmpresaValida(empresaid)) return -1;
    new tipo = EmpresaInfo[empresaid][empresaTipo];
    if(!EmpresaTipoValido(tipo)) return -1;
    if(GetPlayerInterior(playerid) != InteriorEmpresa[tipo]) return -1;
    return empresaid;
}

stock EmpresaNoBalcao(playerid, empresaid)
{
    if(!EmpresaValida(empresaid)) return false;
    new tipo = EmpresaInfo[empresaid][empresaTipo];
    if(!EmpresaTipoValido(tipo)) return false;
    return bool:IsPlayerInRangeOfPoint(playerid, 2.0, PosCompraEmpresa[tipo][0], PosCompraEmpresa[tipo][1], PosCompraEmpresa[tipo][2]);
}

stock EmpresaTotalProdutos(tipo)
{
    if(!EmpresaTipoValido(tipo)) return 0;
    new total = 0;
    for(new i = 0; i < MAX_PRODUTOS_TIPO; i++)
    {
        if(EmpresaCatalogo[tipo][i] == -1) break;
        total++;
    }
    return total;
}

stock EmpresaProdutoDoIndice(tipo, indice)
{
    if(!EmpresaTipoValido(tipo)) return -1;
    if(indice < 0 || indice >= MAX_PRODUTOS_TIPO) return -1;
    return EmpresaCatalogo[tipo][indice];
}

stock EmpresaProdutoEhArma(produto)
{
    return (produto == PROD_AK || produto == PROD_M4 || produto == PROD_DESERT || produto == PROD_MP5);
}

stock EmpresaProdutoEhUnico(produto)
{
    return (produto == PROD_CELULAR || produto == PROD_JBL || produto == PROD_ROUPA);
}

stock EmpresaLimiteProduto(produto)
{
    switch(produto)
    {
        case PROD_CELULAR, PROD_JBL: return 1;
        case PROD_KITREPARO, PROD_GALAO: return 5;
        case PROD_AGUA, PROD_REFRIGERANTE, PROD_HAMBURGUER, PROD_PIZZA, PROD_CERVEJA, PROD_VODKA, PROD_WHISKY: return 30;
    }
    return 0;
}

stock EmpresaProdutoModelo(produto)
{
    switch(produto)
    {
        case PROD_CELULAR: return 330;
        case PROD_JBL: return 2226;
        case PROD_KITREPARO: return 19921;
        case PROD_GALAO: return 1650;
        case PROD_AK: return 355;
        case PROD_M4: return 356;
        case PROD_DESERT: return 348;
        case PROD_MP5: return 353;
        case PROD_AGUA: return 1484;
        case PROD_REFRIGERANTE: return 1543;
        case PROD_HAMBURGUER: return 2703;
        case PROD_PIZZA: return 2702;
        case PROD_CERVEJA: return 1527;
        case PROD_VODKA: return 1484;
        case PROD_WHISKY: return 1544;
        case PROD_ROUPA: return 0;
    }
    return 0;
}

stock EmpresaArmaID(produto)
{
    switch(produto)
    {
        case PROD_AK: return 30;
        case PROD_M4: return 31;
        case PROD_DESERT: return 24;
        case PROD_MP5: return 29;
    }
    return 0;
}

stock EmpresaPrecoProduto(produto)
{
    if(produto < 0 || produto >= MAX_PRODUTOS) return 0;
    return ProdutoPrecoPadrao[produto];
}

stock EmpresaEstoqueProduto(empresaid, produto)
{
    if(!EmpresaValida(empresaid) || produto < 0 || produto >= MAX_PRODUTOS) return 0;
    if(!EmpresaInfo[empresaid][empresaOcupada]) return MAX_ESTOQUE_ITEM; 
    return EmpresaEstoque[empresaid][produto];
}

stock EmpresaInicializarEstoque(empresaid)
{
    new tipo = EmpresaInfo[empresaid][empresaTipo];
    for(new p = 0; p < MAX_PRODUTOS; p++) EmpresaEstoque[empresaid][p] = 0;
    for(new i = 0, t = EmpresaTotalProdutos(tipo); i < t; i++) EmpresaEstoque[empresaid][EmpresaCatalogo[tipo][i]] = 100;
    return 1;
}

stock EmpresaDiasEpoch()
{
    new ano, mes, dia;
    getdate(ano, mes, dia);
    new a = ano;
    if(mes <= 2) a--;
    new era = ((a >= 0) ? a : (a - 399)) / 400;
    new yoe = a - era * 400;
    new doy = (153 * (mes + ((mes > 2) ? -3 : 9)) + 2) / 5 + dia - 1;
    new doe = yoe * 365 + (yoe / 4) - (yoe / 100) + doy;
    return era * 146097 + doe - 719468;
}

stock EmpresaDiaSemana() 
{
    new z = EmpresaDiasEpoch();
    return (((z + 3) % 7) + 7) % 7;
}

stock EmpresaSemanaAtual()
{
    return EmpresaDiasEpoch() - EmpresaDiaSemana();
}

stock EmpresaRegistrarVenda(empresaid, valor)
{
    if(!EmpresaValida(empresaid) || valor <= 0) return 0;
    if(!EmpresaInfo[empresaid][empresaOcupada]) return 1; 

    new semana = EmpresaSemanaAtual();
    if(EmpresaInfo[empresaid][empresaSemana] != semana)
    {
        EmpresaInfo[empresaid][empresaSemana] = semana;
        EmpresaInfo[empresaid][empresaVendasSemana] = 0;
        for(new i = 0; i < 7; i++) EmpresaInfo[empresaid][empresaVendasDia][i] = 0;
    }
    new dia = EmpresaDiaSemana();
    if(dia < 0 || dia > 6) dia = 0;
    EmpresaInfo[empresaid][empresaSaldo] += valor;
    EmpresaInfo[empresaid][empresaVendasSemana] += valor;
    EmpresaInfo[empresaid][empresaVendasDia][dia] += valor;
    return 1;
}

stock EmpresaDestruirVisual(empresaid)
{
    if(!EmpresaValida(empresaid)) return 0;
    if(EmpresaInfo[empresaid][empresaPickup] != -1)
    {
        DestroyDynamicPickup(EmpresaInfo[empresaid][empresaPickup]);
        EmpresaInfo[empresaid][empresaPickup] = -1;
    }
    if(EmpresaInfo[empresaid][empresaPickupBomba] != -1)
    {
        DestroyDynamicPickup(EmpresaInfo[empresaid][empresaPickupBomba]);
        EmpresaInfo[empresaid][empresaPickupBomba] = -1;
    }
    if(EmpresaInfo[empresaid][empresaLabel] != Text3D:INVALID_3DTEXT_ID)
    {
        DestroyDynamic3DTextLabel(EmpresaInfo[empresaid][empresaLabel]);
        EmpresaInfo[empresaid][empresaLabel] = Text3D:INVALID_3DTEXT_ID;
    }
    if(EmpresaInfo[empresaid][empresaLabelBomba] != Text3D:INVALID_3DTEXT_ID)
    {
        DestroyDynamic3DTextLabel(EmpresaInfo[empresaid][empresaLabelBomba]);
        EmpresaInfo[empresaid][empresaLabelBomba] = Text3D:INVALID_3DTEXT_ID;
    }
    if(EmpresaInfo[empresaid][empresaCP] != -1)
    {
        DestroyDynamicCP(EmpresaInfo[empresaid][empresaCP]);
        EmpresaInfo[empresaid][empresaCP] = -1;
    }
    if(EmpresaInfo[empresaid][empresaIcon] != -1)
	{
	    DestroyDynamicMapIcon(EmpresaInfo[empresaid][empresaIcon]);
	    EmpresaInfo[empresaid][empresaIcon] = -1;
	}
    return 1;
}

stock EmpresaAtualizarVisual(empresaid)
{
    if(!EmpresaValida(empresaid)) return 0;
    
    EmpresaDestruirVisual(empresaid);
    
    new tipo = EmpresaInfo[empresaid][empresaTipo];
    if(!EmpresaTipoValido(tipo)) return 0;
    
    new icone;
	switch(tipo)
	{
	    case EMPRESA_24_7: icone = 14;
	    case EMPRESA_ROUPAS: icone = 45;
	    case EMPRESA_ARMAS: icone = 18;
	    case EMPRESA_RESTAURANTE: icone = 29;
	    case EMPRESA_BAR: icone = 49;
	    case EMPRESA_POSTO: icone = 56;
	    default: icone = 0;
	}
    new texto[192];
    if(EmpresaInfo[empresaid][empresaOcupada])
    {
        EmpresaInfo[empresaid][empresaPickup] = CreateDynamicPickup(19523, 1, EmpresaInfo[empresaid][empresaExtX], EmpresaInfo[empresaid][empresaExtY], EmpresaInfo[empresaid][empresaExtZ], 0);
        format(texto, sizeof(texto), "{FFFFFF}%s {DC143C}#%d\n{FFFFFF}Proprietario: {DC143C}%s\n{FFFFFF}Pressione {DC143C}'H' {FFFFFF}para interagir", EmpresaNomeTipo(tipo), empresaid, EmpresaInfo[empresaid][empresaDono]);
    }
    else
    {
        EmpresaInfo[empresaid][empresaPickup] = CreateDynamicPickup(19524, 1, EmpresaInfo[empresaid][empresaExtX], EmpresaInfo[empresaid][empresaExtY], EmpresaInfo[empresaid][empresaExtZ], 0);
        format(texto, sizeof(texto), "{FFFFFF}%s {DC143C}#%d\n{FFFFFF}A venda por: {00FF00}$%d\n{FFFFFF}Pressione {DC143C}'H' {FFFFFF}para interagir", EmpresaNomeTipo(tipo), empresaid, EmpresaInfo[empresaid][empresaValor]);
    }
    EmpresaInfo[empresaid][empresaLabel] = CreateDynamic3DTextLabel(texto, COR_INFO, EmpresaInfo[empresaid][empresaExtX], EmpresaInfo[empresaid][empresaExtY], EmpresaInfo[empresaid][empresaExtZ] + 0.5, 15.0);
    EmpresaInfo[empresaid][empresaCP] = CreateDynamicCP(PosCompraEmpresa[tipo][0], PosCompraEmpresa[tipo][1], PosCompraEmpresa[tipo][2], 1.0, EMPRESA_VW_BASE + empresaid, InteriorEmpresa[tipo], -1, 100.0);
	EmpresaInfo[empresaid][empresaIcon] = CreateDynamicMapIcon(EmpresaInfo[empresaid][empresaExtX], EmpresaInfo[empresaid][empresaExtY], EmpresaInfo[empresaid][empresaExtZ], icone, -1, 0, 0, -1, 200.0);
	
    if(tipo == EMPRESA_POSTO && (EmpresaInfo[empresaid][empresaBombaX] != 0.0 || EmpresaInfo[empresaid][empresaBombaY] != 0.0))
    {
        EmpresaInfo[empresaid][empresaPickupBomba] = CreateDynamicPickup(1650, 1, EmpresaInfo[empresaid][empresaBombaX], EmpresaInfo[empresaid][empresaBombaY], EmpresaInfo[empresaid][empresaBombaZ] + 0.5, 0);
        format(texto, sizeof(texto), "{FFFFFF}Bomba de Combustivel\n{00FF00}$%d {FFFFFF}por litro", EmpresaInfo[empresaid][empresaPrecoGasolina]);
        EmpresaInfo[empresaid][empresaLabelBomba] = CreateDynamic3DTextLabel(texto, COR_INFO, EmpresaInfo[empresaid][empresaBombaX], EmpresaInfo[empresaid][empresaBombaY], EmpresaInfo[empresaid][empresaBombaZ] + 0.8, 10.0);
    }
    return 1;
}

stock EmpresaSalvar(empresaid)
{
    if(!EmpresaValida(empresaid)) return 0;

    new file[64], key[32];
    format(file, sizeof(file), "Empresas/%d.ini", empresaid);
    if(!DOF2_FileExists(file)) DOF2_CreateFile(file);

    DOF2_SetInt(file, "Tipo", EmpresaInfo[empresaid][empresaTipo]);
    DOF2_SetFloat(file, "ExtX", EmpresaInfo[empresaid][empresaExtX]);
    DOF2_SetFloat(file, "ExtY", EmpresaInfo[empresaid][empresaExtY]);
    DOF2_SetFloat(file, "ExtZ", EmpresaInfo[empresaid][empresaExtZ]);
    DOF2_SetFloat(file, "ExtA", EmpresaInfo[empresaid][empresaExtA]);
    DOF2_SetInt(file, "Valor", EmpresaInfo[empresaid][empresaValor]);
    if(EmpresaInfo[empresaid][empresaOcupada])
    {
	    DOF2_SetString(file, "Dono", EmpresaInfo[empresaid][empresaDono]);
	}
	else
	{
	    DOF2_SetString(file, "Dono", "Ninguem");
	}
    DOF2_SetInt(file, "Ocupada", (EmpresaInfo[empresaid][empresaOcupada]) ? 1 : 0);
    DOF2_SetInt(file, "Saldo", EmpresaInfo[empresaid][empresaSaldo]);
    DOF2_SetFloat(file, "BombaX", EmpresaInfo[empresaid][empresaBombaX]);
    DOF2_SetFloat(file, "BombaY", EmpresaInfo[empresaid][empresaBombaY]);
    DOF2_SetFloat(file, "BombaZ", EmpresaInfo[empresaid][empresaBombaZ]);
    DOF2_SetInt(file, "PrecoGasolina", EmpresaInfo[empresaid][empresaPrecoGasolina]);
    DOF2_SetInt(file, "Semana", EmpresaInfo[empresaid][empresaSemana]);
    DOF2_SetInt(file, "VendasSemana", EmpresaInfo[empresaid][empresaVendasSemana]);

    for(new i = 0; i < 7; i++)
    {
        format(key, sizeof(key), "VendaDia%d", i);
        DOF2_SetInt(file, key, EmpresaInfo[empresaid][empresaVendasDia][i]);
    }
    for(new p = 0; p < MAX_PRODUTOS; p++) DOF2_SetInt(file, ProdutoKey[p], EmpresaEstoque[empresaid][p]);
    DOF2_SaveFile();
    return 1;
}

stock EmpresaCarregar(empresaid)
{
    new file[64], key[32];
    format(file, sizeof(file), "Empresas/%d.ini", empresaid);
    if(!DOF2_FileExists(file)) return 0;

    EmpresaInfo[empresaid][empresaPickup] = -1;
    EmpresaInfo[empresaid][empresaPickupBomba] = -1;
    EmpresaInfo[empresaid][empresaLabel] = Text3D:INVALID_3DTEXT_ID;
    EmpresaInfo[empresaid][empresaLabelBomba] = Text3D:INVALID_3DTEXT_ID;
    EmpresaInfo[empresaid][empresaCP] = -1;
    EmpresaInfo[empresaid][empresaIcon] = -1;

    EmpresaInfo[empresaid][empresaTipo] = DOF2_GetInt(file, "Tipo");
    if(!EmpresaTipoValido(EmpresaInfo[empresaid][empresaTipo])) EmpresaInfo[empresaid][empresaTipo] = EMPRESA_24_7;
    EmpresaInfo[empresaid][empresaExtX] = DOF2_GetFloat(file, "ExtX");
    EmpresaInfo[empresaid][empresaExtY] = DOF2_GetFloat(file, "ExtY");
    EmpresaInfo[empresaid][empresaExtZ] = DOF2_GetFloat(file, "ExtZ");
    EmpresaInfo[empresaid][empresaExtA] = DOF2_GetFloat(file, "ExtA");
    EmpresaInfo[empresaid][empresaValor] = DOF2_GetInt(file, "Valor");
    if(EmpresaInfo[empresaid][empresaValor] <= 0) EmpresaInfo[empresaid][empresaValor] = ValorEmpresa[EmpresaInfo[empresaid][empresaTipo]];

    format(EmpresaInfo[empresaid][empresaDono], MAX_PLAYER_NAME, "%s", DOF2_GetString(file, "Dono"));
    EmpresaInfo[empresaid][empresaOcupada] = (DOF2_GetInt(file, "Ocupada") == 1 && EmpresaInfo[empresaid][empresaDono][0] != EOS);
    EmpresaInfo[empresaid][empresaSaldo] = DOF2_GetInt(file, "Saldo");
    if(EmpresaInfo[empresaid][empresaSaldo] < 0) EmpresaInfo[empresaid][empresaSaldo] = 0;

    EmpresaInfo[empresaid][empresaBombaX] = DOF2_GetFloat(file, "BombaX");
    EmpresaInfo[empresaid][empresaBombaY] = DOF2_GetFloat(file, "BombaY");
    EmpresaInfo[empresaid][empresaBombaZ] = DOF2_GetFloat(file, "BombaZ");

    EmpresaInfo[empresaid][empresaPrecoGasolina] = DOF2_GetInt(file, "PrecoGasolina");
    if(EmpresaInfo[empresaid][empresaPrecoGasolina] < 1 || EmpresaInfo[empresaid][empresaPrecoGasolina] > 20) EmpresaInfo[empresaid][empresaPrecoGasolina] = 5;

    EmpresaInfo[empresaid][empresaSemana] = DOF2_GetInt(file, "Semana");
    EmpresaInfo[empresaid][empresaVendasSemana] = DOF2_GetInt(file, "VendasSemana");

    for(new i = 0; i < 7; i++)
    {
        format(key, sizeof(key), "VendaDia%d", i);
        EmpresaInfo[empresaid][empresaVendasDia][i] = DOF2_GetInt(file, key);
    }
    for(new p = 0; p < MAX_PRODUTOS; p++)
    {
        EmpresaEstoque[empresaid][p] = DOF2_GetInt(file, ProdutoKey[p]);
        if(EmpresaEstoque[empresaid][p] < 0) EmpresaEstoque[empresaid][p] = 0;
        if(EmpresaEstoque[empresaid][p] > MAX_ESTOQUE_ITEM) EmpresaEstoque[empresaid][p] = MAX_ESTOQUE_ITEM;
    }
    if(!EmpresaInfo[empresaid][empresaOcupada])
    {
        EmpresaInfo[empresaid][empresaDono][0] = EOS;
        EmpresaInfo[empresaid][empresaSaldo] = 0;
        for(new p = 0; p < MAX_PRODUTOS; p++) EmpresaEstoque[empresaid][p] = 0;
    }
    EmpresaAtualizarVisual(empresaid);
    return 1;
}

stock EmpresaSalvarConfig()
{
    if(!DOF2_FileExists("Empresas/config.ini")) DOF2_CreateFile("Empresas/config.ini");
    DOF2_SetInt("Empresas/config.ini", "Total", TotalEmpresas);
    DOF2_SaveFile();
    return 1;
}

stock EmpresaCarregarTodas()
{
    if(!DOF2_FileExists("Empresas/config.ini"))
    {
        TotalEmpresas = 0;
        return 1;
    }
    TotalEmpresas = DOF2_GetInt("Empresas/config.ini", "Total");
    if(TotalEmpresas < 0) TotalEmpresas = 0;
    if(TotalEmpresas > MAX_EMPRESAS) TotalEmpresas = MAX_EMPRESAS;
    for(new i = 0; i < TotalEmpresas; i++) EmpresaCarregar(i);
    printf("[EMPRESAS] %d empresa(s) carregada(s).", TotalEmpresas);
    return 1;
}

stock EmpresaAbrirMenu(playerid, empresaid)
{
    if(!EmpresaValida(empresaid)) return 0;
    if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Apenas o proprietario pode usar este menu.");

    SetPVarInt(playerid, "EmpresaMenuID", empresaid);
    new titulo[64], texto[700];
    format(titulo, sizeof(titulo), "%s #%d", EmpresaNomeTipo(EmpresaInfo[empresaid][empresaTipo]), empresaid);
    format(texto, sizeof(texto), 
	    "{C8C8C8}Opcao\t{C8C8C8}Detalhe\n\
	    {FFFFFF}Informacoes\t{969696}Dados gerais da empresa\n\
	    {FFFFFF}Financeiro\t{00FF00}$%d {969696}no cofre\n\
	    {FFFFFF}Estoque\t{969696}Itens da sua empresa\n\
	    {FFFFFF}Reabastecer estoque\t{969696}Comprar itens com o cofre\n\
	    {FFFFFF}Sacar dinheiro\t{969696}Retirar do cofre\n\
	    {FFFFFF}Vender empresa\t{FF6347}Vender ao Estado\n\
	    {FFFFFF}Vender para jogador\t{FF6347}Transferir empresa", 
	    EmpresaInfo[empresaid][empresaSaldo]);
	
    ShowPlayerDialog(playerid, DIALOG_EMPRESA_MENU, DIALOG_STYLE_TABLIST_HEADERS, titulo, texto, "Selecionar", "Fechar");
    return 1;
}

stock EmpresaAbrirProdutos(playerid, empresaid)
{
    if(!EmpresaValida(empresaid)) return 0;
    if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao esta dentro desta empresa.");
    if(!EmpresaNoBalcao(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce precisa estar no balcao (checkpoint vermelho).");

    new tipo = EmpresaInfo[empresaid][empresaTipo];
    if(!EmpresaTipoValido(tipo)) return 0;

    new total = EmpresaTotalProdutos(tipo);
    if(total <= 0) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Esta empresa nao possui produtos.");

    if(EmpresaInfo[empresaid][empresaOcupada])
    {
        new disponiveis;
        for(new i = 0; i < total; i++)
        {
            new produto = EmpresaCatalogo[tipo][i];
            if(EmpresaEstoque[empresaid][produto] > 0)
                disponiveis++;
        }
        if(disponiveis == 0)
            return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Esta empresa esta sem estoque.");
    }
    SetPVarInt(playerid, "EmpresaLojaID", empresaid);

    for(new i = 0; i < total; i++)
    {
        new produto = EmpresaCatalogo[tipo][i];
        new modelo = EmpresaProdutoModelo(produto);
        new preco = EmpresaPrecoProduto(produto);
        new stack = 1;
        new descricao[128];

        if(EmpresaProdutoEhArma(produto))
        {
            stack = 500;
            format(descricao, sizeof(descricao), "Municao: 50 a 500 balas.");
        }
        else if(produto == PROD_ROUPA)
        {
            stack = 1;
            format(descricao, sizeof(descricao), "Escolha sua skin de 0 a 311.");
        }
        else
        {
            stack = 1;
            format(descricao, sizeof(descricao), "Produto disponivel para compra.");
        }
        MenuStore_AddItem(playerid, produto, modelo, ProdutoNome[produto], preco, descricao, 0.0, true, stack);
    }
    new nomeLoja[64];
    format(nomeLoja, sizeof(nomeLoja), "%s", EmpresaNomeTipo(tipo));

    MenuStore_Show(playerid, Empresa, nomeLoja, "R$", "Comprar");
    MenuStore_ToggleControll(playerid, true);
    return 1;
}

Store:Empresa(playerid, bool:confirm, itemid, modelid, total, quantidade, nome[])
{
    if(!confirm)
    {
        DeletePVar(playerid, "EmpresaLojaID");
        return 1;
    }
    new empresaid = GetPVarInt(playerid, "EmpresaLojaID");
    if(!EmpresaValida(empresaid))
    {
        DeletePVar(playerid, "EmpresaLojaID");
        return 1;
    }
    if(EmpresaNoInterior(playerid) != empresaid || !EmpresaNoBalcao(playerid, empresaid))
    {
        DeletePVar(playerid, "EmpresaLojaID");
        return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce saiu do balcao.");
    }
    if(itemid < 0 || itemid >= MAX_PRODUTOS)
    {
        DeletePVar(playerid, "EmpresaLojaID");
        return 1;
    }
    if(EmpresaInfo[empresaid][empresaOcupada] && EmpresaEstoque[empresaid][itemid] <= 0)
    {
        return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Este produto esta sem estoque.");
    }
    if(itemid == PROD_ROUPA)
    {
        SetPVarInt(playerid, "EmpresaProduto", itemid);

        new texto[256];
        format(texto, sizeof(texto), "{FFFFFF}Valor: {00FF00}$%d\n{FFFFFF}Skins validas: {DC143C}0 a 311\n\n{FFFFFF}Digite o ID da skin desejada:", EmpresaPrecoProduto(itemid));

        ShowPlayerDialog(playerid, DIALOG_EMPRESA_ROUPA, DIALOG_STYLE_INPUT, "Comprar Roupa", texto, "Comprar", "Cancelar");
        return 1;
    }
    if(EmpresaProdutoEhArma(itemid))
    {
        if(quantidade < 50 || quantidade > 500)
        {
            return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] A quantidade deve ser entre 50 e 500 balas.");
        }
        return EmpresaProcessarCompra(playerid, empresaid, itemid, 1, quantidade);
    }
    return EmpresaProcessarCompra(playerid, empresaid, itemid, quantidade, 0);
}

stock EmpresaAbrirEstoque(playerid, empresaid)
{
    if(!EmpresaValida(empresaid)) return 0;
    if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao esta dentro desta empresa.");
    if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Apenas o proprietario pode acessar o estoque.");

    new tipo = EmpresaInfo[empresaid][empresaTipo];
    if(!EmpresaTipoValido(tipo)) return 0;

    new total = EmpresaTotalProdutos(tipo);
    new texto[800], linha[160];
    format(texto, sizeof(texto), "{C8C8C8}Produto\t{C8C8C8}Estoque\t{C8C8C8}Situacao\n");

    for(new i = 0; i < total; i++)
    {
        new produto = EmpresaCatalogo[tipo][i];
        new qtd = EmpresaEstoque[empresaid][produto];
        new situacao[32];
        if(qtd <= 0) format(situacao, sizeof(situacao), "{FF4500}Esgotado");
        else if(qtd <= 20) format(situacao, sizeof(situacao), "{FFA500}Baixo");
        else format(situacao, sizeof(situacao), "{00FF00}Normal");
        format(linha, sizeof(linha), "{FFFFFF}%s\t{FFFFFF}%d / %d\t%s\n", ProdutoNome[produto], qtd, MAX_ESTOQUE_ITEM, situacao);
        strcat(texto, linha, sizeof(texto));
    }
    ShowPlayerDialog(playerid, DIALOG_EMPRESA_ESTOQUE, DIALOG_STYLE_TABLIST_HEADERS, "Estoque da Empresa", texto, "Voltar", "Fechar");
    return 1;
}

stock EmpresaAbrirReabastecer(playerid, empresaid)
{
    if(!EmpresaValida(empresaid)) return 0;
    if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce nao esta dentro desta empresa.");
    if(!EmpresaEhDono(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Apenas o proprietario pode reabastecer a empresa.");

    new tipo = EmpresaInfo[empresaid][empresaTipo];
    if(!EmpresaTipoValido(tipo)) return 0;

    new total = EmpresaTotalProdutos(tipo);
    new totalFalta = 0;
    new custoTotal = 0;
    new texto[1200];
    new linha[160];

    format(texto, sizeof(texto), "{C8C8C8}Produto\tEstoque atual\n");

    for(new i = 0; i < total; i++)
    {
        new produto = EmpresaCatalogo[tipo][i];
        new falta = MAX_ESTOQUE_ITEM - EmpresaEstoque[empresaid][produto];

        if(falta > 0)
        {
            totalFalta += falta;
            custoTotal += falta * ProdutoCusto[produto];
        }
        format(linha, sizeof(linha), "{FFFFFF}%s\t{FFFFFF}%d / %d\n", ProdutoNome[produto], EmpresaEstoque[empresaid][produto], MAX_ESTOQUE_ITEM);
        strcat(texto, linha, sizeof(texto));
    }
    if(totalFalta < 50)
    {
        format(linha, sizeof(linha), "\n{C8C8C8}Faltam apenas {FFFFFF}%d {C8C8C8}unidades no total.", totalFalta);
        strcat(texto, linha, sizeof(texto));
    }
    else
    {
        format(linha, sizeof(linha), "\n{C8C8C8}Total para repor: {FFFFFF}%d\n{C8C8C8}Custo do reabastecimento: {00FF00}$%d", totalFalta, custoTotal);
        strcat(texto, linha, sizeof(texto));
    }
    ShowPlayerDialog(playerid, DIALOG_EMPRESA_REABASTECER, DIALOG_STYLE_TABLIST_HEADERS, "Reabastecer Estoque", texto, "Continuar", "Voltar");
    return 1;
}

stock EmpresaProcessarCompra(playerid, empresaid, produto, qtd, extra)
{
    if(!IsPlayerConnected(playerid)) return 0;
    if(!EmpresaValida(empresaid) || produto < 0 || produto >= MAX_PRODUTOS) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Compra invalida.");
    if(EmpresaNoInterior(playerid) != empresaid) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce saiu da empresa.");
    if(!EmpresaNoBalcao(playerid, empresaid)) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce saiu do balcao.");

    new tipo = EmpresaInfo[empresaid][empresaTipo];
    new pertence;

    for(new i = 0, t = EmpresaTotalProdutos(tipo); i < t; i++)
    {
        if(EmpresaCatalogo[tipo][i] == produto)
        {
            pertence = 1;
            break;
        }
    }

    if(!pertence) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Este produto nao e vendido aqui.");
    if(qtd < 1) qtd = 1;

    new bool:comDono = EmpresaInfo[empresaid][empresaOcupada];

    if(comDono && EmpresaEstoque[empresaid][produto] < qtd)
        return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Estoque insuficiente para esta quantidade.");

    new preco = EmpresaPrecoProduto(produto);
    if(preco <= 0) return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Produto sem preco definido.");

    if(produto == PROD_CELULAR)
    {
        if(Player[playerid][pCelular])
            return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce ja possui um celular.");
        qtd = 1;
    }

    if(produto == PROD_JBL)
    {
        if(Player[playerid][pJBL])
            return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Voce ja possui uma JBL.");
        qtd = 1;
    }

    if(EmpresaProdutoEhArma(produto))
    {
        qtd = 1;
        if(extra < 50 || extra > 500)
            return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] A municao deve ser entre 50 e 500 balas.");
        if(EmpresaArmaID(produto) <= 0)
            return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Arma invalida.");
    }

    if(produto == PROD_ROUPA)
    {
        qtd = 1;
        if(extra < 0 || extra > 311)
            return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Skin invalida (0 a 311).");
    }

    new total;
    if(EmpresaProdutoEhArma(produto))
        total = preco * extra;
    else
        total = preco * qtd;

    if(total <= 0)
        return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Valor invalido.");

    if(GetPlayerCash(playerid) < total)
        return SendClientMessage(playerid, COR_ERRO, "[EMPRESA] Dinheiro insuficiente.");

    if(EmpresaProdutoEhArma(produto))
    {
        if(!InventarioAdicionarArma(playerid, EmpresaArmaID(produto), extra))
            return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Nao foi possivel adicionar a arma ao inventario.");
    }
    else if(produto == PROD_ROUPA)
    {
        if(!InventarioAdicionarRoupa(playerid, extra))
            return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Nao foi possivel adicionar a roupa ao inventario.");
    }
    else if(produto == PROD_CELULAR)
    {
        Player[playerid][pCelular] = 1;
    }
    else if(produto == PROD_JBL)
    {
        Player[playerid][pJBL] = 1;
    }
    else
    {
        new invItem;

        switch(produto)
        {
            case PROD_AGUA: invItem = INV_ITEM_AGUA;
            case PROD_REFRIGERANTE: invItem = INV_ITEM_REFRIGERANTE;
            case PROD_HAMBURGUER: invItem = INV_ITEM_HAMBURGUER;
            case PROD_PIZZA: invItem = INV_ITEM_PIZZA;
            case PROD_CERVEJA: invItem = INV_ITEM_CERVEJA;
            case PROD_VODKA: invItem = INV_ITEM_VODKA;
            case PROD_WHISKY: invItem = INV_ITEM_WHISKY;
            case PROD_KITREPARO: invItem = INV_ITEM_KIT_REPARO;
            case PROD_GALAO: invItem = INV_ITEM_GALAO;
            default: invItem = 0;
        }

        if(invItem <= 0)
            return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Produto invalido.");

        if(produto == PROD_KITREPARO || produto == PROD_GALAO)
        {
            if(!InventarioAdicionarOutro(playerid, invItem, qtd))
                return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Nao foi possivel adicionar o item ao inventario.");
        }
        else
        {
            if(!InventarioAdicionarItem(playerid, invItem, qtd))
                return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Nao foi possivel adicionar o item ao inventario.");
        }
    }

    sGivePlayerCash(playerid, -total);

    if(comDono)
    {
        EmpresaEstoque[empresaid][produto] -= qtd;
        if(EmpresaEstoque[empresaid][produto] < 0)
            EmpresaEstoque[empresaid][produto] = 0;

        EmpresaRegistrarVenda(empresaid, total);
        EmpresaSalvar(empresaid);
    }

    new msg[160];

    if(EmpresaProdutoEhArma(produto))
        format(msg, sizeof(msg), "[EMPRESA] Voce comprou %s com %d balas por $%d. A arma foi enviada para seu inventario.", ProdutoNome[produto], extra, total);
    else if(produto == PROD_ROUPA)
        format(msg, sizeof(msg), "[EMPRESA] Voce comprou a roupa ID %d por $%d. Ela foi enviada para seu inventario.", extra, total);
    else if(produto == PROD_CELULAR)
        format(msg, sizeof(msg), "[EMPRESA] Voce comprou um celular por $%d.", total);
    else if(produto == PROD_JBL)
        format(msg, sizeof(msg), "[EMPRESA] Voce comprou uma JBL por $%d.", total);
    else
        format(msg, sizeof(msg), "[EMPRESA] Voce comprou %dx %s por $%d. O item foi enviado para seu inventario.", qtd, ProdutoNome[produto], total);

    SendClientMessage(playerid, COR_SUCESSO, msg);
    InventarioSalvar(playerid);
    return 1;
}

stock EmpresaTeclaH(playerid)
{
    new empresaid = EmpresaNoInterior(playerid);
    if(empresaid != -1)
    {
        new tipo = EmpresaInfo[empresaid][empresaTipo];
        if(IsPlayerInRangeOfPoint(playerid, 2.0, PosInteriorEmpresa[tipo][0], PosInteriorEmpresa[tipo][1], PosInteriorEmpresa[tipo][2]))
        {
            SetPlayerInterior(playerid, 0);
            SetPlayerVirtualWorld(playerid, 0);
            SetPlayerPos(playerid, EmpresaInfo[empresaid][empresaExtX], EmpresaInfo[empresaid][empresaExtY], EmpresaInfo[empresaid][empresaExtZ]);
            SetPlayerFacingAngle(playerid, EmpresaInfo[empresaid][empresaExtA]);
            SetCameraBehindPlayer(playerid);
            return 1;
        }
        return 0;
    }
    empresaid = EmpresaMaisProxima(playerid, 2.0);
    if(empresaid == -1) return 0;
    SetPVarInt(playerid, "EmpresaProxima", empresaid);

    if(EmpresaInfo[empresaid][empresaOcupada] == true)
    {
        EmpresaEntrar(playerid, empresaid);
        return 1;
    }
    new titulo[64], texto[256];
    format(titulo, sizeof(titulo), "%s #%d", EmpresaNomeTipo(EmpresaInfo[empresaid][empresaTipo]), empresaid);
    format(texto, sizeof(texto), "{C8C8C8}Opcao\t{C8C8C8}Detalhe\n{FFFFFF}Entrar\t{969696}Visitar a empresa\n{FFFFFF}Comprar empresa\t{00FF00}$%d", EmpresaInfo[empresaid][empresaValor]);
    ShowPlayerDialog(playerid, DIALOG_EMPRESA_INTERACAO, DIALOG_STYLE_TABLIST_HEADERS, titulo, texto, "Selecionar", "Cancelar");
    return 1;
}

stock EmpresaEntrar(playerid, empresaid)
{
    if(!EmpresaValida(empresaid)) return 0;

    new tipo = EmpresaInfo[empresaid][empresaTipo];
    if(!EmpresaTipoValido(tipo)) return 0;

    SetPlayerInterior(playerid, InteriorEmpresa[tipo]);
    SetPlayerVirtualWorld(playerid, EMPRESA_VW_BASE + empresaid);
    SetPlayerPos(playerid, PosInteriorEmpresa[tipo][0], PosInteriorEmpresa[tipo][1], PosInteriorEmpresa[tipo][2]);
    SetPlayerFacingAngle(playerid, PosInteriorEmpresa[tipo][3]);
    SetCameraBehindPlayer(playerid);
    return 1;
}

stock InventarioAbrir(playerid)
{
    InventarioCategoria[playerid] = INV_CATEGORIA_OUTROS;
    InventarioSelecionado[playerid] = -1;
    InventarioAberto[playerid] = true;

    InventarioAtualizar(playerid);

    for(new i = 0; i < 40; i++)
        PlayerTextDrawShow(playerid, InvPlayer[playerid][i]);

    SelectTextDraw(playerid, -1);
    return 1;
}

stock InventarioFechar(playerid)
{
    InventarioAberto[playerid] = false;
    InventarioSelecionado[playerid] = -1;

    for(new i = 0; i < 40; i++)
        PlayerTextDrawHide(playerid, InvPlayer[playerid][i]);

    CancelSelectTextDraw(playerid);
    return 1;
}

stock InventarioAbrirCategoria(playerid, categoria)
{
    if(categoria < INV_CATEGORIA_COMIDAS || categoria > INV_CATEGORIA_OUTROS)
        return 0;

    InventarioCategoria[playerid] = categoria;
    InventarioSelecionado[playerid] = -1;

    InventarioAtualizar(playerid);
    return 1;
}

stock InventarioAdicionarItem(playerid, itemid, quantidade)
{
    if(itemid <= 0 || quantidade <= 0)
        return 0;

    new slot = -1;
    for(new i = 0; i < INV_SLOTS; i++)
    {
        if(Inventario[playerid][INV_CATEGORIA_COMIDAS][i][invID] == itemid)
        {
            slot = i;
            break;
        }
    }
    if(slot == -1)
    {
        for(new i = 0; i < INV_SLOTS; i++)
        {
            if(Inventario[playerid][INV_CATEGORIA_COMIDAS][i][invID] == 0)
            {
                slot = i;
                break;
            }
        }
    }
    if(slot == -1)
        return 0;

    Inventario[playerid][INV_CATEGORIA_COMIDAS][slot][invID] = itemid;
    Inventario[playerid][INV_CATEGORIA_COMIDAS][slot][invQuantidade] += quantidade;
    InventarioAtualizar(playerid);
    return 1;
}

stock InventarioAdicionarOutro(playerid, itemid, quantidade)
{
    if(itemid <= 0 || quantidade <= 0)
        return 0;

    new slot = -1;
    for(new i = 0; i < INV_SLOTS; i++)
    {
        if(Inventario[playerid][INV_CATEGORIA_OUTROS][i][invID] == itemid)
        {
            slot = i;
            break;
        }
    }
    if(slot == -1)
    {
        for(new i = 0; i < INV_SLOTS; i++)
        {
            if(Inventario[playerid][INV_CATEGORIA_OUTROS][i][invID] == 0)
            {
                slot = i;
                break;
            }
        }
    }
    if(slot == -1)
        return 0;

    Inventario[playerid][INV_CATEGORIA_OUTROS][slot][invID] = itemid;
    Inventario[playerid][INV_CATEGORIA_OUTROS][slot][invQuantidade] += quantidade;
    InventarioAtualizar(playerid);
    return 1;
}

stock InventarioAdicionarRoupa(playerid, skinid)
{
    if(skinid < 0 || skinid > 311)
        return 0;

    for(new i = 0; i < INV_SLOTS; i++)
    {
        if(Inventario[playerid][INV_CATEGORIA_ROUPAS][i][invID] == skinid)
            return 0;
    }
    new slot = -1;
    for(new i = 0; i < INV_SLOTS; i++)
    {
        if(Inventario[playerid][INV_CATEGORIA_ROUPAS][i][invID] == 0)
        {
            slot = i;
            break;
        }
    }
    if(slot == -1)
        return 0;

    Inventario[playerid][INV_CATEGORIA_ROUPAS][slot][invID] = skinid;
    Inventario[playerid][INV_CATEGORIA_ROUPAS][slot][invQuantidade] = 1;
    Inventario[playerid][INV_CATEGORIA_ROUPAS][slot][invExtra] = skinid;
    InventarioAtualizar(playerid);
    return 1;
}

stock InventarioAdicionarArma(playerid, weaponid, municao)
{
    if(weaponid < 1 || weaponid > 46 || municao <= 0)
        return 0;

    for(new i = 0; i < INV_SLOTS; i++)
    {
        if(Inventario[playerid][INV_CATEGORIA_ARMAS][i][invID] == weaponid)
        {
            Inventario[playerid][INV_CATEGORIA_ARMAS][i][invExtra] += municao;
            InventarioAtualizar(playerid);
            return 1;
        }
    }
    new slot = -1;
    for(new i = 0; i < INV_SLOTS; i++)
    {
        if(Inventario[playerid][INV_CATEGORIA_ARMAS][i][invID] == 0)
        {
            slot = i;
            break;
        }
    }
    if(slot == -1)
        return 0;

    Inventario[playerid][INV_CATEGORIA_ARMAS][slot][invID] = weaponid;
    Inventario[playerid][INV_CATEGORIA_ARMAS][slot][invQuantidade] = 1;
    Inventario[playerid][INV_CATEGORIA_ARMAS][slot][invExtra] = municao;
    InventarioAtualizar(playerid);
    return 1;
}

stock InventarioAtualizar(playerid)
{
    new categoria = InventarioCategoria[playerid];
    for(new i = 0; i < INV_SLOTS; i++)
    {
        new td = InventarioSlotTD[i];
        new modelo = 19300;
        if(Inventario[playerid][categoria][i][invID] != 0)
        {
            if(categoria == INV_CATEGORIA_ROUPAS)
            {
                modelo = Inventario[playerid][categoria][i][invID];
            }
            else
            {
                modelo = InventarioModeloItem(Inventario[playerid][categoria][i][invID]);
            }
            PlayerTextDrawSetPreviewModel(playerid, InvPlayer[playerid][td], modelo);
            PlayerTextDrawShow(playerid, InvPlayer[playerid][td]);
        }
        else
        {
            PlayerTextDrawSetPreviewModel(playerid, InvPlayer[playerid][td], 19300);
            PlayerTextDrawShow(playerid, InvPlayer[playerid][td]);
        }
    }
    PlayerTextDrawHide(playerid, InvPlayer[playerid][9]);
    PlayerTextDrawHide(playerid, InvPlayer[playerid][7]);
    return 1;
}

stock InventarioModeloItem(itemid)
{
    switch(itemid)
    {
        case INV_ITEM_AGUA: return 1484;
        case INV_ITEM_REFRIGERANTE: return 1546;
        case INV_ITEM_HAMBURGUER: return 2768;
        case INV_ITEM_PIZZA: return 2702;
        case INV_ITEM_CERVEJA: return 1669;
        case INV_ITEM_VODKA: return 19823;
        case INV_ITEM_WHISKY: return 19822;
        case INV_ITEM_KIT_REPARO: return 19921;
        case INV_ITEM_GALAO: return 1650;
    }
    return 19300;
}

stock InventarioSelecionar(playerid, slot)
{
    if(slot < 0 || slot >= INV_SLOTS)
        return 0;

    new categoria = InventarioCategoria[playerid];
    if(Inventario[playerid][categoria][slot][invID] == 0)
        return 0;

    InventarioSelecionado[playerid] = slot;
    InventarioMostrarSelecionado(playerid);
    return 1;
}

stock InventarioMostrarSelecionado(playerid)
{
    new categoria = InventarioCategoria[playerid];
    new slot = InventarioSelecionado[playerid];
    if(slot < 0 || slot >= INV_SLOTS)
        return 0;

    new itemid = Inventario[playerid][categoria][slot][invID];
    if(!itemid)
        return 0;

    new modelo;
    if(categoria == INV_CATEGORIA_ROUPAS)
    {
        modelo = itemid;
    }
    else
    {
        modelo = InventarioModeloItem(itemid);
    }
    PlayerTextDrawSetPreviewModel(playerid, InvPlayer[playerid][9], modelo);
    PlayerTextDrawShow(playerid, InvPlayer[playerid][9]);

    new texto[64];
    if(categoria == INV_CATEGORIA_ROUPAS)
    {
        format(texto, sizeof(texto), "Skin ID: %d", itemid);
    }
    else
    {
        format(texto, sizeof(texto), "%s - %dx", InventarioNomeItem(itemid), Inventario[playerid][categoria][slot][invQuantidade]);
    }
    PlayerTextDrawSetString(playerid, InvPlayer[playerid][7], texto);
    PlayerTextDrawShow(playerid, InvPlayer[playerid][7]);
    return 1;
}

stock InventarioNomeItem(itemid)
{
    new nome[32];
    switch(itemid)
    {
        case INV_ITEM_AGUA: format(nome, sizeof(nome), "Agua");
        case INV_ITEM_REFRIGERANTE: format(nome, sizeof(nome), "Refrigerante");
        case INV_ITEM_HAMBURGUER: format(nome, sizeof(nome), "Hamburguer");
        case INV_ITEM_PIZZA: format(nome, sizeof(nome), "Pizza");
        case INV_ITEM_CERVEJA: format(nome, sizeof(nome), "Cerveja");
        case INV_ITEM_VODKA: format(nome, sizeof(nome), "Vodka");
        case INV_ITEM_WHISKY: format(nome, sizeof(nome), "Whisky");
        case INV_ITEM_KIT_REPARO: format(nome, sizeof(nome), "Kit de reparo");
        case INV_ITEM_GALAO: format(nome, sizeof(nome), "Galao");
        default: format(nome, sizeof(nome), "Item");
    }
    return nome;
}

stock InventarioUsarSelecionado(playerid)
{
    new categoria = InventarioCategoria[playerid];
    new slot = InventarioSelecionado[playerid];

    if(slot < 0 || slot >= INV_SLOTS)
        return 0;

    new itemid = Inventario[playerid][categoria][slot][invID];

    if(itemid == 0)
        return 0;

    // IMPLEMENTAR USO DOS ITENS AQUI.

    return 1;
}

stock InventarioExcluirSelecionado(playerid)
{
    new categoria = InventarioCategoria[playerid];
    new slot = InventarioSelecionado[playerid];

    if(slot < 0 || slot >= INV_SLOTS)
        return 0;

    new itemid = Inventario[playerid][categoria][slot][invID];
    new quantidade = Inventario[playerid][categoria][slot][invQuantidade];

    if(itemid <= 0 || quantidade <= 0)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Selecione um item.");

    SetPVarInt(playerid, "InvExcluirCategoria", categoria);
    SetPVarInt(playerid, "InvExcluirSlot", slot);

    if(quantidade == 1)
    {
        ShowPlayerDialog(playerid, DIALOG_INV_CONFIRMAR_EXCLUSAO, DIALOG_STYLE_MSGBOX, "Excluir item", "{FFFFFF}Deseja excluir este item?", "Excluir", "Voltar");
        return 1;
    }

    new texto[128];
    format(texto, sizeof(texto), "{FFFFFF}Digite a quantidade que deseja excluir.\n\nQuantidade disponivel: {F1C40F}%d", quantidade);

    ShowPlayerDialog(playerid, DIALOG_INV_QUANTIDADE_EXCLUIR, DIALOG_STYLE_INPUT, "Excluir item", texto, "Excluir", "Voltar");
    return 1;
}

stock InventarioTransferirSelecionado(playerid)
{
    new categoria = InventarioCategoria[playerid];
    new slot = InventarioSelecionado[playerid];

    if(categoria < INV_CATEGORIA_COMIDAS || categoria > INV_CATEGORIA_OUTROS)
        return 0;

    if(slot < 0 || slot >= INV_SLOTS)
        return 0;

    new itemid = Inventario[playerid][categoria][slot][invID];
    new quantidade = Inventario[playerid][categoria][slot][invQuantidade];

    if(itemid <= 0 || quantidade <= 0)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Selecione um item.");

    SetPVarInt(playerid, "InvTransferCategoria", categoria);
    SetPVarInt(playerid, "InvTransferSlot", slot);

    ShowPlayerDialog(playerid, DIALOG_INV_TRANSFERIR, DIALOG_STYLE_INPUT, "Transferir item", "{FFFFFF}Digite o {F1C40F}ID do jogador {FFFFFF}que recebera o item:", "Enviar", "Voltar");
    return 1;
}

stock InventarioTransferirQuantidade(playerid, quantidade)
{
    new categoria = GetPVarInt(playerid, "InvTransferCategoria");
    new slot = GetPVarInt(playerid, "InvTransferSlot");
    new alvo = InventarioTransferirID[playerid];

    if(!IsPlayerConnected(alvo) || alvo == playerid)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Jogador invalido.");

    new Float:px, Float:py, Float:pz;
    GetPlayerPos(alvo, px, py, pz);

    if(!IsPlayerInRangeOfPoint(playerid, 3.0, px, py, pz))
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] O jogador precisa estar a ate 3 metros de voce.");

    if(categoria < INV_CATEGORIA_COMIDAS || categoria > INV_CATEGORIA_OUTROS)
        return 0;

    if(slot < 0 || slot >= INV_SLOTS)
        return 0;

    new itemid = Inventario[playerid][categoria][slot][invID];
    new disponivel = Inventario[playerid][categoria][slot][invQuantidade];
    new extra = Inventario[playerid][categoria][slot][invExtra];

    if(itemid <= 0 || disponivel <= 0)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Item invalido.");

    if(categoria == INV_CATEGORIA_ARMAS || categoria == INV_CATEGORIA_ROUPAS)
        quantidade = 1;

    if(quantidade < 1 || quantidade > disponivel)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Quantidade invalida.");

    new limite = 30;

    if(itemid == INV_ITEM_GALAO || itemid == INV_ITEM_KIT_REPARO)
        limite = 5;

    if(categoria == INV_CATEGORIA_ARMAS || categoria == INV_CATEGORIA_ROUPAS)
        limite = 1;

    if(quantidade > limite)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Quantidade acima do limite permitido.");

    new bool:adicionado;

    switch(categoria)
    {
        case INV_CATEGORIA_COMIDAS:
            adicionado = InventarioAdicionarItem(alvo, itemid, quantidade);

        case INV_CATEGORIA_ROUPAS:
            adicionado = InventarioAdicionarRoupa(alvo, extra);

        case INV_CATEGORIA_ARMAS:
            adicionado = InventarioAdicionarArma(alvo, itemid, extra);

        case INV_CATEGORIA_OUTROS:
            adicionado = InventarioAdicionarOutro(alvo, itemid, quantidade);
    }

    if(!adicionado)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Nao foi possivel transferir. O inventario do jogador pode estar cheio ou ele ja possui este item.");

    Inventario[playerid][categoria][slot][invQuantidade] -= quantidade;

    if(Inventario[playerid][categoria][slot][invQuantidade] <= 0)
    {
        Inventario[playerid][categoria][slot][invID] = 0;
        Inventario[playerid][categoria][slot][invQuantidade] = 0;
        Inventario[playerid][categoria][slot][invExtra] = 0;
    }

    InventarioSalvar(playerid);
    InventarioSalvar(alvo);

    new nome[64], msg[144];
    InventarioNomeItem(itemid, nome, sizeof(nome));

    if(categoria == INV_CATEGORIA_ARMAS)
        format(msg, sizeof(msg), "[INVENTARIO] Voce transferiu %s com %d balas para o jogador %d.", nome, extra, alvo);
    else if(categoria == INV_CATEGORIA_ROUPAS)
        format(msg, sizeof(msg), "[INVENTARIO] Voce transferiu a roupa ID %d para o jogador %d.", extra, alvo);
    else
        format(msg, sizeof(msg), "[INVENTARIO] Voce transferiu %dx %s para o jogador %d.", quantidade, nome, alvo);

    SendClientMessage(playerid, COR_SUCESSO, msg);

    if(categoria == INV_CATEGORIA_ARMAS)
        format(msg, sizeof(msg), "[INVENTARIO] Voce recebeu %s com %d balas do jogador %d.", nome, extra, playerid);
    else if(categoria == INV_CATEGORIA_ROUPAS)
        format(msg, sizeof(msg), "[INVENTARIO] Voce recebeu a roupa ID %d do jogador %d.", extra, playerid);
    else
        format(msg, sizeof(msg), "[INVENTARIO] Voce recebeu %dx %s do jogador %d.", quantidade, nome, playerid);

    SendClientMessage(alvo, COR_SUCESSO, msg);

    InventarioMostrar(playerid);
    return 1;
}

stock InventarioExcluirQuantidade(playerid, quantidade)
{
    new categoria = GetPVarInt(playerid, "InvExcluirCategoria");
    new slot = GetPVarInt(playerid, "InvExcluirSlot");

    if(slot < 0 || slot >= INV_SLOTS || categoria < 0 || categoria >= INV_CATEGORIAS)
        return 0;

    new disponivel = Inventario[playerid][categoria][slot][invQuantidade];

    if(disponivel <= 0)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Item invalido.");

    if(quantidade < 1 || quantidade > disponivel)
        return SendClientMessage(playerid, COR_ERRO, "[INVENTARIO] Quantidade invalida.");

    Inventario[playerid][categoria][slot][invQuantidade] -= quantidade;

    if(Inventario[playerid][categoria][slot][invQuantidade] <= 0)
    {
        Inventario[playerid][categoria][slot][invID] = 0;
        Inventario[playerid][categoria][slot][invQuantidade] = 0;
        Inventario[playerid][categoria][slot][invExtra] = 0;
    }

    InventarioSalvar(playerid);
    InventarioMostrar(playerid);

    SendClientMessage(playerid, COR_SUCESSO, "[INVENTARIO] Item excluido com sucesso.");
    return 1;
}

stock InventarioArquivo(playerid)
{
    new caminho[128];
    format(caminho, sizeof(caminho), "Inventarios/%s.ini", pName(playerid));
    return caminho;
}

stock InventarioSalvar(playerid)
{
    new arquivo[128];
    format(arquivo, sizeof(arquivo), "Inventarios/%s.ini", pName(playerid));

    if(!DOF2_FileExists(arquivo))
        DOF2_CreateFile(arquivo);

    for(new c = 0; c < INV_CATEGORIAS; c++)
    {
        for(new s = 0; s < INV_SLOTS; s++)
        {
            new chave[32];
            format(chave, sizeof(chave), "ID_%d_%d", c, s);
            DOF2_SetInt(arquivo, chave, Inventario[playerid][c][s][invID]);
            format(chave, sizeof(chave), "QTD_%d_%d", c, s);
            DOF2_SetInt(arquivo, chave, Inventario[playerid][c][s][invQuantidade]);
            format(chave, sizeof(chave), "EXT_%d_%d", c, s);
            DOF2_SetInt(arquivo, chave, Inventario[playerid][c][s][invExtra]);
        }
    }
    DOF2_SaveFile();
    return 1;
}

stock InventarioCarregar(playerid)
{
    new arquivo[128];
    format(arquivo, sizeof(arquivo), "Inventarios/%s.ini", pName(playerid));

    if(!DOF2_FileExists(arquivo))
        return 1;

    for(new c = 0; c < INV_CATEGORIAS; c++)
    {
        for(new s = 0; s < INV_SLOTS; s++)
        {
            new chave[32];
            format(chave, sizeof(chave), "ID_%d_%d", c, s);
            Inventario[playerid][c][s][invID] = DOF2_GetInt(arquivo, chave);
            format(chave, sizeof(chave), "QTD_%d_%d", c, s);
            Inventario[playerid][c][s][invQuantidade] = DOF2_GetInt(arquivo, chave);
            format(chave, sizeof(chave), "EXT_%d_%d", c, s);
            Inventario[playerid][c][s][invExtra] = DOF2_GetInt(arquivo, chave);
        }
    }
    return 1;
}

stock JBLValida(jblid)
{
    if(jblid < 0 || jblid >= MAX_JBLS) return 0;
    if(!JBLInfo[jblid][jblAtiva]) return 0;
    return 1;
}

stock JBLSlotLivre()
{
    for(new i = 0; i < MAX_JBLS; i++)
    {
        if(!JBLInfo[i][jblAtiva])
            return i;
    }
    return -1;
}

stock JBLDoDono(playerid)
{
    if(playerid < 0 || playerid >= MAX_PLAYERS) return -1;
    for(new i = 0; i < MAX_JBLS; i++)
    {
        if(JBLInfo[i][jblAtiva] && JBLInfo[i][jblDono] == playerid)
            return i;
    }
    return -1;
}

stock JBLProxima(playerid, Float:raio)
{
    new id = -1;
    new Float:menor = raio;
    for(new i = 0; i < MAX_JBLS; i++)
    {
        if(!JBLInfo[i][jblAtiva]) continue;
        new Float:dist = JBLDistancia(playerid, i);
        if(dist < 0.0) continue;
        if(dist <= menor)
        {
            menor = dist;
            id = i;
        }
    }
    return id;
}

stock JBLExistePerto(Float:x, Float:y, Float:z, interior, vw)
{
    for(new i = 0; i < MAX_JBLS; i++)
    {
        if(!JBLInfo[i][jblAtiva]) continue;
        if(JBLInfo[i][jblInterior] != interior || JBLInfo[i][jblVW] != vw) continue;
        new Float:dist = floatsqroot(
            floatpower(x - JBLInfo[i][jblX], 2.0) +
            floatpower(y - JBLInfo[i][jblY], 2.0) +
            floatpower(z - JBLInfo[i][jblZ], 2.0)
        );
        if(dist < 100.0) return 1;
    }
    return 0;
}

stock JBLNoRaio(playerid, jblid)
{
    new Float:dist = JBLDistancia(playerid, jblid);
    if(dist < 0.0) return 0;
    return (dist <= 80.0);
}

stock JBLLinkValido(const link[])
{
    new tam = strlen(link);
    if(tam < 15 || tam > 190) return 0;

    if(strfind(link, "http://", true) != 0 && strfind(link, "https://", true) != 0)
        return 0;

    for(new i = 0; i < tam; i++)
        if(link[i] <= ' ') return 0;

    new fim = tam;
    new q = strfind(link, "?");
    if(q != -1) fim = q;

    if(!(fim > 4 && (
           !strcmp(link[fim-4], ".mp3", true, 4) ||
           !strcmp(link[fim-4], ".ogg", true, 4) ||
           !strcmp(link[fim-4], ".m3u", true, 4) ||
           !strcmp(link[fim-4], ".pls", true, 4))))
        return 0;

    if(strfind(link, "localhost", true) != -1) return 0;
    if(strfind(link, "127.0.0.1", true) != -1) return 0;
    if(strfind(link, "192.168.", true) != -1) return 0;
    return 1;
}

stock JBLAtualizarLabel(jblid)
{
    if(!JBLValida(jblid)) return 0;
    
    new texto[220];
    format(texto, sizeof(texto), "{F1C40F}[ JBL ]\n{FFFFFF}Dono: {BDC3C7}%s\n{FFFFFF}Status: %s", JBLInfo[jblid][jblDonoNome], (JBLInfo[jblid][jblTocando]) ? ("{2ECC71}Tocando") : ("{E74C3C}Desligada"));
    
    if(JBLInfo[jblid][jblLabel] == Text3D:INVALID_3DTEXT_ID)
    {
        JBLInfo[jblid][jblLabel] = CreateDynamic3DTextLabel(texto, -1, JBLInfo[jblid][jblX], JBLInfo[jblid][jblY], JBLInfo[jblid][jblZ] - 0.10, 15.0, INVALID_PLAYER_ID, INVALID_VEHICLE_ID, 0, JBLInfo[jblid][jblVW], JBLInfo[jblid][jblInterior]);
    }
    else
    {
        UpdateDynamic3DTextLabelText(JBLInfo[jblid][jblLabel], -1, texto);
    }
    return 1;
}

stock JBLColocar(playerid)
{
    if(Player[playerid][pJBL] == 0) return SendClientMessage(playerid, COR_ERRO, "[JBL] Voce nao possui uma jbl.");
    if(JBLDoDono(playerid) != -1) return SendClientMessage(playerid, COR_ERRO, "[JBL] Voce ja tem uma caixa de som no chao. Use /jbl para guardar.");
    if(GetPlayerState(playerid) != PLAYER_STATE_ONFOOT) return SendClientMessage(playerid, COR_ERRO, "[JBL] Voce precisa estar a pe para colocar a jbl.");

	new interior = GetPlayerInterior(playerid);
    new vw = GetPlayerVirtualWorld(playerid);
    new Float:x, Float:y, Float:z, Float:a;
    GetPlayerPos(playerid, x, y, z);
    GetPlayerFacingAngle(playerid, a);
    if(JBLExistePerto(x, y, z, interior, vw)) return SendClientMessage(playerid, COR_ERRO, "[JBL] Existe outra JBL a menos de 100 metros daqui.");
    
    new jblid = JBLSlotLivre();
    if(jblid == -1) return SendClientMessage(playerid, COR_ERRO, "[JBL] O servidor atingiu o limite de jbls ativas.");

    JBLInfo[jblid][jblAtiva] = true;
    JBLInfo[jblid][jblDono] = playerid;
    JBLInfo[jblid][jblX] = x;
    JBLInfo[jblid][jblY] = y;
    JBLInfo[jblid][jblZ] = z - 0.95;
    JBLInfo[jblid][jblA] = a;
    JBLInfo[jblid][jblInterior] = interior;
    JBLInfo[jblid][jblVW] = vw;
    JBLInfo[jblid][jblTocando] = false;
    JBLInfo[jblid][jblLink][0] = EOS;
    JBLInfo[jblid][jblLabel] = Text3D:INVALID_3DTEXT_ID;

    GetPlayerName(playerid, JBLInfo[jblid][jblDonoNome], MAX_PLAYER_NAME);
    JBLInfo[jblid][jblObjeto] = CreateDynamicObject(2226, JBLInfo[jblid][jblX], JBLInfo[jblid][jblY], JBLInfo[jblid][jblZ], 0.0, 0.0, JBLInfo[jblid][jblA], vw, interior, -1, 120.0);
    JBLAtualizarLabel(jblid);
    
	ApplyAnimation(playerid, "BOMBER", "BOM_Plant", 4.1, 0, 0, 0, 0, 0, 1);
    SendClientMessage(playerid, COR_SUCESSO, "[INFO] Jbl colocada no chao. Use /jbl para tocar uma musica.");
    return 1;
}

stock JBLTocar(jblid, const link[])
{
    if(!JBLValida(jblid)) return 0;
    
    format(JBLInfo[jblid][jblLink], 192, "%s", link);
    JBLInfo[jblid][jblTocando] = true;

    foreach(Player, i)
    {
        if(!JBLNoRaio(i, jblid))
        {
            if(JBLOuvindo[i] == jblid)
            {
                StopAudioStreamForPlayer(i);
                JBLOuvindo[i] = -1;
            }
            continue;
        }
        if(JBLOuvindo[i] != -1 && JBLOuvindo[i] != jblid) StopAudioStreamForPlayer(i);
        PlayAudioStreamForPlayer(i, JBLInfo[jblid][jblLink], JBLInfo[jblid][jblX], JBLInfo[jblid][jblY], JBLInfo[jblid][jblZ], 80.0, 1);
        JBLOuvindo[i] = jblid;
    }
    JBLAtualizarLabel(jblid);
    return 1;
}

stock JBLDesligar(jblid)
{
    if(!JBLValida(jblid)) return 0;

    JBLInfo[jblid][jblTocando] = false;
    JBLInfo[jblid][jblLink][0] = EOS;

    foreach(Player, i)
    {
        if(JBLOuvindo[i] == jblid)
        {
            StopAudioStreamForPlayer(i);
            JBLOuvindo[i] = -1;
        }
    }
    JBLAtualizarLabel(jblid);
    return 1;
}

stock JBLGuardar(jblid)
{
    if(!JBLValida(jblid)) return 0;

    JBLDesligar(jblid);

    if(JBLInfo[jblid][jblObjeto] != INVALID_OBJECT_ID)
    {
        if(IsValidDynamicObject(JBLInfo[jblid][jblObjeto]))
            DestroyDynamicObject(JBLInfo[jblid][jblObjeto]);
        JBLInfo[jblid][jblObjeto] = INVALID_OBJECT_ID;
    }
    if(JBLInfo[jblid][jblLabel] != Text3D:INVALID_3DTEXT_ID)
    {
        DestroyDynamic3DTextLabel(JBLInfo[jblid][jblLabel]);
        JBLInfo[jblid][jblLabel] = Text3D:INVALID_3DTEXT_ID;
    }
    JBLInfo[jblid][jblAtiva] = false;
    JBLInfo[jblid][jblDono] = INVALID_PLAYER_ID;
    JBLInfo[jblid][jblDonoNome][0] = EOS;
    JBLInfo[jblid][jblTocando] = false;
    JBLInfo[jblid][jblLink][0] = EOS;
    return 1;
}

stock JBLMostrarPlaylists(playerid)
{
    new pagina = JBLPlaylistPagina[playerid];
    new inicio = pagina * 10, texto[1200], linha[128];
    texto[0] = EOS;

    for(new i = inicio; i < inicio + 10 && i < MAX_PLAYLISTS; i++)
    {
        if(strlen(JBLPlaylistNome[playerid][i]))
        {
            format(linha, sizeof(linha), "%s\n", JBLPlaylistNome[playerid][i]);
        }
        else
        {
            format(linha, sizeof(linha), "{1E90FF}Playlist: %d\n", i + 1);
        }
        strcat(texto, linha, sizeof(texto));
    }
    if(pagina == 0)
    {
        strcat(texto, "{FFD700}Proximo", sizeof(texto));
    }
    else
    {
        if(inicio + 10 < MAX_PLAYLISTS)
        {
            strcat(texto, "{FF0000}Voltar\n{FFD700}Proximo", sizeof(texto));
        }
        else
        {
            strcat(texto, "{FF0000}Voltar", sizeof(texto));
        }
    }
    return ShowPlayerDialog(playerid, DIALOG_JBL_PLAYLISTS, DIALOG_STYLE_LIST, "{F1C40F}JBL - Playlists", texto, "Selecionar", "Fechar");
}

stock JBLAbrirPlaylist(playerid, playlistid)
{
    if(playlistid < 0 || playlistid >= MAX_PLAYLISTS) return 0;
    JBLPlaylistSelecionada[playerid] = playlistid;
    if(strlen(JBLPlaylistNome[playerid][playlistid]) == 0)
    {
        return JBLPlaylistPedirLink(playerid);
    }
    new texto[256];
    format(texto, sizeof(texto), "{F1C40F}Playlist\n%s\n", JBLPlaylistNome[playerid][playlistid]);
    return ShowPlayerDialog(playerid, DIALOG_JBL_PLAYLIST_ACAO, DIALOG_STYLE_LIST, "{F1C40F}Playlist", "Tocar musica\nExcluir musica", "Selecionar", "Voltar");
}

stock JBLPlaylistPedirLink(playerid)
{
    new texto[900];
    format(texto, sizeof(texto), 
        "{FFFFFF}Agora digite o {00BFFF}Link {FFFFFF}da musica que voce quer adicionar.\n\n\
        {FF0000}OBS:{FFFFFF} Esse link precisa ser um link direto {FFFF00}(.mp3){FFFFFF} ou um link de download.\n\n\
        {FFFFFF}Para conseguir o link direto de uma musica, voce tem 2 opcoes:\n\n\
        {FF0000}1ª OPCAO\n\
        {FFFFFF}Fazer o download da musica na internet\n\
        e logo apos iniciar o download em seu navegador, copie o endereco do link.\n\n\
        {FF0000}2ª OPCAO\n\
        {FFFFFF}Fazer upload da musica (arquivo mp3) e enviar para uma plataforma de hospedagem mp3\n\
        {808080}(Ex: Dropbox/SoundCloud/Palco MP3/4shared/google drive)\n\n\
        {FFFFFF}Apos ter o link da musica em maos, coloque ele aqui abaixo:");        
    return ShowPlayerDialog(playerid, DIALOG_JBL_LINK, DIALOG_STYLE_INPUT, "Link da Musica", texto, "Adicionar", "Cancelar");
}

// funções em loop/stocks utilitárias
stock sdados()
{
    foreach(Player, i)
    {
        if(PlayerLogado[i] == true)
        {
            SalvarConta(i);
        }
    }
    for(new c = 0; c < TotalCasas; c++)
    {
        CasaSalvar(c);
    }
    for(new e = 0; e < TotalEmpresas; e++)
    {
        EmpresaSalvar(e);
    }
    CasaSalvarConfig();
    EmpresaSalvarConfig();
    KillTimer(T_ChamadaUmSegundo);
    KillTimer(T_ChamadaUmMinuto);
}

stock AtualizarDataHora()
{
    new hora, minuto, segundo, texto[64];
    gettime(hora, minuto, segundo);
    format(texto, sizeof(texto), "%02d:%02d", hora, minuto);
    foreach(Player, i)
    {
	    PlayerTextDrawSetString(i, Necessidades[i][25], texto);
    }
    SetWorldTime(hora);
    return 1;
}

function ChamadaUmMinuto()
{
	AtualizarDataHora();
	return 1;
}

function ChamadaUmSegundo()
{
	new texto[10];
	foreach(Player, i)
	{
		if(PlayerLogado[i] == true)
	    {				
		    format(texto, sizeof(texto), "%.0f", GetPlayerHealthEx(i));
		    PlayerTextDrawSetString(i, Necessidades[i][5], texto);
		    format(texto, sizeof(texto), "%.0f", GetPlayerArmourEx(i));
		    PlayerTextDrawSetString(i, Necessidades[i][22], texto);
		
	        if(GetPlayerMoney(i) > GetPlayerCash(i))
		    {
		        new const old_money = GetPlayerCash(i);
		        ResetPlayerCash(i);
                GivePlayerCash(i, old_money);
		    }
	        new atual = JBLOuvindo[i];
	        if(atual != -1 && JBLValida(atual) && JBLInfo[atual][jblTocando] && JBLNoRaio(i, atual)) continue;
	
	        new alvo = -1, Float:menor = 80.0;
	        for(new j = 0; j < MAX_JBLS; j++)
	        {
	            if(JBLInfo[j][jblAtiva] == false || JBLInfo[j][jblTocando] == false) continue;
	
	            new Float:dist = JBLDistancia(i, j);
	            if(dist < 0.0) continue;
	            if(dist <= menor)
	            {
	                menor = dist;
	                alvo = j;
	            }
	        }
	        if(alvo == atual) continue;	
	        if(atual != -1) StopAudioStreamForPlayer(i);	
	        if(alvo != -1)
	        {
	            PlayAudioStreamForPlayer(i, JBLInfo[alvo][jblLink], JBLInfo[alvo][jblX], JBLInfo[alvo][jblY], JBLInfo[alvo][jblZ], 80.0, 1);
	        }
	        JBLOuvindo[i] = alvo;
	    }
    }
    return 1;
}

stock ResetarJogador(playerid)
{
	ReportAtivo[playerid] = false;
	ReportRespondendo[playerid] = INVALID_PLAYER_ID;
	ReportAguardandoAvaliacao[playerid] = false;
	ReportAvaliacaoAdmin[playerid] = INVALID_PLAYER_ID;
	ReportAvaliacaoAdminNome[playerid][0] = EOS;
	ReportTexto[playerid][0] = EOS;
    PlayerLogado[playerid] = false;
    AdmTrabalhando[playerid] = false;
    gPlayerAnimLibReloaded[playerid] = 0;
    ReportAvaliacaoAdminIDFixo[playerid] = -1;
    TentativasLogin[playerid] = -1;
    TimerCadeia[playerid] = -1;
    TimerMuted[playerid] = -1;
    TimerFerido[playerid] = -1;
    TimerFome[playerid] = -1;
	TimerSede[playerid] = -1;
	TimerSono[playerid] = -1;
    VehPublico[playerid] = -1;
    JBLOuvindo[playerid] = -1;
    JBLCooldown[playerid] = -1;
    JBLPlaylistPagina[playerid] = -1;
	JBLPlaylistSelecionada[playerid] = -1;
	
	for(new i = 0; i < MAX_PLAYLISTS; i++)
	{
	    JBLPlaylistNome[playerid][i][0] = EOS;
	    JBLPlaylistLink[playerid][i][0] = EOS;
	}
	LabelIDFixo[playerid] = Text3D:INVALID_3DTEXT_ID;
    
    SetPVarInt(playerid, "SpecTarget", -1);
    SetPVarInt(playerid, "CasaProxima", -1);
    SetPVarInt(playerid, "CasaOfertaID", -1);
    SetPVarInt(playerid, "CasaOfertaVendedor", -1);
    SetPVarInt(playerid, "CasaConviteID", -1);
    SetPVarInt(playerid, "CasaConviteSlot", -1);
    SetPVarInt(playerid, "CasaSlotMorador", -1);
    SetPVarInt(playerid, "AcabouDeRegistrar", 0);
    
    DeletePVar(playerid, "ReportSelecionado");
    DeletePVar(playerid, "CasaProxima");
    DeletePVar(playerid, "CasaSlotMorador");
    DeletePVar(playerid, "CasaOfertaID");
    DeletePVar(playerid, "CasaOfertaVendedor");
    DeletePVar(playerid, "CasaOfertaVendedorNome");
    DeletePVar(playerid, "CasaOfertaTempo");
    DeletePVar(playerid, "CasaOfertaValor");
    DeletePVar(playerid, "CasaConviteID");
    DeletePVar(playerid, "CasaConviteSlot");
    DeletePVar(playerid, "CasaConviteDono");
    DeletePVar(playerid, "CasaConviteTempo");
    DeletePVar(playerid, "EmpresaProxima");
    DeletePVar(playerid, "EmpresaMenuID");
    DeletePVar(playerid, "EmpresaLojaID");
    DeletePVar(playerid, "EmpresaProduto");
    DeletePVar(playerid, "EmpresaProdutoQtd");
    return 1;
}

stock SepararGrana(number)
{
    static value[32], length;
    format(value, sizeof(value), "%d", (number < 0) ? (-number) : (number));
    
    if((length = strlen(value)) > 3)
    {
        for(new i = length, l = 0; --i >= 0; l ++) 
        {
            if((l > 0) && (l % 3 == 0)) strins(value, ".", i + 1);
        }
    }
    if(number < 0)
        strins(value, "-", 0);
    return value;
}

stock ProxDetector(Float:radi, playerid, string[], col1, col2, col3, col4, col5)
{
    if(IsPlayerConnected(playerid))
    {
        new Float:posx, Float:posy, Float:posz;
        new Float:oldposx, Float:oldposy, Float:oldposz;
        new Float:tempposx, Float:tempposy, Float:tempposz;
        GetPlayerPos(playerid, oldposx, oldposy, oldposz);
        for(new i, p = GetPlayerPoolSize(); i <= p; ++i)
        {
            if(IsPlayerConnected(i) && GetPlayerVirtualWorld(playerid) == GetPlayerVirtualWorld(i) && GetPlayerInterior(playerid) == GetPlayerInterior(i))
            {
                GetPlayerPos(i, posx, posy, posz);
                tempposx = (oldposx -posx);
                tempposy = (oldposy -posy);
                tempposz = (oldposz -posz);
                if (((tempposx < radi/16) && (tempposx > -radi/16)) && ((tempposy < radi/16) && (tempposy > -radi/16)) && ((tempposz < radi/16) && (tempposz > -radi/16)))
                {
                    SendClientMessage(i, col1, string);
                }
                else if (((tempposx < radi/8) && (tempposx > -radi/8)) && ((tempposy < radi/8) && (tempposy > -radi/8)) && ((tempposz < radi/8) && (tempposz > -radi/8)))
                {
                    SendClientMessage(i, col2, string);
                }
                else if (((tempposx < radi/4) && (tempposx > -radi/4)) && ((tempposy < radi/4) && (tempposy > -radi/4)) && ((tempposz < radi/4) && (tempposz > -radi/4)))
                {
                    SendClientMessage(i, col3, string);
                }
                else if (((tempposx < radi/2) && (tempposx > -radi/2)) && ((tempposy < radi/2) && (tempposy > -radi/2)) && ((tempposz < radi/2) && (tempposz > -radi/2)))
                {
                    SendClientMessage(i, col4, string);
                }
                else if (((tempposx < radi) && (tempposx > -radi)) && ((tempposy < radi) && (tempposy > -radi)) && ((tempposz < radi) && (tempposz > -radi)))
                {
                    SendClientMessage(i, col5, string);
                }
            }
        }
    }
    return 1;
}

stock GetVehicleModelName(modelid, output[], len)
{
    if(modelid >= 400 && modelid <= 611)
    {
        format(output, len, "%s", NomesVeiculos[modelid - 400]);
    }
    else
    {
        format(output, len, "Desconhecido");
    }
    return 1;
}

stock RemoverPlayerWeapon(playerid, weaponid)
{
    new wData[13][2];
    for(new slot = 0; slot < 13; slot++)
    {
        GetPlayerWeaponData(playerid, slot, wData[slot][0], wData[slot][1]);
    }
    ResetPlayerWeapons(playerid);
    for(new slot = 0; slot < 13; slot++)
    {
        if(wData[slot][0] != weaponid && wData[slot][0] != 0)
        {
            GivePlayerWeapon(playerid, wData[slot][0], wData[slot][1]);
        }
    }
    return 1;
}

stock GetWeaponModel(weaponid)
{
    switch(weaponid)
    {
        case 1: return 331;
        case 2: return 333;
        case 3: return 334;
        case 4: return 335;
        case 5: return 336;
        case 6: return 337;
        case 7: return 338;
        case 8: return 339;
        case 9: return 341;
        case 10: return 321;
        case 11: return 322;
        case 12: return 323;
        case 13: return 324;
        case 14: return 325;
        case 15: return 326;
        case 16: return 342;
        case 17: return 343;
        case 18: return 344;
        case 22: return 346;
        case 23: return 347;
        case 24: return 348;
        case 25: return 349;
        case 26: return 350;
        case 27: return 351;
        case 28: return 352;
        case 29: return 353;
        case 30: return 355;
        case 31: return 356;
        case 32: return 372;
        case 33: return 357;
        case 34: return 358;
        case 35: return 359;
        case 36: return 360;
        case 37: return 361;
        case 38: return 362;
        case 39: return 363;
        case 40: return 364;
        case 41: return 365;
        case 42: return 366;
        case 43: return 367;
        case 46: return 371;
    }
    return 0;
}
