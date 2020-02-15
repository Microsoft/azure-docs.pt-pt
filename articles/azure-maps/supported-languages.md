---
title: Apoio à localização  Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre idiomas suportados para os serviços no Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5de04b4e37e79a47f89bb235aad2b0f9cab69e6a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208153"
---
# <a name="localization-support-in-azure-maps"></a>Apoio à localização no Azure Maps

O Azure Maps apoia várias línguas e pontos de vista baseados em país/região. Este artigo fornece as línguas e pontos de vista suportados para ajudar a orientar a implementação do Seu Azure Maps.


## <a name="azure-maps-supported-languages"></a>Línguas apoiadas pelo Azure Maps

O Azure Maps foi localizado em línguas de variedades em todos os seus serviços. A tabela seguinte fornece os códigos linguísticos suportados para cada serviço.  
  

| ID         | Nome                   |  Maps | Pesquisa | Encaminhamento | Meteorologia | Incidentes de trânsito | Controlo de mapas JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar SA      | Árabe                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (Índia)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bósnio                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Basco (Basco)                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ES de AC      | Catalão                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chinês (Simplificado)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chinês (RAE de Hong Kong)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chinês (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| RH de RH      | Croata               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Checo                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Holandês (Bélgica)        |       |    ✓   |         |      ✓    |                   |                |
| NL-NL      | Holandês (Países Baixos)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| pt-PT      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estónio               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| FR-FR      | Francês                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francês (Canadá)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galego               |       |    ✓   |         |         |                   |                |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el GR      | Grego                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Guzarate                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebraico                 |       |    ✓   |         |     ✓    |         ✓         |                |
| IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Islandês              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonês               |       |        |         |     ✓    |                   |                |
| kn-IN      | Canarim                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Cazaque                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Espanhol latino-americano |       |    ✓   |         |         |                   |                |
| lv-LV      | Letão                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Macedónio             |       |       |         |     ✓    |                   |                |
| MS-MY      | Malaio (latim)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marata                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Bokmål norueguês       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutral Ground Truth - Línguas oficiais para todas as regiões em scripts locais, se disponível |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral Ground Truth - Exonyms latinos. Script latino será usado se disponível |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polaco                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | Punjabi                 |       |       |         |     ✓    |                   |                |
| RO-RO      | Romeno               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Sérvio (Cirílico)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Sérvio (Latim)        |       |       |         |     sr-latn    |                   |                |
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
| seu PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Usbeque                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamita             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps apoiou pontos de vista

> [!Note]
> Em 1 de agosto de 2019, o Azure Maps foi lançado nos seguintes países/regiões:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> Depois de 1 de agosto de 2019, o parâmetro **View** definirá o conteúdo do mapa devolvido para as novas regiões/países acima listados. O parâmetro Azure Maps **View** (também designado por "parâmetro da região do utilizador") é um Código de País ISO-3166 de duas letras que mostrará os mapas corretos para aquele país/região especificando qual o conjunto de conteúdos geopoliticamente contestados é devolvido através dos serviços do Azure Maps, incluindo fronteiras e etiquetas exibidas no mapa. 

Certifique-se de que configura o parâmetro **'Ver'** conforme necessário para as APIs rest e os SDKs, que os seus serviços estão a utilizar.
>  
>
>  **Afídeos de repouso:**
>  
>  Certifique-se de que instalou o parâmetro 'Ver' conforme necessário. Veja especificações de parâmetros que conjunto de conteúdos geopoliticamente contestados são devolvidos através dos serviços do Azure Maps. 
>
>  Serviços de DESCANSO dos Mapas Azure afetados:
>    
>    * Obter azulejo sinuoso
>    * Obter imagem do mapa 
>    * Obter Search Fuzzy
>    * Obtenha POI de pesquisa
>    * Obtenha a categoria POI de pesquisa
>    * Faça pesquisa nas proximidades
>    * Obter Endereço de Pesquisa
>    * Obter endereço de pesquisa estruturado
>    * Obter endereço de pesquisa invertido
>    * Obtenha endereço de pesquisa Reverse Cross Street
>    * Pós-Pesquisa Dentro da Geometria
>    * Pré-visualização do lote de endereço de pesquisa de post
>    * Pós-Endereço de pesquisa Reverter pré-visualização do lote
>    * Pós Pesquisa ao longo da rota
>    * Pré-visualização de lote de pesquisa de pós-pesquisa fuzzy
>
>    
>  **SDKs:**
>
>  Certifique-se de que configura o parâmetro **'Ver'** conforme necessário e tem a versão mais recente do Web SDK e Android SDK. SDKs afetados:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Por predefinição, o parâmetro View está definido para **Unificado,** mesmo que não o tenha definido no pedido. Determine a localização dos seus utilizadores. Em seguida, defina corretamente o parâmetro **'Vista'** para esse local. Em alternativa, pode definir 'View=Auto', que devolverá os dados do mapa com base no endereço IP do pedido.  O parâmetro **View** in Azure Maps deve ser utilizado de acordo com as leis aplicáveis, incluindo as leis sobre mapeamento do país onde mapas, imagens e outros dados e conteúdos de terceiros que está autorizado a aceder através do Azure Maps são disponibilizados.


A tabela seguinte fornece pontos de vista apoiados.

| Vista         | Descrição                            |  Maps | Pesquisa | Controlo de mapas JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (Vista Árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (Visão Argentina)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (Vista Árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (Indian View)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (Vista Árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (Vista Árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (Vista Árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (Vista Árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (Vista Marroquina)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (Vista Árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (Vista Paquistanesa)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestiniana (Visão Árabe)    |   ✓   |        |     ✓          |
| QA           | Qatar (Vista Árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (Vista Árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (Vista Árabe)                   |   ✓   |        |     ✓          |
| Ye           | Iémen (Vista Árabe)                   |   ✓   |        |     ✓          |
| Automático         | Devolva os dados do mapa com base no endereço IP do pedido.|   ✓   |    ✓   |     ✓          |
| Unificada      | Visão Unificada (Outros)                  |   ✓   |   ✓     |     ✓          |
