---
title: Aprender conceitos de ferramentas de revisão - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Conheça a ferramenta Content Moderator Review, um site que coordena um esforço combinado de IA e moderação de revisão humana.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76169510"
---
# <a name="content-moderator-review-tool"></a>Ferramenta de revisão de moderador de conteúdo

O Azure Content Moderator fornece serviços para combinar a moderação de conteúdos de aprendizagem automática com avaliações humanas, e o website da [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) é uma frente frontal amigável que dá acesso detalhado a estes serviços.

![O dashboard de ferramentas de revisão em um navegador](./images/0-dashboard.png)

## <a name="what-it-does"></a>O que faz

A [ferramenta Review](https://contentmoderator.cognitive.microsoft.com), quando utilizada em conjunto com as APIs de moderação assistida pela máquina, permite-lhe realizar as seguintes tarefas no processo de moderação de conteúdos:

- Utilize um conjunto de ferramentas para moderar o conteúdo em vários formatos (texto, imagem e vídeo).
- Automatizar a criação de [críticas](../review-api.md#reviews) humanas quando os resultados da API de moderação chegarem.
- Atribuir ou escalar as avaliações de conteúdo a várias equipas de revisão, organizadas por categoria de conteúdo ou nível de experiência.
- Utilize filtros lógicos padrão ou personalizados[(fluxos de trabalho)](../review-api.md#workflows)para classificar e rastrear o conteúdo, sem escrever qualquer código.
- Utilize [conectores](./configure.md#connectors) para processar conteúdos com os serviços Microsoft PhotoDNA, Text Analytics e Face, para além das APIs moderadoras de conteúdo.
- Construa o seu próprio conector para criar fluxos de trabalho para qualquer API ou processo de negócio.
- Obtenha métricas de desempenho chave nos seus processos de moderação de conteúdo.

## <a name="review-tool-dashboard"></a>Painel de ferramentas de revisão

No **separador Dashboard,** pode ver métricas-chave para avaliações de conteúdo efetuadas dentro da ferramenta. Consulte o número de avaliações totais, completas e pendentes para conteúdos de imagem, texto e vídeo. Pode também ver a desagregação de utilizadores e equipas que tenham concluído as avaliações, bem como as etiquetas de moderação que foram aplicadas.

![Ver Dashboard](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Rever credenciais de ferramentas

Quando se inscrever na [ferramenta Review,](https://contentmoderator.cognitive.microsoft.com)será solicitado que selecione uma região Azure para a sua conta. Isto porque a [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) gera uma chave de teste gratuita para os serviços de Moderador de Conteúdo Azure; necessitará desta chave para aceder a qualquer um dos serviços a partir de uma chamada REST ou sDK cliente. Pode visualizar a sua chave e URL de ponto final da API selecionando**credenciais**de **definições** > .

![Credenciais moderadoras de conteúdo](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passos seguintes

Consulte [a ferramenta 'Rever'](./configure.md) para aprender a aceder aos recursos da ferramenta De revisão e alterar as definições.