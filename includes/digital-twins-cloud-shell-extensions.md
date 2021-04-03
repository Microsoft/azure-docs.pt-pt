---
author: baanders
description: incluir arquivo para Azure Digital Twins - configurar a mais recente extensão IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90606482"
---
Primeiro, faça este comando para ver uma lista de todas as extensões que já instalou.

```azurecli-interactive
az extension list
```

A saída é um conjunto de todas as extensões que você tem atualmente. Procure o `"name"` campo para cada entrada na lista para ver os nomes das extensões.

Utilize a saída para determinar quais dos seguintes comandos a executar para a configuração da extensão (pode executar mais de um).
* Se a lista `azure-iot` contiver: Já tem a extensão. Execute este comando para se certificar de que tem a última atualização e não há mais atualizações disponíveis:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Se a lista **não** `azure-iot` contiver: É necessário instalar a extensão. Utilize este comando:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Se a lista `azure-iot-cli-ext` contiver: Esta é a versão antiga da extensão. Apenas uma versão da extensão deve ser instalada de cada vez, pelo que deve desinstalar a extensão do legado. Utilize este comando:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```