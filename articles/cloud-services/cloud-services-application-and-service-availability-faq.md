---
title: Problemas de disponibilidade de aplicação e serviço saem com FAQ
titleSuffix: Azure Cloud Services
description: Este artigo lista as perguntas frequentes sobre a aplicação e disponibilidade de serviços para os Serviços Cloud Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c294d4583ba2690e1f4952441ffb43bff1459059
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75386923"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de disponibilidade de aplicação e serviço para serviços azure cloud: Perguntas frequentes (PERGUNTAS)

Este artigo inclui perguntas frequentes sobre aplicações e problemas de disponibilidade de serviços para [os Serviços Cloud Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar a [página tamanho VM](cloud-services-sizes-specs.md) dos Serviços cloud para obter informações sobre tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>O meu papel foi reciclado. Houve alguma atualização para o meu serviço na nuvem?
Cerca de uma vez por mês, a Microsoft lança uma nova versão do Os Do MS do Windows Azure PaaS.O Os convidado é apenas uma dessas atualizações no pipeline. Uma libertação pode ser afetada por muitos outros fatores. Além disso, azure funciona em centenas de milhares de máquinas. Portanto, é impossível prever a data e a hora exata sem parar quando os seus papéis vão reiniciar. Atualizamos o Feed RSS do Guest OS Update com as informações mais recentes que temos, mas deve considerar que o tempo reportado é um valor aproximado. Estamos conscientes de que isso é problemático para os clientes e estamos a trabalhar num plano para limitar ou precisamente reiniciar o tempo.

Para obter detalhes completos sobre as recentes atualizações do Os guest, consulte os [lançamentos do Os do IO do Hóspede e](cloud-services-guestos-update-matrix.md)a matriz de compatibilidade sdK .

Para obter informações úteis sobre reinícios e indicações para detalhes técnicos das atualizações do Osso de Hóspedes e Anfitriões, consulte o blog mSDN post [Role Instance Restarts Devido a Atualizações do OS](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Porque é que o primeiro pedido ao meu serviço na nuvem depois do serviço estar inativo há algum tempo demora mais do que o habitual?
Quando o Servidor Web recebe o primeiro pedido, primeiro recompila o código e, em seguida, processa o pedido. É por isso que o primeiro pedido demora mais do que os outros. Por padrão, o pool de aplicações é encerrado em casos de inatividade do utilizador. O pool de aplicações também reciclará por padrão a cada 1.740 minutos (29 horas).

Os conjuntos de aplicações do Internet Information Services (IIS) podem ser reciclados periodicamente para evitar estados instáveis que podem levar a falhas de aplicação, enforcamentos ou fugas de memória.

Os seguintes documentos irão ajudá-lo a compreender e mitigar esta questão:
* [Fixação da carga inicial lenta para iIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 aplicação web primeiro pedido após reciclagem de app-pool muito lento](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Se quiser alterar o comportamento padrão do IIS, terá de utilizar tarefas de arranque, pois se aplicar manualmente alterações nas instâncias de Role Web, as alterações acabarão por perder-se.

Para mais informações, consulte [Como configurar e executar tarefas](cloud-services-startup-tasks.md)de arranque para um serviço na nuvem .
