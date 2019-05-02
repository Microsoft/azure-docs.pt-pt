---
title: Suporte de localização do Azure Maps | Documentos da Microsoft
description: Saiba mais sobre os idiomas suportados para os serviços do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1928185521419006a487a933e2ecba79894a09d3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686788"
---
# <a name="localization-support-in-azure-maps"></a>Suporte de localização do Azure Maps

Mapas do Azure suporta vários idiomas e modos de exibição com base no país/região. Este artigo fornece os idiomas suportados e vistas para ajudar a orientar a sua implementação do Azure Maps.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte do Azure Maps

Foram localizado numa variedade de linguagens do Azure Maps em serviços. A tabela seguinte fornece os códigos de idiomas suportados para cada serviço.  
  

| ID         | Name                   |  Maps | Pesquisa | Encaminhamento | Incidentes de tráfego | Controlo de mapas JS | Fuso horário |
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
| kk-KZ      | Cazaquistanês                 |       |    ✓   |         |                   |                |     ✓     |
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

Parâmetro de vista de mapas do Azure (também referido como "parâmetro de região do utilizador") é um código de país ISO 3166 de 2 letras que mostrará os mapas corretos para esse país/região a especificar qual conjunto de geopolitically questionadas bordas e as etiquetas são apresentadas no mapa.  Por predefinição, o parâmetro de modo de exibição é definido como **"Unified"**.  País/regiões que não estejam na lista de vista será predefinido para a vista de "Unificado". É da responsabilidade do cliente para determinar a localização dos seus utilizadores e, em seguida, defina o parâmetro de modo de exibição corretamente para esse local. O parâmetro de ver no mapas do Azure tem de ser utilizado em conformidade com as leis aplicáveis, incluindo aquelas sobre mapeamento do país em que os mapas, imagens e outros conteúdos de dados e de terceiros que está autorizado a aceder através do Azure Maps é disponibilizado.

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
| Unified      | Vista unificada (outros)                  |   ✓   |   ✓     |     ✓          |
