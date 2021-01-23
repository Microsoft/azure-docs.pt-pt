---
title: Questões de aplicação e disponibilidade de serviços FAQ
description: Este artigo lista as perguntas frequentes sobre a disponibilidade de aplicações e serviços para os Serviços cloud do Microsoft Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c1a5b63a33f951857bd4837380c1465af5b7583e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742901"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problemas de aplicação e disponibilidade de serviços para Azure Cloud Services (clássico): Perguntas frequentes (PERGUNTAS)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Este artigo inclui perguntas frequentes sobre problemas de aplicação e disponibilidade de serviços para [os Serviços cloud do Microsoft Azure.](https://azure.microsoft.com/services/cloud-services) Também pode consultar a [página de tamanho VM dos Serviços cloud](cloud-services-sizes-specs.md) para obter informações sobre o tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>O meu papel foi reciclado. Houve alguma atualização para o meu serviço na nuvem?
Aproximadamente uma vez por mês, a Microsoft lança uma nova versão guest OS para Windows Azure PaaS VMs.O Guest OS é apenas uma dessas atualizações no pipeline. Uma libertação pode ser afetada por muitos outros fatores. Além disso, o Azure funciona com centenas de milhares de máquinas. Portanto, é impossível prever a data e a hora exatas em que os seus papéis serão reiniciados. Atualizamos o Feed RSS da Atualização de Hóspedes com as informações mais recentes que temos, mas deve considerar que o tempo reportado é um valor aproximado. Estamos cientes de que isto é problemático para os clientes e estamos a trabalhar num plano para limitar ou precisamente reiniciar o tempo.

Para obter detalhes completos sobre as recentes atualizações do Guest OS, consulte [os lançamentos do Azure Guest OS e a matriz de compatibilidade SDK](cloud-services-guestos-update-matrix.md).

Para obter informações úteis sobre reinícios e ponteiros para detalhes técnicos das atualizações do Guest and Host OS, consulte o blog MSDN post [Role Instance Restarts Devido às atualizações do OS](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Porque é que o primeiro pedido para o meu serviço de nuvem depois do serviço estar inativo há algum tempo demora mais do que o habitual?
Quando o Web Server recebe o primeiro pedido, primeiro recompensa o código e, em seguida, processa o pedido. É por isso que o primeiro pedido demora mais do que os outros. Por padrão, o pool de aplicações é encerrado em casos de inatividade do utilizador. O pool de aplicações também recicla por padrão a cada 1.740 minutos (29 horas).

Os grupos de aplicações dos Serviços de Informação da Internet (IIS) podem ser reciclados periodicamente para evitar estados instáveis que podem levar a falhas de aplicações, peneirações ou fugas de memória.

Os seguintes documentos irão ajudá-lo a compreender e a mitigar esta questão:
* [Fixação de carga inicial lenta para IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 web application primeiro pedido após app-pool reciclar muito lento](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Se quiser alterar o comportamento padrão do IIS, terá de utilizar tarefas de arranque, porque se aplicar manualmente alterações nas instâncias de Função Web, as alterações acabarão por ser perdidas.

Para obter mais informações, consulte [Como configurar e executar tarefas de arranque para um serviço de cloud](cloud-services-startup-tasks.md).