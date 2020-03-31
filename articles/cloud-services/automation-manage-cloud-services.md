---
title: Gerir os Serviços Azure Cloud utilizando a Automação Azure [ Microsoft Docs
description: Saiba como o serviço De automação Azure pode ser usado para gerir os serviços de nuvem Azure em escala.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72439067"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Gerir os Serviços Azure Cloud utilizando a Automação Azure
Este guia irá apresentá-lo ao serviço de Automação Azure, e como pode ser usado para simplificar a gestão dos seus serviços de nuvem Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A Azure Automation](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar a gestão da nuvem através da automatização de processos. A utilização de tarefas de longa duração, manual, propensaa a erros e frequentemente repetidas pode ser automatizada para aumentar a fiabilidade, eficiência e tempo para valorizar a sua organização.

A Azure Automation fornece um motor de execução de fluxo de trabalho altamente fiável e altamente disponível que escala para atender às suas necessidades à medida que a sua organização cresce. Na Automação Azure, os processos podem ser iniciados manualmente, por sistemas de terceiros, ou em intervalos programados para que as tarefas aconteçam exatamente quando necessário.

Baixar as despesas operacionais e libertar os colaboradores de TI/DevOps para se concentrarem no trabalho que acrescenta valor ao negócio, movendo as suas tarefas de gestão de nuvem para serem executados automaticamente pela Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Como pode a Azure Automation ajudar a gerir os serviços de nuvem Azure?
Os serviços de nuvem Azure podem ser geridos na Automatização Azure utilizando os cmdlets PowerShell que estão disponíveis nas [ferramentas Azure PowerShell.](/powershell/) A Azure Automation dispõe destes cmdlets powerShell de serviço na nuvem disponíveis fora da caixa, para que possa executar todas as suas tarefas de gestão de serviços na nuvem dentro do serviço. Também pode emparelhar estes cmdlets na Automatização Azure com os cmdlets para outros serviços Azure, para automatizar tarefas complexas em serviços Azure e sistemas de terceiros.

Alguns exemplos de utilizações da Azure Automation para gerir os Serviços azure cloud incluem:

* [Implantação contínua de um Serviço de Nuvem sempre que cscfg ou cspkg são atualizados no armazenamento Azure Blob](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reiniciar as instâncias do Cloud Service em paralelo, um domínio de upgrade de cada vez](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu o básico da Automação Azure e como pode ser usado para gerir os serviços de nuvem Azure, siga estes links para saber mais sobre a Automação Azure.

* [Descrição Geral da Automatização do Azure](../automation/automation-intro.md)
* [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
