---
title: incluir ficheiro
description: incluir ficheiro
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a5532b7a4574f3c2cc057255742c72bf032d180b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456803"
---
Estes exemplos ilustram várias tarefas que se pode realizar em cenários de gestão entre inquilinos.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Implementa contas de armazenamento em dois grupos de recursos diferentes.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Cria os serviços de gestão do Azure, interliga-os e implementa soluções adicionais. Para fazer uma implementação completa, utilize o modelo **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Ativa e configura o Centro de Segurança do Azure na subscrição do Azure visada. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Implementa e ativa o Azure Sentinel numa área de trabalho do Log Analytics existente numa subscrição delegada. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Estes modelos permitem-lhe implementar extensões de VM do Log Analytics nas suas VMs do Windows e Linux, ligando-as à área de trabalho do Log Analytics designada |
