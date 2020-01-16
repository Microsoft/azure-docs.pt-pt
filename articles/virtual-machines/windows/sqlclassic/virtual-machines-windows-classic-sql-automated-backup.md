---
title: Backup automatizado para máquinas virtuais SQL Server (clássico) | Microsoft Docs
description: 'Explica o recurso de backup automatizado para SQL Server em execução em máquinas virtuais do Azure usando o Resource Manager. '
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978105"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Backup automatizado para SQL Server em máquinas virtuais do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Clássico](../classic/sql-automated-backup.md)
> 
> 

O backup automatizado configura automaticamente o [backup gerenciado para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os bancos de dados novos e existentes em uma VM do Azure em execução SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups de banco de dados regulares que utilizam o armazenamento de BLOBs do Azure durável. O backup automatizado depende da [extensão do agente IaaS SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../../../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para exibir a versão do Resource Manager deste artigo, consulte [backup automatizado para SQL Server no Gerenciador de recursos de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar o backup automatizado, considere os seguintes pré-requisitos:

**Sistema operacional**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão/edição do SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Há suporte para o backup automatizado para o SQL Server 2016 com máquinas virtuais do Resource Manager. Para obter mais informações, consulte [backup automatizado v2 para máquinas virtuais do Azure SQL Server 2016 (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Configuração do banco de dados**:

* Os bancos de dados de destino devem usar o modelo de recuperação completa.

**Azure PowerShell**:

* [Instale os comandos mais recentes do Azure PowerShell](/powershell/azure/overview).

**SQL Server extensão IaaS**:

* [Instale a extensão de IaaS SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela a seguir descreve as opções que podem ser configuradas para backup automatizado. Para VMs clássicas, você deve usar o PowerShell para definir essas configurações.

| Definição | Intervalo (padrão) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** |Habilitar/desabilitar (desabilitado) |Habilita ou desabilita o backup automatizado para uma VM do Azure que executa o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** |1-30 dias (30 dias) |O número de dias para manter um backup. |
| **Storage Account** |Conta de armazenamento do Azure (a conta de armazenamento criada para a VM especificada) |Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de backup automatizados no armazenamento de BLOBs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A Convenção de nomenclatura do arquivo de backup inclui a data, a hora e o nome do computador. |
| **Encriptação** |Habilitar/desabilitar (desabilitado) |Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificada no mesmo contêiner AutomaticBackup usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanecerá para restaurar os backups anteriores. |
| **Palavra-passe** |Texto da senha (nenhum) |Uma senha para chaves de criptografia. Isso só será necessário se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deve ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito. |
| **Bancos de dados do sistema de backup** | Habilitar/desabilitar (desabilitado) | Faça backups completos de mestre, modelo e MSDB |
| **Configurar agendamento de backup** | Manual/automatizado (automatizado) | Selecione **automatizado** para fazer backups completos e de log automaticamente com base no crescimento do log. Selecione **manual** para especificar a agenda para backups completos e de log. |

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
No exemplo do PowerShell a seguir, o backup automatizado é configurado para uma VM existente do SQL Server 2014. O comando **New-AzureVMSqlServerAutoBackupConfig** define as configurações de backup automatizadas para armazenar backups na conta de armazenamento do Azure especificada pela variável $storageAccount. Esses backups serão mantidos por 10 dias. O comando **set-AzureVMSqlServerExtension** atualiza a VM do Azure especificada com essas configurações.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode levar vários minutos para instalar e configurar o agente IaaS SQL Server.

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro Enableencryption e junto com uma senha (cadeia de caracteres segura) para o parâmetro CertificatePassword. O script a seguir habilita as configurações de backup automatizado no exemplo anterior e adiciona criptografia.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desabilitar o backup automático, execute o mesmo script sem o parâmetro **-Enable** para o **New-AzureVMSqlServerAutoBackupConfig**. Assim como acontece com a instalação, pode levar vários minutos para desabilitar o backup automatizado.

> [!NOTE]
> Desabilitar e desinstalar o agente IaaS SQL Server não remove as configurações de backup gerenciado anteriormente definidas. Você deve desabilitar o backup automatizado antes de desabilitar ou desinstalar o agente IaaS SQL Server.
> 
> 

## <a name="next-steps"></a>Passos seguintes
O backup automatizado configura o backup gerenciado em VMs do Azure. Portanto, é importante [examinar a documentação do backup gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e as implicações.

Você pode encontrar diretrizes adicionais de backup e restauração para SQL Server em VMs do Azure no tópico a seguir: [backup e restauração para SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [SQL Server extensão do agente IaaS](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre como executar SQL Server em VMs do Azure, consulte [visão geral de SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

