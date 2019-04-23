---
title: Resolver problemas de implementação do serviço em nuvem | Documentos da Microsoft
description: Existem alguns problemas comuns que poderão ocorrer ao implementar um serviço em nuvem para o Azure. Este artigo fornece soluções para alguns deles.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: cc2a0177525013736445db5fd1befa478dc9b9b8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798575"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Resolver problemas de implementação do serviço de nuvem
Quando implementar um pacote de aplicação de serviço cloud do Azure, pode obter informações sobre a implementação a partir da **propriedades** painel no portal do Azure. Pode utilizar os detalhes neste painel para o ajudar a resolver problemas relacionados com o serviço em nuvem, e pode fornecer estas informações para o suporte do Azure ao abrir um novo pedido de suporte.

Pode encontrar os **propriedades** painel da seguinte forma:

* No portal do Azure, clique na implementação do seu serviço cloud, clique em **todas as definições**e, em seguida, clique em **propriedades**.

> [!NOTE]
> Pode copiar o conteúdo do **propriedades** painel para a área de transferência ao clicar no ícone no canto superior direito do painel.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: Não consigo aceder a meu site, mas minha implementação é iniciada e todas as instâncias de função estão prontas
A ligação de URL de Web site mostrado no portal não inclui a porta. A porta predefinida para Web sites é 80. Se a sua aplicação está configurada para ser executado numa porta diferente, tem de adicionar o número de porta correto para o URL ao aceder ao Web site.

1. No portal do Azure, clique na implementação do seu serviço cloud.
2. Na **propriedades** painel do portal do Azure, verifique as portas para as instâncias de função (sob **pontos finais de entrada**).
3. Se a porta não for 80, adicione o valor de porta correto para o URL quando aceder à aplicação. Para especificar uma porta não predefinida, escreva o URL, seguido de dois pontos (:), seguido do número de porta, sem espaços.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: Minhas instâncias de função recicladas sem que eu fazer nada
Serviço de recuperação ocorre automaticamente quando o Azure Deteta nós de problema e, portanto, move instâncias de função para novos nós. Quando isto ocorrer, poderá ver as instâncias de função Reciclagem automaticamente. Para saber se recuperação do serviço ocorreu:

1. No portal do Azure, clique na implementação do seu serviço cloud.
2. Na **propriedades** painel do portal do Azure, reveja as informações e determinar se recuperação do serviço ocorreu durante o tempo que observou que a Reciclagem de funções.

Funções também haverá Reciclagem aproximadamente uma vez por mês durante o SO anfitrião e atualizações de SO convidado.  
Para obter mais informações, consulte a mensagem de blogue [função instância é reiniciado devido a atualizações do SO](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: Eu não é possível fazer uma alternância de VIP e receber um erro
Uma alternância de VIP não é permitida se uma atualização de implementação está em curso. Implementação de atualizações podem ocorrer automaticamente quando:

* Um novo sistema de operativo convidado está disponível e estão configurados para atualizações automáticas.
* Ocorre a recuperação de serviço.

Para saber se uma atualização automática está a impedir a fazer uma alternância de VIP:

1. No portal do Azure, clique na implementação do seu serviço cloud.
2. Na **propriedades** painel do portal do Azure, observar o valor de **estado**. Se for **pronto**, em seguida, verifique **última operação** ver se um recentemente aconteceu que poderá impedir a troca de VIP.
3. Repita os passos 1 e 2 para a implementação de produção.
4. Se uma atualização automática está em curso, aguarde que termine antes de tentar fazer a troca de VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Uma instância de função está executando loop entre iniciado, a inicializar, ocupado e parado
Esta condição poderá indicar um problema com o código, o pacote ou o ficheiro de configuração da aplicação. Nesse caso, deverá conseguir ver o estado de alteração de intervalos de poucos minutos e o portal do Azure pode dizer algo como **reciclar**, **ocupado**, ou **inicializar**. Isto indica que há algo errado com a aplicação que é manter a instância de função a execução.

Para obter mais informações sobre como resolver problemas para este problema, consulte a mensagem de blogue [dados de diagnóstico de computação do Azure PaaS](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) e [problemas comuns que causam as funções reciclem](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problema: Meu aplicativo deixou de funcionar
1. No portal do Azure, clique na instância de função.
2. Na **propriedades** painel do portal do Azure, considere as seguintes condições para resolver o problema:
   * Se a instância de função recentemente tiver parado (pode verificar o valor de **abortadas**), foi possível atualizar a implementação. Espere para ver se a instância de função continua a funcionar no seu próprio.
   * Se a instância de função for **ocupado**, verifique o código da aplicação para ver se o [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) evento é processado. Poderá ter de adicionar ou corrigir algum código que manipula esse evento.
   * Pesquisar os dados de diagnóstico e resolução de problemas de cenários na mensagem de blogue [dados de diagnóstico de computação do Azure PaaS](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Se a Reciclagem de seu serviço cloud, repor as propriedades para a implementação, com eficiência apagar as informações para o problema original.
>
>

## <a name="next-steps"></a>Passos Seguintes
Ver mais [artigos de resolução de problemas](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) para serviços em nuvem.

Para saber como resolver problemas de função do serviço cloud, utilizando dados de diagnóstico do computador de PaaS do Azure, veja [série de blogue de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
