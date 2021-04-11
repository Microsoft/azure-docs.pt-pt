---
title: Cobertura dos serviços de meteorologia do Microsoft Azure Maps (Pré-visualização)
description: Saiba mais sobre a cobertura dos serviços de clima do Microsoft Azure Maps (Pré-visualização)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: references_regions
manager: philmea
ms.openlocfilehash: 6c4e9eb765a72b7a0b495f81a954b484ef6aa2b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905490"
---
# <a name="azure-maps-weather-services-preview-coverage"></a>Cobertura dos serviços Azure Maps Weather (Pré-visualização)

> [!IMPORTANT]
> Os serviços Azure Maps Weather estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo fornece informações de cobertura para [os serviços Azure](/rest/api/maps/weather)Maps Weather . Azure Maps Os serviços de dados meteorológicos retornam detalhes como azulejos de radar, condições meteorológicas atuais, previsões meteorológicas e o tempo ao longo de uma rota.

O Azure Maps não tem o mesmo nível de informação e precisão para todos os países e regiões.

A tabela seguinte fornece informações sobre que tipo de informações meteorológicas pode solicitar de cada país/região.

| Símbolo | Significado |
|--------|---------|
|*       |Abrange as condições atuais, previsão horária, previsão trimestral, previsão diária, meteorologia ao longo da rota e índices diários. |


## <a name="americas"></a>Américas

| Pais/região              |  Azulejos de satélite | Previsão de Minutos, Azulejos de Radar | Alertas meteorológicos severos | Outros* |
|-----------------------------|:----------------:|:-----------------:|:-----------------:|:--------:|  
| Anguila                                 | ✓ |   | |  ✓| 
| Antártica                               | ✓ |   | |✓|
| Antígua e Barbuda                      | ✓ |   | |✓| 
| Argentina                                | ✓ |   | |✓| 
| Aruba                                    | ✓ |   | |✓| 
| Baamas                                  | ✓ |   | |✓| 
| Barbados                                 | ✓ |   | |✓| 
| Belize                                   | ✓ |   | |✓| 
| Bermudas                                  | ✓ |   | |✓| 
| Bolívia                                  | ✓ |   | |✓| 
| Bonaire                                  | ✓ |   | |✓| 
| Brasil                                   | ✓ |   | ✓ |✓| 
| Ilhas Virgens Britânicas                   | ✓ |   | |✓| 
| Canadá                                   | ✓ | ✓ | ✓ | ✓| 
| Ilhas Caimão                           | ✓ |   | |✓| 
| Chile                                    | ✓ |   | |✓| 
| Colômbia                                 | ✓ |   | |✓| 
| Costa Rica                               | ✓ |   | |✓| 
| Cuba                                     | ✓ |   | |✓| 
| Curaçao                                  | ✓ |   | |✓| 
| Dominica                                 | ✓ |   | |✓| 
| República Dominicana                       | ✓ |   | |✓| 
| Equador                                  | ✓ |   | |✓| 
| Salvador                              | ✓ |   | |✓| 
| Ilhas Falkland (Malvinas)                         | ✓ |   | |✓| 
| Guiana Francesa                            | ✓ |   | |✓| 
| Gronelândia                                | ✓ |   | |✓| 
| Granada                                  | ✓ |   | |✓| 
| Guadalupe                               | ✓ |   | |✓| 
| Guatemala                                | ✓ |   | |✓| 
| Guiana                                   | ✓ |   | |✓| 
| Haiti                                    | ✓ |   | |✓| 
| Honduras                                 | ✓ |   | |✓| 
| Jamaica                                  | ✓ |   | |✓| 
| Martinica                               | ✓ |   | |✓| 
| México                                   | ✓ |   | |✓| 
| Montserrate                               | ✓ |   | |✓| 
| Nicarágua                                | ✓ |   | |✓| 
| Panamá                                   | ✓ |   | |✓| 
| Paraguai                                 | ✓ |   | |✓| 
| Peru                                     | ✓ |   | |✓| 
| Porto Rico                              | ✓ |   | ✓ |✓| 
| São Bartolomeu                         | ✓ |   | |✓| 
| São Cristóvão e Neves                    | ✓ |   | |✓| 
| Santa Lúcia                              | ✓ |   | |✓| 
| São Martinho (Saint Martin)                             | ✓ |   | |✓| 
| São Pedro e Miquelão                | ✓ |   | |✓| 
| São Vicente e Granadinas         | ✓ |   | |✓| 
| Santo Eustáquio                           | ✓ |   | |✓|  
| São Martinho (Sint Maarten)                             | ✓ |   | |✓| 
| Ilhas Geórgia do Sul e Sandwich do Sul | ✓ |   | |✓| 
| Suriname                                 | ✓ |   | |✓| 
| Trindade e Tobago                      | ✓ |   | |✓| 
| Ilhas Turcas e Caicos                 | ✓ |   | |✓| 
| Ilhas Desondo dos EUA                    | ✓ |   | |✓| 
| Ilhas Virgens Americanas                      | ✓ |   | ✓|✓| 
| Estados Unidos da América                            | ✓ | ✓ | ✓| ✓| 
| Uruguai                                  | ✓ |   | |✓| 
| Venezuela                                | ✓ |   | |✓| 


## <a name="middle-east-and-africa"></a>Médio Oriente e África

| Pais/região              |  Azulejos de satélite | Previsão de Minutos, Azulejos de Radar | Alertas meteorológicos severos | Outros* | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:| 
| Argélia                     | ✓               |                              |     |  ✓| 
| Angola                      | ✓               |                              |     |  ✓| 
| Barém                     | ✓               |                              |     |  ✓| 
| Benim                       | ✓               |                              |     |  ✓| 
| Botsuana                    | ✓               |                              |     |  ✓| 
| Ilha Bouvet               | ✓               |                              |     |  ✓| 
| Burkina Faso                | ✓               |                              |     |  ✓| 
| Burundi                     | ✓               |                              |     |  ✓| 
| Camarões                    | ✓               |                              |     |  ✓| 
| Cabo Verde                  | ✓               |                              |     |  ✓| 
| República Centro-Africana    | ✓               |                              |     |  ✓| 
| Chade                        | ✓               |                              |     |  ✓| 
| Comoros                     | ✓               |                              |     |  ✓| 
| República Democrática do Congo                 | ✓               |                              |     |  ✓|
| Costa do Marfim (Côte d’Ivoire)               | ✓               |                              |     |  ✓| 
| Jibuti                    | ✓               |                              |     |  ✓| 
| Egito                       | ✓               |                              |     |  ✓| 
| Guiné Equatorial           | ✓               |                              |     |  ✓| 
| Eritreia                     | ✓               |                              |     |  ✓| 
| eSwatini                    | ✓               |                              |     |  ✓| 
| Etiópia                    | ✓               |                              |     |  ✓| 
| Territórios Austrais Franceses | ✓               |                              |     |  ✓| 
| Gabão                       | ✓               |                              |     |  ✓| 
| Gâmbia                      | ✓               |                              |     |  ✓| 
| Gana                       | ✓               |                              |     |  ✓| 
| Guiné                      | ✓               |                              |     |  ✓| 
| Guiné-Bissau               | ✓               |                              |     |  ✓| 
| Irão                        | ✓               |                              |     |  ✓| 
| Iraque                        | ✓               |                              |     |  ✓| 
| Israel                      | ✓               |                              |   ✓   |  ✓| 
| Jordânia                      | ✓               |                              |     |  ✓| 
| Quénia                       | ✓               |                              |     |  ✓| 
| Koweit                      | ✓               |                              |     |  ✓| 
| Líbano                     | ✓               |                              |     |  ✓| 
| Lesoto                     | ✓               |                              |     |  ✓| 
| Libéria                     | ✓               |                              |     |  ✓| 
| Líbia                       | ✓               |                              |     |  ✓| 
| Madagáscar                  | ✓               |                              |     |  ✓| 
| Maláui                      | ✓               |                              |     |  ✓| 
| Mali                        | ✓               |                              |     |  ✓| 
| Mauritânia                  | ✓               |                              |     |  ✓| 
| Maurícias                   | ✓               |                              |     |  ✓| 
| Maiote                     | ✓               |                              |     |  ✓| 
| Marrocos                     | ✓               |                              |     |  ✓| 
| Moçambique                  | ✓               |                              |     |  ✓| 
| Namíbia                     | ✓               |                              |     |  ✓| 
| Níger                       | ✓               |                              |     |  ✓| 
| Nigéria                     | ✓               |                              |     |  ✓| 
| Omã                        | ✓               |                              |     |  ✓| 
| Autoridade Palestiniana       | ✓               |                              |     |  ✓| 
| Catar                       | ✓               |                              |     |  ✓| 
| Reunião                     | ✓               |                              |     |  ✓| 
| Ruanda                      | ✓               |                              |     |  ✓| 
| Santa Helena, Ascensão, Tristão da Cunha        | ✓               |            |     |  ✓| 
| São Tomé e Príncipe       | ✓               |                              |     |  ✓| 
| Arábia Saudita                | ✓               |                              |     |  ✓| 
| Senegal                     | ✓               |                              |     |  ✓| 
| Seicheles                  | ✓               |                              |     |  ✓| 
| Serra Leoa                | ✓               |                              |     |  ✓| 
| Somália                     | ✓               |                              |     |  ✓| 
| África do Sul                | ✓               |                              |     |  ✓| 
| Sudão do Sul                 | ✓               |                              |     |  ✓| 
| Sudão                       | ✓               |                              |     |  ✓| 
| Síria                       | ✓               |                              |     |  ✓| 
| Tanzânia                    | ✓               |                              |     |  ✓| 
| Togo                        | ✓               |                              |     |  ✓| 
| Tunísia                     | ✓               |                              |     |  ✓| 
| Uganda                      | ✓               |                              |     |  ✓| 
| Emirados Árabes Unidos        | ✓               |                              |     |  ✓| 
| Iémen                       | ✓               |                              |     |  ✓| 
| Zâmbia                      | ✓               |                              |     |  ✓| 
| Zimbabué                    | ✓               |                              |     |  ✓| 


## <a name="asia-pacific"></a>Ásia-Pacífico

| Pais/região              |  Azulejos de satélite | Previsão de Minutos, Azulejos de Radar | Alertas meteorológicos severos | Outros* |
|-----------------------------|:----------------:|:-----------------:|:--------:|  :--------:|
| Afeganistão                       | ✓ |   | | ✓| 
| Samoa Americana                    | ✓ |   |  ✓| ✓| 
| Austrália                         | ✓ | ✓ |  ✓ | ✓| 
| Bangladesh                        | ✓ |   | | ✓| 
| Butão                            | ✓ |   | | ✓| 
| Território Britânico do Oceano Índico    | ✓ |   | | ✓| 
| Brunei                            | ✓ |   | | ✓| 
| Camboja                          | ✓ |   | | ✓| 
| China                             | ✓ | ✓ |  ✓ | ✓| 
| Ilha do Natal                  | ✓ |   | | ✓| 
| Ilhas dos Cocos (Keeling)           | ✓ |   | | ✓| 
| Ilhas Cook                      | ✓ |   | | ✓| 
| Fiji                              | ✓ |   | | ✓| 
| Polinésia Francesa                  | ✓ |   | | ✓| 
| Guame                              | ✓ |   |  ✓| ✓| 
| Ilhas Heard e McDonald | ✓ |   | | ✓| 
| R.A.E. de Hong Kong                     | ✓ |   | | ✓| 
| Índia                             | ✓ |   | | ✓| 
| Indonésia                         | ✓ |   | | ✓| 
| Japão                             | ✓ | ✓ |  ✓| ✓| 
| Cazaquistão                        | ✓ |   | | ✓| 
| Quiribáti                          | ✓ |   | | ✓| 
| Coreia                             | ✓ | ✓ | ✓ |  ✓| 
| Quirguistão                        | ✓ |   | | ✓| 
| Laos                              | ✓ |   | | ✓| 
| RAE de Macau                         | ✓ |   | | ✓| 
| Malásia                          | ✓ |   | | ✓| 
| Maldivas                          | ✓ |   | | ✓| 
| Ilhas Marshall                  | ✓ |   | ✓ | ✓| 
| Micronésia                        | ✓ |   | ✓ | ✓| 
| Mongólia                          | ✓ |   | | ✓| 
| Mianmar                           | ✓ |   | | ✓| 
| Nauru                             | ✓ |   | | ✓| 
| Nepal                             | ✓ |   | | ✓| 
| Nova Caledónia                     | ✓ |   | | ✓| 
| Nova Zelândia                       | ✓ |   | ✓ | ✓| 
| Niuê                              | ✓ |   | | ✓| 
| Ilha Norfolk                    | ✓ |   | | ✓| 
| Coreia do Norte                       | ✓ |   | | ✓| 
| Ilhas Marianas do Norte          | ✓ |   | ✓ | ✓| 
| Paquistão                          | ✓ |   | | ✓| 
| Palau                             | ✓ |   | ✓ | ✓| 
| Papua-Nova Guiné                  | ✓ |   | | ✓| 
| Filipinas                       | ✓ |   | ✓ | ✓| 
| Ilhas Pitcairn                  | ✓ |   | | ✓| 
| Samoa                             | ✓ |   | | ✓| 
| Singapura                         | ✓ |   | | ✓| 
| Ilhas Salomão                   | ✓ |   | | ✓| 
| Sri Lanca                         | ✓ |   | | ✓| 
| Taiwan                            | ✓ |   | | ✓| 
| Tajiquistão                        | ✓ |   | | ✓| 
| Tailândia                          | ✓ |   | | ✓| 
| Timor-Leste                       | ✓ |   | | ✓| 
| Toquelau                           | ✓ |   | | ✓| 
| Tonga                             | ✓ |   | | ✓| 
| Turquemenistão                      | ✓ |   | | ✓| 
| Tuvalu                            | ✓ |   | | ✓| 
| Usbequistão                        | ✓ |   | | ✓| 
| Vanuatu                           | ✓ |   | | ✓| 
| Vietname                           | ✓ |   | | ✓| 
| Wallis e Futuna                 | ✓ |   | | ✓| 


## <a name="europe"></a>Europa

| Pais/região              |  Azulejos de satélite | Previsão de Minutos, Azulejos de Radar | Alertas meteorológicos severos | Outros* | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:|
| Albânia                | ✓ |   | | ✓| 
| Andorra                | ✓ |   | ✓ | ✓| 
| Arménia                | ✓ |   | | ✓| 
| Áustria                | ✓ | ✓ | ✓ | ✓|
| Azerbaijão             | ✓ |   | | ✓| 
| Bielorrússia                | ✓ |   | | ✓| 
| Bélgica                | ✓ | ✓ |  ✓| ✓| 
| Bósnia e Herzegovina | ✓ | ✓ | ✓ | ✓| 
| Bulgária               | ✓ |   |  ✓| ✓| 
| Croácia                | ✓ | ✓ |  ✓| ✓| 
| Chipre                 | ✓ |   | ✓ | ✓| 
| Checa                | ✓ | ✓ | ✓ | ✓| 
| Dinamarca                | ✓ | ✓ | ✓ | ✓| 
| Estónia                | ✓ | ✓ |  ✓| ✓| 
| Ilhas Faroé          | ✓ |   | | ✓| 
| Finlândia                | ✓ | ✓ | ✓ | ✓| 
| França                 | ✓ | ✓ | ✓ | ✓| 
| Geórgia                | ✓ |   | | ✓| 
| Alemanha                | ✓ | ✓ | ✓ | ✓| 
| Gibraltar              | ✓ | ✓ | | ✓| 
| Grécia                 | ✓ |   |  ✓| ✓| 
| Guernesey               | ✓ |   | | ✓| 
| Hungria                | ✓ | ✓ |  ✓| ✓| 
| Islândia                | ✓ |   | ✓ | ✓| 
| Irlanda                | ✓ | ✓ |  ✓| ✓| 
| Itália                  | ✓ |   |  ✓| ✓|
| Ilha de Man            | ✓ |   | | ✓| 
| Jan Mayen              | ✓ |   | | ✓| 
| Jersey                 | ✓ |   | | ✓| 
| Kosovo                 | ✓ |   |  ✓| ✓| 
| Letónia                 | ✓ |   | ✓ | ✓| 
| Listenstaine          | ✓ | ✓ |  ✓| ✓| 
| Lituânia              | ✓ |   | ✓ | ✓| 
| Luxemburgo             | ✓ | ✓ |  ✓| ✓| 
| Macedónia do Norte        | ✓ |   | ✓ | ✓| 
| Malta                  | ✓ |   | ✓ | ✓| 
| Moldávia                | ✓ | ✓ | ✓ | ✓| 
| Mónaco                 | ✓ | ✓ |  ✓| ✓| 
| Montenegro             | ✓ | ✓ |  ✓| ✓| 
| Países Baixos            | ✓ | ✓ |  ✓| ✓| 
| Noruega                 | ✓ | ✓ |  ✓| ✓| 
| Polónia                 | ✓ | ✓ |  ✓| ✓| 
| Portugal               | ✓ | ✓ |  ✓| ✓| 
| Roménia                | ✓ | ✓ |  ✓| ✓| 
| Rússia                 | ✓ |   |  ✓| ✓| 
| São Marino             | ✓ |   |  ✓| ✓| 
| Sérvia                 | ✓ | ✓ |  ✓| ✓| 
| Eslováquia               | ✓ | ✓ |  ✓| ✓| 
| Eslovénia               | ✓ | ✓ |  ✓| ✓| 
| Espanha                  | ✓ | ✓ |  ✓| ✓| 
| Svalbard               | ✓ |   | | ✓|
| Suécia                 | ✓ | ✓ |  ✓| ✓| 
| Suíça            | ✓ | ✓ | ✓| ✓| 
| Turquia                 | ✓ |   | | ✓| 
| Ucrânia                | ✓ |   | | ✓| 
| Reino Unido         | ✓ | ✓ | ✓| ✓| 
| Cidade do Vaticano           | ✓ |   |✓ | ✓|