---
title: Preços para ofertas de máquinas virtuais / Mercado Azure
description: Explica os três métodos de especificação dos preços das ofertas de máquinas virtuais.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 487e66b39bc63363497cb3497d32158efd0c6c8a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255894"
---
<a name="pricing-for-virtual-machine-offers"></a>Preços das ofertas de máquina virtual
==================================

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Existem três formas de especificar os preços das ofertas de máquinas virtuais: preços de base personalizados, preços por núcleo e preços de folha de cálculo.


<a name="customized-core-pricing"></a>Preços de núcleo personalizados
-----------------------

Os preços são específicos para cada região e combinação central. Todas as regiões da lista de venda devem ser especificadas na**região** **virtualDePreçosPreços**/da definição.  Utilize os códigos de moeda corretos para cada [região](#regions) a seu pedido.  O exemplo que se segue demonstra estes requisitos:

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

Neste caso, as editoras especificam um preço em USD para o seu SKU e todos os outros preços são automaticamente gerados. O preço por núcleo é especificado no **parâmetro único** do pedido.

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


<a name="spreadsheet-pricing"></a>Preços da folha de cálculo
-------------------

A editora também pode carregar a sua folha de cálculo de preços para um local de armazenamento temporário, em seguida, incluir o URI no pedido como outros artefactos de arquivo. A folha de cálculo é então carregada, traduzida para avaliar o calendário de preços especificado, e finalmente atualiza a oferta com a informação sobre preços. Os pedidos subsequentes do GET para a oferta devolverão a folha de cálculo URI e os preços avaliados para a região.

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

As editoras vM foram notificadas em 2 de julho de 2019 da adição de novos preços para novos tamanhos de máquinas virtuais Azure (com base no número de núcleos).  Os novos preços são para os tamanhos 10, 44, 48, 60, 120, 208 e 416.  Para as ofertas vm existentes, os novos preços para estes tamanhos de núcleoforam automaticamente calculados com base nos preços correntes.  As editoras têm até 1 de agosto de 2019 para rever os preços adicionais e fazer quaisquer alterações desejadas.  Após esta data, se ainda não for republicado pela editora, os preços calculados automaticamente para estes novos tamanhos de núcleo entrarão em vigor.


<a name="regions"></a>Regiões
-------

O quadro seguinte mostra as diferentes regiões que pode especificar para preços de base personalizados e os seus códigos de moeda correspondentes.

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
| IS         | Islândia              | ISK               |
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
| MK         | Macedónia do Norte      | Rio MKD               |
| MY         | Malásia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | México               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marrocos              | LOUCO               |
| NL         | Países Baixos          | EUR               |
| NZ         | Nova Zelândia          | NZD               |
| NG         | Nigéria              | NGN               |
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
| UY         | Uruguai              | Uyu               |
| VE         | Venezuela            | USD               |
|  |  |  |
