---
title: Backup automatizado para máquinas virtuais do servidor SQL (Clássico) Microsoft Docs
description: 'Explica a função de cópia de segurança automatizada para o servidor SQL em execução em Máquinas Virtuais Azure utilizando o Gestor de Recursos. '
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
ms.openlocfilehash: f62004f01e48a42702c93493e3b0dc1c11f6eb30
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078111"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Backup automatizado para servidor SQL em Máquinas Virtuais Azure (Clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
> * [Clássico](../classic/sql-automated-backup.md)
> 
> 

Cópia de segurança automatizada configura automaticamente [o Backup Gerido para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas as bases de dados existentes e novas num VM Azure que executa o SQL Server 2014 Standard ou Enterprise. Isto permite-lhe configurar cópias de dados regulares que utilizam o armazenamento de bolhas Azure duráveis. A cópia de segurança automatizada depende da extensão do [agente IAAS do sql Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de Recursos deste artigo, consulte backup automatizado para O Servidor SQL no Gestor de [Recursos de Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a cópia de segurança automatizada, considere os seguintes pré-requisitos:

**Sistema operativo:**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão/edição do SQL Server:**

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> A Cópia de Segurança Automatizada para O Servidor SQL 2016 é suportada com máquinas virtuais do Gestor de Recursos. Para obter mais informações, consulte [Backup automático v2 para SQL Server 2016 Azure Virtual Machines (Gestor de Recursos)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Configuração da base de dados:**

* As bases de dados-alvo devem utilizar o modelo de recuperação total.

**Azure PowerShell:**

* [Instale os mais recentes comandos Azure PowerShell](/powershell/azure/overview).

**Extensão IAAS do servidor SQL:**

* [Instale a extensão IAAS do servidor SQL](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela a seguir descreve as opções que podem ser configuradas para cópia de segurança automatizada. Para os VM clássicos, deve utilizar o PowerShell para configurar estas definições.

| Definição | Alcance (Padrão) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** |Ativar/Desativar (Desativado) |Ativa ou desativa a Cópia de Segurança Automatizada para um Azure VM que executa o SQL Server 2014 Standard ou Enterprise. |
| **Período de Retenção** |1-30 dias (30 dias) |O número de dias para reter um reforço. |
| **Conta de Armazenamento** |Conta de armazenamento Azure (a conta de armazenamento criada para o VM especificado) |Uma conta de armazenamento Azure para utilizar para armazenar ficheiros de backup automatizados no armazenamento de bolhas. Neste local é criado um contentor para armazenar todos os ficheiros de backup. A convenção de nomeação de ficheiros de reserva inclui a data, hora e nome da máquina. |
| **Encriptação** |Ativar/Desativar (Desativado) |Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada no mesmo recipiente de retorno automático utilizando a mesma convenção de nomeação. Se a palavra-passe mudar, é gerado um novo certificado com essa palavra-passe, mas o certificado antigo permanece para restaurar cópias de segurança prévias. |
| **Palavra-passe** |Texto de senha (Nenhum) |Uma senha para chaves de encriptação. Isto só é necessário se a encriptação estiver ativada. Para restaurar uma cópia de segurança encriptada, deve ter a senha correta e certificado relacionado que foi usado no momento em que a cópia de segurança foi tomada. |
| **Realizar cópia de segurança das bases de dados do sistema** | Ativar/Desativar (Desativado) | Pegue cópias de segurança completas de Master, Model e MSDB |
| **Configurar agenda da cópia de segurança** | Manual/Automatizado (Automatizado) | Selecione **Automated** para tomar automaticamente cópias de segurança completas e registar backups com base no crescimento do registo. Selecione **Manual** para especificar o horário para cópias de segurança completas e registar. |

## <a name="configuration-with-powershell"></a>Configuração com PowerShell
No exemplo seguinte da PowerShell, a Cópia de Segurança Automatizada está configurada para um SQL Server 2014 VM existente. O comando **New-AzureVMSqlServerAutoBackupConfig** configura as definições de Backup Automatizada para armazenar cópias de segurança na conta de armazenamento Azure especificada pela variável $storageaccount. Estes reforços serão retidos por 10 dias. O comando **Set-AzureVMSqlServerExtension** atualiza o VM Azure especificado com estas definições.

```azurepowershell
$storageaccount = "<storageaccountname>"
$storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
$storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
$autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM
```

Pode levar vários minutos para instalar e configurar o Agente IAAS do SQL Server.

Para ativar a encriptação, modifique o script anterior para passar o parâmetro EnableEncrypation juntamente com uma palavra-passe (cadeia segura) para o parâmetro CertificatePassword. O seguinte script permite as definições de Backup Automatizada no exemplo anterior e adiciona encriptação.

```azurepowershell
$storageaccount = "<storageaccountname>"
$storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
$storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
$autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM
```

Para desativar a cópia de segurança automática, execute o mesmo script sem o parâmetro **-Ativar** o **novo-AzureVMSqlServerAutoBackupConfig**. Tal como na instalação, pode levar vários minutos a desativar a cópia de segurança automatizada.

> [!NOTE]
> Desativar e desinstalar o Agente IAAS do Servidor SQL não remove as definições de Cópia de Segurança Gerida previamente configuradas. Deve desativar a cópia de segurança automatizada antes de desativar ou desinstalar o Agente IAAS do Servidor SQL.
> 
> 

## <a name="next-steps"></a>Próximos passos
Cópia de segurança automatizada configura cópia de segurança gerida em VMs Azure. Por isso, é importante [rever a documentação para o Managed Backup](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e as implicações.

Pode encontrar cópias de segurança adicionais e restaurar a orientação para o SQL Server em VMs Azure no seguinte tópico: [Backup and Restore for SQL Server in Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte [a extensão do agente iaaS do servidor SQL.](../classic/sql-server-agent-extension.md)

Para obter mais informações sobre a execução do SQL Server em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

