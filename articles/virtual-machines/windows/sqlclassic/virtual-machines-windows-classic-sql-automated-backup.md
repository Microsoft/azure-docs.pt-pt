---
title: Cópia de segurança automatizada para máquinas virtuais (clássico) do SQL Server | Documentos da Microsoft
description: 'Explica a funcionalidade de cópia de segurança automatizada para SQL Server em execução em máquinas virtuais do Azure com o Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aeb97d661d330ed6afb3ca5e5e1eb924dacc4024
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096304"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Cópia de segurança automatizada para SQL Server em máquinas virtuais do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Clássico](../classic/sql-automated-backup.md)
> 
> 

Configura automaticamente a cópia de segurança automatizada [cópia de segurança gerida para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os novos e existentes bases de dados numa VM do Azure com o SQL Server 2014 Standard ou Enterprise. Isto permite-lhe configurar cópias de segurança regular da base de dados que utilizam o armazenamento de Blobs do Azure durável. Cópia de segurança automatizada depende da [extensão de agente IaaS do SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Resource Manager deste artigo, consulte [cópia de segurança automatizada para SQL Server no Gestor de recursos de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a cópia de segurança automatizada, considere os seguintes pré-requisitos:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão/edição do SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Cópia de segurança automatizada para SQL Server 2016 é suportada com as máquinas virtuais do Resource Manager. Para obter mais informações, consulte [v2 de cópia de segurança automatizada para SQL Server 2016 máquinas virtuais do Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Configuração de base de dados**:

* Bases de dados de destino tem de utilizar o modelo de recuperação completo.

**Azure PowerShell**:

* [Instalar os comandos do Azure PowerShell mais recente](/powershell/azure/overview).

**Extensão de IaaS do SQL Server**:

* [Instalar a extensão de IaaS do SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizada. Para VMs clássicas, tem de utilizar o PowerShell para configurar estas definições.

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** |Ativar/desativar (desativado) |Ativa ou desativa a cópia de segurança automatizada para uma VM do Azure com o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** |1-30 dias (30 dias) |O número de dias a manter uma cópia de segurança. |
| **Storage Account** |Conta de armazenamento do Azure (conta de armazenamento criada para a VM especificada) |Uma conta de armazenamento do Azure a utilizar para armazenar ficheiros de cópia de segurança automatizada no armazenamento de Blobs. É criado um contentor nesta localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e o nome da máquina. |
| **Encriptação** |Ativar/desativar (desativado) |Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada no mesmo contentor automaticbackup usando a mesma Convenção de nomenclatura. Se alterar a palavra-passe, um novo certificado é gerado com essa palavra-passe, mas o certificado antigo permanece restaurar cópias de segurança anteriores. |
| **Palavra-passe** |Texto de palavra-passe (nenhum) |Uma palavra-passe para as chaves de encriptação. Isto só é necessário se a encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que da cópia de segurança. |
| **Bases de dados do sistema de cópia de segurança** | Ativar/desativar (desativado) | Fazer cópias de segurança completas de mestra, modelo e da MSDB |
| **Configurar agenda de cópia de segurança** | Manual/automatizada (automatizada) | Selecione **automatizada** automaticamente tirar e cópias de segurança com base no crescimento do registo de registo. Selecione **Manual** para especificar o agendamento de completo e backups de log. |

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
No exemplo de PowerShell seguinte, a cópia de segurança automatizada está configurada para uma VM existente do SQL Server 2014. O **New-AzureVMSqlServerAutoBackupConfig** comando configura as definições de cópia de segurança automatizada para armazenar cópias de segurança na conta de armazenamento do Azure especificada pela variável $storageaccount. Estas cópias de segurança serão mantidas durante 10 dias. O **Set-AzureVMSqlServerExtension** comando atualiza a VM do Azure especificado com estas definições.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode demorar vários minutos para instalar e configurar o agente IaaS do SQL Server.

Para ativar a encriptação, modificar o script anterior para passar o parâmetro EnableEncryption juntamente com uma palavra-passe (cadeia segura) para o parâmetro CertificatePassword. O seguinte script permite que as definições de cópia de segurança automatizada no exemplo anterior e adiciona a encriptação.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desativar a cópia de segurança automática, execute o mesmo script sem o **-ative** parâmetro para o **New-AzureVMSqlServerAutoBackupConfig**. Tal como acontece com a instalação, pode demorar vários minutos para desativar a cópia de segurança automatizada.

> [!NOTE]
> Desativar e desinstalar o agente IaaS do SQL Server não remove as definições de cópia de segurança gerida anteriormente configuradas. Deve desativar a cópia de segurança automatizada antes de desabilitar ou desinstalar o agente IaaS do SQL Server.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Cópia de segurança automatizada configura a cópia de segurança gerida em VMs do Azure. Portanto, é importante [reveja a documentação de cópia de segurança gerida](https://msdn.microsoft.com/library/dn449496.aspx) para compreender o comportamento e as implicações.

Pode encontrar a cópia de segurança adicional e restaurar as orientações para o SQL Server em VMs do Azure no seguinte tópico: [Cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [extensão de agente IaaS do SQL Server](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

