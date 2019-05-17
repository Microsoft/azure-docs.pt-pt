---
title: Cobertura de geocodificação do Azure Maps | Documentos da Microsoft
description: Saiba mais sobre a cobertura de Geocodificação do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a5e5f4ab286289e223a2fe10ff8cf45f43309f04
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785947"
---
# <a name="azure-maps-geocoding-coverage"></a>Cobertura de geocodificação do Azure Maps

Quando procurar uma localização com o Azure Maps, o serviço de pesquisa demora seus termos de pesquisa e devolve as coordenadas de latitude e longitude, um processo chamado geocodificação. No entanto, o Maps não tem o mesmo nível de informações e a precisão para todas as regiões e países. Utilize este artigo para determinar que tipo de localizações pode fiável Procurar em cada região. 

A capacidade de geocode num país/região é dependente da cobertura de dados de estrada e a precisão da geocodificação do serviço de geocodificação. São utilizadas as seguintes categorizações especificar o nível de suporte de geocodificação em cada país/região.
* **Pontos de endereços** -dados de endereços podem ser resolvidos para uma coordenadas de latitude/longitude dentro do parcel de endereço (limite de propriedade). Por vezes referido como "Rooftop' precisa. Este é o maior nível de precisão disponível para endereços. 
* **Hospedar números** -endereços são interpolados para uma coordenada de latitude/longitude na rua.
* **Nível de rua** -endereços são resolvidos para as coordenadas de latitude/longitude da rua que contém o endereço. O número de casa não pode ser processado.
* **Nível de cidade** -são suportados nomes de locais de cidade.

## <a name="americas"></a>Américas

| País/Região                                       | Pontos de endereço | Números de casa | Nível de rua | Nível de cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguila                                            |                 |                |              |      ✓     |          ✓         |
| Antártica                                          |                 |                |              |      ✓     |          ✓         |
| Antígua e Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Baamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolívia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Santo Eustáquio e Saba                   |                 |                |              |      ✓     |          ✓         |
| Brasil                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canadá                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Caimão                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colômbia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Domínica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Equador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Falkland                                    |                 |                |              |      ✓     |          ✓         |
| Guiana Francesa                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Granada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadalupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guiana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinica                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| México                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monserrate                                          |                 |                |              |      ✓     |          ✓         |
| Nicarágua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panamá                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguai                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Perú                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Porto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| São Bartolomeu                                    |                 |                |       ✓      |      ✓     |          ✓         |
| São Cristóvão e Neves                               |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Lúcia                                         |                 |                |              |      ✓     |          ✓         |
| São Martinho                                        |                 |                |       ✓      |      ✓     |          ✓         |
| São Pedro e Miquelão                           |                 |                |       ✓      |      ✓     |          ✓         |
| São Vicente e Granadinas                    |                 |                |              |      ✓     |          ✓         |
| São Martinho (Saint-Martin)                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Geórgia do Sul e Sandwich do Sul        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trindade e Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Menores Distantes dos Estados Unidos                |                 |                |              |      ✓     |          ✓         |
| Estados Unidos da América                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguai                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Virgens Britânicas                              |                 |                |              |      ✓     |          ✓         |
| As Ilhas Virgens                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Ásia-Pacífico

| País/Região                                      | Pontos de endereço |Números de casa | Nível de rua | Nível de cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa Americana                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Austrália                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladeche                                          |                 |                |              |      ✓     |          ✓         |
| Butão                                              |                 |                |              |      ✓     |          ✓         |
| Território Britânico do Oceano Índico                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Camboja                                            |                 |                |              |      ✓     |          ✓         |
| China                                               |                 |                |              |      ✓     |          ✓         |
| Ilha do Natal                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Ilhas dos Cocos (Keeling)                             |                 |                |              |      ✓     |          ✓         |
| Comores                                             |                 |                |              |      ✓     |          ✓         |
| Ilhas Cook                                        |                 |                |              |      ✓     |          ✓         |
| Fiji                                                |                  |                |              |      ✓     |          ✓        |
| Polinésia Francesa                                    |                 |                |              |      ✓     |          ✓         |
| Ilhas Heard e McDonald                   |                 |                |              |      ✓     |          ✓         |
| R.A.E. Hong Kong                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Índia                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japão                                               |                 |                |              |      ✓     |          ✓         |
| Quiribati                                            |                 |                |              |      ✓     |          ✓         |
| Coreia                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| R.A.E. de Macau                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malásia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronésia                                          |                 |                |              |      ✓     |          ✓         |
| Mongólia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| República Democrática Federal do Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Nova Caledónia                                       |                 |                |              |      ✓     |          ✓         |
| Nova Zelândia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Ilha Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Coreia do Norte                                         |                 |                |              |      ✓     |          ✓         |
| Ilhas Marianas do Norte                            |                 |                |       ✓      |      ✓     |          ✓         |
| Paquistão                                            |                 |                |              |      ✓     |          ✓         |
| Ilhas Palaus                                               |                 |                |              |      ✓     |          ✓         |
| Papua-Nova Guiné                                    |                 |                |              |      ✓     |          ✓         |
| Ilhas Paracel                                     |                 |                |              |      ✓     |                    |
| Filipinas                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Estado Independente de Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Ilhas Senkaku/Diaoyutai                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapura                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Salomão                                     |                 |                |              |      ✓     |          ✓         |
| Territórios Austrais Kurils                                     |        ✓        |                |              |      ✓     |          ✓         |
| Ilhas spratly                                     |                 |                |              |      ✓     |                    |
| Sri Lanca                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tailândia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Ilhas Turcas e Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietname                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis e Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| País/Região                                      | Pontos de endereço |Números de casa | Nível de rua | Nível de cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albânia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Arménia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Áustria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaijão                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Bélgica                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bósnia e Herzegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgária                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bielorrússia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croácia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Chipre                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| República Checa                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dinamarca                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estónia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Faroé                                       |                 |                |              |      ✓     |          ✓         |
| Finlândia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| França                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Geórgia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Alemanha                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grécia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gronelândia                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hungria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islândia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlanda                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilha de Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Itália                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Cazaquistão                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Quirguizistão                                          |                 |                |              |      ✓     |          ✓         |
| Letónia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Listenstaine                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituânia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburgo                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedónia do Norte                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldávia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mónaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Países Baixos                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Noruega                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polónia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| + Açores e Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Roménia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federação Russa                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| São Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sérvia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslováquia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslovénia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Espanha                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Suécia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Suíça                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tajiquistão                                          |                 |                |              |      ✓     |          ✓         |
| Turquia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turquemenistão                                        |                 |                |              |      ✓     |          ✓         |
| Ucrânia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Reino Unido                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbequistão                                          |                 |                |              |      ✓     |          ✓         |
| Cidade do Vaticano                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Médio Oriente e África

| País/Região                                      | Pontos de endereço |Números de casa | Nível de rua | Nível de cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afeganistão                                         |                 |                |              |      ✓     |          ✓         |
| Argélia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Barém                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benim                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botsuana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Ilha Bouvet                                       |                 |                |              |      ✓     |          ✓         |
| Burquina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Camarões                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| República Centro Africana                            |                 |                |       ✓      |      ✓     |          ✓         |
| Chade                                                |                 |                |       ✓      |      ✓     |          ✓         |
| República do Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire (Costa do Marfim)                                       |                 |                |       ✓      |      ✓     |          ✓         |
| República Democrática do Congo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Jibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egito                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guiné Equatorial, República da                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritreia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiópia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Territórios Austrais Franceses|                        |                |              |      ✓     |          ✓         |
| Gabão                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gâmbia                                              |                 |                |              |      ✓     |          ✓         |
| Gana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guiné                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guiné Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Irão                                                |                 |                |              |      ✓     |          ✓         |
| Iraque                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israel                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordânia                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Quénia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Líbano                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesoto                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Líbia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagáscar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malavi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldivas                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Marshall                                    |                 |                |              |      ✓     |          ✓         |
| Mauritânia                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Maurícias                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marrocos                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Moçambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namíbia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Níger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigéria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Omã                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Catar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Reunião                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Helena                                        |                 |                |              |      ✓     |          ✓         |
| Arábia Saudita                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seicheles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Serra Leoa                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somália                                             |                 |                |              |      ✓     |          ✓         |
| África do Sul                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudão do Sul                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudão                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Suazilândia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Síria                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé e Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzânia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunísia                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Emirados Árabes Unidos                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Iémen                                               |                 |                |              |      ✓     |          ✓         |
| Zâmbia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabué                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a geocodificação de mapas do Azure, consulte a [pesquisa](https://docs.microsoft.com/rest/api/maps/search) páginas de referência.

Saiba mais sobre o [áreas de cobertura para os mapas de serviço de tráfego](traffic-coverage.md). 

