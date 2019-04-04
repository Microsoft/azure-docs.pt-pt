---
title: Gerir serviços Cloud do Azure com a automatização do Azure | Documentos da Microsoft
description: Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir serviços cloud do Azure em escala.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: b3660901c86dd644369e6d1913e825cbd5ea316b
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918472"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Gerir serviços Cloud do Azure com a automatização do Azure
Este guia apresenta-lhe para o serviço de automatização do Azure e como ele pode ser usado para simplificar a gestão de serviços cloud do Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A automatização do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar a gestão da cloud através de automatização de processos. Utilizar a automatização do Azure, as tarefas de longa execução, manuais, propenso a erros e repetidas com frequência podem ser automatizadas para aumentar a confiabilidade, a eficiência e a hora de valor para a sua organização.

A automatização do Azure fornece um motor de execução do fluxo de trabalho altamente fiável e de elevada disponibilidade que se dimensiona para atender às suas necessidades, à medida que aumenta a sua organização. Na automatização do Azure, processos podem ser iniciados manualmente, por sistemas de terceiros 3rd ou em intervalos agendados, de modo a que tarefas acontecem exatamente quando necessário.

Reduzir o overhead operacional e liberar IT / equipa do DevOps para se dedicar ao trabalho que adiciona business value movendo as tarefas de gestão na cloud a ser executada automaticamente pela automatização do Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Como pode automatização do Azure ajudar a gerir serviços cloud do Azure?
Serviços cloud do Azure podem ser geridos na automatização do Azure, utilizando os cmdlets do PowerShell que estão disponíveis no [ferramentas do Azure PowerShell](/powershell/). A automatização do Azure tem estes cmdlets do PowerShell cloud service disponíveis de imediato, para que possa executar todas as suas tarefas de gestão de serviço cloud no serviço. Também pode emparelhar estes cmdlets na automatização do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas em serviços do Azure e 3º sistemas de terceiros.

Alguns usos de exemplo da automatização do Azure para gerir serviços Cloud do Azure incluem:

* [Implementação contínua de um serviço Cloud sempre que cscfg ou cspkg é atualizado no armazenamento de Blobs do Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reiniciar instâncias de serviço em nuvem em paralelo, um domínio de atualização de cada vez](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Próximos Passos
Agora que aprendeu as noções básicas da automatização do Azure e como ele pode ser usado para gerir serviços cloud do Azure, siga estas ligações para saber mais sobre a automatização do Azure.

* [Descrição Geral da Automatização do Azure](../automation/automation-intro.md)
* [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizagem de automatização do Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
