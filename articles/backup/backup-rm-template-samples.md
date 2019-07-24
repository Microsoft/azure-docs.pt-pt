---
title: Modelos do Azure Resource Manager para o Azure Backup
description: Exemplos do PowerShell para o Azure Backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7bd502f86cabfcfb5048921d118802a780cd86e4
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464958"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Modelos do Azure Resource Manager para o Azure Backup

A tabela seguinte inclui ligações para modelos do Azure Resource Manager para utilização com os cofres dos Serviços de Recuperação e as funcionalidades do Azure Backup. Para saber mais sobre a sintaxe e as propriedades JSON, consulte [tipos de recurso Microsoft. recoveryservices](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Cofre dos Serviços de Recuperação** | |
| [Criar um cofre dos Serviços de Recuperação](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Crie um cofre dos Serviços de Recuperação. O cofre pode ser utilizado no Azure Backup e no Azure Site Recovery. |
|**Fazer uma cópia de segurança das máquinas virtuais**| |
| [Fazer uma cópia de segurança das VMs do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Utilize o cofre dos Serviços de Recuperação e a política de Cópia de Segurança existentes para fazer uma cópia de segurança das máquinas virtuais do Resource Manager no mesmo grupo de recursos.|
| [Fazer uma cópia de segurança das VMs IaaS para o cofre dos Serviços de Recuperação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Modelo para fazer cópias de segurança de máquinas virtuais clássicas e do Resource Manager. |
| [Criar uma política de Cópia de Segurança Semanal para VMs IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | O modelo cria um cofre de serviços de recuperação e uma política de backup semanal, que é usada para fazer backup de máquinas virtuais clássicas e do Resource Manager.|
| [Criar uma política de Cópia de Segurança Diária para VMs IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | O modelo cria um cofre de serviços de recuperação e uma política de backup diário, que é usada para fazer backup de máquinas virtuais clássicas e do Resource Manager.|
| [Implementar a VM do Windows Server com cópia de segurança ativada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | O modelo cria uma VM do Windows Server e um cofre dos Serviços de Recuperação com a política de cópia de segurança predefinida ativada.|
|**Monitorizar tarefas de cópia de segurança** |  |
| [Usar logs de Azure Monitor com o backup do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | O modelo implanta logs de Azure Monitor com o backup do Azure, que permite monitorar trabalhos de backup e restauração, alertas de backup e o armazenamento em nuvem usado em seus cofres de serviços de recuperação.|  
|**Fazer backup de SQL Server na VM do Azure** |  |
| [Fazer backup de SQL Server na VM do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | O modelo cria um cofre dos serviços de recuperação e uma política de backup específica da carga de trabalho. Ele registra a VM com o serviço de backup do Azure e configura a proteção nessa VM. Atualmente, ele funciona apenas para imagens da galeria do SQL. |
|   |   |
