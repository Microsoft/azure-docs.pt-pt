---
title: Gerenciar serviços de nuvem do Azure usando a automação do Azure | Microsoft Docs
description: Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar os serviços de nuvem do Azure em escala.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 1fc1ac9668454578ee137a2ddd1b85319fea0a28
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945508"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Gerenciando serviços de nuvem do Azure usando a automação do Azure
Este guia apresentará o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento de seus serviços de nuvem do Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
A [automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Usando a automação do Azure, tarefas de longa execução, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na automação do Azure, os processos podem ser iniciados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas ocorram exatamente quando necessário.

Diminua a sobrecarga operacional e libere a equipe de ti/DevOps para se concentrar no trabalho que agrega valor comercial, movendo suas tarefas de gerenciamento de nuvem para serem executadas automaticamente pela automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Como a automação do Azure pode ajudar a gerenciar os serviços de nuvem do Azure?
Os serviços de nuvem do Azure podem ser gerenciados na automação do Azure usando os cmdlets do PowerShell que estão disponíveis nas [ferramentas de Azure PowerShell](/powershell/). A automação do Azure tem esses cmdlets do PowerShell de serviço de nuvem disponíveis prontos para uso, para que você possa executar todas as suas tarefas de gerenciamento de serviço de nuvem dentro do serviço. Você também pode emparelhar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.

Alguns exemplos de uso da automação do Azure para gerenciar os serviços de nuvem do Azure incluem:

* [Implantação contínua de um serviço de nuvem sempre que cscfg ou cspkg é atualizado no armazenamento de BLOBs do Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reinicializando instâncias do serviço de nuvem em paralelo, um domínio de atualização por vez](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Próximos Passos
Agora que você aprendeu os conceitos básicos da automação do Azure e como ele pode ser usado para gerenciar os serviços de nuvem do Azure, siga estes links para saber mais sobre a automação do Azure.

* [Visão geral da automação do Azure](../automation/automation-intro.md)
* [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizagem da automação do Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
