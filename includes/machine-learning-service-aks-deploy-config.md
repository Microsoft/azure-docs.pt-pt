---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e17f14d3acf8d74d1715d14fbd914ee536d29931
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102511127"
---
As entradas no mapa do `deploymentconfig.json` documento aos parâmetros para [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration). A tabela a seguir descreve o mapeamento entre as entidades do documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Description |
| ----- | ----- | ----- |
| `computeType` | ND | O destino de computação. Para a AKS, o valor deve `aks` ser. |
| `autoScaler` | ND | Contém elementos de configuração para autoescala. Consulte a tabela de autoescaladores. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Se permitir a autoscalagem para o serviço web. Se, `numReplicas`  =  `0` de `True` outra forma, . . `False` . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | O número mínimo de recipientes a utilizar ao fazer a autoscalagem deste serviço web. Predefinição, `1` . |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | O número máximo de recipientes a utilizar ao fazer a autoscalagem deste serviço web. Predefinição, `10` . |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Quantas vezes o autoescalador tenta escalar este serviço web. Predefinição, `1` . |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A utilização do alvo (em percentagem em 100) que o autoscaler deve tentar manter para este serviço web. Predefinição, `70` . |
| `dataCollection` | ND | Contém elementos de configuração para a recolha de dados. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Se permitir a recolha de dados de modelos para o serviço web. Predefinição, `False` . |
| `authEnabled` | `auth_enabled` | Se deve ou não permitir a autenticação da chave para o serviço web. Ambos `tokenAuthEnabled` e não podem `authEnabled` `True` ser. Predefinição, `True` . |
| `tokenAuthEnabled` | `token_auth_enabled` | Quer permita ou não a autenticação simbólica para o serviço web. Ambos `tokenAuthEnabled` e não podem `authEnabled` `True` ser. Predefinição, `False` . |
| `containerResourceRequirements` | ND | Contentor para a CPU e entidades de memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a atribuir a este serviço web. Predefinições, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a atribuir a este serviço web. Predefinição, `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Se ativar o registo de Insights de Aplicação para o serviço web. Predefinição, `False` . |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Um tempo limite para impor chamadas para o serviço web. Predefinição, `60000` . |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Os pedidos simultâneos máximos por nó para este serviço web. Predefinição, `1` . |
| `maxQueueWaitMs` | `max_request_wait_time` | O tempo máximo de um pedido permanecerá na fila de teia (em milissegundos) antes de um erro de 503 ser devolvido. Predefinição, `500` . |
| `numReplicas` | `num_replicas` | O número de contentores a atribuir para este serviço web. Não existem valores predefinidos. Se este parâmetro não estiver definido, o autoescalador é ativado por predefinição. |
| `keys` | ND | Contém elementos de configuração para as teclas. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Uma chave primária para usar para este Webservice |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Uma chave secundária para usar para este Webservice |
| `gpuCores` | `gpu_cores` | O número de núcleos de GPU (réplica por contentor) para alocar para este Webservice. O padrão é 1. Suporta apenas valores de números inteiros. |
| `livenessProbeRequirements` | ND | Contém elementos de configuração para requisitos de sonda de vivacidade. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Quantas vezes (em segundos) para executar a sonda de vida. Predefinição a 10 segundos. O valor mínimo é 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Número de segundos após o início do recipiente antes de serem iniciadas as sondas de vida. Incumprimentos para 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Número de segundos após o qual a sonda de vida se esgotar. Predefinições a 2 segundos. O valor mínimo é 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Mínimos de sucesso consecutivos para a sonda de vida ser considerada bem sucedida depois de ter falhado. Incumprimentos para 1. O valor mínimo é 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando um Pod começa e a sonda de vida falha, kubernetes tentará falhar Tempos de retenção antes de desistir. Incumprimentos para 3. O valor mínimo é 1. |
| `namespace` | `namespace` | O espaço de nome Kubernetes em que o webservice é implantado. Até 63 caracteres alfanuméricos minúsculos ('a'z', '0'-9') e caracteres hífen ('-'). Os primeiros e últimos caracteres não podem ser hífenes. |

O seguinte JSON é uma configuração de implementação de exemplo para utilização com o CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```