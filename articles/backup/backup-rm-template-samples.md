---
title: Modelos do Azure Resource Manager para o Azure Backup
description: Exemplos do PowerShell para o Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bed2c002cacdac1e47852e6fa3181885af6733d2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236790"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Modelos do Azure Resource Manager para o Azure Backup

A tabela seguinte inclui ligações para modelos do Azure Resource Manager para utilização com os cofres dos Serviços de Recuperação e as funcionalidades do Azure Backup. Para saber mais sobre a sintaxe JSON e propriedades, veja [tipos de recursos Microsoft. recoveryservices](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Cofre dos Serviços de Recuperação** | |
| [Criar um cofre dos Serviços de Recuperação](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Crie um cofre dos Serviços de Recuperação. O cofre pode ser utilizado no Azure Backup e no Azure Site Recovery. |
|**Fazer uma cópia de segurança das máquinas virtuais**| |
| [Fazer uma cópia de segurança das VMs do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Utilize o cofre dos Serviços de Recuperação e a política de Cópia de Segurança existentes para fazer uma cópia de segurança das máquinas virtuais do Resource Manager no mesmo grupo de recursos.|
| [Fazer uma cópia de segurança das VMs IaaS para o cofre dos Serviços de Recuperação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Modelo para fazer cópias de segurança de máquinas virtuais clássicas e do Resource Manager. |
| [Criar uma política de Cópia de Segurança Semanal para VMs IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | O modelo cria o Cofre dos serviços de recuperação e uma política de cópia de segurança semana, que é utilizada para fazer uma cópia de segurança de máquinas virtuais para o Resource Manager e clássicas.|
| [Criar uma política de Cópia de Segurança Diária para VMs IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | O modelo cria o Cofre dos serviços de recuperação e uma política de cópia de segurança diária, que é utilizada para fazer uma cópia de segurança de máquinas virtuais para o Resource Manager e clássicas.|
| [Implementar a VM do Windows Server com cópia de segurança ativada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | O modelo cria uma VM do Windows Server e um cofre dos Serviços de Recuperação com a política de cópia de segurança predefinida ativada.|
|**Monitorizar tarefas de cópia de segurança** |  |
| [Utilizar registos de Monitor do Azure com o Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Modelo implementa logs de Monitor do Azure com o Azure Backup, que permite-lhe monitorizar a cópia de segurança e restaurar trabalhos, alertas de cópia de segurança e o armazenamento na Cloud utilizadas nos seus cofres de serviços de recuperação.|  
|**Criar cópias de segurança do SQL Server na VM do Azure** |  |
| [Criar cópias de segurança do SQL Server na VM do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | O modelo cria um cofre dos serviços de recuperação e a política de cópia de segurança específica de carga de trabalho. Ele registra a VM com o serviço de cópia de segurança do Azure e configura proteção nessa VM. Atualmente, só funciona para as imagens da Galeria de SQL. |
|   |   |
