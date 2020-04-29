---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77198348"
---
| Recurso | [Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) | Plano de [Serviço de Aplicações](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Aumentar horizontalmente | Orientada por eventos | Orientada por eventos | [Manual/escala automática](../articles/app-service/manage-scale-up.md) | 
| Casos max | 200 | 100 | 10-20 |
|Duração do tempo de [paragem](../articles/azure-functions/functions-scale.md#timeout) por defeito (min) |5 | 30 |30<sup>2</sup> |
|Duração máxima [do tempo](../articles/azure-functions/functions-scale.md#timeout) limite (min) |10 | ilimitada<sup>8</sup> | sem<sup>limites 3</sup> |
| Ligações de saída max (por exemplo) | 600 ativos (1200 no total) | sem limites | sem limites |
| Tamanho máximo do pedido (MB)<sup>4</sup> | 100 | 100 | 100 |
| Comprimento de corda de consulta máxima<sup>4</sup> | 4096 | 4096 | 4096 |
| Comprimento de URL de pedido máximo<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) por instância | 100 | 210-840 | 100-840 |
| Memória máxima (GB por exemplo) | 1.5 | 3.5-14 | 1.75-14 |
| Aplicativos de função por plano |100 |100 |ilimitada<sup>5</sup> |
| [Planos do Serviço de Aplicações](../articles/app-service/overview-hosting-plans.md) | 100 por [região](https://azure.microsoft.com/global-infrastructure/regions/) |100 por grupo de recursos |100 por grupo de recursos |
| Armazenamento<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Domínios personalizados por app</a> |500<sup>7</sup> |500 |500 |
| Suporte [sSL](../articles/app-service/configure-ssl-bindings.md) de domínio personalizado |Conexão SNI SSL não limitada incluída | Conexões SNI SSL e 1 IP SSL incluídas |Conexões SNI SSL e 1 IP SSL incluídas | 

<sup>1</sup> Para limites específicos para as várias opções do plano de app service, consulte os limites do [plano do Serviço de Aplicações](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> Por predefinição, o tempo limite para as Funções 1.x tempo de execução num plano de Serviço de Aplicações não está limitado.  
<sup>3</sup> Requer que o plano de serviço de aplicações seja definido para [sempre ligado](../articles/azure-functions/functions-scale.md#always-on). Pague a [preços](https://azure.microsoft.com/pricing/details/app-service/)padrão.  
<sup>4</sup> Estes limites estão [fixados no hospedeiro](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> O número real de aplicações de função que pode acolher depende da atividade das aplicações, do tamanho das instâncias da máquina e da utilização de recursos correspondentes.  
<sup>6</sup> O limite de armazenamento é o tamanho total do conteúdo em armazenamento temporário em todas as aplicações no mesmo plano de Serviço de Aplicações. O plano de consumo utiliza ficheiros Azure para armazenamento temporário.  
<sup>7</sup> Quando a sua aplicação de funções estiver hospedada num plano de [consumo,](../articles/azure-functions/functions-scale.md#consumption-plan)apenas a opção CNAME é suportada. Para aplicações de função num [plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) ou num plano de Serviço de [Aplicações,](../articles/azure-functions/functions-scale.md#app-service-plan)pode mapear um domínio personalizado usando um CNAME ou um registo A.  
<sup>8</sup> Garantido até 60 minutos.