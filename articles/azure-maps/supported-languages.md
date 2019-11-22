---
title: Suporte à localização no Azure Maps | Microsoft Docs
description: Saiba mais sobre os idiomas com suporte para os serviços no Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 64197cc85822997926a8011af8cb3b981fa9064d
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286423"
---
# <a name="localization-support-in-azure-maps"></a>Suporte à localização no Azure Maps

O Azure Maps dá suporte a várias linguagens e exibições baseadas em país/região. Este artigo fornece os idiomas e as exibições com suporte para ajudar a orientar sua implementação do Azure Maps.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte do Azure Maps

Os mapas do Azure foram localizados em uma variedade de linguagens entre os serviços. A tabela a seguir fornece os códigos de idioma com suporte para cada serviço.  
  

| ID         | Nome                   |  Maps | Pesquisa | Encaminhamento | Meteorologia | Incidentes de tráfego | Controle de mapa de JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar SA      | Árabe                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bilhão-BD      | Bengali (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bilhão-IN      | Bengali (Índia)         |       |       |         |     ✓    |                   |                |
| BS-BA      | Bósnio                 |       |       |         |     ✓    |                   |                |
| UE-ES      | Basco (Basco)                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ES de AC      | Catalão                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chinês (Simplificado)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chinês (RAE de Hong Kong)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chinês (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| RH de RH      | Croata               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Checo                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| NL-ser      | Holandês (Bélgica)        |       |    ✓   |         |      ✓    |                   |                |
| NL-NL      | Holandês (Países Baixos)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estónio               |       |    ✓   |         |      ✓    |         ✓         |                |
| Fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| FR-FR      | Francês                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francês (Canadá)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galego               |       |    ✓   |         |         |                   |                |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el GR      | Grego                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| Gu-IN      | Guzarate                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebraico                 |       |    ✓   |         |     ✓    |         ✓         |                |
| IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| IS-IS      | Islandês              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonês               |       |        |         |     ✓    |                   |                |
| kN-IN      | Canarim                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Cazaque                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Espanhol da América Latina |       |    ✓   |         |         |                   |                |
| lv-LV      | Letão                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK-MK      | Macedônio             |       |       |         |     ✓    |                   |                |
| MS-MY      | Malaio (latino)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr-IN      | Marata                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norueguês, Bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Linguagem de aterramento neutra-idiomas oficiais para todas as regiões em scripts locais, se disponíveis |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | A terra neutra de verdade – latim exonyms. O script Latin será usado se estiver disponível |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polaco                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | Punjabi                 |       |       |         |     ✓    |                   |                |
| RO-RO      | Romeno               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Sérvio (Cirílico)     |       |   Sr-RS  |         |    Sr-RS     |                   |                |
| Sr-Latn-RS | Sérvio (Latim)        |       |       |         |     Sr-Latn    |                   |                |
| sk SK      | Eslovaco             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Esloveno              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Espanhol                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Espanhol (México)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| SV-SE      | Sueco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-IN      | Tamil (Índia)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (Índia)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Tailandês                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ucraniano               |       |    ✓   |         |     ✓    |                   |                |
| sua CP      | Urdu                 |       |       |         |     ✓    |                   |                |
| Uz-Latn-UZ | Uzbeque                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamita             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Exibições com suporte do Azure Maps

> [!Note]
> O Azure Maps foi lançado nos seguintes países/regiões em 1º de agosto de 2019:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> Após 1º de agosto de 2019, a configuração de parâmetros de **exibição** definirá o conteúdo do mapa retornado para as novas regiões/países listados acima. Incentivamos você a garantir que tenha configurado o parâmetro de exibição conforme necessário para as APIs REST e SDKs que seus serviços estão usando.
>  
>
>  **APIs REST:**
>  
>  Verifique se você configurou o parâmetro de exibição conforme necessário. O parâmetro View especifica qual conjunto de conteúdo de litígio geopolíticos é retornado por meio dos serviços do Azure Maps. 
>
>  Serviços REST do Azure Maps afetados:
>    
>    * Obter bloco de mapa
>    * Obter imagem do mapa 
>    * Obter difusa de pesquisa
>    * Obter POI de pesquisa
>    * Obter categoria de POI de pesquisa
>    * Obter pesquisa próxima
>    * Obter endereço de pesquisa
>    * Obter o endereço de pesquisa estruturado
>    * Obter reversão de endereço de pesquisa
>    * Obter endereço de cruz inverso da pesquisa
>    * Postar pesquisa dentro da geometria
>    * Versão prévia do lote de endereços de pesquisa
>    * Pós pesquisa endereço versão prévia do lote reverso
>    * Postar pesquisa ao longo do roteiro
>    * Visualização do lote difusa da pesquisa posterior
>
>    
>  **SDK**
>
>  Verifique se você configurou o parâmetro de exibição conforme necessário e se tem a versão mais recente do SDK Web e SDK do Android. SDKs afetados:
>
>    * SDK para Web do Azure Maps
>    * SDK do Android do Azure Maps


O parâmetro de **exibição** do Azure Maps (também conhecido como "parâmetro de região do usuário") é um código de país ISO-3166 de duas letras que mostrará os mapas corretos para esse país/região especificando qual conjunto de conteúdo geoestruturado geoestruturado será retornado por meio dos serviços do Azure Maps, incluindo bordas e rótulos exibidos no mapa. 

Por padrão, o parâmetro de exibição é definido como **unificado**, mesmo que você não o tenha definido na solicitação. É sua responsabilidade determinar o local dos usuários e, em seguida, definir o parâmetro de exibição corretamente para esse local. Como alternativa, você tem a opção de definir ' View = auto ', que retornará os dados do mapa com base no endereço IP da solicitação.  O parâmetro View no Azure Maps deve ser usado em conformidade com as leis aplicáveis, incluindo aquelas em relação ao mapeamento do país em que mapas, imagens e outros dados e conteúdo de terceiros que você está autorizado a acessar por meio do mapas do Azure são disponibilizados.


A tabela a seguir fornece exibições com suporte.

| Vista         | Descrição                            |  Maps | Pesquisa | JS Controle de Mapeamento |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (modo de exibição árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (exibição de Argentinian)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (exibição em árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (exibição indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (exibição em árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (exibição em árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (exibição em árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (exibição em árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (exibição de Tamazight)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (exibição em árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (exibição Rúpia)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestina (exibição em árabe)    |   ✓   |        |     ✓          |
| QA           | Catar (exibição em árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (exibição em árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (exibição em árabe)                   |   ✓   |        |     ✓          |
| SI           | Iêmen (exibição em árabe)                   |   ✓   |        |     ✓          |
| Automático         | Retornar os dados do mapa com base no endereço IP da solicitação.|   ✓   |    ✓   |     ✓          |
| Unificado      | Exibição unificada (outras)                  |   ✓   |   ✓     |     ✓          |
