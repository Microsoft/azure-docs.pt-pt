---
author: baanders
description: incluir ficheiro para carregar um modelo para a azure digital twins instância
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304307"
---
O modelo tem o seguinte aspeto:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Para **fazer o upload deste modelo para a sua instância gémea,** executar o seguinte comando Azure CLI, que carrega o modelo acima como JSON em linha. Pode executar o comando em [Azure Cloud Shell](../articles/cloud-shell/overview.md) no seu browser (use o ambiente **Bash)** ou na sua máquina se tiver o CLI [instalado localmente.](/cli/azure/install-azure-cli)

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```