---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118789"
---
### <a name="cacheskuname"></a>cacheSKUName

O escalão de preço da Cache do Azure nova para Redis.

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

O modelo define os valores que são permitidos para este parâmetro (Basic, Standard ou Premium) e atribui um valor predefinido (básico), se for especificado nenhum valor. Basic fornece um único nó com vários tamanhos disponíveis se a 53 GB. Standard fornece dois nós primário/réplica com vários tamanhos disponíveis de cópia de segurança para o SLA de 53 GB e 99,9%.

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

O tamanho da Cache do Azure nova para a instância de Redis.

Para as famílias de básico e Standard:

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

A capacidade de cache do valor de Premium é definida a mesma, exceto os valores permitidos executar a partir de 1 a 5 em vez de 0 a 6.

O modelo define os valores de número inteiro que têm permissão para este parâmetro (0 a 6 para as famílias de básico e Standard; 1 a 5 para a família de Premium). Se for especificado nenhum valor, o modelo atribui um valor padrão de 0 para básico e Standard, 1 para Premium.

Os valores correspondem aos seguintes tamanhos de cache:

| Value | Básico e Standard<br>Tamanho da cache | Premium<br>Tamanho da cache |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (predefinição)                 | n/d                   |
| 1     | 1 GB                             | 6 GB (predefinição)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/d                   |
