---
title: Gerir os Serviços Azure Cloud utilizando a Azure Automation Microsoft Docs
description: Saiba como o serviço Azure Automation pode ser usado para gerir os serviços em nuvem Azure em escala.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "72439067"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Gerir os Serviços de Nuvem Azure utilizando a Azure Automation
Este guia irá apresentá-lo ao serviço Azure Automation e como pode ser usado para simplificar a gestão dos seus serviços em nuvem Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A Azure Automation](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar a gestão de nuvem através da automatização de processos. A utilização de Azure Automation, tarefas de longa duração, manual, propensas a erros e tarefas frequentemente repetidas pode ser automatizada para aumentar a fiabilidade, eficiência e tempo de valor para a sua organização.

A Azure Automation fornece um motor de execução de fluxo de trabalho altamente fiável e altamente disponível que escala para atender às suas necessidades à medida que a sua organização cresce. Na Azure Automation, os processos podem ser iniciados manualmente, por sistemas de terceiros ou em intervalos programados para que as tarefas ocorram exatamente quando necessário.

Baixar a sobrecarga operacional e libertar pessoal de TI/DevOps para se concentrar no trabalho que adiciona valor de negócio, movendo as suas tarefas de gestão de nuvem para ser executado automaticamente pela Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Como pode a Azure Automation ajudar a gerir os serviços de nuvem Azure?
Os serviços em nuvem Azure podem ser geridos na Azure Automation utilizando os cmdlets PowerShell que estão disponíveis nas [ferramentas Azure PowerShell](/powershell/). A Azure Automation dispõe de estes cmdlets de serviço de cloud PowerShell disponíveis fora da caixa, para que possa executar todas as suas tarefas de gestão de serviços na nuvem dentro do serviço. Pode ainda emparelhar estes cmdlets na Azure Automation com os cmdlets para outros serviços Azure, para automatizar tarefas complexas através dos serviços Azure e sistemas de terceiros.

Alguns exemplos de utilização da Azure Automation para gerir os Serviços Azure Cloud incluem:

* [Implantação contínua de um Serviço de Nuvem sempre que cscfg ou cspkg é atualizado no armazenamento de Azure Blob](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reiniciar instâncias do Serviço de Nuvem em paralelo, um domínio de upgrade de cada vez](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu os fundamentos da Azure Automation e como pode ser usado para gerir os serviços em nuvem Azure, siga estes links para saber mais sobre a Azure Automation.

* [Descrição Geral da Automatização do Azure](../automation/automation-intro.md)
* [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
