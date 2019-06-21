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
ms.openlocfilehash: da4177fd54c0d8777f15175cea3a74a8b01c0954
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305318"
---
1. Certifique-se de que tem sessão iniciada na sua conta do Azure e estiver a utilizar a subscrição que pretende carregar para esta pré-visualização. Utilize o seguinte exemplo de inscrição:

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Voltar a registar a sua subscrição mais uma vez com o *Network* espaço de nomes do fornecedor.

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Utilize o seguinte comando para verificar se o *AllowBastionHost* funcionalidade está registrada com a sua subscrição:

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Pode demorar alguns minutos para o registo concluir.
