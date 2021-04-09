---
title: Exemplo de script do Azure CLI - Criar uma transformação
description: As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita"). Neste artigo, o script da CLI do Azure mostra como criar uma transformação.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4623610960d8f21a2dab3293c7499a2112416254
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718918"
---
# <a name="create-a-transform"></a>Criar uma transformação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Neste artigo, o script da CLI do Azure mostra como criar uma transformação. As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita"). Deve sempre verificar se já existe uma Transformação com o nome e a "receita" pretendidos. Se for o caso, deve reutilizá-la.

## <a name="prerequisites"></a>Pré-requisitos

[Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

## <a name="cli"></a>[CLI](#tab/cli/)

> [!NOTE]
> Só é possível especificar um caminho para um ficheiro JSON pré-configurado standard standard para [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset), ver o código de codificação com um exemplo [de transformação personalizado.](custom-preset-cli-howto.md)
>
> Não é possível passar um nome de ficheiro quando utilizar [o BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
