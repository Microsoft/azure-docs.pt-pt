---
title: Apoio à localização / Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre idiomas suportados para os serviços no Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0d3adc4bc49379a9ec3408ab76b913a096840dbb
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127898"
---
# <a name="localization-support-in-azure-maps"></a>Apoio à localização no Azure Maps

O Azure Maps suporta várias línguas e pontos de vista baseados em país/região. Este artigo fornece as línguas e pontos de vista suportados para ajudar a orientar a implementação do Azure Maps.


## <a name="azure-maps-supported-languages"></a>Azure Maps apoiou línguas

O Azure Maps tem sido localizado em línguas de variedades em todos os seus serviços. A tabela seguinte fornece os códigos linguísticos suportados para cada serviço.  
  

| ID         | Name                   |  Mapas | Pesquisar | Encaminhamento | Meteorologia | Incidentes de trânsito | Controlo de mapas JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Árabe                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (Índia)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bósnio                 |       |       |         |     ✓    |                   |                |
| EU-ES      | Basco                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Catalão                |       |    ✓   |         |    ✓      |                   |                |
| zh-hans    | Chinês (Simplificado)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chinês (HONG KONG SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chinês (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Croata               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Checo                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Neerlandês (Bélgica)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Neerlandês (Países Baixos)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| pt-PT      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estónio               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francês                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francês (Canadá)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galego               |       |    ✓   |         |         |                   |                |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Grego                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Guzerate                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebraico                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Islandês              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonês               |       |        |         |     ✓    |                   |                |
| kn-IN      | Canarês                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Cazaque                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Espanhol Latino-Americano |       |    ✓   |         |         |                   |                |
| lv-LV      | Letão                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Macedónio             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malaio (latim)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marata                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norueguês Bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutral Ground Truth - Línguas oficiais para todas as regiões em scripts locais, se disponíveis |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral Ground Truth - Exonymos latinos. O script latino será usado se disponível |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polaco                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-in      | Punjabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Romeno               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Sérvio (Cirílico)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Sérvio (Latim)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Eslovaco             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Esloveno              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Espanhol                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Espanhol (México)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Sueco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tamil (Índia)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (Índia)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Tailandês                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ucraniano               |       |    ✓   |         |     ✓    |                   |                |
| o seu PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| Uz-Latn-UZ | Usbeque                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamita             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps apoiou vistas

> [!Note]
> Em 1 de agosto de 2019, a Azure Maps foi lançada nos seguintes países/regiões:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> Depois de 1 de agosto de 2019, o parâmetro **View** definirá o conteúdo do mapa devolvido para as novas regiões/países listados acima. Azure Maps **Ver** o parâmetro (também designado por "parâmetro da região do utilizador") é uma letra ISO-3166 Country Code que mostrará os mapas corretos para esse país/região especificando que conjunto de conteúdo geopoliticamente disputado é devolvido através dos serviços Azure Maps, incluindo fronteiras e etiquetas exibidas no mapa. 

Certifique-se de que configura o parâmetro **'Ver',** conforme necessário para as APIs REST e os SDKs, que os seus serviços estão a utilizar.
>  
>
>  **APIs de repouso:**
>  
>  Certifique-se de que definiu o parâmetro 'Ver' conforme necessário. Ver parâmetros especifica que conjunto de conteúdo geopoliticamente disputado é devolvido através dos serviços Azure Maps. 
>
>  Serviços de DESCANSO Azure Maps Afetados:
>    
>    * Obter azulejo do mapa
>    * Obtenha imagem do mapa 
>    * Obter Pesquisa Fuzzy
>    * Obter pesquisa POI
>    * Obter Pesquisa POI Categoria
>    * Obtenha pesquisa nas proximidades
>    * Obter Endereço de Pesquisa
>    * Obter Endereço de Pesquisa Estruturado
>    * Obter Endereço de Pesquisa Ao Contrário
>    * Obter Endereço de pesquisa Reverse Cross Street
>    * Pesquisa de pós-geometria interna
>    * Pré-visualização do lote de endereço de pesquisa de post
>    * Pré-visualização do lote invertido do endereço de pesquisa de pós-pesquisa
>    * Pesquisa de post ao longo da rota
>    * Pré-visualização do lote fuzzy de pesquisa de pesquisa
>
>    
>  **SDKs:**
>
>  Certifique-se de que definiu o parâmetro **'Ver'** conforme necessário e que tem a versão mais recente do Web SDK e do Android SDK. SDKs afetados:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Por predefinição, o parâmetro 'Ver' é definido como **Unificado,** mesmo que não o tenha definido no pedido. Determine a localização dos seus utilizadores. Em seguida, desa um ponto de **vista** corretamente para o local. Em alternativa, pode definir 'View=Auto', que irá devolver os dados do mapa com base no endereço IP do pedido.  O parâmetro **Ver** no Azure Maps deve ser usado em conformidade com as leis aplicáveis, incluindo as leis sobre mapeamento do país/região onde mapas, imagens e outros dados e conteúdos de terceiros que está autorizado a aceder via Azure Maps são disponibilizados.


A tabela a seguir fornece pontos de vista apoiados.

| Vista         | Descrição                            |  Mapas | Pesquisar | JS Controlo de Mapas |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (Visão Árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (Visão Argentina)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (Vista Árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (Visão Indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (Visão Árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (Visão Árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (Visão Árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (Visão Árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (Vista Marroquina)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (Vista Árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (Visão Paquistanesa)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestiniana (Visão Árabe)    |   ✓   |        |     ✓          |
| QA           | Qatar (Visão Árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (Visão Árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (Visão Árabe)                   |   ✓   |        |     ✓          |
| YE           | Iémen (Visão Árabe)                   |   ✓   |        |     ✓          |
| Automático         | Devolva os dados do mapa com base no endereço IP do pedido.|   ✓   |    ✓   |     ✓          |
| Unificado      | Vista Unificada (Outros)                  |   ✓   |   ✓     |     ✓          |
