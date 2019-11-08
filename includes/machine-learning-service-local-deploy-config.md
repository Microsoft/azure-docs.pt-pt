---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: f35245aea0d7ba20561d1504b111e747ccbe5e0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800048"
---
As entradas no documento `deploymentconfig.json` são mapeadas para os parâmetros de [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

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
