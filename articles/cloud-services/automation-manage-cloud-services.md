---
title: Gerir os Serviços de Nuvem Azure (clássico) utilizando a Azure Automation | Microsoft Docs
description: Saiba como o serviço Azure Automation pode ser usado para gerir os serviços em nuvem Azure em escala.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 530efd09f3632637c6a12648495dcff0e7bf0e6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743496"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Gerir os Serviços Azure Cloud (clássico) utilizando a Azure Automation

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).
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
* [O meu primeiro runbook](../automation/learn/automation-tutorial-runbook-graphical.md)