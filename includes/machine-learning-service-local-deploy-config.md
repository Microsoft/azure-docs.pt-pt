---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508100"
---
As entradas no mapa do `deploymentconfig.json` documento aos parâmetros para [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration). A tabela a seguir descreve o mapeamento entre as entidades do documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Description |
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