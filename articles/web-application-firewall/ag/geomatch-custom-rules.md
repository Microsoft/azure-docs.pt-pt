---
title: Azure Web Application Firewall (WAF) Regras personalizadas de geomatch
description: Este artigo é uma visão geral das regras personalizadas de geomatch da Web Application Firewall (WAF) no Gateway de aplicações Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: bdb115cf6d0f0aa2c55f6143015a323df6faa114
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83744291"
---
# <a name="geomatch-custom-rules-preview"></a>Regras personalizadas geomatch (pré-visualização)

As regras personalizadas permitem criar regras personalizadas de acordo com as necessidades exatas das suas aplicações e políticas de segurança. Agora, pode restringir o acesso às suas aplicações web por país/região. Tal como acontece com todas as regras personalizadas, esta lógica pode ser agravada com outras regras de acordo com as necessidades da sua aplicação.

Para criar uma regra personalizada de geo-filtragem, basta selecionar *Geolocalização* como o Tipo de Correspondência e, em seguida, selecione o país/região ou países/regiões que deseja permitir/bloquear a partir da sua aplicação. Veja [como criar regras personalizadas em Powershell](configure-waf-custom-rules.md) e exemplos de regras mais personalizados (create-custom-waf-rules.md) para obter mais informações.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

## <a name="countryregion-codes"></a>Códigos país/região

Se estiver a utilizar o operador Geomatch, os selecionadores podem ser qualquer um dos seguintes códigos de dois dígitos país/região. 

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

Depois de aprender sobre regras personalizadas, [crie as suas próprias regras personalizadas.](create-custom-waf-rules.md)
