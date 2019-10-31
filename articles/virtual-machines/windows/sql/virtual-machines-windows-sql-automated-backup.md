---
title: Backup automatizado para máquinas virtuais do Azure SQL Server 2014 | Microsoft Docs
description: Explica o recurso de backup automatizado para VMs SQL Server 2014 em execução no Azure. Este artigo é específico para VMs que usam o Gerenciador de recursos.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0cfcbdaee5a39a947bd89c677f49214c8c3cb98a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162857"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Backup automatizado para máquinas virtuais SQL Server 2014 (Gerenciador de recursos)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

O backup automatizado configura automaticamente o [backup gerenciado para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os bancos de dados novos e existentes em uma VM do Azure em execução SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups de banco de dados regulares que utilizam o armazenamento de BLOBs do Azure durável. O backup automatizado depende da [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para usar o backup automatizado, considere os seguintes pré-requisitos:

**Sistema operacional**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server**:

- Padrão SQL Server 2014
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> O backup automatizado funciona com o SQL Server 2014. Se você estiver usando o SQL Server 2016/2017, poderá usar o backup automatizado v2 para fazer backup de seus bancos de dados. Para obter mais informações, consulte [backup automatizado v2 para máquinas virtuais do Azure SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuração do banco de dados**:

- Os bancos de dados de destino devem usar o modelo de recuperação completa. Para obter mais informações sobre o impacto do modelo de recuperação completa em backups, consulte [backup no modelo de recuperação completa](https://technet.microsoft.com/library/ms190217.aspx).
- Os bancos de dados de destino devem estar na instância de SQL Server padrão. A extensão IaaS SQL Server não oferece suporte a instâncias nomeadas.

> [!NOTE]
> O backup automatizado depende da extensão do agente IaaS SQL Server. As imagens atuais da Galeria de máquinas virtuais do SQL adicionam essa extensão por padrão. Para obter mais informações, consulte [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições

A tabela a seguir descreve as opções que podem ser configuradas para backup automatizado. As etapas de configuração reais variam dependendo se você usar o portal do Azure ou os comandos do Windows PowerShell do Azure.

| Definição | Intervalo (padrão) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Habilitar/desabilitar (desabilitado) | Habilita ou desabilita o backup automatizado para uma VM do Azure que executa o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** | 1-30 dias (30 dias) | O número de dias para manter um backup. |
| **Storage Account** | Conta de armazenamento do Azure | Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de backup automatizados no armazenamento de BLOBs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A Convenção de nomenclatura do arquivo de backup inclui a data, a hora e o nome do computador. |
| **Encriptação** | Habilitar/desabilitar (desabilitado) | Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificada no mesmo contêiner de `automaticbackup` usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanecerá para restaurar os backups anteriores. |
| **Palavra-passe** | Texto da senha | Uma senha para chaves de criptografia. Isso só será necessário se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deve ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito. |

## <a name="configure-in-the-portal"></a>Configurar no portal

Você pode usar o portal do Azure para configurar o backup automatizado durante o provisionamento ou para VMs existentes do SQL Server 2014.

## <a name="configure-new-vms"></a>Configurar novas VMs

Use o portal do Azure para configurar o backup automatizado ao criar uma nova máquina virtual SQL Server 2014 no modelo de implantação do Gerenciador de recursos.

Na guia **configurações de SQL Server** , role para baixo até **backup automatizado** e selecione **habilitar**. Você também pode especificar o período de retenção e a conta de armazenamento, bem como habilitar a criptografia, fazer backup de bancos de dados do sistema e configurar um agendamento de backup.  A captura de tela a seguir portal do Azure mostra as configurações de **backup automatizado do SQL** .

![Configuração de backup automatizado do SQL no portal do Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Configurar VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para máquinas virtuais SQL Server existentes, navegue até o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e, em seguida, selecione **backups**. 

![Backup automatizado do SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Quando terminar, selecione o botão **aplicar** na parte inferior da página **backups** para salvar as alterações.

Se você estiver habilitando o backup automatizado pela primeira vez, o Azure configurará o SQL Server agente IaaS em segundo plano. Durante esse tempo, o portal do Azure pode não mostrar que o backup automatizado está configurado. Aguarde alguns minutos para que o agente seja instalado, configurado. Depois disso, o portal do Azure refletirá as novas configurações.

> [!NOTE]
> Você também pode configurar o backup automatizado usando um modelo. Para obter mais informações, consulte [modelo de início rápido do Azure para backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Configurar com o PowerShell

Você pode usar o PowerShell para configurar o backup automatizado. Antes de começar, você deve:

- [Baixe e instale a Azure PowerShell mais recente](https://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associe-o à sua conta com o comando **Connect-AzAccount** .

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Instalar a extensão IaaS do SQL
Se você provisionou uma máquina virtual SQL Server do portal do Azure, a extensão IaaS SQL Server já deve estar instalada. Você pode determinar se ele está instalado para sua VM chamando o comando **Get-AzVM** e examinando a propriedade **Extensions** .

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Se a extensão do agente IaaS SQL Server estiver instalada, você deverá vê-la listada como "SqlIaaSAgent" ou "SQLIaaSExtension". **ProvisioningState** para a extensão também deve mostrar "êxito".

Se ele não estiver instalado ou não tiver sido provisionado, você poderá instalá-lo com o comando a seguir. Além do nome da VM e do grupo de recursos, você também deve especificar a região ( **$Region**) em que sua VM está localizada.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Se a extensão ainda não estiver instalada, a instalação da extensão reiniciará o serviço de SQL Server.

### <a id="verifysettings"></a>Verificar as configurações atuais

Se você habilitou o backup automatizado durante o provisionamento, poderá usar o PowerShell para verificar a configuração atual. Execute o comando **Get-AzVMSqlServerExtension** e examine a propriedade **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Você deve obter uma saída semelhante à seguinte:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Se a saída mostrar que **habilitar** está definido como **false**, você precisará habilitar o backup automatizado. A boa notícia é que você habilita e configura o backup automatizado da mesma maneira. Consulte a próxima seção para obter essas informações.

> [!NOTE] 
> Se você verificar as configurações imediatamente após fazer uma alteração, será possível que você obtenha os valores de configuração antigos. Aguarde alguns minutos e verifique as configurações novamente para certificar-se de que as alterações foram aplicadas.

### <a name="configure-automated-backup"></a>Configurar backup automatizado
Você pode usar o PowerShell para habilitar o backup automatizado, bem como para modificar sua configuração e comportamento a qualquer momento.

Primeiro, selecione ou crie uma conta de armazenamento para os arquivos de backup. O script a seguir selecionará uma conta de armazenamento ou a criará se ela não existir.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> O backup automatizado não dá suporte ao armazenamento de backups no armazenamento Premium, mas pode fazer backups de discos de VM que usam o armazenamento Premium.

Em seguida, use o comando **New-AzVMSqlServerAutoBackupConfig** para habilitar e definir as configurações de backup automatizado para armazenar backups na conta de armazenamento do Azure. Neste exemplo, os backups são mantidos por 10 dias. O segundo comando, **set-AzVMSqlServerExtension**, atualiza a VM do Azure especificada com essas configurações.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pode levar vários minutos para instalar e configurar o agente IaaS SQL Server.

> [!NOTE]
> Há outras configurações para **New-AzVMSqlServerAutoBackupConfig** que se aplicam somente ao SQL Server 2016 e ao backup automatizado v2. SQL Server 2014 não oferece suporte às seguintes configurações: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**e  **LogBackupFrequencyInMinutes**. Se você tentar definir essas configurações em uma máquina virtual SQL Server 2014, não haverá erro, mas as configurações não serão aplicadas. Se você quiser usar essas configurações em uma máquina virtual SQL Server 2016, consulte [backup automatizado v2 para máquinas virtuais do Azure SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro **enableencryption e** junto com uma senha (cadeia de caracteres segura) para o parâmetro **CertificatePassword** . O script a seguir habilita as configurações de backup automatizado no exemplo anterior e adiciona criptografia.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Para confirmar se as configurações foram aplicadas, [Verifique a configuração de backup automatizado](#verifysettings).

### <a name="disable-automated-backup"></a>Desabilitar backup automatizado

Para desabilitar o backup automatizado, execute o mesmo script sem o parâmetro **-Enable** para o comando **New-AzVMSqlServerAutoBackupConfig** . A ausência do parâmetro **-Enable** sinaliza o comando para desabilitar o recurso. Assim como acontece com a instalação, pode levar vários minutos para desabilitar o backup automatizado.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de exemplo

O script a seguir fornece um conjunto de variáveis que você pode personalizar para habilitar e configurar o backup automatizado para sua VM. No seu caso, talvez seja necessário personalizar o script com base em suas necessidades. Por exemplo, você teria que fazer alterações se quisesse desabilitar o backup de bancos de dados do sistema ou habilitar a criptografia.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorização

Para monitorar o backup automatizado no SQL Server 2014, você tem duas opções principais. Como o backup automatizado usa o SQL Server recurso de backup gerenciado, as mesmas técnicas de monitoramento se aplicam a ambos.

Primeiro, você pode sondar o status chamando [msdb. smart_admin. sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consulte a função com valor de tabela [msdb. smart_admin. fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

> [!NOTE]
> O esquema para backup gerenciado no SQL Server 2014 é **msdb. smart_admin**. No SQL Server 2016, isso mudou para **msdb. managed_backup**e os tópicos de referência usam esse esquema mais recente. Mas, para SQL Server 2014, você deve continuar a usar o esquema **smart_admin** para todos os objetos de backup gerenciados.

Outra opção é aproveitar o recurso interno de Database Mail para notificações.

1. Chame o procedimento armazenado [msdb. smart_admin. sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) para atribuir um endereço de email ao parâmetro **SSMBackup2WANotificationEmailIds** . 
1. Habilite o [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os emails da VM do Azure.
1. Use o servidor SMTP e o nome de usuário para configurar Database Mail. Você pode configurar Database Mail em SQL Server Management Studio ou com comandos Transact-SQL. Para obter mais informações, consulte [Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configure SQL Server Agent para usar Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Verifique se a porta SMTP é permitida por meio do firewall da VM local e do grupo de segurança de rede para a VM.

## <a name="next-steps"></a>Passos seguintes

O backup automatizado configura o backup gerenciado em VMs do Azure. Portanto, é importante [examinar a documentação do backup gerenciado no SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Você pode encontrar diretrizes adicionais de backup e restauração para SQL Server em VMs do Azure no seguinte artigo: [backup e restauração para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar SQL Server em VMs do Azure, consulte [visão geral de SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
