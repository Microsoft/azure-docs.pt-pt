---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025789"
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