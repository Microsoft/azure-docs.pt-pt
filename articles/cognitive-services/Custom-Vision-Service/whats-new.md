---
title: Quais as novidades na Visão Personalizada?
titleSuffix: Azure Cognitive Services
description: Este artigo contém novidades sobre a Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 10/23/2020
ms.author: pafarley
ms.openlocfilehash: a87e76d4a726f7a01b96d602e7f41d60409dae56
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521497"
---
# <a name="whats-new-in-custom-vision"></a>Novidades na Visão Personalizada

Saiba o que há de novo no serviço. Estes itens podem ser notas de lançamento, vídeos, posts de blog e outros tipos de informação. Marque esta página para manter-se atualizado com o serviço.

## <a name="july-2020"></a>Julho de 2020

### <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

* A Custom Vision suporta o controlo de acesso baseado em funções Azure (Azure RBAC), um sistema de autorização para gerir o acesso individual aos recursos Azure. Para aprender a gerir o acesso aos seus projetos de Visão Personalizada, consulte [o controlo de acesso baseado em funções da Azure.](./role-based-access-control.md)

### <a name="subset-training"></a>Treinamento de subconjunto

* Ao treinar um projeto de deteção de objetos, pode treinar opcionalmente apenas um subconjunto das suas etiquetas aplicadas. Podes querer fazer isto se ainda não aplicaste o suficiente de certas etiquetas, mas tens o suficiente de outras. Siga o arranque rápido da [biblioteca cliente](./quickstarts/object-detection.md) para C# ou Python para saber mais.

### <a name="azure-storage-notifications"></a>Notificações de armazenamento Azure

* Pode integrar o seu projeto Custom Vision com uma fila de armazenamento de blob Azure para obter notificações push da atividade de treino/exportação de projetos e cópias de backup de modelos publicados. Esta funcionalidade é útil para evitar que o serviço seja continuamente sondada para obter resultados quando as operações prolongadas estiverem a decorrer. Em vez disso, pode integrar as notificações da fila de armazenamento no seu fluxo de trabalho. Consulte o guia [de integração do Armazenamento](./storage-integration.md) para saber mais.

### <a name="copy-and-move-projects"></a>Copiar e mover projetos

* Agora pode copiar projetos de uma conta De Visão Personalizada em outras. Você pode querer mover um projeto de um ambiente de desenvolvimento para o ambiente de produção, ou apoiar um projeto para uma conta em uma região de Azure diferente para uma maior segurança de dados. Consulte o guia [de projetos Copy e move](./copy-move-projects.md) para saber mais.

## <a name="september-2019"></a>Setembro de 2019

### <a name="suggested-tags"></a>Etiquetas sugeridas

* A ferramenta Smart Labeler no [site Da Visão Personalizada](https://www.customvision.ai/) gera etiquetas sugeridas para as suas imagens de treino. Isto permite-lhe rotular um grande número de imagens mais rapidamente ao treinar um modelo de Visão Personalizada. Para obter instruções sobre como utilizar esta função, consulte [as etiquetas sugeridas](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Atualizações do Serviço Cognitivo

[Anúncios de atualização da Azure para Serviços Cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)