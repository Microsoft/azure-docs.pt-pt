---
title: Conheça os conceitos da ferramenta de análise – Content Moderator
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a ferramenta de análise de Content Moderator, um site que coordena um esforço combinado de moderação de reavaliação de ia e análise humana.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169510"
---
# <a name="content-moderator-review-tool"></a>Ferramenta de revisão de Content Moderator

O Azure Content Moderator fornece serviços para combinar a moderação do conteúdo do Machine Learning com revisões humanas e o site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) é um front-end amigável que fornece acesso detalhado a esses serviços.

![O painel de ferramentas de revisão em um navegador](./images/0-dashboard.png)

## <a name="what-it-does"></a>O que faz

A [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), quando usada em conjunto com as APIs de moderação assistida por computador, permite que você realize as seguintes tarefas no processo de moderação de conteúdo:

- Use um conjunto de ferramentas para conteúdo moderado em vários formatos (texto, imagem e vídeo).
- Automatize a criação de [revisões](../review-api.md#reviews) humanas quando os resultados da API de moderação chegarem.
- Atribua ou escale revisões de conteúdo para várias equipes de revisão, organizadas por categoria de conteúdo ou nível de experiência.
- Use filtros lógicos padrão ou personalizados ([fluxos de trabalho](../review-api.md#workflows)) para classificar e controlar o conteúdo, sem escrever nenhum código.
- Use [conectores](./configure.md#connectors) para processar conteúdo com o Microsoft PhotoDNA, análise de texto e serviços de face além das APIs de Content Moderator.
- Crie seu próprio conector para criar fluxos de trabalho para qualquer processo de API ou de negócios.
- Obtenha métricas chave de desempenho em seus processos de moderação de conteúdo.

## <a name="review-tool-dashboard"></a>Examinar painel de ferramentas

Na guia **painel** , você pode ver as principais métricas para revisões de conteúdo feitas dentro da ferramenta. Consulte o número de revisões totais, completas e pendentes para conteúdo de imagem, texto e vídeo. Você também pode ver a divisão de usuários e equipes que concluíram revisões, bem como as marcas de moderação que foram aplicadas.

![Ver Dashboard](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Examinar as credenciais da ferramenta

Ao se inscrever com a [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), você será solicitado a selecionar uma região do Azure para sua conta. Isso ocorre porque a [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) gera uma chave de avaliação gratuita para os serviços de Content moderator do Azure; Você precisará dessa chave para acessar qualquer um dos serviços de uma chamada REST ou do SDK do cliente. Você pode exibir a URL de ponto de extremidade da API e de chave selecionando **configurações** > **credenciais**.

![Content Moderator credenciais](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passos seguintes

Consulte [Configurar a ferramenta de revisão](./configure.md) para saber como acessar os recursos da ferramenta de análise e alterar as configurações.