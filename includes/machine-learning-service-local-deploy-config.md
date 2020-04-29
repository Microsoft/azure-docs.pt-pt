---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477277"
---
As entradas `deploymentconfig.json` no mapa de documentos para os parâmetros para [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A tabela seguinte descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `computeType` | ND | O destino de computação. Para os alvos locais, `local`o valor deve ser . |
| `port` | `port` | O porto local para expor o ponto final http do serviço. |

Este JSON é uma configuração de implementação de exemplo para utilização com o CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
