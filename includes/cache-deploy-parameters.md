---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184068"
---
### <a name="cacheskuname"></a>cacheSKUName

O nível de preços do novo Azure Cache para redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

O modelo define os valores que são permitidos para este parâmetro (Básico, Standard ou Premium), e atribui um valor predefinido (Básico) se não for especificado qualquer valor. Basic fornece um único nó com vários tamanhos disponíveis até 53 GB. A Standard fornece dois nós Primário/Réplica com vários tamanhos disponíveis até 53 GB e 99,9% SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

A família para o sku.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

O tamanho do novo Azure Cache para o caso Redis.

Para as famílias Básicas e Standard:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

A capacidade de cache de valor Premium é definida da mesma forma, com exceção dos valores permitidos que vão de 1 a 5 em vez de 0 a 6.

O modelo define os valores inteiros que são permitidos para este parâmetro (0 a 6 para as famílias Básicas e Standard; 1 a 5 para a família Premium). Se não for especificado qualquer valor, o modelo atribui um valor predefinido de 0 para Básico e Standard, 1 para Premium.

Os valores correspondem aos seguintes tamanhos de cache:

| Valor | Básico e Standard<br>tamanho cache | Premium<br>tamanho cache |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (padrão)                 | n/d                   |
| 1     | 1 GB                             | 6 GB (padrão)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/d                   |
