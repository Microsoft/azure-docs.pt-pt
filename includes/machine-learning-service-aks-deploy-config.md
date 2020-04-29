---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79485920"
---
As entradas `deploymentconfig.json` no mapa de documentos para os parâmetros de [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). A tabela seguinte descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `computeType` | ND | O destino de computação. Para a AKS, `aks`o valor deve ser. |
| `autoScaler` | ND | Contém elementos de configuração para escala automática. Veja a tabela de autoescaladores. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Se permite a autodimensionamento para o serviço web. `numReplicas`  =  `0`Se, `True`; caso contrário, `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | O número mínimo de contentores a utilizar ao autoscalcificar este serviço web. Padrão, `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | O número máximo de contentores a utilizar ao autoscalcificar este serviço web. Padrão, `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Quantas vezes o autoscaler tenta escalar este serviço web. Padrão, `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A utilização do alvo (em percentagem de 100) que o autoscaler deve tentar manter para este serviço web. Padrão, `70`. |
| `dataCollection` | ND | Contém elementos de configuração para recolha de dados. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Se permite a recolha de dados do modelo para o serviço web. Padrão, `False`. |
| `authEnabled` | `auth_enabled` | Permitir ou não a autenticação chave para o serviço web. Ambos `tokenAuthEnabled` `authEnabled` e `True`não podem ser. Padrão, `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Permitir ou não a autenticação simbólica para o serviço web. Ambos `tokenAuthEnabled` `authEnabled` e `True`não podem ser. Padrão, `False`. |
| `containerResourceRequirements` | ND | Contentor para a CPU e entidades de memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a atribuir para este serviço web. Incumprimentos,`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a atribuir para este serviço web. Padrão,`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Se permite o registo de Insights de Aplicação para o serviço web. Padrão, `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Um tempo para impor as chamadas para o serviço web. Padrão, `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Os pedidos máximos simultâneos por nó para este serviço web. Padrão, `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | O tempo máximo que um pedido permanecerá na fila de tee (em milissegundos) antes de um erro de 503 ser devolvido. Padrão, `500`. |
| `numReplicas` | `num_replicas` | O número de contentores a atribuir para este serviço web. Não existem valores predefinidos. Se este parâmetro não estiver definido, o esbanjadores automáticos está ativado por defeito. |
| `keys` | ND | Contém elementos de configuração para teclas. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Uma chave principal auth para usar para este Serviço Web |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Uma chave secundária auth para usar para este Serviço Web |
| `gpuCores` | `gpu_cores` | O número de núcleos de GPU (réplica por contentor) para alocar para este Serviço Web. O padrão é 1. Só suporta valores de número inteiros. |
| `livenessProbeRequirements` | ND | Contém elementos de configuração para requisitos de sonda de vivacidade. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Quantas vezes (em segundos) para executar a sonda de vivacidade. Padrão para 10 segundos. O valor mínimo é 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Número de segundos após o início do recipiente antes de as sondas de vivacidade serem iniciadas. Incumprimentos para 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Número de segundos após o qual a sonda de vivacidade sai. Predefinidos a 2 segundos. O valor mínimo é 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Os mínimos sucessos consecutivos para a sonda de vivacidade ser considerado bem sucedido depois de terem falhado. Incumprimentos para 1. O valor mínimo é 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando um Pod começa e a sonda de vivacidade falha, os Kubernetes tentarão falhar os tempos limiar antes de desistir. Incumprimentos a 3. O valor mínimo é 1. |
| `namespace` | `namespace` | O espaço de nome Kubernetes em que o serviço web é implantado. Até 63 caracteres alfanuméricos minúsculos ('a'-'z', '0'-'9') e caracteres de hífen ('-').". O primeiro e último caracteres não podem ser hífenes. |

O JSON seguinte é uma configuração de implementação de exemplo para utilização com o CLI:

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
