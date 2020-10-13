---
author: baanders
description: incluir arquivo para Azure Digital Twins - configurar a mais recente extensão IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606482"
---
Primeiro, faça este comando para ver uma lista de todas as extensões que já instalou.

```azurecli
az extension list
```

A saída é um conjunto de todas as extensões que você tem atualmente. Procure o `"name"` campo para cada entrada na lista para ver os nomes das extensões.

Utilize a saída para determinar quais dos seguintes comandos a executar para a configuração da extensão (pode executar mais de um).
* Se a lista `azure-iot` contiver: Já tem a extensão. Execute este comando para se certificar de que tem a última atualização e não há mais atualizações disponíveis:

   ```azurecli
   az extension update --name azure-iot
   ```

* Se a lista **não** `azure-iot` contiver: É necessário instalar a extensão. Utilize este comando:

    ```azurecli
    az extension add --name azure-iot
    ```

* Se a lista `azure-iot-cli-ext` contiver: Esta é a versão antiga da extensão. Apenas uma versão da extensão deve ser instalada de cada vez, pelo que deve desinstalar a extensão do legado. Utilize este comando:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```