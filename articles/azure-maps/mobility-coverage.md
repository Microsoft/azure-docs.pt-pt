---
title: Cobertura de mobilidade (trânsito) nos serviços de mobilidade microsoft Azure Maps (Preview)
description: Saiba qual o nível de cobertura que os serviços de mobilidade Azure Maps (Preview) fornecem em que regiões para recursos de trânsito público, tais como alertas de encaminhamento e serviço.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e902f313edf22d75f6b183575c3dc8d0dd94bc1f
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904759"
---
# <a name="azure-maps-mobility-services-preview-coverage"></a>Cobertura dos serviços de mobilidade Azure Maps (Pré-visualização)

> [!IMPORTANT]
> Os serviços de mobilidade Azure Maps estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Os [serviços de Mobilidade](/rest/api/maps/mobility) Azure Maps melhoram o tempo de desenvolvimento de aplicações com características de trânsito público, como o encaminhamento de trânsito e a procura de paragens de trânsito público nas proximidades. Os utilizadores podem obter informações detalhadas sobre paragens, linhas e horários de trânsito. Os serviços de Mobilidade também permitem que os utilizadores recuperem geometrias de paragem e linha, alertas para paragens, linhas e áreas de serviço, e chegadas e alertas de serviço em tempo real de trânsito público. Além disso, os serviços de Mobilidade fornecem capacidades de encaminhamento com opções de planeamento de viagens multimodais. O planeamento de viagens multimodais incorpora opções de caminhada, ciclismo e trânsito público, tudo numa viagem. Os utilizadores também podem aceder a itinerários multimodais detalhados passo a passo.

O Azure Maps não fornece o mesmo nível de informação e precisão para todas as cidades e países/regiões. A capacidade de chamar dados de trânsito público depende da área metropolitana. Além disso, os dados do mapa podem não incluir todas as opções de trânsito público e agências que servem a área metropolitana.

A tabela seguinte fornece informações de cobertura para os serviços de mobilidade Azure Maps.

| Símbolo | Significado |
|--------|---------|
| *      |Cobertura quase total para o país/região.|

## <a name="americas"></a>Américas

| Pais/região |  Cidade (Área metropolitana) |
|----------------|---------|
| Antígua e Barbuda | Antígua e Barbuda* |
| Argentina       | <p>Azul, Bahía Blanca, Buenos Aires, Caleta Olivia, Catamarca, Chivilcoy, Comodoro Rivadavia, Concordia, Córdoba, Corrientes, General Pico, Gualeguaychu, La Rioja, Mar del Plata, Mendoza, Miramar, Necochea, Neuquén,Oberá, Olavarría, Paraná, Posadas, Rafaela, Rio Tercero, Rosário, Salta, San Carlos de Bariloche, San Luis, San Miguel de Tucumán, San Pedro, Santa Fé, Tandil, Ushuaia, Victoria,</p>|
| Barbados       |  Barbados* |
| Brasil         | <p>Angra dos Reis, Anápolis, Apucarana, Aracaju, Araraquara, Araxa, Araçatuba, Atibaia, Bage, Barretos, Bauru, Bebedouro, Belém, Belo Horizonte, Blumenau, Boa Vista, Botucatu, Brasília, Caldas Novas, Campina Grande, Campinas, Campo Belo, Campo Grande, Caraguatatuba, Caratinga, Cascavel, Cataguases, Caxias, Leopoldina e Região, Catalão, Caxias do Sul, Chapecó, Cianorte, Conselheiro Lafaiete, Corumbá, Criciúma, Cruzeiro do Sul, Cuiabá, Curitiba, Curitiban, Curitiban, Curloban, Divinópolis, Dourados, Estrela, Feira de Santana, Fernando de Noronha, Florianópolis, Fortaleza, Foz do Iguaçu, Franca, Garanhuns, Goiania, Governador Valadares, Guarapuava, Imperatriz, Ipatinga, Irati, Itabira, Itabuna, Itajaí, Itajuba, Itui Colôn, Jaguarao, Jaraguá do Sul, João Pessoa, Joinville, Juazeiro do Norte, Juiz de Fora, Jundiaí, Lages, Lavras e Regiao, Lucas do Rio Verde, Macapa, Macaé, Maceió, Mafra e Rio Negro, Manaus, Manhuacu, Maringá, Marília, Monte Carmelo Montes Claros, Mossoró, Natal, Osório, Ourinhos, Ouro Preto, Palmas, Paracatu, Paranaguá, Parnaíba, Passo Fundo, Passos, Pato Branco, Patos de Minas, Patrocínio, Pelotas, Picos, Piracicaba, Pirapora, Pocos de Caldas, Ponta Grossa, Porto Alegre, Porto Velho, Praia Grande, Recife, Ribeirão Preto, Rio Branco, Rio Verde, Ronó Salvador, Santa Cruz do Sul, Santa Maria, Santa Rita do Sapucaí, Santarém, Santiago del Estero, Santos, São Gabriel do Oeste, São João del Rei, Tiradentes e Regiao, São José do Rio Preto, São Mateus, São Paulo, Sorocaba, São Carlos, São Francisco do Sul, São José dos Campos, São Lourenço, São Luís, Taubaté, Telemaco Borba, Teofilo Otoni, Teresina, Toledo, Três Lagoas, Tucurui, Ubatuba, Uberaba, Uberlândia, Ubá, Uruguaiana, Varginha, Vicosa, Videira & Fraiburgo , Vitória, Vitória da Conquista, Volta Redonda, Votuporanga </p>|
| Canadá | Banff (AB), Brandon (MB), Calgary (AB), Chatham-Kent (ON), Comox Valley (BC), Cowichan Valley (BC), Cranbrook (BC),Edmonton (AB), Forte st. John, Fredericton (NB), Greater Sudbury (ON), Greater Vancouver (BC), Halifax (NS), Kamloops (BC), Kelowna - Vernon (BC), Kingston (ON), Londres (ON), Moncton (NB), Montreal (QC), Nanaimo (BC), Ottawa (ON), Prince George (BC), Québec City (QC), Red Deer (AB), Regina (SK), Rimouski (QC), Saskatoon (SK), Sherbrooke (QC), Southwest British Columbia (BC), Squamish (BC), St. John's (NL), Sunshine Coast, Thunder Bay (ON), Toronto (ON), Victoria (BC), West Kootenay (BC), Whistler (BC), Windsor (ON), Winnipeg (MB), Woodstock</p>|
| Chile  | <p>Antofagasta, Arica, Aysén, Chillán, Concepción, Constitución, Copiapó, Curicó, Iquique, La Serena y Coquimbo, Linares, Los Angeles (Chile), Los Lagos, Punta Arenas, Rancagua, Santiago, Talca, Temuco, Valdivia, Valparaíso, Viña del Mar</p>|
| Colômbia | <p>Barranquilla, Bogotá, Bucaramanga, Cali, Cartagena, Ibagué, Medellín, Pasto, Popayán, Santa Marta, Sincelejo, Valledupar</p>|  
| Costa Rica | São José|
| República Dominicana | Santo Domingo |
| Equador | Cuenca, Guayaquil, Loja, Manta, Milagro|
| Salvador | São Salvador |
 | Guatemala | Ciudad de Guatemala (GT) |
| México | Acapulco, Aguascalientes, Cancun, Durango, Cidade do México, Guadalajara, Leão, Merida, Monterrey, Puebla, Puerto Vallarta, Querétaro, San Luis Potosi, Tijuana, Torreon|
| Nicarágua | Manágua |
| Panamá | Panamá*|
| Peru | Cusco |
| Porto Rico | San Juan |
| Suriname | Rio Paramaribo |
| Uruguai | Montevidéu, Paysandu, Punta del Este, Salto |
| Estados Unidos da América | <p>Albany (GA), Albany (NY), Albuquerque (NM), Anchorage (AK), Ann Arbor (MI) Appleton-Oshkosh-Neenah (WI), Asheville (NC), Atenas (GA), Atenas (OH), Atlanta (GA), Austin (TX), Bakersfield (CA), Baltimore (MD), Bend-Redmond (OR), Berkshire County (MA), Birmingham (AL), Bloomington (IN), Boise (ID), Boston (MA), Boulder (CO), Bowling Green (KY), Brevard County (FL), Buffalo (NY), Butte (MT), Cape Cod (MA), Centre County (PA), Champaign-Urbana (IL), Charleston (SC), Charleston (WV), Charlotte (NC), Charlottesville (VA), Chattanooga (TN), Cheyenne (WY), Chicago (IL)), Cincinnati (OH), Citrus County (FL), Cleveland (OH), Coachella Valley (CA), Colorado Springs (CO), Columbia (TN), Columbia (SC), Columbus (OH), Corpus Christi (TX), Dallas/Forth Worth (TX), Dayton (OH), Delaware, Denver (CO), Des Moines (IA), Detroit (MI), Duluth (MN), El Paso (TX), Eugene (OR), Fairbanks (AK), Fargo (ND) , Fayetteville (NC), Flagstaff (AZ), Flint (MI) Fort Collins (CO), Fort Wayne (IN), Fresno (CA), Gainesville (FL), Grand Forks (ND), Grand Rapids (MI), Green Bay (WI), Greensboro (NC), Greenville (SC), Gunnison (CO), Hampton Roads (Hampton Roads) VA), Hanford (CA), Hartford (CT), Hernando County (FL), Hinesville (GA), Honolulu (HI), Houston (TX), Humboldt County (CA), Huntsville (AL), Indianapolis (IN), Ithaca (NY), Jackson (MS), Jackson (TN), Jacksonville - St. John's County (FL), Johnson city (TN), Jonesboro (AR), Joplin (MO), Juneau (AK), Kalamazoo (MI), Kalispell (MT), Kansas City (MO), Kauai (HI), Ketchum (ID), Knoxville (TN), Lafayette (IN), Lancaster (PA), Lansing (MI), Laredo (TX), Las Vegas (NV), Lawrence (KS), Lee County (FL), Lexington (KY), Lincoln County (OR), Little Rock (AR), Los Angeles (CA), Louisville (KY), Lubbock (TX), Madison (WI), Manchester (NH) , McAllen (TX), Memphis (TN), Miami (FL), Milwaukee-Waukesha (WI), Minneapolis-St. Paul (MN), Missoula (MT), Modesto (EUA), Moline (IL), Monroe County (PA), Montgomery (AL), Morgantown (WV), Nashville (TN), Navajo Nation), New Haven (CT), New Orleans (LA), NYC-NJ Area(NY), Ocala (FL), Condado de Okaloosa (FL), Oklahoma City (OK), Omaha (NE), Orlando (FL), Palm Desert (CA), Panamá City (FL), Pensacola (FL), Peoria (IL), Filadélfia (PA), Phoenix (AZ), Pittsburgh (PA), Portland (ME), Portland (OR), Racine (WI), Raleigh (NC), Redding (CA), Reno & Lake Tahoe (NV), Richmond (VA), Roanoke Valley (VA - Lynchburg), Rochester (NY), Rockford (IL), Rocky Mount (NC), Rocky Mountain National Park (CO), Rogue Valley (OR), Roseburg (OR), Roseville (CA), Sacramento (CA), Salem (OR), Salt Lake City (UT), San Antonio (TX), San Diego (CA), San Luis Obispo (CA), Santa Barbara (CA), Santa Fé (NM), Sarasota (FL) , Savannah (GA), Região da Costa (NH), Seattle-Tacoma-Bellevue (WA), SF Bay Area (CA), SF-San Área de José (CA), Sioux City (IA), Sioux Falls (SD), Sitka (AK), Spokane (WA), Springfield (MA), South Bend (IN), Springfield (IL), Springfield (Mass), St. George (UT), St. Louis (MO), Stockton (CA), Syracuse-Utica (NY), Tallahassee (FL), Tampa-St. Petersburg (FL), Terre Haute (IN), Toledo (OH), Topeka (KS), Traverse City (MI), Tucson (AZ), Tulsa (OK), Vermont, Victorville (CA), Volusia County (FL), Waco (TX), Washington (DC), Waterbury (CT), Wichita (KS), Wichita Falls (TX) Wilmington (NC), Yakima (WA), Youngstown (OH),</p>|
| +Ilhas Virgens Americanas | Ilhas Virgens Americanas* |
| Venezuela | Caracas |

## <a name="asia-pacific"></a>Ásia-Pacífico

| Pais/região |  Cidade (Área metropolitana) |
|--------|---------|
| Austrália | <p>Adelaide, Alice Springs, Bowen, Brisbane, Bundaberg QLD, Burnie, Cairns, Camberra, Darwin, Gladstone, Hobart, Innisfail, Launceston, Mackay, Magnetic Island, Maryborough-Hervey Bay, Melbourne, New South Wales, Perth, RockHampton, South East Queensland, Sydney, Toowoomba, Townsville, Victoria, Warwick, Yeppoon</p> |
| Brunei | Bandar Seri Begawan |
| China | <p> Changchun, Changsha, Chengdu, Chongqing, Dalian, Datong, Dongguan, Hangzhou, Harbin, Jiangyin, Jinan, Nanjing, Nantong, Ningbo, Pingdingshan, Qingdao, Shenyang, Suzhou, Tangshan, Tianjin, Weifang, Wuhan, Wuxi, Yantai, Yixing, Zhuhai, Shanghai, Beijing, Guangzhou, Shenzhen, Zhengzhou</P>|
| R.A.E. de Hong Kong | Hong Kong SAR*|
| RAE de Macau | SAR DE Macau*|
| Maldivas | Homens |
| Índia | Ahmedabad, Bengaluru, Deli, Hyderabad, Mumbai, Mysuru, Pune|
| Indonésia | Bandung, Banjarmasin, Banyuwangi, Batam, Denpasar, Jacarta, Kediri, Malang, Palembang, Semarang, Surabaya, Surakarta, Yogyakarta |
| Japão | Hokkaido, Província de Shizuoka, Tóquio, Wakkanai, Prefeitura de Yamanashi |
| Malásia | Ipoh, Johar Bahru, Kuala Lumpur, Kuantan, Penang |
| Nova Zelândia | Auckland, Christchurch, Dunedin, Queenstown, Timaru, Wellington|
| Filipinas | Rio Manila |
| Singapura | Singapura* |
| Coreia do Sul | Busan, Seul |
| Taiwan | Condado de Changhua |
| Tailândia | Bangkok |
| Usbequistão | Rio Samarkand |
| Vietname | Hanoi |

## <a name="europe"></a>Europa

| Pais/região |  Cidade (Área metropolitana) |
|----------------|---------|
| Andorra        | Andorra la Vella |
| Áustria        | Viena |
| Bielorrússia        | Gomel, Grodno, Polotsk & Novopolotsk, Zhlobin, Vileyka, Maladziečna, Minsk, Rechytsa |
| Bélgica        | Bélgica* |
| Bolívia        | La Paz |
| Bósnia e Herzegovina | Sarajevo |
| Bulgária       | <p>Balchik, Blagoevgrad, Burgas, Dobrich, Gabrovo, Haskovo, Kardzhali, Lovech, Nessebar, Pazardzhik, Pernik, Pleven, Plovdiv, Ruse, Shumen, Sliven, Stara Zagora, Vratsa, Yambol, Varna, Veliko, Sofia</P> |
| Croácia | Crikvenica, Dubrovnik, Rijeka, Slovanski Brod, Zagreb |
| Chipre | Larnaca, Limassol, Nicosia |
| República Checa | Brno, Jablonec, Karlovy Vary, Liberec, Ostrava, Praga |
| Dinamarca   | Dinamarca* |
| Estónia   | Estónia* |
| Finlândia   | Hämeenlinna, Helsínquia, Joensuu, Jyväskylä, Kajaani, Kouvola - Kotka, Kuopio, Lappeenranta, Mikkeli, Oulu, Pori, Rovaniemi, Seinäjoki, Tampere, Turku, Vaasa|
| França    | <p>Amberieu-en-Bugey, Amiens, Angers, Annecy, Annonay, Arras, Aubenas, Bayonne, Besançon, Blois, Bordeaux, Boulogne sur Mer, Brest, Briançon, cannes, Châlons-en-Champagne, Chartres, Clermont-Ferrand, Colmar, Côte d'Azur, Dax, Dijon, Grenoble, Haguenau, La Rochelle, Le Mans, MACS, Marselha & Provence, Metz, Millau, Mont-de-Marsan, Montpellier, Mulhouse, Nancy, Nantes, Nice, Nice Côte d'Azur, Nimes, Normandia, Nyon Paris, Poitiers, Privas, Quimper, Rennes, Saint Malo, Saint-Étienne, Saint-Nazaire, Saintes, Sarrebourg, Sete, Estrasburgo, Tarbes, Toulouse, Tours</P> |
| +Guiana Francesa | Caiena |
| +Nova Caledónia | Nouméa  |
| Geórgia | Tbilisi |
| Alemanha | <p>Berlim, Brandemburgo, Bremen & Niedersachsen, Colónia, Eisenach, Frankfurt, Hamburgo, Karlsruhe, Mainz, München - Munique, região Rhein-Neckar, região Rhein-Ruhr, Estugarda, Titisee-Neustadt, Ulm</P> |
| Grécia | <p>Agrinio, Aigio, Atenas, Arta, Amorgos, Chania, Corfu, Chios Kos, Heraklion, Ioannina, Kavala, Kalamata, Khios, Komotini, Kos, Larissa, Meganisi, Milos, Mykonos, Patra, Rethimno, Rhodes, Santorini, Serres, Syros, Tinos, Thessalon, Veria, </P> |
| Hungria | Budapeste, Gyor, Miskolc, Condado de Nograd, Pesc, Szeged, Székesfehérvár |
| Islândia | Ísland - Islândia* |
| Irlanda | Irlanda* |
| Itália   | <p>Agrigento, Alessandria, Ancona, Bari, Bolonha - Bolonha, Cagliari - Sardenha, Campobasso, Catania e Messina, Cosenza, Crema, Cremona, Cuneo, Firenze - Florença, Foggia, Genova - Génova, Iglesias, La Spezia, Lecce, Matera, Milano - Milão, Nápoles - Nápoles, Padova, Palermo, Parma, Perugia, Pescara, Pisa, Potenza, Roma - Roma, Siena e Grosseto, Siracusa - Syracuse, Taranto, Torino - Turino, Trento, Trieste, Uin, </p> |
| Letónia | Rio Rīga |
| Listenstaine | Liechtenstein* |
| Lituânia | Druskininkai, Kauno, Klaipėda, Panevėžys, Vilnius |
| Luxemburgo | Luxemburgo* |
| Moldávia | Rio Chisinau |
| Montenegro | A Podgorica |
| Países Baixos | Países Baixos* |
| Noruega | Noruega* |
| Polónia | <p>Wrocław, Białystok, Bydgoszcz, Elbląg, Elk, Gorzow, Kętrzyna, Cracóvia, Leszno, Lodz, Lublin, Mrągowo, Olsztyn, Poznań, Rzeszów, Sanok, Starachowice, Świonujście, Szczecin, Tricity, Varsóvia, Wodzisław Śląski, Wroaw</p> |
| Portugal | Bragança, Coimbra, Funchal, Leiria, Lisboa, Portimão, Porto|
| Malta | Malta* |
| Roménia | <p>Alba Iulia, Arad, Bistriţa, Brăila, Braşov, Bucareste, Buzau, Cluj Napoca, Constanţa, Craiova, Deva, Focșani, Galati, Iaşi, Miercurea Ciuc, Oradea, Piatra Neamt, Pitești, Ploieşti, Reșiţa, Satu Mare, Sibiu, Suceava, Targues, Timisoara,</p> | 
| Rússia  | Kaliningrado, Rostov-on-Don, Volgograd, Yekaterinburg, Kazan, Kirov, Krasnodar, Moscovo, Nalchik, Nizhny Novgorod, Novosibirsk, Noyabrsk, Omsk, Oryol, Perm, São Petersburgo, Pyatigorsk, Tver, Tomsk |
| Sérvia  | Beograd, Kragujevac, Nis, Novi Sad, Valjevo, Subotica | 
| Eslováquia | Banská Bystrica, Bratislava, Kosice, Presov, Prievidza, Ruzomberok a Liptovsky Mikulas, Stará Ėubovėa, Trencin |
| Eslovénia | Koper |
| Espanha    | <p>Albacete, Corunha, Alicante, Almería, Astúrias, Ávila, Badajoz, Baía de Cádiz, Barcelona, Bilbau, Burgos, Cáceres, Campo de Gibraltar, Castellon de la Plana, Ceuta, Ciudad Real, Córdoba, Cuenca, El Hierro, Ferrol, Fuerteventura, Gran Canaria, Granada, Huelva, Huesca, Ibiza, Jaén - Úbeda, La Gomera, La Palma, Lanzarote, León, Lleida, Logroño, Lugo, Madrid, Málaga, Maiorca - Maiorca, Melilla, Menorca, Merida, Murcia, Ourense, Palencia, , Santander, Santiago de Compostela, Segóvia, Sevilha, Soria, Tarragona - Reus, Tenerife, Toledo, Valência, Valladolid, Vigo, Vitoria-Gasteiz, Zaragoza - Saragoça</p> |
| Suécia | Goteborg/Gotemburgo/Jonkoping, Malmö kommun - Malmö, Norrköping och Linköping, Estocolmo, Sundsvall |
| Suíça | Basileia, Genebra, Yverdon-les-Bains, Zurique | 
| Turquia | Adana-Mersin, Ancara, Antalya, Balıkesir, Bilecik, Bolu, Bursa, Çorum, Denizli, Duzce, Edirne, Elazig, Eskisehir, Istambul, Izmir-Aydin, Kahramanmaras, Kayseri, Konya, Malatya, Muğla, Samsun, Şanlıfa, Trab |
| Reino Unido | East Anglia, East Midlands, Londres e Sudeste, Nordeste, Noroeste, Irlanda do Norte, Escócia, Sudoeste, País de Gales, West Midlands, Yorkshire |
| Ucrânia | Kharkiv, Zhytomyr, Kiev, Lviv, Chernivtsi |

## <a name="middle-east-and-africa"></a>Médio Oriente e África

| Pais/região |  Cidade (Área metropolitana) |
|---------|---------|
| Barém | Bahrein* |
| Burkina Faso | Rio Ouagadougou |
| Congo | Rio Kinshasa |
| Egito | Cairo    |
| Israel| Israel*  |
| Quénia | Nairobi  |
| Madagáscar | Antananarivo |
| Marrocos | Casablanca, Essaouira, Khouribga, Tétouan|
| Catar| Doha|
| Arábia Saudita | Rio Thuwal |
| Senegal | Rio Dakar |
| África do Sul | Cidade do Cabo |
| Tunísia | Rio Kairouan |
| Emirados Árabes Unidos  | Abu Dhabi |


## <a name="next-steps"></a>Passos seguintes

Saiba como solicitar dados de trânsito utilizando serviços de mobilidade (Pré-visualização):

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Saiba como solicitar dados em tempo real utilizando serviços de mobilidade (Pré-visualização):

> [!div class="nextstepaction"]
> [Como solicitar dados em tempo real](how-to-request-real-time-data.md)

Explore a documentação da API dos serviços de mobilidade Azure Maps (Preview)

> [!div class="nextstepaction"]
> [Documentação da API dos serviços de mobilidade](/rest/api/maps/mobility)
