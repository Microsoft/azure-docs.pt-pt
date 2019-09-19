---
title: Exemplo do Script da CLI do Azure – Criar uma transformação | Microsoft Docs
description: Utilize o script da CLI do Azure para criar uma Transformação.
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
ms.openlocfilehash: 7fea8d997d25d6075e30620e7fd3527003c6a8bb
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128796"
---
# <a name="cli-example-create-a-transform"></a>Exemplo da CLI: Criar uma transformação

Neste artigo, o script da CLI do Azure mostra como criar uma transformação. As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita"). Deve sempre verificar se já existe uma Transformação com o nome e a "receita" pretendidos. Se for o caso, deve reutilizá-la.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Você só pode especificar um caminho para um arquivo JSON de predefinição de codificador padrão personalizado para [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), consulte o exemplo [codificar com uma transformação personalizada](custom-preset-cli-howto.md) .
>
> Você não pode passar um nome de arquivo ao usar [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Passos seguintes

[AZ AMS Transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
