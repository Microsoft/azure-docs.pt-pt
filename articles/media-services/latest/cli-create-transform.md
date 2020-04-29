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
ms.openlocfilehash: c21a16d043f972042949d6340985774741b3df6a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74888620"
---
# <a name="cli-example-create-a-transform"></a>Exemplo da CLI: Criar uma Transformação

Neste artigo, o script da CLI do Azure mostra como criar uma transformação. As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita"). Deve sempre verificar se já existe uma Transformação com o nome e a "receita" pretendidos. Se for o caso, deve reutilizá-la.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services.](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Só pode especificar um caminho para um ficheiro JSON padrão padrão personalizado para [StandardEncoderPreset,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)consulte o código de codificação com um exemplo de [transformação personalizada.](custom-preset-cli-howto.md)
>
> Não é possível passar um nome de ficheiro quando utilizar [o BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Passos seguintes

[az ams transformar (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
