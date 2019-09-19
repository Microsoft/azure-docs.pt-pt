---
title: Solucionar problemas de implantação do serviço de nuvem | Microsoft Docs
description: Há alguns problemas comuns que você pode encontrar ao implantar um serviço de nuvem no Azure. Este artigo fornece soluções para algumas delas.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: ccb08f853ae0f941dd5f9c0eca8c77f0f650905a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122756"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Solucionar problemas de implantação do serviço de nuvem
Ao implantar um pacote de aplicativo de serviço de nuvem no Azure, você pode obter informações sobre a implantação no painel **Propriedades** na portal do Azure. Você pode usar os detalhes neste painel para ajudá-lo a solucionar problemas com o serviço de nuvem e pode fornecer essas informações para o suporte do Azure ao abrir uma nova solicitação de suporte.

Você pode encontrar o painel **Propriedades** da seguinte maneira:

* No portal do Azure, clique na implantação do serviço de nuvem, clique em **todas as configurações**e clique em **Propriedades**.

> [!NOTE]
> Você pode copiar o conteúdo do painel **Propriedades** para a área de transferência clicando no ícone no canto superior direito do painel.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Persisti Não consigo acessar meu site, mas minha implantação foi iniciada e todas as instâncias de função estão prontas
O link de URL do site mostrado no portal não inclui a porta. A porta padrão para sites é 80. Se seu aplicativo estiver configurado para ser executado em uma porta diferente, você deverá adicionar o número de porta correto à URL ao acessar o site.

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** da portal do Azure, verifique as portas das instâncias de função (em pontos de **extremidade de entrada**).
3. Se a porta não for 80, adicione o valor correto da porta à URL ao acessar o aplicativo. Para especificar uma porta não padrão, digite a URL, seguida por dois-pontos (:), seguido pelo número da porta, sem espaços.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Persisti Minhas instâncias de função recicladas sem me fazer nada
A recuperação de serviço ocorre automaticamente quando o Azure detecta nós com problemas e, portanto, move as instâncias de função para novos nós. Quando isso ocorrer, você poderá ver as instâncias de função se reciclando automaticamente. Para descobrir se a recuperação do serviço ocorreu:

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** da portal do Azure, examine as informações e determine se a recuperação de serviço ocorreu durante o tempo em que você observou a reciclagem de funções.

As funções também serão recicladas aproximadamente uma vez por mês durante as atualizações do so do host e do sistema operacional convidado.  
Para obter mais informações, consulte a postagem de blog [reinicializações de instância de função devido a atualizações do so](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Persisti Não consigo fazer uma permuta de VIP e recebo um erro
Uma permuta de VIP não será permitida se uma atualização de implantação estiver em andamento. As atualizações de implantação podem ocorrer automaticamente quando:

* Um novo sistema operacional convidado está disponível e você está configurado para atualizações automáticas.
* A recuperação do serviço ocorre.

Para descobrir se uma atualização automática está impedindo a realização de uma permuta de VIP:

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** da portal do Azure, examine o valor de **status**. Se estiver **pronto**, verifique a **última operação** para ver se ocorreu um recentemente que pode impedir a permuta de VIP.
3. Repita as etapas 1 e 2 para a implantação de produção.
4. Se uma atualização automática estiver em andamento, aguarde até que ela seja concluída antes de tentar realizar a permuta de VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Persisti Uma instância de função está fazendo um loop entre iniciado, inicializando, ocupado e parado
Esta condição poderá indicar um problema com o código, o pacote ou o ficheiro de configuração da aplicação. Nesse caso, você deve ser capaz de ver o status mudando a cada poucos minutos e o portal do Azure pode dizer algo como **reciclagem**, **ocupado**ou **inicialização**. Isso indica que há algo errado com o aplicativo que está impedindo a execução da instância de função.

Para obter mais informações sobre como solucionar esse problema, consulte a postagem de blog [dados de diagnóstico de computação de PaaS do Azure](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) e [problemas comuns que fazem com que as funções sejam recicladas](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Persisti Meu aplicativo parou de funcionar
1. Na portal do Azure, clique na instância de função.
2. No painel **Propriedades** da portal do Azure, considere as seguintes condições para resolver o problema:
   * Se a instância de função foi interrompida recentemente (você pode verificar o valor de **contagem de anulação**), a implantação pode estar sendo atualizada. Aguarde para ver se a instância de função continua funcionando por conta própria.
   * Se a instância de função estiver **ocupada**, verifique o código do aplicativo para ver se o evento [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) é manipulado. Talvez seja necessário adicionar ou corrigir algum código que manipule esse evento.
   * Percorra os dados de diagnóstico e os cenários de solução de problemas na postagem [do blog dados de diagnóstico de computação do Azure PaaS](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Se você reciclar seu serviço de nuvem, redefina as propriedades da implantação, apagando efetivamente as informações do problema original.
>
>

## <a name="next-steps"></a>Passos seguintes
Veja mais [artigos de solução de problemas](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) para serviços de nuvem.

Para saber como solucionar problemas de função de serviço de nuvem usando dados de diagnóstico de computador de PaaS do Azure, consulte a [série de Blogs de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
