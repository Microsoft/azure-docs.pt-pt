---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: eb61bf5734d0aea1534735b23a2b95b52000f5ad
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322912"
---
| Resource | [Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plano de serviço de aplicativo](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Aumentar horizontalmente | Controlado por evento | Controlado por evento | [Manual/dimensionamento automático](../articles/app-service/manage-scale-up.md) | 
| Máximo de instâncias | 200 | 100 | 10-20 |
|[Duração do tempo limite](../articles/azure-functions/functions-scale.md#timeout) padrão (min) |5 | 30 |30<sup>2</sup> |
|Duração máxima de [tempo limite](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | 60 | Não associado<sup>3</sup> |
| Máximo de conexões de saída (por instância) | 600 ativo (total de 1200) | unbounded | unbounded |
| Tamanho máximo da solicitação (MB)<sup>4</sup> | 100 | 100 | 100 |
| Tamanho máximo da cadeia de caracteres de consulta<sup>4</sup> | 4096 | 4096 | 4096 |
| Comprimento máximo da URL de solicitação<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) por instância | 100 | 210-840 | 100-840 |
| Memória máxima (GB por instância) | 1.5 | 3,5-14 | 1,75-14 |
| Aplicativos de funções por plano |100 |100 |Não associado<sup>5</sup> |
| [Planos do Serviço de Aplicações](../articles/app-service/overview-hosting-plans.md) | 100 por [região](https://azure.microsoft.com/global-infrastructure/regions/) |100 por grupo de recursos |100 por grupo de recursos |
| Armazenamento<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Domínios personalizados por aplicativo</a> |500<sup>7</sup> |500 |500 |
| Suporte a [SSL](../articles/app-service/configure-ssl-bindings.md) de domínio personalizado |conexão SSL SNI não vinculada incluída | conexões SSL SNI não associadas e 1 IP SSL incluídas |conexões SSL SNI não associadas e 1 IP SSL incluídas | 

<sup>1</sup> para limites específicos para as várias opções do plano do serviço de aplicativo, consulte os [limites do plano do serviço de aplicativo](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> por padrão, o tempo limite para o tempo de execução do Functions 1. x em um plano do serviço de aplicativo é não associado.  
<sup>3</sup> requer que o plano do serviço de aplicativo seja definido como [Always on](../articles/azure-functions/functions-scale.md#always-on). Pague com [tarifas](https://azure.microsoft.com/pricing/details/app-service/)padrão.  
<sup>4</sup> esses limites são [definidos no host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> o número real de aplicativos de funções que você pode hospedar depende da atividade dos aplicativos, do tamanho das instâncias de máquina e da utilização de recursos correspondente.  
<sup>6</sup> o limite de armazenamento é o tamanho total do conteúdo no armazenamento temporário em todos os aplicativos no mesmo plano do serviço de aplicativo. O plano de consumo usa os arquivos do Azure para armazenamento temporário.  
<sup>7</sup> quando seu aplicativo de funções está hospedado em um [plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan), somente a opção CNAME tem suporte. Para aplicativos de funções em um [plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) ou um [plano do serviço de aplicativo](../articles/azure-functions/functions-scale.md#app-service-plan), é possível mapear um domínio personalizado usando um registro CNAME ou um.
