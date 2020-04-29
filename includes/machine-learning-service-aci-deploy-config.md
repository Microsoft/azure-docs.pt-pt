---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79486039"
---
As entradas `deploymentconfig.json` no mapa de documentos para os parâmetros de [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). A tabela seguinte descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `computeType` | ND | O destino de computação. Para a ACI, `ACI`o valor deve ser . |
| `containerResourceRequirements` | ND | Contentor para a CPU e entidades de memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a atribuir. Incumprimentos,`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a atribuir para este serviço web. Padrão,`0.5` |
| `location` | `location` | A região de Azure para implantar este Serviço Web para. Se não especificado, será utilizado o local do espaço de trabalho. Mais detalhes sobre as regiões disponíveis podem ser consultados aqui: [Regiões ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Se permitir auth para este Serviço Web. Incumprimentos a Falsos |
| `sslEnabled` | `ssl_enabled` | Se permite o SSL para este Serviço Web. Incumprimentos a Falso. |
| `appInsightsEnabled` | `enable_app_insights` | Se permite o AppInsights para este Serviço Web. Incumprimentos a Falsos |
| `sslCertificate` | `ssl_cert_pem_file` | O ficheiro cert necessário se o SSL estiver ativado |
| `sslKey` | `ssl_key_pem_file` | O ficheiro chave necessário se o SSL estiver ativado |
| `cname` | `ssl_cname` | O nome para se o SSL estiver ativado |
| `dnsNameLabel` | `dns_name_label` | A etiqueta de nome dns para o ponto final de pontuação. Se não especificado, será gerada uma etiqueta de nome dns única para o ponto final de pontuação. |

O JSON seguinte é uma configuração de implementação de exemplo para utilização com o CLI:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```