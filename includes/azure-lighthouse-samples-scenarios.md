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
ms.openlocfilehash: 4c50ca2449787d681d8b9571083a92077d95dcd8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986643"
---
Estes exemplos ilustram várias tarefas que se pode realizar em cenários de gestão entre inquilinos.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Implementa contas de armazenamento em dois grupos de recursos diferentes.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Cria os serviços de gestão do Azure, interliga-os e implementa soluções adicionais. Para fazer uma implementação completa, utilize o modelo **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Ativa e configura o Centro de Segurança do Azure na subscrição do Azure visada. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Implementa e ativa o Azure Sentinel numa área de trabalho do Log Analytics existente numa subscrição delegada. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Estes modelos permitem-lhe implementar extensões de VM do Log Analytics nas suas VMs do Windows e Linux, ligando-as à área de trabalho do Log Analytics designada |
