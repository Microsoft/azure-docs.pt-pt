---
title: Perguntas frequentes sobre problemas de disponibilidade de aplicativos e serviços
titleSuffix: Azure Cloud Services
description: Este artigo lista as perguntas frequentes sobre a disponibilidade de aplicativos e serviços para Serviços de Nuvem do Microsoft Azure.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75386923"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de disponibilidade de aplicativo e serviço para serviços de nuvem do Azure: perguntas frequentes (FAQs)

Este artigo inclui perguntas frequentes sobre problemas de disponibilidade de aplicativos e serviços para [serviços de nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página tamanho da VM dos serviços de nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Minha função foi reciclada. Alguma atualização foi distribuída para o meu serviço de nuvem?
Aproximadamente uma vez por mês, a Microsoft lança uma nova versão do sistema operacional convidado para VMs de PaaS do Windows Azure. O SO convidado é apenas uma dessas atualizações no pipeline. Uma versão pode ser afetada por muitos outros fatores. Além disso, o Azure é executado em centenas de milhares de computadores. Portanto, é impossível prever a data e a hora exatas em que suas funções serão reinicializadas. Atualizamos o RSS feed de atualização do SO convidado com as informações mais recentes que temos, mas você deve considerar que o tempo relatado é um valor aproximado. Estamos cientes de que isso é problemático para os clientes e estamos trabalhando em um plano para limitar ou reinicializar com precisão a hora.

Para obter detalhes completos sobre atualizações recentes do sistema operacional convidado, consulte [versões do SO convidado do Azure e matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).

Para obter informações úteis sobre reinicializações e ponteiros para detalhes técnicos de atualizações de sistema operacional de convidado e host, consulte a postagem de blog do MSDN [reinicializações de instância de função devido a atualizações do sistema operacional](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Por que a primeira solicitação para meu serviço de nuvem depois que o serviço esteve ocioso por algum tempo demora mais do que o normal?
Quando o servidor Web recebe a primeira solicitação, ele primeiro recompila o código e, em seguida, processa a solicitação. É por isso que a primeira solicitação demora mais do que as outras. Por padrão, o pool de aplicativos é desligado em casos de inatividade do usuário. O pool de aplicativos também será reciclado por padrão a cada 1.740 minutos (29 horas).

Os pools de aplicativos Serviços de Informações da Internet (IIS) podem ser reciclados periodicamente para evitar Estados instáveis que podem levar a falhas de aplicativo, travas ou vazamentos de memória.

Os documentos a seguir irão ajudá-lo a entender e atenuar esse problema:
* [Corrigindo carga inicial lenta para o IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [Aplicativo Web do IIS 7,5-primeira solicitação após a reciclagem do pool de aplicativos muito lenta](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Se desejar alterar o comportamento padrão do IIS, você precisará usar as tarefas de inicialização, porque se você aplicar alterações manualmente às instâncias da função Web, as alterações serão eventualmente perdidas.

Para obter mais informações, consulte [como configurar e executar tarefas de inicialização para um serviço de nuvem](cloud-services-startup-tasks.md).
