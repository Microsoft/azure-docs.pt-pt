---
title: Geofiltração em um domínio para serviço de porta frontal Azure
description: Neste artigo, vai conhecer a política de filtragem geográfica para o Azure Front Door Service
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 7c49892f97d9c15efcaecccb6133c67133e81c87
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137565"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>O que é geofiltração num domínio para a Porta frontal do Azure?

Por predefinição, o Azure Front Door Service responde aos pedidos de utilizador independentemente da localização do utilizador que efetua o pedido. No entanto, em alguns casos, poderá querer restringir o acesso às suas aplicações web por país/região. O serviço de firewall de aplicações web (WAF) na Porta frontal permite-lhe definir uma política utilizando regras de acesso personalizadas para um caminho específico no seu ponto final para permitir ou bloquear o acesso de países/regiões especificados. 

Uma política waf geralmente inclui um conjunto de regras personalizadas. Uma regra é constituída por condições de correspondência, uma ação e uma prioridade. Numa condição de correspondência, vai definir uma variável de correspondência, um operador e um valor de correspondência.  Para a regra de filtragem geográfica, a variável é REMOTE_ADDR, o operador é GeoMatch e o valor é o código de país de duas letras de interesse. Pode combinar uma condição GeoMatch e uma condição de correspondência de cadeia de carateres REQUEST_URI para criar uma regra de filtragem geográfica baseada no caminho.

Pode configurar uma política de geofiltração para a sua Porta Frontal utilizando o portal Azure, [o Azure PowerShell](waf-front-door-tutorial-geo-filtering.md) ou o nosso [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="country-code-reference"></a>Referência do código do país

|Indicativo de país | Nome do país |
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
| BD | Bangladeche|
| BE | Bélgica|
| BF | Burkina Faso|
| BG | Bulgária|
| BH | Barém|
| BI | Burundi|
| BJ | Benim|
| BL | São Barthélemy|
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
| HK | RAE de Hong Kong|
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
| KI | Kiribati|
| KN | São Cristóvão e Neves|
| KP | Coreia, República Democrática Popular da|
| KR | Coreia, República da|
| KW | Kuwait|
| KY | Ilhas Caimão|
| KZ | Cazaquistão|
| LA | República Democrática Popular do Laos|
| LB | Líbano|
| LI | Liechtenstein|
| LK | Sri Lanka|
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
| MM | Myanmar|
| MN | Mongólia|
| MO | RAE de Macau|
| MQ | Martinica|
| MR | Mauritânia|
| MT | Malta|
| MV | Maldivas|
| MW | Malawi|
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
| SV | El Salvador|
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
| EUA | Estados Unidos|
| UY | Uruguai|
| UZ | Uzbequistão|
| VC | São Vicente e Granadinas|
| VE | Venezuela|
| VG | Ilhas Virgens Britânicas|
| VI | Ilhas Virgens, E.U.A.|
| VN | Vietname|
| ZA | África do Sul|
| ZM | Zâmbia|
| ZW | Zimbabué|

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [segurança de camada de aplicação com o Front Door](../../frontdoor/front-door-application-security.md).
- Saiba como [criar um Front Door](../../frontdoor/quickstart-create-front-door.md).
