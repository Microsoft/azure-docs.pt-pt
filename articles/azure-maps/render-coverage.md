---
title: Renderize a cobertura no Azure Maps | Microsoft Docs
description: Saiba mais sobre a cobertura de renderização no Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5affc9952cfe13ebfeede126051f2236499d1343
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934297"
---
# <a name="azure-maps-render-coverage"></a>Cobertura de renderização do Azure Maps

O Azure Maps usa blocos de rasterização e blocos de vetor para criar mapas. Em sua resolução mais baixa, o mundo inteiro se ajusta a um único bloco. Em sua resolução mais alta, um único bloco representa 38 metros quadrados. Ao ampliar um mapa, portanto, você pode ver cada vez mais detalhes sobre continentes, regiões, cidades e ruas individuais. Para obter mais informações, consulte [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md).

No entanto, o Maps não tem o mesmo nível de informações e precisão para todas as regiões. As tabelas a seguir fornecem informações sobre o nível de detalhes renderizados que você pode esperar de cada região.

## <a name="legend"></a>Legenda

| Símbolo | Significado |
|--------|---------|
| ✓ | A região é representada com dados detalhados.   |
| A partir do | A região é representada com dados simplificados. |


## <a name="africa"></a>África 


| País/Região | Blocos de varredura unificados | Blocos de vetor unificados |
| ------ | :------------------: | :------------------: |
| Argélia                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benim                            | ✓ | ✓ |
| Botsuana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Camarões                         | ✓ | ✓ |
| República Centro-Africana         | ✓ | A partir do |
| Chad                             | ✓ | A partir do |
| Ilhas                          | ✓ | A partir do |
| República Democrática do Congo | ✓ | ✓ |
| Costa do Marfim (Côte d’Ivoire)                    | ✓ | A partir do |
| Djibuti                         | ✓ | A partir do |
| Egito                            | ✓ | ✓ |
| Guiné Equatorial                | ✓ | A partir do |
| Eritreia                          | ✓ | A partir do |
| Etiópia                         | ✓ | A partir do |
| Gabão                            | ✓ | ✓ |
| Gâmbia                           | ✓ | A partir do |
| Gana                            | ✓ | ✓ |
| Guiné                           | ✓ | A partir do |
| Guiné-Bissau                    | ✓ | A partir do |
| Quénia                            | ✓ | ✓ |
| Lesoto                          | ✓ | ✓ |
| Libéria                          | ✓ | A partir do |
| Líbia                            | ✓ | A partir do |
| Madagáscar                       | ✓ | A partir do |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauritânia                       | ✓ | ✓ |
| Maurícia                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marrocos                          | ✓ | ✓ |
| Moçambique                       | ✓ | ✓ |
| Namíbia                          | ✓ | ✓ |
| Níger                            | ✓ | ✓ |
| Nigéria                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Ruanda                           | ✓ | ✓ |
| Santa Helena, ascensão e Tristão da Cunha | ✓ | A partir do |
| São Tomé e Príncipe            | ✓ | A partir do |
| Senegal                          | ✓ | ✓ |
| Serra Leoa                     | ✓ | ✓ |
| Somália                          | ✓ | ✓ |
| África do Sul                     | ✓ | ✓ |
| Sudão do Sul                      | ✓ | ✓ |
| Sudão                            | ✓ | ✓ |
| Suazilândia                        | ✓ | ✓ |
| República americana da Tanzânia      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunísia                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zâmbia                           | ✓ | ✓ |
| Zimbabué                         | ✓ | ✓ |

## <a name="americas"></a>Américas

| País/Região | Blocos de varredura unificados | Blocos de vetor unificados |
| ------ | :------------------: | :------------------: |
| Anguila                  | ✓ | ✓ |
| Antígua e Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Baamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermudas                   | ✓ | ✓ |
| Estado Plurinacional da Bolívia | ✓ | ✓ |
| Bonaire, Santo Eustáquio e Saba | ✓ | ✓ |
| Brasil                    | ✓ | ✓ |
| Canadá                    | ✓ | ✓ |
| Ilhas Caimão            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Colômbia                  | ✓ | ✓ |
| Costa Rica                | ✓ | ✓ |
| Cuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| República Dominicana        | ✓ | ✓ |
| Equador                   | ✓ | ✓ |
| Ilhas Malvinas (Malvinas) | ✓ | ✓ |
| Guiana Francesa             | ✓ | ✓ |
| Gronelândia                 | ✓ | A partir do |
| Granada                   | ✓ | ✓ |
| Guadalupe                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guiana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaica                   | ✓ | ✓ |
| Martinica                | ✓ | ✓ |
| México                    | ✓ | ✓ |
| Monserrate                | ✓ | ✓ |
| Nicarágua                 | ✓ | ✓ |
| Ilhas Marianas do Norte  | ✓ | ✓ |
| Panamá                    | ✓ | ✓ | 
| Paraguai                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Porto Rico               | ✓ | ✓ |
| Quebec (Canadá)           | ✓ | ✓ |
| São Bartolomeu          | ✓ | ✓ |
| São Cristóvão e Neves     | ✓ | ✓ |
| Santa Lúcia               | ✓ | ✓ |
| Saint Martin (francês)     | ✓ | ✓ |
| São Pedro e Miquelon | ✓ | ✓ |
| São Vicente e Granadinas | ✓ | ✓ |
| Santo Maarten (Holandês)      | ✓ | ✓ |
| Ilhas Geórgia do Sul e Sandwich do Sul | ✓ | ✓ |
| Suriname                  | ✓ | ✓ |
| Trinidad e Tobago       | ✓ | ✓ |
| Ilhas Turcas e Caicos  | ✓ | ✓ |
| Estados Unidos             | ✓ | ✓ |
| Uruguai                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Ilhas Virgens Britânicas   | ✓ | ✓ |
| Ilhas Virgens, E.U.A.      | ✓ | ✓ |

## <a name="asia"></a>Ásia 

| País/Região | Blocos de varredura unificados | Blocos de vetor unificados |
| ------ | :------------------: | :------------------: |
| Afeganistão               |   | A partir do |
| Bahrain                   | ✓ | ✓ |
| Bangladeche                |   | A partir do |
| Butão                    |   | A partir do |
| Território Britânico do Oceano Índico |   | A partir do |
| Brunei                    | ✓ | ✓ |
| Camboja                  |   | A partir do |
| China                     |   | A partir do |
| Ilhas Cocos (Keeling)   |   | A partir do |
| República Democrática Popular da Coreia do Norte |   | A partir do |
| RAE de Hong Kong             | ✓ | ✓ |
| Índia                     | A partir do | ✓ | 
| Indonésia                 | ✓ | ✓ |
| Irã                      |   | A partir do |
| Iraque                      | ✓ | ✓ |
| Israel                    |   | ✓ |
| Japão                     |   | A partir do |
| Jordânia                    | ✓ | ✓ |
| Cazaquistão                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Quirguizistão                |   | A partir do |
| República Democrática Popular do Laos |   | A partir do |
| Líbano                   | ✓ | ✓ |
| Rae de Macau                 | ✓ | ✓ |
| Malásia                  | ✓ | ✓ |
| Maldivas                  |   | A partir do |
| Mongólia                  |   | A partir do |
| Myanmar                   |   | A partir do |
| Nepal                     |   | A partir do |
| Omã                      | ✓ | ✓ |
| Paquistão                  |   | A partir do |
| Filipinas               | ✓ | ✓ |
| Catar                     | ✓ | ✓ |
| República da Coreia         | ✓ | A partir do |
| Arábia Saudita              | ✓ | ✓ |
| Ilhas Senkaku           |   | ✓ |
| Singapura                 | ✓ | ✓|
| Sri Lanka                 |   | A partir do |
| República Árabe Síria      |   | A partir do |
| Taiwan                    | ✓ | ✓ |
| Tajiquistão                |   | A partir do |
| Tailândia                  | ✓ | ✓ |
| Timor-Timor Leste               |   | A partir do |
| Turquemenistão              |   | A partir do |
| Emirados Árabes Unidos      | ✓ | ✓ |
| Ilhas Menores Afastadas dos Estados Unidos |   | A partir do |
| Usbequistão                |   | A partir do |
| Vietname                   | ✓ | ✓ |
| Iémen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceania

| País/Região | Blocos de varredura unificados | Blocos de vetor unificados |
| ------ | :------------------: | :------------------: |
| Samoa Americana            |   | ✓ |
| Austrália                 | ✓ | ✓ |
| Ilhas Cook              |   | A partir do |
| Fiji                      |   | A partir do |
| Polinésia francesa          |   | A partir do |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | A partir do |
| Ilhas Marshall          |   | A partir do |
| Micronésia                |   | A partir do |
| Nauru                     |   | A partir do |
| Nova Caledônia             |   | A partir do |
| Nova Zelândia               | ✓ | ✓ |
| Niue                      |   | A partir do |
| Ilha Norfolk            |   | A partir do |
| Palau                     |   | A partir do |
| Papua-Nova Guiné          |   | A partir do |
| Pitcairn                  |   | A partir do |
| Samoa                     |   | A partir do |
| Ilhas Salomão           |   | A partir do|
| Toquelau                   |   | A partir do |
| Tonga                     |   | A partir do |
| Tuvalu                    |   | A partir do |
| Vanuatu                   |   | A partir do |
| Wallis e Futuna         |   | A partir do |


## <a name="europe"></a>Europa

| País/Região | Blocos de varredura unificados | Blocos de vetor unificados |
| ------ | :------------------: | :------------------: |
| Albânia                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Arménia                   | ✓ | A partir do |
| Áustria                   | ✓ | ✓ |
| Azerbaijão                | ✓ | A partir do |
| Bielorrússia                   | A partir do | ✓ |
| Bélgica                   | ✓ | ✓ |
| Bósnia-Herzegovina        | ✓ | ✓ |
| Bulgária                  | ✓ | ✓ |
| Croácia                   | ✓ | ✓ |
| Chipre                    | ✓ | ✓ |
| República Checa            | ✓ | ✓ |
| Dinamarca                   | ✓ | ✓ |
| Estónia                   | ✓ | ✓ |
| Ilhas Faroé             | ✓ | A partir do |
| Finlândia                   | ✓ | ✓ |
| França                    | ✓ | ✓ |
| Geórgia                   | ✓ | A partir do |
| Alemanha                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Grécia                    | ✓ | ✓ |
| Guernsey                  | ✓ | ✓ |
| Hungria                   | ✓ | ✓ |
| Islândia                   | ✓ | ✓ |
| Irlanda                   | ✓ | ✓ |
| Ilha de Man               | ✓ | ✓ |
| Itália                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Camisas                    | ✓ | ✓ |
| Letónia                    | ✓ | ✓ |
| Listenstaine             | ✓ | ✓ |
| Lituânia                 | ✓ | ✓ |
| Luxemburgo                | ✓ | ✓ |
| Nordeste da Macedônia           | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldova                   | ✓ | ✓ |
| Mónaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Países Baixos               | ✓ | ✓ |
| Noruega                    | ✓ | ✓ |
| Polónia                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Roménia                   | ✓ | ✓ |
| Federação Russa        | ✓ | ✓ |
| São Marinho                | ✓ | ✓ |
| Sérvia                    | ✓ | ✓ |
| Eslováquia                  | ✓ | ✓ |
| Eslovénia                  | ✓ | ✓ |
| Curilas do Sul           | ✓ | ✓ |
| Espanha                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Suécia                    | ✓ | ✓ |
| Suíça               | ✓ | ✓ |
| Turquia                    | ✓ | ✓ |
| Ucrânia                   | ✓ | ✓ |
| Reino Unido            | ✓ | ✓ |
| Cidade do Vaticano              | ✓ | ✓ |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a renderização do Azure Maps, consulte [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md).

Saiba mais sobre as [áreas de cobertura para o serviço de roteamento de mapas](routing-coverage.md). 