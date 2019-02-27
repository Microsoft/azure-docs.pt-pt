---
title: Como administrar o Azure Cache de Redis | Documentos da Microsoft
description: Saiba como efetuar tarefas de administração, tais como o reinício e agendar atualizações para a Cache do Azure para Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 81ef669b62c822e10d8bf5c45e58dd769c5dbeb9
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888394"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Como administrar o Azure Cache de Redis
Este tópico descreve como executar tarefas de administração, tal como [reinicialização](#reboot) e [agendar atualizações](#schedule-updates) para a sua Cache do Azure para instâncias de Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Reiniciar
O **reiniciar** painel permite-lhe reiniciar uma ou mais nós da cache. Esta capacidade de reinício permite-lhe testar a sua aplicação para resiliência se ocorrer uma falha de um nó de cache.

![Reiniciar](./media/cache-administration/redis-cache-administration-reboot.png)

Selecione os nós para reiniciar e clique em **reiniciar**.

![Reiniciar](./media/cache-administration/redis-cache-reboot.png)

Se tiver uma cache premium com clustering ativado, pode selecionar qual shards da cache para reiniciar o computador.

![Reiniciar](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reiniciar uma ou mais nós da cache, selecione os nós pretendido e clique em **reiniciar**. Se tiver uma cache premium com clustering ativado, selecione as partições horizontais pretendidas para reiniciar e, em seguida, clique em **reiniciar**. Após alguns minutos, o reinício de nós selecionados e são novamente online alguns minutos mais tarde.

O impacto em aplicativos de cliente varia de acordo com os nós nos quais se que reinicie.

* **Mestre** - quando o nó principal é reiniciado, a Cache de Redis do Azure efetua a ativação pós-falha para o nó de réplica e promove a mestre. Durante esta ativação pós-falha, pode haver um curto intervalo em que as ligações podem falhar à cache.
* **Subordinado do** - quando o nó subordinado é reinicializado, geralmente não é afetado para clientes de cache.
* **Mestre e subordinado** -quando os nós de cache são reiniciados, todos os dados é perdida no cache e ligações para a cache falharem até o nó primário fica online novamente. Se tiver configurado [persistência de dados](cache-how-to-premium-persistence.md), mais recente cópia de segurança é restaurada quando a cache fica online novamente, mas qualquer gravações de cache que ocorreram após a cópia de segurança mais recente são perdidas.
* **Nós de um premium da cache com clustering ativado** - quando reiniciar os nós de um ou mais de uma cache premium com clustering ativado, o comportamento para os nós selecionados é o mesmo que quando reiniciar o nó correspondente ou nós de uma cache não-clusterizados.

> [!IMPORTANT]
> O reinício está agora disponível para todos os escalões de preço.
> 
> 

## <a name="reboot-faq"></a>FAQ de reinício
* [Nó que eu reinicializo para testar a minha aplicação?](#which-node-should-i-reboot-to-test-my-application)
* [Pode reiniciar o cache para limpar as ligações de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Irei perder dados da minha cache se fizer um reinício?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Pode reiniciar minha cache com o PowerShell, CLI ou outras ferramentas de gestão?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Os preços camadas, podem utilizar a funcionalidade de reinício?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Nó que eu reinicializo para testar a minha aplicação?
Para testar a resiliência da sua aplicação contra falhas de nó primário da cache, reinicie o **mestre** nó. Para testar a resiliência da sua aplicação contra falhas de nó secundário, reinicie o **subordinado** nó. Para testar a resiliência da sua aplicação contra a falha total da cache, de reinício **ambos** nós.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Pode reiniciar o cache para limpar as ligações de cliente?
Sim, se reiniciar o cache de todas as ligações de cliente são limpos. A reiniciar pode ser útil no caso em que todas as ligações de cliente foram utilizadas devido a um erro de lógica ou um bug no aplicativo cliente. Cada escalão de preço tem diferentes [limites de ligações de cliente](cache-configure.md#default-redis-server-configuration) para vários tamanhos e, uma vez, estes limites são atingidos, não existem mais ligações de cliente são aceites. Reiniciar o cache fornece uma forma de limpar todas as ligações de cliente.

> [!IMPORTANT]
> Se reiniciar o cache para limpar as ligações de cliente, o stackexchange. redis volta automaticamente assim que o nó de Redis está online. Se o problema subjacente não for resolvido, as ligações de cliente podem continuar a ser utilizados.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Irei perder dados da minha cache se fizer um reinício?
Se reiniciar ambos os **mestre** e **subordinado** nós, todos os dados na cache (ou nessa partição horizontal se estiver a utilizar uma cache premium com clustering ativado) podem ser perdidos, mas isso não é garantido qualquer um. Se tiver configurado [persistência de dados](cache-how-to-premium-persistence.md), mais recente cópia de segurança vai ser restaurada quando a cache fica online novamente, mas qualquer gravações de cache que ocorreram depois foi efetuada a cópia de segurança são perdidas.

Se reiniciar apenas um de nós, os dados não são normalmente perdidos, mas ainda pode ser. Para o exemplo, se o nó principal é reiniciado e uma gravação de cache está em curso, os dados a partir da escrita de cache é perdido. Outro cenário perda de dados seria se reinicie um nó e o outro nó acontece descer devido a uma falha ao mesmo tempo. Para obter mais informações sobre as causas possíveis perda de dados, consulte [o que aconteceu aos meus dados no Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Pode reiniciar minha cache com o PowerShell, CLI ou outras ferramentas de gestão?
Sim, para o PowerShell obter instruções, consulte [reiniciar uma Cache do Azure para Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Os preços camadas, podem utilizar a funcionalidade de reinício?
O reinício está disponível para todos os escalões de preço.

## <a name="schedule-updates"></a>Agendar atualizações
O **agendar atualizações** painel permite-lhe designar uma janela de manutenção para a sua cache de escalão Premium. Quando a janela de manutenção é especificada, todas as atualizações de servidor de Redis são feitas durante esta janela. 

> [!NOTE] 
> A janela de manutenção só se aplica a Redis atualizações do servidor e não para qualquer serviço de atualizações ou atualiza para o sistema operativo das VMs que alojam a cache.
> 
> 

![Agendar atualizações](./media/cache-administration/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. Tenha em atenção que o tempo de janela de manutenção é em UTC. 

A janela de manutenção predefinida e mínima de atualizações é cinco horas. Este valor não é configurável no portal do Azure, mas pode configurá-lo no PowerShell com o `MaintenanceWindow` parâmetro do [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) cmdlet. Para obter mais informações, veja o que posso gerir com o PowerShell, CLI ou outras ferramentas de gestão de atualizações agendadas?


## <a name="schedule-updates-faq"></a>Agendar atualizações FAQ
* [Quando as atualizações ocorrem se eu não uso o recurso de atualizações de agenda?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Que tipo de atualizações são efetuadas durante a janela de manutenção agendada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Pode gerir com o PowerShell, CLI ou outras ferramentas de gestão de atualizações agendadas?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Os preços camadas, podem utilizar a funcionalidade de atualizações de agenda?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando as atualizações ocorrem se eu não uso o recurso de atualizações de agenda?
Se não especificar uma janela de manutenção, as atualizações podem ser feitas em qualquer altura.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Que tipo de atualizações são efetuadas durante a janela de manutenção agendada?
Apenas servidor atualizações são efetuadas durante a janela de manutenção agendada de Redis. Não se aplica a janela de manutenção para atualizações do Azure ou as atualizações para o sistema operacional VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Posso gerir atualizações agendadas, com o PowerShell, CLI ou outras ferramentas de gestão?
Sim, pode gerir as atualizações agendadas com os seguintes cmdlets do PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Os preços camadas, podem utilizar a funcionalidade de atualizações de agenda?
O **agendar atualizações** funcionalidade só está disponível no escalão de preços premium.

## <a name="next-steps"></a>Passos Seguintes
* Explorar mais [Cache do Azure para o escalão premium Redis](cache-premium-tier-intro.md) funcionalidades.

