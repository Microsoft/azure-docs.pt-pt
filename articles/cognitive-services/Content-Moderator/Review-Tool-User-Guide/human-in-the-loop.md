---
title: Aprenda conceitos de ferramentas de Revisão - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Conheça a ferramenta content Moderator Review, um site que coordena um esforço combinado de moderação de IA e revisão humana.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76169510"
---
# <a name="content-moderator-review-tool"></a>Ferramenta de Revisão do Moderador de Conteúdo

O Azure Content Moderador fornece serviços para combinar moderação de conteúdo de machine learning com avaliações humanas, e o site [da ferramenta Review](https://contentmoderator.cognitive.microsoft.com) é uma frente amigável ao utilizador que dá acesso detalhado a estes serviços.

![O painel de ferramentas de revisão em um navegador](./images/0-dashboard.png)

## <a name="what-it-does"></a>O que faz

A [ferramenta Review](https://contentmoderator.cognitive.microsoft.com), quando utilizada em conjunto com as APIs de moderação assistida por máquinas, permite-lhe realizar as seguintes tarefas no processo de moderação de conteúdos:

- Utilize um conjunto de ferramentas para moderar o conteúdo em vários formatos (texto, imagem e vídeo).
- Automatizar a criação de [avaliações humanas](../review-api.md#reviews) quando os resultados da API de moderação chegarem.
- Atribua ou agrave as avaliações de conteúdo a várias equipas de revisão, organizadas por categoria de conteúdo ou nível de experiência.
- Utilize filtros lógicos predefinidos ou[personalizados (fluxos de trabalho)](../review-api.md#workflows)para classificar e rastrear o conteúdo, sem escrever qualquer código.
- Utilize [conectores](./configure.md#connectors) para processar conteúdos com os serviços Microsoft PhotoDNA, Text Analytics e Face, além das APIs moderadoras de conteúdo.
- Construa o seu próprio conector para criar fluxos de trabalho para qualquer API ou processo de negócio.
- Obtenha métricas de desempenho chave nos seus processos de moderação de conteúdo.

## <a name="review-tool-dashboard"></a>Painel de ferramentas de revisão

No **separador Dashboard,** pode ver as métricas-chave para avaliações de conteúdo efetuadas dentro da ferramenta. Consulte o número de comentários totais, completos e pendentes para conteúdo de imagem, texto e vídeo. Pode também ver a desagregação de utilizadores e equipas que completaram comentários, bem como as etiquetas de moderação que foram aplicadas.

![Ver Dashboard](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Rever credenciais de ferramenta

Quando se inscreve na [ferramenta 'Revisão',](https://contentmoderator.cognitive.microsoft.com)será solicitado que selecione uma região Azure para si. Isto porque a [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) gera uma chave de teste gratuita para os serviços de Moderador de Conteúdo Azure; necessitará desta chave para aceder a qualquer um dos serviços a partir de uma chamada REST ou do cliente SDK. Pode ver a sua chave e URL **Settings**de ponto final da API selecionando  >  **Credenciais de Definições**.

![Credenciais moderadoras de conteúdo](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passos seguintes

Consulte [a ferramenta Configure a ferramenta Review](./configure.md) para aprender a aceder aos recursos da ferramenta de Revisão e alterar as definições.