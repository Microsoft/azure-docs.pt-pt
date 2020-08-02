---
title: Exemplo do Script da CLI do Azure – Criar uma transformação | Microsoft Docs
description: As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita"). Neste artigo, o script da CLI do Azure mostra como criar uma transformação.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.custom: devx-track-azurecli
ms.openlocfilehash: 538f48d427a4d8b51f77ae50bb0bee95909f0b09
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494473"
---
# <a name="cli-example-create-a-transform"></a>Exemplo da CLI: Criar uma Transformação

Neste artigo, o script da CLI do Azure mostra como criar uma transformação. As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita"). Deve sempre verificar se já existe uma Transformação com o nome e a "receita" pretendidos. Se for o caso, deve reutilizá-la.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Só é possível especificar um caminho para um ficheiro JSON pré-configurado standard standard para [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset), ver o código de codificação com um exemplo [de transformação personalizado.](custom-preset-cli-howto.md)
>
> Não é possível passar um nome de ficheiro quando utilizar [o BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Passos seguintes

[az ams transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
