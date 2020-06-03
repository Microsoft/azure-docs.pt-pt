---
title: incluir ficheiro
description: incluir ficheiro
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e7dbac1f4fad940b817befa3a45447cf7367c28c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317754"
---
A utilização de VMs spot permite-lhe tirar partido da nossa capacidade não utilizada com uma poupança significativa de custos. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure irá despejar os VM spot. Por isso, os VM spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

A quantidade de capacidade disponível pode variar em função do tamanho, região, hora do dia e muito mais. Ao implementar VMs spot, o Azure irá alocar os VMs se houver capacidade disponível, mas não há SLA para estes VMs. Um Spot VM não oferece garantias de alta disponibilidade. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure despejará VMs spot com aviso prévio de 30 segundos. 


## <a name="eviction-policy"></a>Política de despejo

Os VMs podem ser despejados com base na capacidade ou no preço máximo que definiu. Para máquinas virtuais, a política de despejo está definida para *Deallocate* que move os seus VMs despejados para o estado de paragem, permitindo-lhe recolocar os VMs despejados mais tarde. No entanto, a realocação de VMs spot dependerá da disponibilidade da capacidade spot. Os VMs deallocados contarão com a sua quota vCPU spot e você será cobrado pelos seus discos subjacentes. 

Os utilizadores podem optar por receber notificações in-VM através de [Eventos Agendados Azure.](../articles/virtual-machines/linux/scheduled-events.md) Isto irá notificá-lo se os seus VMs estiverem a ser despejados e terá 30 segundos para terminar quaisquer trabalhos e executar tarefas de encerramento antes do despejo. 


| Opção | Resultado |
|--------|---------|
| O preço máximo está definido para >= o preço atual. | A VM é implantada se a capacidade e a quota estiverem disponíveis. |
| O preço máximo está definido para < o preço atual. | O VM não está implantado. Receberá uma mensagem de erro de que o preço máximo precisa de ser >= preço atual. |
| Reiniciar um VM stop/deallocate se o preço máximo for >= o preço atual | Se houver capacidade e quota, então o VM é implantado. |
| Reiniciar um VM stop/deallocate se o preço máximo for < o preço atual | Receberá uma mensagem de erro de que o preço máximo precisa de ser >= preço atual. | 
| O preço do VM subiu e está agora > o preço máximo. | O VM é despejado. Recebe uma notificação dos anos 30 antes do despejo real. | 
| Após o despejo, o preço do VM volta a ser < o preço máximo. | O VM não será reiniciada automaticamente. Você pode reiniciar o VM por si mesmo, e será cobrado pelo preço atual. |
| Se o preço máximo for definido para`-1` | O VM não será despejado por razões de preços. O preço máximo será o preço atual, até ao preço dos VM padrão. Nunca será cobrado acima do preço normal.| 
| Alterar o preço máximo | Tens de negociar com o VM para alterar o preço máximo. Deallocate o VM, definir um novo preço máximo, em seguida, atualizar o VM. |

## <a name="limitations"></a>Limitações

Os seguintes tamanhos VM não são suportados para VMs spot:
 - Série B
 - Versões promocionais de qualquer tamanho (como tamanhos promocionais Dv2, NV, NC, H)

Os VMs spot não podem atualmente usar discos de SO efémeros.

Os VM spot podem ser implantados em qualquer região, exceto microsoft Azure China 21Vianet.

## <a name="pricing"></a>Preços

Os preços dos VM spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços em VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Com preços variáveis, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765` seria um preço máximo de $0.98765 USD por hora. Se definir o preço `-1` máximo, o VM não será despejado com base no preço. O preço do VM será o preço atual para o spot ou o preço para um VM padrão, que sempre é menor, desde que haja capacidade e quota disponível.


##  <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q:** Uma vez criado, um Spot VM é o mesmo que o VM normal?

**A:** Sim, exceto que não há SLA para VMs spot e podem ser despejados a qualquer momento.


**Q:** O que fazer quando for despejado, mas ainda precisa de capacidade?

**A:** Recomendamos que utilize VMs padrão em vez de VMs spot se precisar de capacidade imediatamente.


**Q:** Como é gerida a quota para os VMs spot?

**A:** Spot VMs terá uma quota separada. A quota pontual será partilhada entre VMs e instâncias definidas em escala. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Q:** Posso pedir uma quota adicional para o Spot?

**A:** Sim, poderá submeter o pedido de aumento da sua quota de VMs spot através do [processo padrão de pedido de quota.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**Q:** Que canais suportam Spot VMs?

**A:** Consulte a tabela abaixo para obter disponibilidade do Spot VM.

<a name="channel"></a>

| Canais Azure               | Disponibilidade de VMs Azure Spot       |
|------------------------------|-----------------------------------|
| Contrato Enterprise         | Yes                               |
| Pay As You Go                | Yes                               |
| Fornecedor de serviços na nuvem (CSP) | [Contacte o seu parceiro](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Contrato de Cliente da Microsoft | Yes                               |
| Benefícios                     | Não disponível                     |
| Patrocinado                    | Yes                               |
| Avaliação Gratuita                   | Não disponível                     |


**Q:** Onde posso fazer perguntas?

**A:** Você pode postar e marcar a sua pergunta com `azure-spot` [q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



