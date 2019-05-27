---
title: Criar uma conta de serviços de multimédia com a CLI do Azure - Azure | Documentos da Microsoft
description: Siga os passos deste início rápido para criar uma conta dos Serviços de Multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/19/2019
ms.author: juliako
ms.openlocfilehash: f2cb2e2ee6393a59125ee879f2058516eb50d6b1
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956731"
---
# <a name="create-an-azure-media-services-account"></a>Criar uma conta dos Azure Media Services

Para começar a encriptar, codificação, analisar, gerir e transmissão em fluxo conteúdo de multimédia do Azure, terá de criar uma conta de Media Services. Conta de Media Services tem de estar associado a uma ou mais contas de armazenamento.

> [!NOTE]
> A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Recomendamos vivamente que utilize as contas de armazenamento na mesma localização que a conta dos Serviços de Multimédia para evitar custos de saída de dados e latência adicionais.

Este artigo descreve os passos para criar uma nova conta de Media Services do Azure com a CLI do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Definir a subscrição do Azure

No comando seguinte, forneça o ID da subscrição do Azure que quer utilizar na conta dos Serviços de Multimédia. Pode ver uma lista de subscrições a que tem acesso ao navegar até [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Passos Seguintes

* [APIs de acesso de v3](access-api-cli-how-to.md)
* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Anexar um armazenamento secundário para uma conta de serviços de multimédia](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

