---
title: Utilizar modelos Azure Resource Manager no Azure Stack | Documentos da Microsoft
description: Saiba como utilizar modelos Azure Resource Manager no Azure Stack para Aprovisionar recursos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: 98bc87dcaf229671647b201fe37eee114abad731
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648817"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utilizar modelos Azure Resource Manager no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode utilizar modelos Azure Resource Manager para implementar e aprovisionar todos os recursos para a sua aplicação numa operação única e coordenada. Também pode voltar a implementar modelos para fazer alterações aos recursos num grupo de recursos.

Estes modelos podem ser implementados com o portal do Microsoft Azure Stack, PowerShell, a linha de comandos e o Visual Studio.

Os seguintes modelos de início rápido estão disponíveis na [GitHub](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Implantar o SharePoint Server (implementação de não-elevada disponibilidade)

Utilizar a extensão DSC do PowerShell para [criar um farm do SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) que inclui os seguintes recursos:

* Uma rede virtual
* Três contas de armazenamento
* Dois balanceadores de carga externo
* Uma máquina de virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio
* Uma VM configurada como um servidor autónomo do SQL Server 2014
* Uma VM configurada como um farm do SharePoint Server 2013 de uma máquina

## <a name="deploy-ad-non-high-availability-deployment"></a>Implementar AD (não-alto--implementação de disponibilidade)

Utilizar a extensão DSC do PowerShell para [criar um servidor de controlador de domínio do AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) que inclui os seguintes recursos:

* Uma rede virtual
* Uma conta de armazenamento
* Um balanceador de carga externo
* Uma máquina de virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio

## <a name="deploy-adsql-non-high-availability-deployment"></a>Implementar o AD/SQL (não-alto--implementação de disponibilidade)

Utilizar a extensão DSC do PowerShell para [criar um servidor autónomo do SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) que inclui os seguintes recursos:

* Uma rede virtual
* Duas contas de armazenamento
* Um balanceador de carga externo
* Uma máquina de virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio
* Uma VM configurada como um servidor autónomo do SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Utilize a extensão DSC do PowerShell para configurar uma máquina virtual existente Gestor de configuração Local (LCM) e registá-lo para um servidor de solicitação de DSC do Azure Automation conta.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Criar uma máquina virtual a partir de uma imagem de utilizador

[Criar uma máquina virtual a partir de uma imagem de utilizador personalizada](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-from-user-image). Este modelo também implementa uma rede virtual (com o DNS), o endereço IP público e uma interface de rede.

## <a name="basic-virtual-machine"></a>Máquinas virtuais básico

[Implementar uma VM do Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) que inclui uma rede virtual (com o DNS), o endereço IP público e uma interface de rede.

## <a name="cancel-a-running-template-deployment"></a>Cancelar uma implementação de modelo em execução

Para cancelar uma implementação de modelo em execução, utilize o [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) cmdlet do PowerShell.

## <a name="next-steps"></a>Passos Seguintes

* [Implementar modelos com o portal](azure-stack-deploy-template-portal.md)
* [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
