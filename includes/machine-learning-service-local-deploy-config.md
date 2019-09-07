---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: ec0f85ff568bcd89d74ccd727d1c5ecfd8aab398
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390686"
---
As entradas no `deploymentconfig.json` documento são mapeadas para os parâmetros de [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | ND | O destino de computação. Para destinos locais, o valor deve ser `local`. |
| `port` | `port` | A porta local na qual expor o ponto de extremidade HTTP do serviço. |

Este JSON é um exemplo de configuração de implantação para uso com a CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
