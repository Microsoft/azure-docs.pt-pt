---
title: Geo-filtração em um domínio para Azure Front Door | Microsoft Docs
description: Neste artigo, você aprende sobre a política de geo-filtragem para Azure Front Door
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2020
ms.author: duau
ms.reviewer: tyao
ms.openlocfilehash: 33ad17e3024011368c909a89e9164ca06d044bad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612087"
---
# <a name="geo-filtering-on-a-domain-for-azure-front-door"></a>Geo-filtração em um domínio para Azure Front Door

Por predefinição, a Porta Frontal Azure responderá a todos os pedidos do utilizador, independentemente do local de onde o pedido está a vir. Em alguns cenários, poderá querer restringir o acesso à sua aplicação web por países/regiões. O serviço de firewall de aplicação Web (WAF) na Porta Frontal permite-lhe definir uma política usando regras de acesso personalizado para uma trajetória específica no seu ponto final para permitir ou bloquear o acesso de países/regiões especificados. 

Uma política da WAF contém um conjunto de regras personalizadas. A regra consiste em condições de jogo, uma ação e uma prioridade. Em condições de jogo, você define uma variável de correspondência, operador e valor de correspondência. Para uma regra de filtragem geo, uma variável de correspondência é REMOTE_ADDR, o operador é GeoMatch, e o valor é um código de interesse país/região de duas letras. O código de país "ZZ" ou o país "Desconhecido" captura endereços IP que ainda não estão mapeados para um país no nosso conjunto de dados. Pode adicionar ZZ à sua condição de jogo para evitar falsos positivos. Pode combinar uma condição GeoMatch e uma condição de correspondência de cordas REQUEST_URI para criar uma regra de geo-filtragem baseada no caminho. 


Pode configurar uma política de geo-filtragem para a sua porta frontal utilizando [o Azure PowerShell](front-door-tutorial-geo-filtering.md) ou utilizando um [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="countryregion-code-reference"></a>Referência de código país/região

|Código país/região | Nome país/região |
| ----- | ----- |
| AD | Andorra |
| AE | Emirados Árabes Unidos|
| AF | Afeganistão|
| AG | Antígua e Barbuda|
| AL | Albânia|
| AM | Arménia|
| AO | Angola|
| AR | Argentina|
| AS | Samoa Americana|
| AT | Áustria|
| AU | Austrália|
| AZ | Azerbaijão|
| BA | Bósnia e Herzegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Bélgica|
| BF | Burkina Faso|
| BG | Bulgária|
| BH | Barém|
| BI | Burundi|
| BJ | Benim|
| BL | São Bartolomeu|
| BN | Brunei Darussalam|
| BO | Bolívia|
| BR | Brasil|
| BS | Baamas|
| BT | Butão|
| BW | Botsuana|
| BY | Bielorrússia|
| BZ | Belize|
| CA | Canadá|
| CD | República Democrática do Congo|
| CF | República Centro-Africana|
| CH | Suíça|
| CI | Costa do Marfim|
| CL | Chile|
| CM | Camarões|
| CN | China|
| CO | Colômbia|
| CR | Costa Rica|
| CU | Cuba|
| CV | Cabo Verde|
| CY | Chipre|
| CZ | República Checa|
| DE | Alemanha|
| DK | Dinamarca|
| DO | República Dominicana|
| DZ | Argélia|
| EC | Equador|
| EE | Estónia|
| EG | Egito|
| ES | Espanha|
| ET | Etiópia|
| FI | Finlândia|
| FJ | Fiji|
| FM | Micronésia, Estados Federados da|
| FR | França|
| GB | Reino Unido|
| GE | Geórgia|
| GF | Guiana Francesa|
| GH | Gana|
| GN | Guiné|
| GP | Guadalupe|
| GR | Grécia|
| GT | Guatemala|
| GY | Guiana|
| HK | R.A.E. de Hong Kong|
| HN | Honduras|
| HR | Croácia|
| HT | Haiti|
| HU | Hungria|
| ID | Indonésia|
| IE | Irlanda|
| IL | Israel|
| IN | Índia|
| IQ | Iraque|
| IR | Irão, República Islâmica do|
| IS | Islândia|
| TI | Itália|
| JM | Jamaica|
| JO | Jordânia|
| JP | Japão|
| KE | Quénia|
| KG | Quirguistão|
| KH | Camboja|
| KI | Quiribáti|
| KN | São Cristóvão e Neves|
| KP | Coreia, República Democrática Popular da|
| KR | Coreia, República da|
| KW | Koweit|
| KY | Ilhas Caimão|
| KZ | Cazaquistão|
| LA | República Democrática Popular do Laos|
| LB | Líbano|
| LI | Listenstaine|
| LK | Sri Lanca|
| LR | Libéria|
| LS | Lesoto|
| LT | Lituânia|
| LU | Luxemburgo|
| LV | Letónia|
| LY | Líbia |
| MA | Marrocos|
| MD | Moldova, República da|
| MG | Madagáscar|
| MK | Macedónia do Norte|
| ML | Mali|
| MM | Mianmar|
| MN | Mongólia|
| MO | RAE de Macau|
| MQ | Martinica|
| MR | Mauritânia|
| MT | Malta|
| MV | Maldivas|
| MW | Maláui|
| MX | México|
| MY | Malásia|
| MZ | Moçambique|
| ND | Namíbia|
| NE | Níger|
| NG | Nigéria|
| NI | Nicarágua|
| NL | Países Baixos|
| NO | Noruega|
| NP | Nepal|
| NR | Nauru|
| NZ | Nova Zelândia|
| OM | Omã|
| PA | Panamá|
| PE | Peru|
| PH | Filipinas|
| PK | Paquistão|
| PL | Polónia|
| PR | Porto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguai|
| QA | Catar|
| RE | Reunião|
| RO | Roménia|
| RS | Sérvia|
| RU | Federação Russa|
| RW | Ruanda|
| SA | Arábia Saudita|
| SD | Sudão|
| SE | Suécia|
| SG | Singapura|
| SI | Eslovénia|
| SK | Eslováquia|
| SN | Senegal|
| SO | Somália|
| SR | Suriname|
| SS | Sudão do Sul|
| SV | Salvador|
| SY | República Árabe Síria|
| SZ | Suazilândia|
| TC | Ilhas Turcas e Caicos|
| TG | Togo|
| TH | Tailândia|
| TN | Tunísia|
| TR | Turquia|
| TT | Trindade e Tobago|
| TW | Taiwan|
| TZ | Tanzânia, República Unida da|
| UA | Ucrânia|
| UG | Uganda|
| EUA | Estados Unidos da América|
| UY | Uruguai|
| UZ | Usbequistão|
| VC | São Vicente e Granadinas|
| VE | Venezuela|
| VG | Ilhas Virgens Britânicas|
| VI | Ilhas Virgens, E.U.A.|
| VN | Vietname|
| ZA | África do Sul|
| ZM | Zâmbia|
| ZW | Zimbabué|

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba como [configurar uma política de WAF de geo-filtragem](front-door-tutorial-geo-filtering.md).
