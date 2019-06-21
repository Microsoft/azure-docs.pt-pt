---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d05d425cc9bfb206207801f15a25e17d60dc0aaf
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191749"
---
1. Certifique-se de que tem sessão iniciada na sua conta do Azure e estiver a utilizar a subscrição que pretende carregar para esta pré-visualização. Utilize o seguinte exemplo de inscrição:

    ```azurepowershell-interactive
    Register-AzureRmProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Voltar a registar a sua subscrição mais uma vez com o *Network* espaço de nomes do fornecedor.

    ```azurepowershell-interactive
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Utilize o seguinte comando para verificar se o *AllowBastionHost* funcionalidade está registrada com a sua subscrição:

    ```azurepowershell-interactive
    Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Pode demorar alguns minutos para o registo concluir.