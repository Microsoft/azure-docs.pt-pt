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
ms.openlocfilehash: 86f455f25bb41833774bb7ad7d81688aad812b5a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236813"
---
# <a name="cli-example-create-a-transform"></a>Exemplo da CLI: Criar uma transformação

Neste artigo, o script da CLI do Azure mostra como criar uma transformação. As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita"). Deve sempre verificar se já existe uma Transformação com o nome e a "receita" pretendidos. Se for o caso, deve reutilizá-la.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral dos serviços de multimédia](media-services-overview.md)
