---
title: Preços para ofertas de máquinas virtuais - Azure Marketplace
description: Explica os três métodos para especificar o preço das ofertas de máquinas virtuais.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: fe8024c60e204a4ec72002c878f7753ae35aa00d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393824"
---
# <a name="pricing-for-virtual-machine-offers"></a>Preços das ofertas de máquina virtual

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser utilizadas para produtos já integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

Existem três formas de especificar os preços das ofertas de máquinas virtuais: preços de base personalizados, preços por núcleo e preços da folha de cálculo.

## <a name="customized-core-pricing"></a>Preços de núcleo personalizados

Os preços são específicos para cada região e combinação de núcleos. Todas as regiões da lista de venda devem ser especificadas na secção **virtualMachinePricingPrices** /  da definição.  Utilize os códigos de moeda corretos para cada [região](#regions) no seu pedido.  O exemplo a seguir demonstra estes requisitos:

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

## <a name="per-core-pricing"></a>Preços por núcleo

Neste caso, os editores especificam um preço em USD para o seu SKU e todos os outros preços são gerados automaticamente. O preço por núcleo é especificado no parâmetro **único** no pedido.

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

## <a name="spreadsheet-pricing"></a>Preços das folhas de cálculo

O editor também pode carregar a sua folha de cálculo de preços para um local de armazenamento temporário, e depois incluir o URI no pedido como outros artefactos de arquivo. A folha de cálculo é então carregada, traduzida para avaliar o calendário de preços especificado, e finalmente atualiza a oferta com a informação de preços. Os pedidos de GET subsequentes para a oferta devolverão a folha de cálculo URI e os preços avaliados para a região.

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

## <a name="new-core-sizes-added-on-722019"></a>Novos tamanhos do núcleo adicionados em 7/2/2019

Os editores da VM foram notificados no dia 2 de julho de 2019 da adição de novos preços para novos tamanhos de máquinas virtuais Azure (com base no número de núcleos).  Os novos preços são para os tamanhos 10, 44, 48, 60, 120, 208 e 416.  Para as ofertas de VM existentes, os novos preços para estes tamanhos de núcleos foram automaticamente calculados com base nos preços correntes. As editoras têm até 1 de agosto de 2019 para rever os preços adicionais e fazer quaisquer alterações desejadas.  Após esta data, se ainda não for republica pela editora, os preços calculados automaticamente para estes novos tamanhos core entrarão em vigor.

## <a name="regions"></a>Regiões

A tabela a seguir mostra as diferentes regiões que pode especificar para preços de base personalizados e os respetivos códigos de moeda correspondentes.

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
| HR         | Croácia              | Rio HRK               |
| CY         | Chipre               | EUR               |
| CZ         | Checa       | CZK               |
| DK         | Dinamarca              | DKK               |
| DO         | República Dominicana   | USD               |
| EC         | Equador              | USD               |
| EG         | Egito                | EGP               |
| SV         | Salvador          | USD               |
| EE         | Estónia              | EUR               |
| FI         | Finlândia              | EUR               |
| FR         | França               | EUR               |
| DE         | Alemanha              | EUR               |
| GR         | Grécia               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | R.A.E. de Hong Kong        | HKD               |
| HU         | Hungria              | HUF               |
| IS         | Islândia              | ISK               |
| IN         | Índia                | INR               |
| ID         | Indonésia            | IDR               |
| IE         | Irlanda              | EUR               |
| IL         | Israel               | ILS               |
| TI         | Itália                | EUR               |
| JP         | Japão                | JPY               |
| JO         | Jordânia               | JOD               |
| KZ         | Cazaquistão           | Rio KZT               |
| KE         | Quénia                | RIO KES               |
| KR         | Coreia                | KRW               |
| KW         | Koweit               | KWD               |
| LV         | Letónia               | EUR               |
| LI         | Listenstaine        | CHF               |
| LT         | Lituânia            | EUR               |
| LU         | Luxemburgo           | EUR               |
| MY         | Malásia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | México               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marrocos              | LOUCO               |
| NL         | Países Baixos          | EUR               |
| NZ         | Nova Zelândia          | NZD               |
| NG         | Nigéria              | NGN               |
| MK         | Macedónia do Norte      | MKD               |
| NO         | Noruega               | NOK               |
| OM         | Omã                 | OMR               |
| PK         | Paquistão             | PKR               |
| PA         | Panamá               | USD               |
| PY         | Paraguai             | PYG               |
| PE         | Peru                 | PEN               |
| PH         | Filipinas          | PHP               |
| PL         | Polónia               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Porto Rico          | USD               |
| QA         | Catar                | RIO QAR               |
| RO         | Roménia              | RIO RON               |
| RU         | Rússia               | RUB               |
| SA         | Arábia Saudita         | SAR               |
| RS         | Sérvia               | Rio RSD               |
| SG         | Singapura            | SGD               |
| SK         | Eslováquia             | EUR               |
| SI         | Eslovénia             | EUR               |
| ZA         | África do Sul         | ZAR               |
| ES         | Espanha                | EUR               |
| LK         | Sri Lanca            | USD               |
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
| EUA         | Estados Unidos da América        | USD               |
| UY         | Uruguai              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
