---
title: Como administrar Azure Cache para Redis
description: Saiba como executar tarefas de administração como reiniciar e agendar atualizações para Azure Cache para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 4afcc3fa5366e3e8938f952b4417b19d50693e37
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84605156"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Como administrar Azure Cache para Redis
Este tópico descreve como executar tarefas de administração como [reiniciar](#reboot) e [agendar atualizações](#schedule-updates) para o seu Azure Cache para instâncias Redis.

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Reiniciar
A lâmina **Reboot** permite-lhe reiniciar um ou mais nós da sua cache. Esta capacidade de reinicialização permite-lhe testar a sua aplicação de resiliência se houver uma falha de um nó de cache.

![Reiniciar](./media/cache-administration/redis-cache-administration-reboot.png)

Selecione os nós para reiniciar e clique em **Reboot**.

![Reiniciar](./media/cache-administration/redis-cache-reboot.png)

Se tiver uma cache premium com clustering ativado, pode selecionar quais os fragmentos da cache para reiniciar.

![Reiniciar](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reiniciar um ou mais nós da sua cache, selecione os nós desejados e clique em **Reboot**. Se tiver uma cache premium com clustering ativado, selecione os fragmentos desejados para reiniciar e, em seguida, clique em **Reboot**. Após alguns minutos, os nós selecionados reiniciam e voltam a estar on-line alguns minutos depois.

O impacto nas aplicações do cliente varia consoante os nós que reinicia.

* **Mestre** - Quando o nó mestre é reiniciado, Azure Cache para Redis falha no nó réplica e promove-o para dominar. Durante este failover, pode haver um curto intervalo em que as ligações podem não conseguir a cache.
* **Slave** - Quando o nó de escravo é reiniciado, normalmente não há impacto para os clientes da cache.
* **Mestre e escravo** - Quando ambos os nós de cache são reiniciados, todos os dados são perdidos na cache e as ligações à cache falham até que o nó primário volte a estar on-line. Se tiver [uma persistência](cache-how-to-premium-persistence.md)de dados configurada, a cópia de segurança mais recente é restaurada quando a cache volta a estar online, mas qualquer cache escreve que ocorreu após a mais recente cópia de segurança ser perdida.
* **Nós de uma cache premium com agrupamento ativado** - Quando reinicia um ou mais nós de uma cache premium com clustering ativado, o comportamento dos nós selecionados é o mesmo que quando reinicia os nós ou nós correspondentes de uma cache não agrupada.

## <a name="reboot-faq"></a>Reiniciar FAQ
* [Que nó devo reiniciar para testar a minha aplicação?](#which-node-should-i-reboot-to-test-my-application)
* [Posso reiniciar a cache para limpar as ligações com o cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Vou perder dados da minha cache se eu fizer um reboot?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Posso reiniciar o meu cache usando PowerShell, CLI ou outras ferramentas de gestão?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Que nó devo reiniciar para testar a minha aplicação?
Para testar a resiliência da sua aplicação contra a falha do nó primário do seu cache, reinicie o nó **Mestre.** Para testar a resiliência da sua aplicação contra a falha do nó secundário, reinicie o nó **de escravo.** Para testar a resiliência da sua aplicação contra a falha total da cache, reinicie **ambos os** nós.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Posso reiniciar a cache para limpar as ligações com o cliente?
Sim, se reiniciar a cache, todas as ligações do cliente estão limpas. O reboot pode ser útil no caso de todas as ligações do cliente serem usadas devido a um erro lógico ou a um erro na aplicação do cliente. Cada nível de preços tem [diferentes limites](cache-configure.md#default-redis-server-configuration) de ligação ao cliente para os vários tamanhos, e uma vez atingidos estes limites, não são aceites mais ligações ao cliente. Reiniciar a cache fornece uma forma de limpar todas as ligações com o cliente.

> [!IMPORTANT]
> Se reiniciar a cache para limpar as ligações do cliente, o StackExchange.Redis volta a ligar-se automaticamente assim que o nó Redis estiver novamente online. Se a questão subjacente não for resolvida, as ligações com o cliente podem continuar a ser usadas.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Vou perder dados da minha cache se eu fizer um reboot?
Se reiniciar os nós **Mestre** e **Escravo,** todos os dados na cache (ou nesse fragmento se estiver a utilizar uma cache premium com clustering ativado) podem ser perdidos, mas isso também não está garantido. Se tiver [uma persistência](cache-how-to-premium-persistence.md)de dados configurada, a cópia de segurança mais recente será restaurada quando a cache voltar a funcionar, mas quaisquer gravações que tenham ocorrido após a cópia de segurança são perdidas.

Se reiniciar apenas um dos nós, os dados não são normalmente perdidos, mas ainda podem ser. Por exemplo, se o nó principal for reiniciado e estiver em curso uma gravação de cache, os dados da escrita da cache perdem-se. Outro cenário para a perda de dados seria se reiniciar um nó e o outro nó cair devido a uma falha ao mesmo tempo. Para obter mais informações sobre possíveis causas para a perda de dados, veja [o que aconteceu aos meus dados no Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Posso reiniciar o meu cache usando PowerShell, CLI ou outras ferramentas de gestão?
Sim, para obter instruções powerShell consulte [reiniciar uma cache Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Agendar atualizações
A lâmina **de atualização da Agenda** permite-lhe designar uma janela de manutenção para a sua instância de cache. Quando a janela de manutenção é especificada, quaisquer atualizações do servidor Redis são efetuadas durante esta janela. 

> [!NOTE] 
> A janela de manutenção aplica-se apenas às atualizações do servidor Redis e não a quaisquer atualizações ou atualizações do Azure ao sistema operativo dos VMs que acolhem a cache.
>

![Agendar atualizações](./media/cache-administration/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique **em OK**. Note que o tempo de janela de manutenção está na UTC. 

A janela de manutenção por defeito e mínima para atualizações é de cinco horas. Este valor não é configurável a partir do portal Azure, mas pode configugá-lo em PowerShell usando o `MaintenanceWindow` parâmetro do [cmdlet New-AzRedisCacheScheduleEntry.](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Para mais informações, consulte posso gerir atualizações programadas utilizando powerShell, CLI ou outras ferramentas de gestão?

## <a name="schedule-updates-faq"></a>Agendar atualizações FAQ
* [Quando ocorrem atualizações se não utilizar a funcionalidade de atualizações de agendamento?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Que tipo de atualizações são feitas durante a janela de manutenção programada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Posso gerir atualizações programadas utilizando powerShell, CLI ou outras ferramentas de gestão?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando ocorrem atualizações se não utilizar a funcionalidade de atualizações de agendamento?
Se não especificar uma janela de manutenção, as atualizações podem ser feitas a qualquer momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Que tipo de atualizações são feitas durante a janela de manutenção programada?
Apenas as atualizações do servidor Redis são efetuadas durante a janela de manutenção programada. A janela de manutenção não se aplica às atualizações ou atualizações do Azure ao sistema operativo VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Posso gerir atualizações programadas usando PowerShell, CLI ou outras ferramentas de gestão?
Sim, pode gerir as atualizações programadas utilizando os seguintes cmdlets PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remover-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Próximos passos
* Explore mais [Azure Cache para funcionalidades de nível premium Redis.](cache-premium-tier-intro.md)

