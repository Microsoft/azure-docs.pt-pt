---
title: Suporte de localização do Azure Maps | Documentos da Microsoft
description: Saiba mais sobre os idiomas suportados para os serviços do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a9446301cc4bb46c989223ad020c7a8e8b353ad3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446192"
---
# <a name="localization-support-in-azure-maps"></a>Suporte de localização do Azure Maps

Mapas do Azure suporta vários idiomas e modos de exibição com base no país/região. Este artigo fornece os idiomas suportados e vistas para ajudar a orientar a sua implementação do Azure Maps.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte do Azure Maps

Foram localizado numa variedade de linguagens do Azure Maps em serviços. A tabela seguinte fornece os códigos de idiomas suportados para cada serviço.  
  

| id         | Name                   |  Maps | Pesquisa | Encaminhamento | Incidentes de tráfego | Controlo de mapas JS | Fuso horário |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar SA      | Árabe                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | Basco (Basco)                 |       |    ✓   |         |                   |                |     ✓     |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ES de AC      | Catalão                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Chinês (Simplificado)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Chinês (Tradicional)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| RH de RH      | Croata               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | Checo                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NL-NL      | Neerlandês                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Holandês (Bélgica)        |       |    ✓   |         |                   |                |     ✓     |
| EN-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EN-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estónio               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| FR-FR      | Francês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Francês (canadense)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | Galego               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el GR      | Grego                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| he-IL      | Hebraico                 |       |    ✓   |         |         ✓         |                |     ✓     |
| IN      | Hindi                  |       |        |         |                   |                |     ✓     |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japonês               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Cazaque                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Latino American espanhol |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | Letão                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| MS-MY      | Malaio (Latim)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| nb-NO      | Norwegian Bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutralidade quanto à raiz verdade - idiomas oficiais para todas as regiões na scripts locais se estiver disponível |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Neutralidade quanto à raiz verdade - exonyms latinos. Script Latino será utilizado se estiver disponível |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polaco                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| RO-RO      | Romeno               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Sérvio (Cirílico)     |       |    Sérvio (Cirílico) (sr-RS)   |         |                   |                |     ✓     |
| SR-Latn-RS | Sérvio (Latim)        |       |        |         |                   |                |     ✓     |
| sk SK      | Slovakian              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | Esloveno              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Espanhol                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Espanhol (México)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | Sueco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | Tailandês                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | Ucraniano               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | Vietnamita             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Vistas de suporte do Azure Maps

> [!Note]
> Estamos a lançar mapas do Azure nos países/regiões seguintes 1 de Agosto de 2019:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> Após 1 de Agosto de 2019 **vista** definição do parâmetro irá definir o conteúdo do mapa retornado para os países/regiões novas listados acima. Aconselhamo-lo para se certificar de que configurou o parâmetro de modo de exibição, conforme necessário para as REST APIs e SDKs que seus serviços estão a utilizar.
>  
>
>  **REST APIs:**
>  
>  Certifique-se de que configurou o parâmetro de modo de exibição, conforme necessário. Parâmetro de modo de exibição Especifica qual conjunto de conteúdo geopolitically questionado é devolvido por meio de serviços do Azure Maps. 
>
>  Serviços REST de mapas do Azure afetado:
>    
>    * Obter o mosaico do mapa
>    * Obter a imagem do mapa 
>    * Obter pesquisa difusa
>    * Obter pesquisa POI
>    * Obter pesquisa POI categoria
>    * Obter pesquisa próximos
>    * Obter o endereço de pesquisa
>    * Obter o endereço de pesquisa estruturado
>    * Obter o endereço de pesquisa inversa
>    * Obter pesquisa endereço inversa entre Rua
>    * Pesquisa de postagem dentro de geometria
>    * Pré-visualização de Batch do endereço de pesquisa de mensagem
>    * Pré-visualização de Batch inversa do endereço de pesquisa de postagem
>    * Pesquisa de POST ao longo da rota
>    * Pré-visualização do POST pesquisa difusa do Batch
>
>    
>  **SDKs:**
>
>  Certifique-se de que configurou o parâmetro de modo de exibição, conforme necessário e tem a versão mais recente do Web SDK e o SDK do Android. SDKs afetados:
>
>    * SDK de Web do Azure Maps
>    * SDK Android do Azure Maps


O Azure Maps **vista** parâmetro (também referido como "parâmetro de região do utilizador") é uma letra dois é o indicativo de país ISO 3166 que mostrará os mapas corretos para esse país/região a especificar qual conjunto de geopolitically questionadas conteúdo retornados por serviços de mapas do Azure, incluindo limites e etiquetas apresentadas no mapa. 

Por predefinição, o parâmetro de modo de exibição é definido como **unificado**, mesmo que ainda não definiu no pedido. É da responsabilidade do cliente para determinar a localização dos seus utilizadores e, em seguida, defina o parâmetro de modo de exibição corretamente para esse local. Em alternativa, tem a opção de definir "View = automática", que irá devolver os dados de mapa com base no endereço IP do pedido.  O parâmetro de ver no mapas do Azure tem de ser utilizado em conformidade com as leis aplicáveis, incluindo aquelas sobre mapeamento do país em que os mapas, imagens e outros conteúdos de dados e de terceiros que está autorizado a aceder através do Azure Maps é disponibilizado.


A tabela seguinte fornece vistas suportadas.

| Vista         | Descrição                            |  Maps | Pesquisa | Controlo de mapas JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (vista de Árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (exibição Argentinian)           |   ✓   |    ✓   |     ✓          |
| BH           | Barém (vista de Árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (exibição indianos)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (vista de Árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (vista de Árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (vista de Árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (vista de Árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (exibição Maroko)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (vista de Árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (exibição Pakistani)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestiniana (vista de Árabe)    |   ✓   |        |     ✓          |
| QA           | Catar (vista de Árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (vista de Árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (vista de Árabe)                   |   ✓   |        |     ✓          |
| VOCÊ           | Iémen (vista de Árabe)                   |   ✓   |        |     ✓          |
| Automática         | Devolve os dados de mapa com base no endereço IP do pedido.|   ✓   |    ✓   |     ✓          |
| Unificado      | Vista unificada (outros)                  |   ✓   |   ✓     |     ✓          |
