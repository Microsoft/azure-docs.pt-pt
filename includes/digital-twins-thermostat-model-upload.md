---
author: baanders
description: incluir ficheiro para carregar um modelo para a azure digital twins instância
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950611"
---
O modelo tem o seguinte aspeto:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Para **fazer o upload deste modelo para a sua instância gémea,** executar o seguinte comando Azure CLI, que carrega o modelo acima como JSON em linha. Pode executar o comando em [Azure Cloud Shell](../articles/cloud-shell/overview.md) no seu navegador ou na sua máquina se tiver o CLI [instalado localmente.](/cli/azure/install-azure-cli)

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Se estiver a utilizar a Cloud Shell no ambiente PowerShell, poderá ter de escapar aos caracteres da marcação de citação nos campos JSON em linha para que os seus valores sejam corretamente analisados. Aqui está o comando para carregar o modelo com esta modificação:
>
> Modelo de upload:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```