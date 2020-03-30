---
title: Problemas de implantação do serviço de nuvem de resolução de problemas / Microsoft Docs
description: Existem alguns problemas comuns que poderá encontrar ao implementar um serviço de nuvem para o Azure. Este artigo fornece soluções para alguns deles.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122756"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Problemas de implementação do serviço de nuvem de resolução de problemas
Ao implementar um pacote de aplicação de serviço na nuvem para o Azure, pode obter informações sobre a implementação do painel **Properties** no portal Azure. Pode utilizar os detalhes deste painel para o ajudar a resolver problemas com o serviço de nuvem, e pode fornecer esta informação ao Suporte Azure ao abrir um novo pedido de suporte.

Pode encontrar o painel **das Propriedades** da seguinte forma:

* No portal Azure, clique na implementação do seu serviço na nuvem, clique em **todas as definições**, e clique em **Propriedades**.

> [!NOTE]
> Pode copiar o conteúdo do painel **Properties** para a área de sobre-ré, clicando no ícone no canto superior direito do painel.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: Não posso aceder ao meu website, mas a minha implantação está iniciada e todas as instâncias de papéis estão prontas
O link URL do site mostrado no portal não inclui a porta. A porta padrão para sites é de 80. Se a sua aplicação estiver configurada para funcionar numa porta diferente, deve adicionar o número de porta correto ao URL ao aceder ao website.

1. No portal Azure, clique na implementação do seu serviço na nuvem.
2. No painel **Propriedades** do portal Azure, verifique as portas para obter as instâncias de função (em **pontos finais**de entrada).
3. Se a porta não tiver 80, adicione o valor de porta correto ao URL quando aceder à aplicação. Para especificar uma porta não predefinida, digite o URL, seguido de um cólon (:), seguido do número da porta, sem espaços.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: As minhas instâncias de papel recicladas sem eu fazer nada
A cicatrização do serviço ocorre automaticamente quando o Azure deteta nódosos problemáticos e, portanto, move as instâncias de função para novos nódosos. Quando isto ocorrer, poderá ver as suas instâncias de função reciclando automaticamente. Para saber se ocorreu a cura do serviço:

1. No portal Azure, clique na implementação do seu serviço na nuvem.
2. No painel **De Propriedades** do portal Azure, reveja a informação e determine se a cicatrização do serviço ocorreu durante o tempo em que observou as funções de reciclagem.

As funções também reciclarão aproximadamente uma vez por mês durante as atualizações host-OS e guest-OS.  
Para mais informações, consulte o blog post [Role Instance Restarts Devido a Atualizações do OS](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: Não posso fazer uma troca VIP e receber um erro
Não é permitida uma troca VIP se estiver em curso uma atualização de implantação. As atualizações de implementação podem ocorrer automaticamente quando:

* Um novo sistema operativo de hóspedes está disponível e está configurado para atualizações automáticas.
* A cura do serviço ocorre.

Para saber se uma atualização automática está a impedir-lhe de fazer uma troca VIP:

1. No portal Azure, clique na implementação do seu serviço na nuvem.
2. No painel **De Propriedades** do portal Azure, veja o valor do **Estado.** Se estiver **pronto,** verifique a **última operação** para ver se aconteceu recentemente uma operação que possa impedir a troca VIP.
3. Repita os passos 1 e 2 para a implantação da produção.
4. Se estiver em curso uma atualização automática, aguarde que termine antes de tentar fazer a troca VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Uma instância de papel está a circular entre Iniciado, Inicializando, Ocupado e Parado
Esta condição poderá indicar um problema com o código, o pacote ou o ficheiro de configuração da aplicação. Nesse caso, deverá poder ver o estado a mudar a cada poucos minutos e o portal Azure pode dizer algo como **Reciclagem,** **Ocupado**ou **Inicialização.** Isto indica que há algo de errado com a aplicação que está a impedir que o exemplo de função seja decorrido.

Para obter mais informações sobre como resolver problemas para este problema, consulte o blog post [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) e [questões comuns que fazem com que as funções reciclem.](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)

## <a name="problem-my-application-stopped-working"></a>Problema: A minha candidatura deixou de funcionar
1. No portal Azure, clique na instância de funções.
2. No painel **De Propriedades** do portal Azure, considere as seguintes condições para resolver o seu problema:
   * Se a instância de funções tiver parado recentemente (pode verificar o valor da contagem de **Aborto),** a implementação poderá estar a ser atualizada. Aguarde para ver se a instância de funções retoma o funcionamento por si só.
   * Se a instância de funções estiver **ocupada,** verifique o código de aplicação para ver se o evento [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) é tratado. Pode ser necessário adicionar ou corrigir algum código que trate deste evento.
   * Acompanhe os dados de diagnóstico e cenários de resolução de problemas no blog post [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Se reciclar o seu serviço de nuvem, redefinir ás propriedades para a implementação, apagando efetivamente a informação para o problema original.
>
>

## <a name="next-steps"></a>Passos seguintes
Veja mais artigos de [resolução de problemas](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) para serviços na nuvem.

Para aprender a resolver problemas de papel de serviço na nuvem utilizando dados de diagnóstico de computador Azure PaaS, consulte a [série de blogs de Kevin Williamson.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
