---
title: O que há de novo Reconhecimento vocal (LUIS)
titleSuffix: Azure Cognitive Services
description: Este artigo é atualizado regularmente com notícias sobre os API de Reconhecimento Vocal de serviços cognitivas do Azure.
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: 62ce0ac36223098d013cc75b7f62fc2c68e358ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953366"
---
# <a name="whats-new-in-language-understanding"></a>O que há de novo no Reconhecimento vocal

Saiba o que há de novo no serviço. Esses itens incluem notas de versão, vídeos, Postagens de blog e outros tipos de informações. Marque esta página para manter-se atualizada com o serviço.  

## <a name="release-notes"></a>Notas de versão 

### <a name="november-4-2019---ignite"></a>4 de novembro de 2019-Ignite

* Produtividade do desenvolvedor aprimorada
    * Disponibilidade geral do nosso [ponto de extremidade de previsão v3](luis-migration-api-v3.md). 
    * Capacidade de importar e exportar aplicativos com o formato. Lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Isso abre o caminho para um processo de CI/CD eficaz. 
* Expansão de idioma
    * [Árabe e híndi](luis-language-support.md) em visualização pública.
* Modelos pré-construídos
    * Os [domínios predefinidos](luis-reference-prebuilt-domains.md) agora estão disponíveis para o público geral (GA)
    * [Entidades predefinidas](luis-reference-prebuilt-entities.md#japanese-entity-support) japonesas-idade, moeda, número, porcentagem não são compatíveis com v3.
    * [Entidades predefinidas](luis-reference-prebuilt-entities.md#italian-entity-support) em italiano-idade, moeda, dimensão, número, resolução percentual alterada de v2.
* Aprimore a experiência do usuário no [portal Preview.Luis.ai](https://preview.luis.ai) – experiência de rotulação remodelada para habilitar a criação e a depuração de modelos complexos. Experimente os tutoriais do portal de visualização:
    * [Somente tentativas](tutorial-intents-only.md)
    * [Entidade aprendida por máquina decomposta](tutorial-machine-learned-entity.md) 
* Recursos de reconhecimento de idioma avançado- [criando modelos de linguagem sofisticados](luis-concept-entity-types.md) com menos esforço. 
* Definir recursos de aprendizado de máquina no nível de modelo e habilitar modelos a serem usados como sinais para outro modelo, como usar entidades como recursos para intenções e outras entidades.
* Novos [limites](luis-boundaries.md) expandidos-máximos superiores para listas de frases e total de frases, novo modelo como limites de recursos
* Extraia informações de texto no formato de estrutura de hierarquia profunda, tornando os aplicativos de conversa mais poderosos.

    ![imagem de entidade aprendida por computador](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 de setembro de 2019

* Recurso de criação do Azure – [migrar agora](luis-migration-authoring.md).
    * 500 aplicativos por recurso do Azure
    * 100 versões por aplicativo
* Suporte a turco para entidades predefinidas
* Suporte italiano para datetimeV2

### <a name="july-23-2019"></a>23 de julho de 2019

* Atualizar os [reconhecedores-texto](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) para 1.2.3
    * Reconhecedores de idade, temperatura, dimensão e moeda em italiano.
    * Melhoria no reconhecimento de feriados em inglês para calcular corretamente as datas baseadas em graças.
    * Melhorias no DateTime em francês para reduzir os falsos positivos de entidades não atualizadas e não de tempo.
    * Suporte para calendário/escola/ano fiscal e acrônimos em DateRange em inglês.
    * Reconhecimento de PhoneNumber aprimorado em chinês e japonês.
    * Suporte aprimorado para NumberRange em inglês.
    * Melhorias de desempenho.

### <a name="june-24-2019"></a>24 de junho de 2019

* [OrdinalV2 entidade predefinida](luis-reference-prebuilt-ordinal-v2.md) para dar suporte a pedidos como, por exemplo, avançar, anterior e último. Somente cultura em inglês.

### <a name="may-6-2019---build-conference"></a>6 de maio, 2019-conferência//Build

Os seguintes recursos foram lançados na conferência Build 2019:

* [Visualização do guia de migração da API v3](luis-migration-api-v3.md)
* [Painel analítico aprimorado](luis-how-to-use-dashboard.md)
* [Domínios predefinidos aprimorados](luis-reference-prebuilt-domains.md) 
* [Entidades de lista dinâmica](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogues

[Estrutura de bot](https://blog.botframework.com/)

## <a name="videos"></a>Vídeos

### <a name="2019-build-videos"></a>vídeos de Build 2019

[Como usar o AI de conversa do Azure para dimensionar sua empresa para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Atualizações de serviço

[Comunicados de atualização do Azure para serviços cognitivas](https://azure.microsoft.com/updates/?product=cognitive-services)
