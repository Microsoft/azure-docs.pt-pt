---
title: Como administrar o Azure Cache para redis
description: Saiba como executar tarefas de administração, tais como reboot e atualizações de horários para Azure Cache para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 69686cad20bc4ce70bff2a92a216c9430522c301
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79278847"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Como administrar o Azure Cache para redis
Este tópico descreve como executar tarefas de administração, tais como [reiniciar](#reboot) e [agendar atualizações](#schedule-updates) para os casos do seu Azure Cache para os casos Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Reiniciar
A lâmina **Reboot** permite-lhe reiniciar um ou mais nós da sua cache. Esta capacidade de reinicialização permite-lhe testar a sua aplicação de resiliência se houver uma falha de um nó de cache.

![Reiniciar](./media/cache-administration/redis-cache-administration-reboot.png)

Selecione os nódosos para reiniciar e clicar em **Reiniciar**.

![Reiniciar](./media/cache-administration/redis-cache-reboot.png)

Se tiver uma cache premium com clustering ativado, pode selecionar quais os fragmentos da cache para reiniciar.

![Reiniciar](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reiniciar um ou mais nós da sua cache, selecione os nós desejados e clique em **Reiniciar**. Se tiver uma cache premium com clustering ativado, selecione os fragmentos desejados para reiniciar e, em seguida, clique em **Reiniciar**. Após alguns minutos, os nós selecionados reiniciam e estão novamente on-line alguns minutos depois.

O impacto nas aplicações do cliente varia consoante os nódosos que reinicie.

* **Mestre** - Quando o nó principal é reiniciado, Azure Cache for Redis falha no nó de réplica e promove-o para dominar. Durante esta falha, pode haver um curto intervalo em que as ligações podem falhar na cache.
* **Escravo** - Quando o nó de escravo é reiniciado, normalmente não há impacto para os clientes cache.
* **Mestre e escravo** - Quando ambos os nós de cache são reiniciados, todos os dados são perdidos na cache e as ligações à cache falham até que o nó principal volte a funcionar. Se tiver configurado a persistência de [dados,](cache-how-to-premium-persistence.md)a cópia de segurança mais recente é restaurada quando a cache volta a funcionar, mas qualquer cache escreve que ocorreu após a cópia de segurança mais recente ser perdida.
* **Nós de uma cache premium com clustering ativado** - Quando você reinicia um ou mais nós de uma cache premium com clustering ativado, o comportamento para os nós selecionados é o mesmo que quando você reinicia o nó ou nós correspondentes de uma cache não agrupada.

## <a name="reboot-faq"></a>Reiniciar FAQ
* [Que nó devo reiniciar para testar a minha candidatura?](#which-node-should-i-reboot-to-test-my-application)
* [Posso reiniciar a cache para limpar as ligações com o cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Vou perder dados da minha cache se fizer uma reinicialização?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Posso reiniciar a minha cache usando PowerShell, CLI ou outras ferramentas de gestão?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Que nó devo reiniciar para testar a minha candidatura?
Para testar a resiliência da sua aplicação contra a falha do nó primário da sua cache, reinicie o nó **Master.** Para testar a resiliência da sua aplicação contra o fracasso do nó secundário, reinicie o nó **slave.** Para testar a resiliência da sua aplicação contra a falha total da cache, reinicie **ambos os** nós.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Posso reiniciar a cache para limpar as ligações com o cliente?
Sim, se reiniciar a cache todas as ligações com o cliente estão limpas. O reinício pode ser útil no caso de todas as ligações do cliente serem usadas devido a um erro lógico ou a um bug na aplicação do cliente. Cada nível de preços tem [diferentes limites](cache-configure.md#default-redis-server-configuration) de ligação ao cliente para os vários tamanhos, e uma vez atingidos estes limites, não são aceites mais ligações de clientes. Reiniciar a cache fornece uma forma de limpar todas as ligações do cliente.

> [!IMPORTANT]
> Se reiniciar a sua cache para limpar as ligações ao cliente, o StackExchange.Redis reconecta-se automaticamente assim que o nó Redis estiver novamente on-line. Se a questão subjacente não for resolvida, as ligações do cliente podem continuar a ser utilizadas.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Vou perder dados da minha cache se fizer uma reinicialização?
Se reiniciar os nós **Master** e **Slave,** todos os dados na cache (ou nesse fragmento se estiver a utilizar uma cache premium com clustering ativado) podem ser perdidos, mas isso também não está garantido. Se tiver configurado a persistência de [dados,](cache-how-to-premium-persistence.md)a cópia de segurança mais recente será restaurada quando a cache voltar a funcionar, mas quaisquer escritos de cache que ocorreram após a cópia de segurança foram perdidas.

Se reiniciar apenas um dos nós, os dados não são normalmente perdidos, mas ainda podem ser. Por exemplo, se o nó principal for reiniciado e uma gravação em cache estiver em andamento, os dados da escrita cache são perdidos. Outro cenário para a perda de dados seria se reiniciar um nó e o outro nó cair devido a uma falha ao mesmo tempo. Para mais informações sobre possíveis causas para a perda de dados, veja [o que aconteceu aos meus dados em Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Posso reiniciar a minha cache usando PowerShell, CLI ou outras ferramentas de gestão?
Sim, para instruções da PowerShell consulte [Para reiniciar um Cache Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Agendar atualizações
A lâmina de **atualizações de Agenda** permite-lhe designar uma janela de manutenção para a sua instância cache. Quando a janela de manutenção é especificada, quaisquer atualizações do servidor Redis são feitas durante esta janela. 

> [!NOTE] 
> A janela de manutenção aplica-se apenas às atualizações do servidor Redis e não a quaisquer atualizações ou atualizações do Azure para o sistema operativo dos VMs que acolhem a cache.
>

![Agendar atualizações](./media/cache-administration/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. Note que o tempo da janela de manutenção está na UTC. 

A janela de manutenção padrão, e mínima, para atualizações é de cinco horas. Este valor não é configurável a partir do portal Azure, `MaintenanceWindow` mas pode configurá-lo no PowerShell utilizando o parâmetro do [cmdlet New-AzRedisCacheScheduleEntry.](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Para mais informações, consulte Posso gerir as atualizações programadas utilizando powerShell, CLI ou outras ferramentas de gestão?

## <a name="schedule-updates-faq"></a>Atualizar atualizações FAQ
* [Quando é que as atualizações ocorrem se eu não utilizar a funcionalidade de atualizações de horários?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Que tipo de atualizações são feitas durante a janela de manutenção programada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Posso gerir as atualizações programadas usando powerShell, CLI ou outras ferramentas de gestão?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando é que as atualizações ocorrem se eu não utilizar a funcionalidade de atualizações de horários?
Se não especificar uma janela de manutenção, as atualizações podem ser feitas a qualquer momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Que tipo de atualizações são feitas durante a janela de manutenção programada?
Apenas as atualizações do servidor Redis são feitas durante a janela de manutenção programada. A janela de manutenção não se aplica às atualizações ou atualizações do Azure ao sistema operativo VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Posso gerir atualizações programadas usando PowerShell, CLI ou outras ferramentas de gestão?
Sim, pode gerir as suas atualizações programadas utilizando os seguintes cmdlets PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [Entrada de novas agendas az-AzRedisCache](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remover-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Passos seguintes
* Explore mais Azure Cache para funcionalidades de [nível premium Redis.](cache-premium-tier-intro.md)

