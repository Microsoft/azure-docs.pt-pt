---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503470"
---
Os seguintes comandos do CLI do Azure irão aprovisionar um recurso de detetor de anomalias no escalão de preço gratuito. Clique nas **experimentar** botão, cole o código e prima enter para executá-la no Azure cloud shell. Ele imprimirá as suas chaves para autenticar a sua aplicação. Depois de terminar, [criar uma variável de ambiente](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) por seu qualquer das suas chaves, com o nome `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * Opcionalmente, pode:
>    * criar um recurso com o [portal do Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md), ou [CLI do Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) no seu computador local.
>    * obter um [avaliação chave](https://azure.microsoft.com/try/cognitive-services/#decision) válido durante sete dias gratuitamente. Depois de inscrever-se irão estar disponível no [Web site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
>    * ver este recurso sobre o [portal do Azure](https://portal.azure.com/). 

Os serviços cognitivos são representados por recursos do Azure que for aprovisionado. Cada conta dos serviços cognitivos (e seu recurso do Azure associado) têm de pertencer a um grupo de recursos do Azure.

1. Criar um grupo de recursos do Azure

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Criar um recurso de detetor de anomalias no escalão gratuito

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Listar as chaves para o seu recurso

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```