---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563680"
---
As entradas no mapa do `deploymentconfig.json` documento aos parâmetros para [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades do documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | ND | O destino de computação. Para os alvos locais, o valor deve `local` ser. |
| `port` | `port` | O porto local para expor o ponto final HTTP do serviço. |

Este JSON é uma configuração de implementação de exemplo para utilização com o CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```