---
title: Modelos do Azure Resource Manager
description: Modelos de Gestor de Recursos Azure para uso com cofres de serviços de recuperação e funcionalidades de backup Azure
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: a4c2f444cb821f7979571b9d777895a59450e7c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96309584"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Modelos do Azure Resource Manager para o Azure Backup

A tabela seguinte inclui ligações para modelos do Azure Resource Manager para utilização com os cofres dos Serviços de Recuperação e as funcionalidades do Azure Backup. Para saber mais sobre a sintaxe e propriedades JSON, consulte os [tipos de recursos microsoft.RecoveryServices](/azure/templates/microsoft.recoveryservices/allversions).

| Modelo | Description |
|---|---|
|**Cofre dos Serviços de Recuperação** | |
| [Criar um cofre dos Serviços de Recuperação](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Crie um cofre dos Serviços de Recuperação. O cofre pode ser utilizado no Azure Backup e no Azure Site Recovery. |
|**Fazer uma cópia de segurança das máquinas virtuais**| |
| [Fazer uma cópia de segurança das VMs do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Utilize o cofre dos Serviços de Recuperação e a política de Cópia de Segurança existentes para fazer uma cópia de segurança das máquinas virtuais do Resource Manager no mesmo grupo de recursos.|
| [Fazer uma cópia de segurança das VMs IaaS para o cofre dos Serviços de Recuperação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Modelo para fazer cópias de segurança de máquinas virtuais clássicas e do Resource Manager. |
| [Criar uma política de Cópia de Segurança Semanal para VMs IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | O modelo cria o cofre dos Serviços de Recuperação e uma política de backup semanal, que é usada para apoiar máquinas clássicas e virtuais do Gestor de Recursos.|
| [Criar uma política de Cópia de Segurança Diária para VMs IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | O modelo cria o cofre dos Serviços de Recuperação e uma política diária de backup, que é usada para apoiar máquinas clássicas e virtuais do Gestor de Recursos.|
| [Implementar a VM do Windows Server com cópia de segurança ativada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | O modelo cria uma VM do Windows Server e um cofre dos Serviços de Recuperação com a política de cópia de segurança predefinida ativada.|
|**Monitorize trabalhos de backup** |  |
| [Use registos do Monitor Azure com backup Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | O modelo implementa registos do Azure Monitor com Azure Backup, que permite monitorizar backup e restaurar trabalhos, alertas de backup e o armazenamento em Nuvem utilizado nos cofres dos Serviços de Recuperação.|  
|**Back up SQL Server em Azure VM** |  |
| [Back up SQL Server em Azure VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | O modelo cria uma abóbada de serviços de recuperação e uma política de backup específica da carga de trabalho. Regista o VM com o serviço de backup Azure e a Proteção de Configurações nesse VM. Atualmente, funciona apenas para imagens da SqL Gallery. |
|**Fazer cópia de segurança das partilhas de ficheiros do Azure** |  |
| [Fazer cópia de segurança das partilhas de ficheiros do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-file-share) | Este modelo configura a proteção para uma partilha de ficheiros Azure existente, especificando detalhes apropriados para a política de cofre e backup dos Serviços de Recuperação. Cria opcionalmente uma nova política de cofre e backup dos Serviços de Recuperação, e regista a conta de armazenamento que contém a parte do ficheiro no cofre dos Serviços de Recuperação. |
|   |   |
