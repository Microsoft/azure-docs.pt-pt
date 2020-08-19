---
title: Faça upload de conteúdo para um ativo Azure Media Services usando Azure CLI
description: O script da CLI do Azure neste tópico mostra como criar um Recurso dos Serviços de Multimédia para o qual carregar conteúdo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585424"
---
# <a name="create-an-asset"></a>Criar um Elemento

Este artigo mostra como criar um Ativo de Serviços de Mídia.  Utilizará um ativo para deter conteúdo sonoro para codificação e streaming.  Para saber mais sobre os ativos dos Media Services, leia [ativos na Azure Media Services v3](assets-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Siga os passos na [Criar uma conta de Serviços](./create-account-howto.md) de Comunicação para criar a conta e grupo de recursos necessários para criar um ativo.

## <a name="methods"></a>Métodos

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[Concha CLI](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>Passos seguintes

[Gerir recursos](manage-asset-concept.md)
