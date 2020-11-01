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
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146642"
---
# <a name="content-moderator-review-tool"></a>Ferramenta de Revisão do Moderador de Conteúdo

O Azure Content Moderador fornece serviços para combinar moderação de conteúdo de aprendizagem automática com avaliações humanas. O site [da ferramenta Review](https://contentmoderator.cognitive.microsoft.com) é uma frente fácil de usar que dá acesso detalhado a estes serviços.

## <a name="what-it-does"></a>O que faz

A [ferramenta Review](https://contentmoderator.cognitive.microsoft.com), quando utilizada em conjunto com as APIs de moderação assistida por máquinas, permite-lhe realizar as seguintes tarefas no processo de moderação de conteúdos:

- Utilize um conjunto de ferramentas para moderar o conteúdo em vários formatos (texto, imagem e vídeo).
- Automatizar a criação de [avaliações humanas](../review-api.md#reviews) quando os resultados da API de moderação chegarem.
- Atribua ou agrave as avaliações de conteúdo a várias equipas de revisão, organizadas por categoria de conteúdo ou nível de experiência.
- Utilize filtros lógicos predefinidos ou[personalizados (fluxos de trabalho)](../review-api.md#workflows)para classificar e rastrear o conteúdo, sem escrever qualquer código.
- Utilize [conectores](./configure.md#connectors) para processar conteúdos com os serviços Microsoft PhotoDNA, Text Analytics e Face, além das APIs moderadoras de conteúdo.
- Obtenha métricas de desempenho chave nos seus processos de moderação de conteúdo.

## <a name="review-tool-dashboard"></a>Painel de ferramentas de revisão

No **separador Dashboard,** pode ver as métricas-chave para avaliações de conteúdo efetuadas dentro da ferramenta. Consulte o número de comentários totais, completos e pendentes para conteúdo de imagem, texto e vídeo. 

A tabela **de comentários pendentes** mostra a desagregação de utilizadores e subtemas que têm revisões pendentes ou concluídas, bem como o tempo restante do SLA. Pode selecionar os itens na tabela para ir às suas avaliações. A caixa de pesquisa acima da tabela permite filtrar os resultados pelo nome da equipa, e o ícone **Filtro** permite filtrar por outras métricas.

Mudar para o separador **de avaliações Concluídas** mostra o número total de itens processados ou preenchidos por utilizadores e subteames. Pode filtrar estes dados da mesma forma que as avaliações pendentes.

Clicar no texto no canto superior direito do painel apresenta as Métricas Pessoais Diárias, que relatam o número de avaliações completas para cada tipo de conteúdo.

> [!div class="mx-imgBorder"]
> ![O painel de ferramentas de revisão em um navegador](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Rever credenciais de ferramenta

Quando se inscreve na [ferramenta 'Revisão',](https://contentmoderator.cognitive.microsoft.com)será solicitado que selecione uma região Azure para si. Isto porque a [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) gera uma chave de teste gratuita para os serviços de Moderador de Conteúdo Azure. Você precisará desta chave para aceder a qualquer um dos serviços a partir de uma chamada REST ou cliente SDK. Pode ver a sua chave e URL **Admin** de ponto final da API selecionando  >  **credenciais** de administrador .

> [!div class="mx-imgBorder"]
> ![Credenciais moderadoras de conteúdo](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passos seguintes

Consulte [a ferramenta Configure a ferramenta Review](./configure.md) para aprender a aceder aos recursos da ferramenta de Revisão e alterar as definições.