---
title: Backup automatizado para Máquinas Virtuais do Servidor SQL (Clássico) [ Microsoft Docs
description: 'Explica a função de backup automatizada para o Servidor SQL em execução em Máquinas Virtuais Azure utilizando o Gestor de Recursos. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ff230d4769a23c9007b3da29858d2105366f9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978105"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Backup automatizado para Servidor SQL em Máquinas Virtuais Azure (Clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Clássica](../classic/sql-automated-backup.md)
> 
> 

Backup automatizado configura automaticamente [Backup Gerido para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas as bases de dados existentes e novas em um Azure VM executando SQL Server 2014 Standard ou Enterprise. Isto permite configurar cópias de dados regulares que utilizam armazenamento de blob Azure durável. A cópia de segurança automatizada depende da extensão do [agente IaaS do Servidor SQL](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de Recursos deste artigo, consulte [backup automatizado para O Servidor SQL no Gestor](../sql/virtual-machines-windows-sql-automated-backup.md)de Recursos de Máquinas Virtuais Azure .

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar backup automatizado, considere os seguintes pré-requisitos:

**Sistema operativo:**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão/edição do SQL Server:**

* Padrão SQL Server 2014
* SQL Server 2014 Enterprise

> [!NOTE]
> Backup automatizado para SQL Server 2016 é suportado com máquinas virtuais do Gestor de Recursos. Para mais informações, consulte [Backup Automatizado v2 para SQL Server 2016 Máquinas Virtuais Azure (Gestor de Recursos)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Configuração da base de dados:**

* As bases de dados-alvo devem utilizar o modelo de recuperação completo.

**Azure PowerShell:**

* [Instale os mais recentes comandos Azure PowerShell](/powershell/azure/overview).

**Extensão SQL Server IaaS**:

* [Instale a extensão SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para Backup Automatizado. Para VMs clássicos, deve utilizar o PowerShell para configurar estas definições.

| Definição | Gama (Padrão) | Descrição |
| --- | --- | --- |
| **Backup automatizado** |Ativar/Desativar (Desativado) |Ativa ou desativa backup automatizado para um Azure VM executando SQL Server 2014 Standard ou Enterprise. |
| **Período de Retenção** |1-30 dias (30 dias) |O número de dias para reter um reforço. |
| **Conta de Armazenamento** |Conta de armazenamento Azure (a conta de armazenamento criada para o VM especificado) |Uma conta de armazenamento Azure para armazenar ficheiros de backup automatizados no armazenamento de blob. Um recipiente é criado neste local para armazenar todos os ficheiros de reserva. A convenção de nomeação de ficheiros de reserva inclui a data, hora e nome da máquina. |
| **Encriptação** |Ativar/Desativar (Desativado) |Permite ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada no mesmo recipiente de reserva automática utilizando a mesma convenção de nomeação. Se a palavra-passe mudar, um novo certificado é gerado com essa palavra-passe, mas o certificado antigo permanece para restaurar cópias de segurança prévias. |
| **Palavra-passe** |Texto de palavra-passe (Nenhum) |Uma senha para chaves de encriptação. Isto só é necessário se a encriptação estiver ativada. Para restaurar uma cópia de segurança encriptada, deve ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento em que a cópia de segurança foi tomada. |
| **Realizar cópia de segurança das bases de dados do sistema** | Ativar/Desativar (Desativado) | Tome todos os backups de Master, Model e MSDB |
| **Configurar agenda da cópia de segurança** | Manual/Automatizado (Automatizado) | **Selecione Automatizado** para tomar automaticamente cópias de segurança completas e de log com base no crescimento do registo. Selecione **Manual** para especificar o horário para cópias de segurança completas e de registo. |

## <a name="configuration-with-powershell"></a>Configuração com PowerShell
No exemplo do PowerShell seguinte, a Backup Automatizada está configurada para um VM SQL Server 2014 existente. O comando **New-AzureVMSqlServerAutoBackupConfig** configura as definições de backup automatizada para armazenar cópias de segurança na conta de armazenamento Azure especificada pela variável $storageaccount. Estes reforços serão retidos por 10 dias. O comando **Set-AzureVMSqlServerExtension** atualiza o VM Azure especificado com estas definições.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode levar vários minutos para instalar e configurar o Agente IaaS do Servidor SQL.

Para permitir a encriptação, modifique o script anterior para passar o parâmetro EnableEncryption juntamente com uma palavra-passe (string segura) para o parâmetro CertificatePassword. O seguinte script permite as definições de Backup Automatizada no exemplo anterior e adiciona encriptação.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desativar a cópia de segurança automática, execute o mesmo script sem o **parâmetro -Ativar** o **Novo AzureVMSqlServerAutoBackupConfig**. Tal como na instalação, pode levar vários minutos a desativar a Cópia de Segurança Automática.

> [!NOTE]
> Desativar e desinstalar o Agente IaaS do Servidor SQL não remove as definições de backup geridas previamente configuradas. Deve desativar a cópia de segurança automática antes de desinstalar ou desinstalar o agente IaaS do Servidor SQL.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Backup automatizado configures Managed Backup em VMs Azure. Por isso, é importante [rever a documentação para Backup Gerido](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e implicações.

Pode encontrar uma cópia de segurança adicional e restaurar a orientação para o Servidor SQL em VMs Azure no seguinte tópico: [Backup e Restore for SQL Server in Azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte a extensão do [agente SQL Server IaaS](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre a execução do Servidor SQL em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

