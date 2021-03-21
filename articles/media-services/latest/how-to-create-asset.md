---
title: Faça upload de conteúdo para um ativo CLI
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
ms.date: 02/16/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3923394e76d5ce4001d3652ae9cbc263df08e4e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545620"
---
# <a name="create-an-asset"></a>Criar um Elemento

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como criar um Ativo de Serviços de Mídia.  Utilizará um ativo para deter conteúdo sonoro para codificação e streaming.  Para saber mais sobre os ativos dos Media Services, leia [ativos na Azure Media Services v3](assets-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Siga os passos na [Criar uma conta de Serviços](./create-account-howto.md) de Comunicação para criar a conta e grupo de recursos necessários para criar um ativo.

## <a name="methods"></a>Métodos

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Utilizar REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Utilizar o cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos serviços de mídia](media-services-overview.md)
