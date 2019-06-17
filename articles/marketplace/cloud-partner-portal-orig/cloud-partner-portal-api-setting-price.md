---
title: Preços de ofertas de máquina virtual | O Azure Marketplace
description: Explica os três métodos de especificar os preços de ofertas de máquina virtual.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dde3e04dc8f30ea5657139d50dd4456e5dfb57c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935749"
---
<a name="pricing-for-virtual-machine-offers"></a>Preços das ofertas de máquina virtual
==================================

Existem três formas para especificar a preços de ofertas de máquina virtual: personalizado core preços, os preços por núcleo e preços de folha de cálculo.


<a name="customized-core-pricing"></a>Preços de principal personalizado
-----------------------

Os preços são específicos para cada combinação de Server core e região. Cada região na lista de venda deve ser especificado no **virtualMachinePricing**/**regionPrices** secção da definição.  Utilize os códigos de moeda correta para cada [região](#regions) no seu pedido.  O exemplo seguinte demonstra estes requisitos:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Preço por núcleo
----------------

Neste caso, os publicadores especificam um preço em dólares americanos para o SKU e todos os outros preços gerados automaticamente. O preço por núcleo é especificado na **único** parâmetro no pedido.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Folha de cálculo de preços
-------------------

O publicador pode também carregar sua folha de cálculo do preço para uma localização de armazenamento temporário, em seguida, inclua o URI no pedido, como outros artefatos de ficheiro. A folha de cálculo, em seguida, é carregada, traduzido para avaliar a agenda de preço especificado e, finalmente, atualiza a oferta com as informações de preços. As solicitações subseqüentes de GET para a oferta irão devolver a URI de folha de cálculo e os preços avaliados para a região.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>Regiões
-------

A tabela seguinte mostra as diferentes regiões que pode especificar para preços core personalizada e seus códigos de moeda correspondente.

| **Região** | **Nome**             | **Código de moeda** |
|------------|----------------------|-------------------|
| DZ         | Argélia              | DZD               |
| AR         | Argentina            | ARS               |
| AU         | Austrália            | AUD               |
| AT         | Áustria              | EUR               |
| BH         | Barém              | BHD               |
| BY         | Bielorrússia              | RUB               |
| BE         | Bélgica              | EUR               |
| BR         | Brasil               | USD               |
| BG         | Bulgária             | BGN               |
| CA         | Canadá               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Colômbia             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Croácia              | HRK               |
| CY         | Chipre               | EUR               |
| CZ         | República Checa       | CZK               |
| DK         | Dinamarca              | DKK               |
| DO         | República Dominicana   | USD               |
| EC         | Equador              | USD               |
| EG         | Egito                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estónia              | EUR               |
| FI         | Finlândia              | EUR               |
| FR         | França               | EUR               |
| DE         | Alemanha              | EUR               |
| GR         | Grécia               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | RAE de Hong Kong        | HKD               |
| HU         | Hungria              | HUF               |
| IS         | Islândia              | ISCO               |
| IN         | Índia                | INR               |
| ID         | Indonésia            | IDR               |
| IE         | Irlanda              | EUR               |
| IL         | Israel               | ILS               |
| TI         | Itália                | EUR               |
| JP         | Japão                | JPY               |
| JO         | Jordânia               | JOD               |
| KZ         | Cazaquistão           | KZT               |
| KE         | Quénia                | KES               |
| KR         | Coreia                | KRW               |
| KW         | Kuwait               | KWD               |
| LV         | Letónia               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Lituânia            | EUR               |
| LU         | Luxemburgo           | EUR               |
| MK         | Macedónia norte      | MKD               |
| MY         | Malásia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | México               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marrocos              | MAD               |
| NL         | Países Baixos          | EUR               |
| NZ         | Nova Zelândia          | NZD               |
| NG         | Nigéria              | NGN               |
| NO         | Noruega               | NOK               |
| OM         | Omã                 | OMR               |
| PK         | Paquistão             | PKR               |
| PA         | Panamá               | USD               |
| PY         | Paraguai             | PYG               |
| PE         | Peru                 | CANETA               |
| PH         | Filipinas          | PHP               |
| PL         | Polónia               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Porto Rico          | USD               |
| QA         | Catar                | QAR               |
| RO         | Roménia              | RON               |
| RU         | Rússia               | RUB               |
| SA         | Arábia Saudita         | SAR               |
| RS         | Sérvia               | RSD               |
| SG         | Singapura            | SGD               |
| SK         | Eslováquia             | EUR               |
| SI         | Eslovénia             | EUR               |
| ZA         | África do Sul         | ZAR               |
| ES         | Espanha                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Suécia               | SEK               |
| CH         | Suíça          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Tailândia             | THB               |
| TT         | Trindade e Tobago  | TTD               |
| TN         | Tunísia              | TND               |
| TR         | Turquia               | TRY               |
| UA         | Ucrânia              | UAH               |
| AE         | Emirados Árabes Unidos | EUR               |
| GB         | Reino Unido       | GBP               |
| EUA         | Estados Unidos        | USD               |
| UY         | Uruguai              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
