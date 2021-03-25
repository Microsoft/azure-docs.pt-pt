---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 91f72117fdbcdbeda1d906a9760243e66404920c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105509"
---
| Recurso |[Plano de consumo](../articles/azure-functions/consumption-plan.md)|[Plano Premium](../articles/azure-functions/functions-premium-plan.md)|[Plano dedicado](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Duração [do tempo limite](../articles/azure-functions/functions-scale.md#timeout) padrão (min) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|Duração [máxima do tempo limite](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | ilimitado<sup>7</sup> | ilimitado<sup>2</sup> | desvinculado | desvinculado |
| Ligações de saída máxima (por exemplo) | 600 ativos (1200 no total) | desvinculado | desvinculado | desvinculado | desvinculado |
| Tamanho do pedido máximo (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Depende do cluster |
| Comprimento da corda de consulta máxima<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Depende do cluster |
| Pedido máximo comprimento URL<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Depende do cluster |
|[ACU](../articles/virtual-machines/acu.md) por exemplo | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Preços AKS](https://azure.microsoft.com/pricing/details/container-service/) |
| Memória máxima (GB por exemplo) | 1.5 | 3.5-14 | 1.75-14 | 3.5 - 14 | Qualquer nó é suportado |
| Contagem de instâncias max | 200 | 100<sup>9</sup> | varia por SKU<sup>10</sup> | 100<sup>10</sup> | Depende do cluster |   
| Aplicativos de função por plano |100 |100 |ilimitado<sup>4</sup> | desvinculado | desvinculado |
| [Planos do Serviço de Aplicações](../articles/app-service/overview-hosting-plans.md) | 100 por [região](https://azure.microsoft.com/global-infrastructure/regions/) |100 por grupo de recursos |100 por grupo de recursos | - | - |
| Armazenamento<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | n/a |
| Domínios personalizados por app</a> |500<sup>6</sup> |500 |500 | 500 | n/a |
| Suporte [SSL](../articles/app-service/configure-ssl-bindings.md) de domínio personalizado |ligação SNI SSL ilimitada incluída | Conexões SNI SSL e 1 IP SSL não limitadas incluídas |Conexões SNI SSL e 1 IP SSL não limitadas incluídas | Conexões SNI SSL e 1 IP SSL não limitadas incluídas | n/a |

<sup>1</sup> Por defeito, o tempo limite para as funções 1.x tempo de funcionamento num plano de Serviço de Aplicações não é limitado.  
<sup>2</sup> Requer que o plano de Serviço de Aplicações seja definido para [Always On](../articles/azure-functions/dedicated-plan.md#always-on). Pague a [preços](https://azure.microsoft.com/pricing/details/app-service/)padrão.  
<sup>3</sup> Estes limites são [definidos no hospedeiro](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> O número real de aplicações de função que pode hospedar depende da atividade das aplicações, do tamanho das instâncias da máquina e da utilização correspondente do recurso.  
<sup>5</sup> O limite de armazenamento é o tamanho total do conteúdo em armazenamento temporário em todas as aplicações no mesmo plano de Serviço de Aplicações. O plano de consumo utiliza ficheiros Azure para armazenamento temporário.  
<sup>6</sup> Quando a sua aplicação de função está hospedada num [plano de Consumo,](../articles/azure-functions/consumption-plan.md)apenas a opção CNAME é suportada. Para aplicações de função num [plano Premium](../articles/azure-functions/functions-premium-plan.md) ou num plano de Serviço [de Aplicações,](../articles/azure-functions/dedicated-plan.md)pode mapear um domínio personalizado utilizando um CNAME ou um registo A.  
<sup>7</sup> Garantido por até 60 minutos.  
<sup>8</sup> Trabalhadores são funções que acolhem aplicações de clientes. Os trabalhadores estão disponíveis em três tamanhos fixos: Um vCPU/3,5 GB de RAM; Dois vCPU/7 GB de RAM; Quatro vCPU/14 GB RAM.   
<sup>9</sup> Ao correr no Linux num plano Premium, está limitado a 20 instâncias.  
<sup>10</sup> Consulte [os limites do Serviço de Aplicações](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) para mais detalhes.