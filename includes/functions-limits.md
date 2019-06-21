---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: f2470f937d2d812bf79cea3c23d89a50717a5a92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277402"
---
| Resource | [Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plano premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plano do App Service](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Aumentar horizontalmente | Condicionada por eventos | Condicionada por eventos | [Manual/autoscale](../articles/app-service/web-sites-scale.md) | 
| Máximo de instâncias | 200 | 20 | 10-20 |
|Predefinido [duração de tempo limite](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Max [duração de tempo limite](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | não vinculado | não vinculado<sup>3</sup> |
| Máx. ligações de saída (por instância) | 600 Active Directory (total de 1200) | não vinculado | não vinculado |
| Tamanho máximo do pedido (MB)<sup>4</sup> | 100 | 100 | 100 |
| Comprimento máximo da cadeia de caracteres de consulta<sup>4</sup> | 4096 | 4096 | 4096 |
| Comprimento máximo do URL de pedido<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) por instância | 100 | 210-840 | 100-840 |
| Máx. de memória (GB por instância) | 1.5 | 3.5-14 | 1.75-14 |
| Aplicações de função por plano |100 |100 |unbounded<sup>5</sup> |
| [Planos do Serviço de Aplicações](../articles/app-service/overview-hosting-plans.md) | 100 por [região](https://azure.microsoft.com/global-infrastructure/regions/) |100 por grupo de recursos |100 por grupo de recursos |
| Armazenamento<sup>6</sup> |1 GB |250 GB |1000 DE 50 GB |
| Domínios personalizados por aplicação</a> |500<sup>7</sup> |500 |500 |
| Domínio personalizado [suporte SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |não vinculada ligação de SNI SSL incluída | não vinculado SNI SSL e 1 ligações de IP SSL incluídos |não vinculado SNI SSL e 1 ligações de IP SSL incluídos | 

<sup>1</sup> para obter limites específicos para as várias opções de plano de serviço de aplicações, consulte a [limites do plano de serviço de aplicações](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> por predefinição, o tempo limite para o runtime 1.x de funções num plano do serviço de aplicações é não vinculado.  
<sup>3</sup> requer o plano do serviço de aplicações de ser definido como [Always On](../articles/azure-functions/functions-scale.md#always-on). Pagar à norma [taxas](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> estes limites são [definido no anfitrião](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> o número real de aplicações de funções que pode alojar depende da atividade das aplicações, o tamanho das instâncias da máquina e a utilização de recursos correspondente.
<sup>6</sup> o limite de armazenamento é o tamanho total do conteúdo no armazenamento temporário em todas as aplicações no mesmo plano de serviço de aplicações. Plano de consumo utiliza ficheiros do Azure para armazenamento temporário.  
<sup>7</sup> quando a aplicação function app está hospedada num [plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan), apenas a opção de CNAME é suportada. Para aplicações de funções numa [plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) ou uma [plano do App Service](../articles/azure-functions/functions-scale.md#app-service-plan), pode mapear um domínio personalizado com um CNAME ou um registo.
