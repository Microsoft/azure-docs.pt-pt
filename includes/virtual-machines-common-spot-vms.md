---
title: incluir ficheiro
description: incluir ficheiro
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d56964b7415e4ca5903950cd46c02b3c27f62d5e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547417"
---
A utilização de VMs spot permite-lhe tirar partido da nossa capacidade não utilizada com uma poupança significativa de custos. A qualquer momento em que o Azure precise de capacidade de volta, a infraestrutura Azure despejará VMs spot. Por isso, os VMs spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes de v/teste, grandes cargas de trabalho de computação, e muito mais.

A quantidade de capacidade disponível pode variar em função do tamanho, região, hora do dia e muito mais. Ao implementar VMs spot, o Azure atribuirá os VMs se houver capacidade disponível, mas não existe SLA para estes VMs. Um Spot VM não oferece garantias de alta disponibilidade. A qualquer momento em que o Azure precise de capacidade de volta, a infraestrutura Azure despeja VMs spot com aviso prévio de 30 segundos. 


## <a name="eviction-policy"></a>Política de despejo

Os VMs podem ser despejados com base na capacidade ou no preço máximo que estabelece. Para máquinas virtuais, a política de despejo está definida para *desalojar* o que move os seus VMs despejados para o estado de paradisíaco, permitindo-lhe recolocar os VMs despejados mais tarde. No entanto, a relocalização de VMs spot será dependente da possibilidade de existir capacidade spot disponível. Os VMs deallocated contarão com a sua quota spot vCPU e você será cobrado pelos seus discos subjacentes. 

Os utilizadores podem optar por receber notificações in-VM através de [Eventos Agendados do Azure.](../articles/virtual-machines/linux/scheduled-events.md) Isto irá notificá-lo se os seus VMs estão sendo despejados e você terá 30 segundos para terminar quaisquer trabalhos e executar tarefas de encerramento antes do despejo. 


| Opção | Resultado |
|--------|---------|
| O preço máximo está definido para >= o preço atual. | A VM é implantada se a capacidade e a quota estiverem disponíveis. |
| O preço máximo está definido para < o preço atual. | O VM não está implantado. Receberá uma mensagem de erro de que o preço máximo precisa de ser >= preço atual. |
| Reiniciar um VM stop/desalocado se o preço máximo for >= o preço atual | Se houver capacidade e quota, então o VM é implantado. |
| Reiniciar um VM stop/desalocado se o preço máximo for < o preço atual | Receberá uma mensagem de erro de que o preço máximo precisa de ser >= preço atual. | 
| O preço do VM subiu e está agora > o preço máximo. | O VM é despejado. Recebes uma notificação dos anos 30 antes do despejo real. | 
| Após o despejo, o preço do VM volta a ser < o preço máximo. | O VM não será automaticamente reiniciado. Você pode reiniciar o VM por si mesmo, e será cobrado ao preço atual. |
| Se o preço máximo for definido para`-1` | O VM não será despejado por razões de preços. O preço máximo será o preço atual, até ao preço dos VMs padrão. Nunca será cobrado acima do preço normal.| 
| Alteração do preço máximo | Precisa de desalojar o VM para alterar o preço máximo. Deslocar o VM, definir um novo preço máximo e, em seguida, atualizar o VM. |

## <a name="limitations"></a>Limitações

Os seguintes tamanhos vm não são suportados para VMs spot:
 - Série B
 - Versões promocionais de qualquer tamanho (como Dv2, NV, NC, H promo sizes)

Os VMs spot não podem atualmente usar discos efémeros.

Os VMs spot podem ser implantados em qualquer região, exceto o Microsoft Azure China 21Vianet.

## <a name="pricing"></a>Preços

Os preços dos VMs spot são variáveis, com base na região e no SKU. Para mais informações, consulte os preços vm para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Com preços variáveis, você tem opção de definir um preço máximo, em dólares americanos (USD), usando até 5 lugares decimais. Por exemplo, `0.98765`o valor seria um preço máximo de $0.98765 USD por hora. Se fixar o preço `-1`máximo para ser, o VM não será despejado com base no preço. O preço do VM será o preço atual para o local ou o preço de um VM padrão, o que sempre é menor, desde que haja capacidade e quota disponíveis.


##  <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q:** Uma vez criado, um Spot VM é o mesmo que o VM padrão normal?

**A:** Sim, exceto que não há SLA para VMs spot e eles podem ser despejados a qualquer momento.


**Q:** O que fazer quando é despejado, mas ainda precisa de capacidade?

**A:** Recomendamos que utilize VMs padrão em vez de VMs spot se precisar de capacidade imediatamente.


**Q:** Como é gerida a quota para VMs spot?

**A:** Os Spot VMs terão um pool de quotas separado. A quota spot será partilhada entre VMs e instâncias de escala. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Q:** Posso pedir quota adicional para o Spot?

**A:** Sim, poderá submeter o pedido de aumento da sua quota para VMs spot através do processo padrão de pedido de [quotas.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**Q:** Que canais suportam Os VMs spot?

**A:** Consulte a tabela abaixo para obter disponibilidade de Spot VM.

<a name="channel"></a>

| Canais Azure               | Disponibilidade de VMs spot azure       |
|------------------------------|-----------------------------------|
| Contrato Enterprise         | Sim                               |
| Pay As You Go                | Sim                               |
| Provedor de Serviço sinuoso (CSP) | [Contacte o seu parceiro](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Vantagens                     | Não disponível                     |
| Patrocinado                    | Não disponível                     |
| Avaliação Gratuita                   | Não disponível                     |


**Q:** Onde posso fazer perguntas?

**A:** Pode publicar e marcar `azure-spot` a sua pergunta no [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



