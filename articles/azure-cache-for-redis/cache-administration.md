---
title: Como administrar o cache do Azure para Redis | Microsoft Docs
description: Saiba como executar tarefas de administração, como reinicializar e agendar atualizações para o cache do Azure para Redis
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
ms.openlocfilehash: afb48c18f9690e77a5478956e1147acf850290ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756619"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Como administrar o cache do Azure para Redis
Este tópico descreve como executar tarefas de administração, como [reinicializar](#reboot) e [agendar atualizações](#schedule-updates) para o cache do Azure para instâncias Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Reiniciar
A folha **reinicializar** permite que você reinicialize um ou mais nós do seu cache. Essa capacidade de reinicialização permite testar seu aplicativo quanto à resiliência se houver uma falha de um nó de cache.

![Reiniciar](./media/cache-administration/redis-cache-administration-reboot.png)

Selecione os nós a serem reinicializados e clique em **reinicializar**.

![Reiniciar](./media/cache-administration/redis-cache-reboot.png)

Se você tiver um cache Premium com clustering habilitado, poderá selecionar quais fragmentos do cache devem ser reinicializados.

![Reiniciar](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reinicializar um ou mais nós do cache, selecione os nós desejados e clique em **reinicializar**. Se você tiver um cache Premium com clustering habilitado, selecione os fragmentos desejados para reinicializar e clique em **reinicializar**. Após alguns minutos, os nós selecionados serão reinicializados e ficarão online novamente alguns minutos depois.

O impacto nos aplicativos cliente varia de acordo com os nós reiniciados.

* **Mestre** -quando o nó mestre é reinicializado, o cache do Azure para Redis faz failover para o nó de réplica e promove-o para o mestre. Durante esse failover, pode haver um pequeno intervalo no qual as conexões podem falhar no cache.
* **Subordinado** – quando o nó subordinado é reinicializado, normalmente não há impacto para os clientes de cache.
* **Mestre e subordinado** -quando ambos os nós de cache são reinicializados, todos os dados são perdidos no cache e as conexões com o cache falham até que o nó primário volte a ficar online. Se você tiver configurado a [persistência de dados](cache-how-to-premium-persistence.md), o backup mais recente será restaurado quando o cache ficar online novamente, mas todas as gravações de cache ocorridas após o backup mais recente serão perdidas.
* **Nós de um cache Premium com clustering habilitado** -quando você reinicializa um ou mais nós de um cache Premium com clustering habilitado, o comportamento para os nós selecionados é o mesmo que quando você reinicializa o nó ou nós correspondentes de um cache não clusterizado.

## <a name="reboot-faq"></a>Reinicializar perguntas frequentes
* [Qual nó devo reinicializar para testar meu aplicativo?](#which-node-should-i-reboot-to-test-my-application)
* [Posso reinicializar o cache para limpar as conexões de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Perderei dados do meu cache se eu fizer uma reinicialização?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Posso reinicializar meu cache usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Qual nó devo reinicializar para testar meu aplicativo?
Para testar a resiliência do seu aplicativo contra falha do nó primário do seu cache, reinicialize o nó **mestre** . Para testar a resiliência do seu aplicativo em relação à falha do nó secundário, reinicialize o nó **subordinado** . Para testar a resiliência do seu aplicativo contra a falha total do cache, reinicialize **os dois** nós.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Posso reinicializar o cache para limpar as conexões de cliente?
Sim, se você reinicializar o cache, todas as conexões de cliente serão limpas. A reinicialização pode ser útil no caso em que todas as conexões de cliente são usadas devido a um erro lógico ou a um bug no aplicativo cliente. Cada tipo de preço tem [limites de conexão de cliente](cache-configure.md#default-redis-server-configuration) diferentes para os vários tamanhos e, depois que esses limites são atingidos, nenhuma outra conexão de cliente é aceita. A reinicialização do cache fornece uma maneira de limpar todas as conexões de cliente.

> [!IMPORTANT]
> Se você reinicializar o cache para limpar as conexões de cliente, o StackExchange. Redis se reconectará automaticamente quando o nó do Redis estiver online novamente. Se o problema subjacente não for resolvido, as conexões do cliente poderão continuar sendo usadas.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Perderei dados do meu cache se eu fizer uma reinicialização?
Se você reinicializar os nós **mestre** e **subordinado** , todos os dados no cache (ou nesse fragmento, se você estiver usando um cache Premium com clustering habilitado), poderão ser perdidos, mas isso não será garantido. Se você tiver configurado a [persistência de dados](cache-how-to-premium-persistence.md), o backup mais recente será restaurado quando o cache ficar online novamente, mas todas as gravações de cache que ocorreram após a realização do backup serão perdidas.

Se você reinicializar apenas um dos nós, os dados normalmente não são perdidos, mas ainda podem ser. Por exemplo, se o nó mestre for reinicializado e uma gravação de cache estiver em andamento, os dados da gravação do cache serão perdidos. Outro cenário de perda de dados seria se você reinicializar um nó e o outro nó ocorrer devido a uma falha ao mesmo tempo. Para obter mais informações sobre as possíveis causas de perda de dados, consulte [o que aconteceu com meus dados no Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Posso reinicializar meu cache usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?
Sim, para instruções do PowerShell, consulte [para reinicializar um cache do Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Atualizações agendadas
A folha **agendar atualizações** permite designar uma janela de manutenção para sua instância de cache. Quando a janela de manutenção é especificada, todas as atualizações do servidor Redis são feitas durante essa janela. 

> [!NOTE] 
> A janela de manutenção aplica-se somente a atualizações do servidor Redis e não a atualizações do Azure ou a atualizações do sistema operacional das VMs que hospedam o cache.
>

![Atualizações agendadas](./media/cache-administration/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. Observe que o tempo da janela de manutenção está em UTC. 

A janela de manutenção padrão e mínima para atualizações é de cinco horas. Esse valor não é configurável no portal do Azure, mas você pode configurá-lo no PowerShell usando o parâmetro `MaintenanceWindow` do cmdlet [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) . Para obter mais informações, consulte posso gerenciar atualizações agendadas usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?

## <a name="schedule-updates-faq"></a>Perguntas frequentes de agendar atualizações
* [Quando as atualizações ocorrerão se eu não usar o recurso agendar atualizações?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Que tipo de atualizações são feitas durante a janela de manutenção agendada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Posso gerenciar atualizações agendadas usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando as atualizações ocorrerão se eu não usar o recurso agendar atualizações?
Se você não especificar uma janela de manutenção, as atualizações poderão ser feitas a qualquer momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Que tipo de atualizações são feitas durante a janela de manutenção agendada?
Somente as atualizações do Redis Server são feitas durante a janela de manutenção agendada. A janela de manutenção não se aplica a atualizações do Azure ou a atualizações para o sistema operacional da VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Posso gerenciar atualizações agendadas usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?
Sim, você pode gerenciar suas atualizações agendadas usando os seguintes cmdlets do PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Passos seguintes
* Explore mais recursos do [cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md) .

