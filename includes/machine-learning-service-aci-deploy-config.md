---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: 6e2a3a75181cf381f09e06b52c9bb3928dee4896
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556849"
---
As entradas no `deploymentconfig.json` documento são mapeadas para os parâmetros de [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | ND | O destino de computação. Para ACI, o valor deve ser `ACI`. |
| `containerResourceRequirements` | ND | Contêiner para as entidades de CPU e memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a serem alocados. Padrões`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a ser alocada para este serviço Web. Os`0.5` |
| `location` | `location` | A região do Azure para a qual implantar este WebService. Se não for especificado, o local do espaço de trabalho será usado. Mais detalhes sobre as regiões disponíveis podem ser encontrados aqui: [Regiões ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Se a autenticação deve ser habilitada para este WebService. O padrão é false |
| `sslEnabled` | `ssl_enabled` | Se o SSL deve ser habilitado para este WebService. O padrão é false. |
| `appInsightsEnabled` | `enable_app_insights` | Se deseja habilitar AppInsights para este WebService. O padrão é false |
| `sslCertificate` | `ssl_cert_pem_file` | O arquivo de certificado necessário se o SSL estiver habilitado |
| `sslKey` | `ssl_key_pem_file` | O arquivo de chave necessário se o SSL estiver habilitado |
| `cname` | `ssl_cname` | O CNAME para se o SSL estiver habilitado |
| `dnsNameLabel` | `dns_name_label` | O rótulo de nome DNS para o ponto de extremidade de pontuação. Se não for especificado, um rótulo de nome DNS exclusivo será gerado para o ponto de extremidade de pontuação. |

O JSON a seguir é um exemplo de configuração de implantação para uso com a CLI:

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