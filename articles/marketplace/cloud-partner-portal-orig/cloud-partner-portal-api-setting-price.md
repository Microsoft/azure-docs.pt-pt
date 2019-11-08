---
title: Preços para ofertas de máquina virtual | Azure Marketplace
description: Explica os três métodos de especificar o preço das ofertas de máquina virtual.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: c7ea5afeb46c30837c2ae53e871bb64f5d8cf292
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827325"
---
<a name="pricing-for-virtual-machine-offers"></a>Preços das ofertas de máquina virtual
==================================

Há três maneiras de especificar o preço para ofertas de máquina virtual: preço de núcleo personalizado, preço por núcleo e preços de planilha.


<a name="customized-core-pricing"></a>Preço de núcleo personalizado
-----------------------

Os preços são específicos para cada região e combinação de núcleo. Todas as regiões na lista de vendas devem ser especificadas na seção **virtualMachinePricing**/**regionPrices** da definição.  Use os códigos de moeda corretos para cada [região](#regions) em sua solicitação.  O exemplo a seguir demonstra esses requisitos:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Preços por núcleo
----------------

Nesse caso, os editores especificam um preço em USD para sua SKU e todos os outros preços são gerados automaticamente. O preço por núcleo é especificado no **único** parâmetro na solicitação.

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


<a name="spreadsheet-pricing"></a>Preços da planilha
-------------------

O Publicador também pode carregar sua planilha de preços em um local de armazenamento temporário e, em seguida, incluir o URI na solicitação, como outros artefatos de arquivo. Em seguida, a planilha é carregada, traduzida para avaliar a agenda de preços especificada e, por fim, atualiza a oferta com as informações de preços. As solicitações GET subsequentes para a oferta retornarão o URI da planilha e os preços avaliados para a região.

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

<a name="new-core-sizes-added-on-722019"></a>Novos tamanhos de núcleo adicionados em 7/2/2019
---------------------------

Os editores de VM foram notificados em 2 de julho de 2019 da adição de novos preços para novos tamanhos de máquina virtual do Azure (com base no número de núcleos).  Os novos preços são para os tamanhos de núcleos 10, 44, 48, 60, 120, 208 e 416.  Para a VM existente oferece novos preços para esses tamanhos de núcleos calculados automaticamente com base nos preços atuais.  Os editores têm até 1º de agosto de 2019 para examinar os preços adicionais e fazer as alterações desejadas.  Após essa data, se ainda não tiver sido publicado novamente pelo Publicador, os preços calculados automaticamente para esses novos tamanhos de núcleo entrarão em vigor.


<a name="regions"></a>Regiões
-------

A tabela a seguir mostra as diferentes regiões que você pode especificar para o preço de núcleo personalizado e seus códigos de moeda correspondentes.

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
| CO         | Colômbia             | &               |
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
| IL         | Israel               | IIS               |
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
| MK         | Macedónia do Norte      | MKD               |
| MY         | Malásia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | México               | MXN               |
| Vou         | Montenegro           | EUR               |
| MA         | Marrocos              | MAD               |
| NL         | Países Baixos          | EUR               |
| NZ         | Nova Zelândia          | NZD               |
| NG         | Nigéria              | NGN               |
| NO         | Noruega               | NOK               |
| OM         | Omã                 | OMR               |
| PK         | Paquistão             | PKR               |
| PA         | Panamá               | USD               |
| PY         | Paraguai             | PYG               |
| PE         | Peru                 | PENA               |
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
