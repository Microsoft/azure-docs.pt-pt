---
title: Carregamento e o armazenamento com os serviços de multimédia do Azure na cloud | Documentos da Microsoft
description: Este carregamento do artigo na cloud e conceitos de armazenamento.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/21/2019
ms.author: juliako
ms.openlocfilehash: cda029dd11e8cb4cb07e9fce7eef95d6d4d78d7e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960226"
---
# <a name="cloud-upload-and-storage"></a>Carregamento e armazenamento na cloud

Para começar a gerir, encriptar, codificar, analisar e transmissão em fluxo conteúdo de multimédia do Azure, terá de criar uma conta de Media Services. Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. 

A conta de Media Services e todas as contas de armazenamento associado tem de ser na mesma subscrição do Azure. É vivamente recomendado para utilizar contas de armazenamento na mesma localização que a conta de serviços de multimédia para evitar custos de saída de latência e dados adicionais

Tem de ter uma conta de armazenamento **Principal** e pode ter qualquer número de contas de armazenamento **Secundárias** associadas à conta dos Serviços de Multimédia. Os Serviços de Multimédia suportam contas para **Fins gerais v2** (GPv2) ou **Fins gerais v1** (GPv1). 

>[!NOTE]
> As contas apenas de blobs não são permitidas como **Principais**. 

Recomendamos que utilize GPv2, para que possa tirar partido de optar entre frequente e esporádico camadas de armazenamento. Para saber mais sobre as contas de armazenamento, veja [descrição geral da conta de armazenamento do Azure](../../storage/common/storage-account-overview.md). 

Existem diferentes SKUs, pode escolher para a sua conta de armazenamento. Para obter mais informações, consulte [contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Se quiser experimentar as contas de armazenamento, utilize `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção deve considerar, `--sku Standard_RAGRS`, que fornece replicação geográfica para continuidade do negócio. 

## <a name="assets-in-a-storage-account"></a>Recursos numa conta de armazenamento

Em serviços de multimédia v3, as APIs de armazenamento são utilizadas para carregar ficheiros para ativos. Para obter mais informações, consulte [conceito de ativos](assets-concept.md).

> [!Note]
> Não deve tentar alterar os conteúdos de contentores de BLOBs que foram gerados pelo SDK de serviços de multimédia sem utilizar as APIs dos serviços de multimédia.
 
## <a name="next-steps"></a>Passos Seguintes

Para saber como anexar uma conta de armazenamento à sua conta de Media Services, veja [criar uma conta](create-account-cli-quickstart.md).
