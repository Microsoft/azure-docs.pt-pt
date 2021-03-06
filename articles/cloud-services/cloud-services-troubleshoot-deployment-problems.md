---
title: Problemas de implementação do serviço de nuvem de resolução de problemas (clássicos) | Microsoft Docs
description: Existem alguns problemas comuns que pode encontrar ao implementar um serviço de nuvem para Azure. Este artigo fornece soluções para alguns deles.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7b3d7a9a674aab3976da9399f71ff4d8df08eb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741082"
---
# <a name="troubleshoot-azure-cloud-services-classic-deployment-problems"></a>Problemas de resolução de problemas Azure Cloud Services (Classic) problemas de implantação

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Quando implementar um pacote de aplicações de serviço em nuvem para o Azure, pode obter informações sobre a implantação a partir do painel **propriedades** no portal Azure. Pode utilizar os detalhes neste painel para ajudá-lo a resolver problemas com o serviço de nuvem, e pode fornecer estas informações ao Azure Support ao abrir um novo pedido de suporte.

Pode encontrar o painel **de propriedades** da seguinte forma:

* No portal Azure, clique na implementação do seu serviço na nuvem, clique em **Todas as definições** e, em seguida, clique em **Propriedades**.

> [!NOTE]
> Pode copiar o conteúdo do painel **de propriedades** para a área de transferência clicando no ícone no canto superior direito do painel.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: Não posso aceder ao meu website, mas a minha implantação começou e todas as instâncias de papel estão prontas
O link URL do site mostrado no portal não inclui a porta. A porta padrão para sites é de 80. Se a sua aplicação estiver configurada para ser executada numa porta diferente, deve adicionar o número de porta correto ao URL ao aceder ao website.

1. No portal Azure, clique na implementação do seu serviço na nuvem.
2. No painel **de propriedades** do portal Azure, verifique as portas para ver as instâncias de função (em **Pontos Finais de Entrada).**
3. Se a porta não for de 80, adicione o valor de porta correto ao URL quando aceder à aplicação. Para especificar uma porta não padrão, digite o URL, seguido de um cólon (:), seguido do número da porta, sem espaços.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: Os meus exemplos de papel reciclados sem eu fazer nada
A cicatrização do serviço ocorre automaticamente quando o Azure deteta nódoas de problemas e, portanto, transfere as instâncias de função para novos nós. Quando isto ocorrer, poderá ver as suas instâncias de função a reciclar-se automaticamente. Para saber se ocorreu a cicatrização do serviço:

1. No portal Azure, clique na implementação do seu serviço na nuvem.
2. No painel **propriedades** do portal Azure, reveja as informações e determine se a cicatrização do serviço ocorreu durante o tempo em que observou a reciclagem das funções.

As funções também reciclam aproximadamente uma vez por mês durante as atualizações host-OS e guest-OS.  
Para mais informações, consulte o blog post [Role Instance Restarts Devido a atualizações de OS](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: Não posso fazer uma troca VIP e receber um erro
Não é permitida uma troca VIP se uma atualização de implementação estiver em curso. As atualizações de implementação podem ocorrer automaticamente quando:

* Um novo sistema operativo para hóspedes está disponível e você está configurado para atualizações automáticas.
* A cicatrização do serviço ocorre.

Para saber se uma atualização automática está a impedi-lo de fazer uma troca VIP:

1. No portal Azure, clique na implementação do seu serviço na nuvem.
2. No painel **propriedades** do portal Azure, veja o valor do **Estado.** Se estiver **Pronto,** verifique a **última operação** para ver se aconteceu recentemente uma que poderá impedir a troca VIP.
3. Repita os passos 1 e 2 para a colocação da produção.
4. Se estiver em curso uma atualização automática, aguarde que termine antes de tentar fazer a troca VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Uma instância de função está a circular entre Iniciado, Inicialização, Ocupado e Parado
Esta condição poderá indicar um problema com o código, o pacote ou o ficheiro de configuração da aplicação. Nesse caso, deverá ser possível ver o estado a mudar a cada poucos minutos e o portal Azure pode dizer algo como **Reciclagem,** **Ocupado** ou **Inicialização.** Isto indica que há algo de errado com a aplicação que está a impedir que a instância de função seja desacorda.

Para obter mais informações sobre como resolver problemas para este problema, consulte o post de blog [Azure PaaS Compute Diagnostics Datas](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) e [questões comuns que fazem com que as funções reciclem.](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)

## <a name="problem-my-application-stopped-working"></a>Problema: A minha candidatura deixou de funcionar
1. No portal Azure, clique na instância de função.
2. No painel **propriedades** do portal Azure, considere as seguintes condições para resolver o seu problema:
   * Se a instância de função tiver parado recentemente (pode verificar o valor da contagem de **Abort),** a implementação poderá estar a ser atualizada. Aguarde para ver se a instância de função retoma o funcionamento por si só.
   * Se a instância de função estiver **ocupada,** verifique o seu código de aplicação para ver se o evento [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) está tratado. Pode ser necessário adicionar ou corrigir algum código que lide com este evento.
   * Ver os dados de diagnóstico e os cenários de resolução de problemas no blog post [Azure PaaS Compute Diagnostics Data](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

> [!WARNING]
> Se reciclar o seu serviço na nuvem, repõe as propriedades para a implantação, apagando efetivamente a informação para o problema original.
>
>

## <a name="next-steps"></a>Passos seguintes
Veja mais [artigos de resolução de problemas](./cloud-services-allocation-failures.md) para serviços na nuvem.

Para aprender a resolver problemas de problemas de funções de serviço em nuvem utilizando dados de diagnóstico de computador Azure PaaS, consulte a [série de blogs de Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).