---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4983c1e1e7f235fa7a5b748a0ce5b1c79176c849
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511128"
---
As entradas no mapa do `deploymentconfig.json` documento aos parâmetros para [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration). A tabela a seguir descreve o mapeamento entre as entidades do documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | ND | O destino de computação. Para a ACI, o valor deve `ACI` ser. |
| `containerResourceRequirements` | ND | Contentor para a CPU e entidades de memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a atribuir. Predefinições, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a atribuir a este serviço web. Predefinição, `0.5` |
| `location` | `location` | A região de Azure para implantar este Webservice para. Se não for especificado, a localização do espaço de trabalho será utilizada. Mais detalhes sobre as regiões disponíveis podem ser consultados aqui: [Regiões ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Se permitir auth para este Webservice. Incumprimentos a Falsos |
| `sslEnabled` | `ssl_enabled` | Se habilita o SSL para este Webservice. Incumprimentos ao Falso. |
| `appInsightsEnabled` | `enable_app_insights` | Se ativar o AppInsights para este Webservice. Incumprimentos a Falsos |
| `sslCertificate` | `ssl_cert_pem_file` | O ficheiro cert necessário se o SSL estiver ativado |
| `sslKey` | `ssl_key_pem_file` | O ficheiro-chave necessário se o SSL estiver ativado |
| `cname` | `ssl_cname` | O cname para se o SSL está habilitado |
| `dnsNameLabel` | `dns_name_label` | A etiqueta de nome DNS para o ponto final de pontuação. Se não for especificada, será gerada uma etiqueta de nome dns única para o ponto final de pontuação. |

O seguinte JSON é uma configuração de implementação de exemplo para utilização com o CLI:

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