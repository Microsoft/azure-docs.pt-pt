---
title: Problemas de disponibilidade de serviço para perguntas frequentes do Microsoft Azure Cloud Services e de aplicação | Documentos da Microsoft
description: Este artigo apresenta uma lista de perguntas mais frequentes sobre a disponibilidade do serviço de aplicações e para os serviços de Cloud do Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fb4b5dde63d8c7c75419d3202d9848cd6fde8b8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547644"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de disponibilidade de serviço para serviços Cloud do Azure e de aplicação: Perguntas mais frequentes (FAQ)

Este artigo contém perguntas mais frequentes sobre a aplicação e problemas de disponibilidade de serviço para [serviços Cloud do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar o [página de tamanho de VM de serviços Cloud](cloud-services-sizes-specs.md) para informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>A minha função de obteve reciclada. Houve qualquer atualização implementada para o meu serviço em nuvem?
Aproximadamente uma vez por mês, a Microsoft lança uma nova versão de SO convidado para VMs do Windows Azure PaaS. O SO convidado é apenas um desse atualização no pipeline. Uma versão pode ser afetada por muitos outros fatores. Além disso, o Azure é executado em centenas de milhares de máquinas. Por conseguinte, é impossível prever a data e hora exatas quando as suas funções serão reiniciado. O convidado SO atualizar Feed RSS é atualizado com as informações mais recentes que temos, mas deve considerar que reportado tempo para ser um valor aproximado. Estamos cientes de que isso é problemático para os clientes e estiver a trabalhar num plano de limite ou precisamente os reinícios de tempo.

Para obter detalhes completos sobre as atualizações recentes de SO convidado, consulte [versões de SO convidado do Azure e matriz de compatibilidade SDK](cloud-services-guestos-update-matrix.md).

Para obter informações úteis sobre os reinícios e ponteiros para os detalhes técnicos de atualizações de convidado e o SO anfitrião, consulte a mensagem de blogue do MSDN [função de instância é reiniciado devido a atualizações de SO](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Por que a primeira solicitação ao meu serviço em nuvem, depois do serviço está inativo há algum tempo demora mais tempo do que o normal?
Quando o servidor Web recebe a primeira solicitação, ele primeiro as recompilações o código e, em seguida, processa a solicitação. É por isso que o primeiro pedido demora mais tempo do que as outras. Por predefinição, o pool de aplicativos é encerrado em casos de inatividade do usuário. O pool de aplicativos também haverá Reciclagem por predefinição a cada 1,740 minutos (29 horas).

Falha de aplicação de serviços de informação Internet (IIS) conjuntos poderem ser periodicamente reciclados para evitar Estados instáveis, que podem levar a aplicação, paradas ou vazamentos de memória.

Os documentos seguintes irão ajudá-lo a entender e atenuar este problema:
* [Corrigir o carregamento inicial lento para o IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 web application primeira solicitação após a Reciclagem de pool de aplicativos muito lenta](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Se pretender alterar o comportamento predefinido do IIS, terá de utilizar tarefas de arranque, porque se aplicar manualmente as alterações para as instâncias de função da Web, as alterações, eventualmente, serão perdidas.

Para obter mais informações, consulte [como configurar e executar tarefas de arranque para um serviço cloud](cloud-services-startup-tasks.md).
