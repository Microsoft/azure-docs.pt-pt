---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384837"
---
| Recurso | Limite |
| --- | :--- |
| Grupos de contentores padrão de sku por região por subscrição | 100<sup>1</sup> |
| Grupos dedicados de contentores sku por região por subscrição | 0<sup>1</sup> |
| Número de contentores por grupo de contentores | 60 |
| Número de volumes por grupo de contentores | 20 |
| Núcleos de sku padrão (CPUs) por região por subscrição | 10<sup>1,2</sup> | 
| Núcleos de sku standard (CPUs) para K80 GPU por região por subscrição | 18<sup>1,2</sup> |
| Núcleos de sku padrão (CPUs) para GPU P100 ou V100 por região por subscrição | 0<sup>1,2</sup> |
| Portas por IP | 5 |
| Tamanho do registo de instância do contentor - instância de execução | 4 MB |
| Tamanho do registo de caso de contentor - instância interrompida | 16 KB ou 1.000 linhas |
| Contentor cria por hora |300<sup>1</sup> |
| Contentor cria por 5 minutos | 100<sup>1</sup> |
| Contentor elimina por hora | 300<sup>1</sup> |
| Contentor elimina por 5 minutos | 100<sup>1</sup> |


<sup>1</sup> Para solicitar um aumento de limite, crie um [pedido de Suporte Azure][azure-support]. As subscrições gratuitas, incluindo [a Conta Livre Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) e [a Azure para Estudantes,](https://azure.microsoft.com/offers/ms-azr-0170p/) não são elegíveis para aumentos de limites ou quotas. Se tiver uma subscrição gratuita, pode [fazer upgrade](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) para uma subscrição Pay-As-You-Go.<br />
<sup>2</sup> Limite predefinido para [subscrição Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) O limite pode diferir para outros tipos de categorias.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
