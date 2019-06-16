---
title: Aprenda os conceitos de ferramenta de revisão - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a ferramenta de revisão de moderador de conteúdos, um Web site que coordena uma IA combinada e o esforço de moderação de revisão humana.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61270009"
---
# <a name="content-moderator-review-tool"></a>Ferramenta de análise do moderador de conteúdo

O Azure Content Moderator fornece serviços para combinar a moderação de conteúdos de aprendizagem de máquina com revisões realizadas por pessoas e o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) Web site é um front-end amigável que permita o acesso detalhado para estes serviços.

![O dashboard de ferramenta de revisão num browser](./images/0-dashboard.png)

## <a name="what-it-does"></a>O que faz

O [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), quando utilizado em conjunto com as APIs, de moderação assistida por computador, pode realizar as seguintes tarefas no processo de moderação de conteúdos:

- Utilize um conjunto de ferramentas para moderar conteúdo em vários formatos (texto, imagem e vídeo).
- Automatizar a criação de humanos [revisões](../review-api.md#reviews) quando a API de moderação resulta vêm em.
- Atribuir ou escalar revisões de conteúdo a várias equipes de revisão, organizadas por nível de categoria ou a experiência de conteúdo.
- Utilizar predefinição ou filtros de lógica personalizada ([fluxos de trabalho](../review-api.md#workflows)) para classificar e controlar o conteúdo, sem ter de escrever qualquer código.
- Uso [conectores](./configure.md#connectors) para processar o conteúdo com o Microsoft PhotoDNA, análise de texto e APIs de rostos, além das APIs de moderador de conteúdo.
- Criar seu próprio conector para criar fluxos de trabalho para qualquer API ou processo comercial.
- Obter métricas chave de desempenho em seus processos de moderação de conteúdos.

## <a name="review-tool-dashboard"></a>Dashboard de ferramenta de revisão

Sobre o **Dashboard** guia, pode ver as principais métricas para análises de conteúdos feitas dentro da ferramenta. Ver o número de total, concluído e pendentes as revisões de imagem, texto e conteúdo de vídeo. Também pode ver a divisão de utilizadores e as equipes que foram concluídos, revisões, bem como as etiquetas de moderação que foram aplicadas.

![Ver Dashboard](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Credenciais da ferramenta de revisão

Quando se inscreve com o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), será solicitado a selecionar uma região do Azure para tem em conta. Isto acontece porque o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) gera uma chave de avaliação gratuita para os serviços do Azure Content Moderator; precisará esta chave para aceder a qualquer um dos serviços de uma chamada REST ou o SDK de cliente. Pode ver o URL de ponto final e a chave de API, selecionando **configurações** > **credenciais**.

![O Content Moderator credenciais](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passos Seguintes

Ver [configurar a ferramenta de revisão](./configure.md) para aprender a aceder aos recursos da ferramenta de revisão e alterar as definições.