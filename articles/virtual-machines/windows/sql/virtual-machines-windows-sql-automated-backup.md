---
title: Cópia de segurança automatizada para SQL Server 2014 máquinas virtuais | Documentos da Microsoft
description: Explica a funcionalidade de cópia de segurança automatizada para SQL Server 2014 VMs em execução no Azure. Este artigo é específico para VMs com o Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2d30d044a26e6a092eba267f223be9b10c3a238b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075835"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Cópia de segurança automatizada para máquinas de virtuais do SQL Server 2014 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Configura automaticamente a cópia de segurança automatizada [cópia de segurança gerida para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os novos e existentes bases de dados numa VM do Azure com o SQL Server 2014 Standard ou Enterprise. Isto permite-lhe configurar cópias de segurança regular da base de dados que utilizam o armazenamento de Blobs do Azure durável. Cópia de segurança automatizada depende da [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a cópia de segurança automatizada, considere os seguintes pré-requisitos:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatizado funciona de cópia de segurança com o SQL Server 2014. Se estiver a utilizar o SQL Server 2016/2017, pode utilizar a cópia de segurança automatizada v2 para fazer backup de seus bancos de dados. Para obter mais informações, consulte [v2 de cópia de segurança automatizada para SQL Server 2016 máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuração de base de dados**:

- Bases de dados de destino tem de utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação completo sobre as cópias de segurança, consulte [cópia de segurança sob a recuperação modelo completo](https://technet.microsoft.com/library/ms190217.aspx).
- Bases de dados de destino tem de ser na instância predefinida do SQL Server. A extensão de IaaS do SQL Server não suporta a instâncias nomeadas.

> [!NOTE]
> Cópia de segurança automatizada conta com a extensão do agente IaaS do SQL Server. Imagens de Galeria de máquinas virtuais SQL atuais adicione esta extensão por predefinição. Para obter mais informações, consulte [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições

A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizada. Os passos de configuração real variam consoante utilize ou o portal do Azure ou os comandos do Azure Windows PowerShell.

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Ativar/desativar (desativado) | Ativa ou desativa a cópia de segurança automatizada para uma VM do Azure com o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** | 1-30 dias (30 dias) | O número de dias a manter uma cópia de segurança. |
| **Storage Account** | Conta de armazenamento do Azure | Uma conta de armazenamento do Azure a utilizar para armazenar ficheiros de cópia de segurança automatizada no armazenamento de Blobs. É criado um contentor nesta localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e o nome da máquina. |
| **Encriptação** | Ativar/desativar (desativado) | Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada na mesma `automaticbackup` contentor utilizando a mesma Convenção de nomenclatura. Se alterar a palavra-passe, um novo certificado é gerado com essa palavra-passe, mas o certificado antigo permanece restaurar cópias de segurança anteriores. |
| **Palavra-passe** | Texto de palavra-passe | Uma palavra-passe para as chaves de encriptação. Isto só é necessário se a encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que da cópia de segurança. |

## <a name="configure-in-the-portal"></a>Configurar no portal

Pode utilizar o portal do Azure para configurar a cópia de segurança automatizada durante o aprovisionamento ou para VMs de 2014 existente do SQL Server.

## <a name="configure-new-vms"></a>Configurar novas VMs

Utilize o portal do Azure para configurar a cópia de segurança automatizada, quando cria uma nova Virtual máquina do SQL Server 2014 no modelo de implementação do Resource Manager.

Na **definições do SQL Server** separador, desloque para baixo até **cópia de segurança automatizada** e selecione **ativar**. Também pode especificar o período de retenção e conta de armazenamento, bem como encriptação, backup de bancos de dados do sistema e configurar uma agenda de cópia de segurança.  A captura de ecrã de portal do Azure a seguir mostra a **cópia de segurança do SQL automatizada** definições.

![Configuração de cópia de segurança do SQL automatizada no portal do Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Configurar as VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para máquinas de virtuais existentes do SQL Server, navegue para o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) e, em seguida, selecione **cópias de segurança**. 

![SQL automatizados cópia de segurança de VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Quando terminar, selecione o **aplicar** botão na parte inferior dos **cópias de segurança** página para guardar as alterações.

Se pretende ativar a cópia de segurança automatizada pela primeira vez, o Azure configura o agente IaaS do SQL Server em segundo plano. Durante este período, o portal do Azure poderá não mostrar que a cópia de segurança automatizada está configurada. Aguarde alguns minutos até que o agente ser instalado, configurado. Depois disso, o portal do Azure irão refletir as novas definições.

> [!NOTE]
> Também pode configurar a cópia de segurança automatizada através de um modelo. Para obter mais informações, consulte [modelo de início rápido do Azure para cópia de segurança automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Configurar com o PowerShell

Pode utilizar o PowerShell para configurar a cópia de segurança automatizada. Antes de começar, tem de:

- [Transfira e instale o Azure PowerShell mais recente](https://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associá-la com a sua conta com o **Connect-AzAccount** comando.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Instalar a extensão de IaaS do SQL
Se aprovisionou uma máquina virtual do SQL Server no portal do Azure, a extensão de IaaS do SQL Server já deve estar instalada. Pode determinar se ele está instalado para a sua VM chamando **Get-AzVM** comando e examinando o **extensões** propriedade.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Se a extensão do agente IaaS do SQL Server estiver instalada, deverá ver que o mesmo listado como "SqlIaaSAgent" ou "SQLIaaSExtension". **ProvisioningState** para a extensão também deve mostrar "Com êxito".

Se não está instalado ou falha no aprovisionamento, pode instalá-lo com o seguinte comando. Juntamente com o grupo de recursos e o nome VM, também tem de especificar a região ( **$region**) situado na sua VM.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Se a extensão já não estiver instalada, instalar a extensão reinicia o serviço SQL Server.

### <a id="verifysettings"></a> Verifique se as definições atuais

Se ativou a cópia de segurança automatizada durante o aprovisionamento, pode utilizar o PowerShell para verificar a configuração atual. Executar o **Get-AzVMSqlServerExtension** comando e examine a **AutoBackupSettings** propriedade:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Obterá saída semelhante ao seguinte:

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

Se o resultado mostra que **ativar** está definida como **falso**, em seguida, tem de ativar cópia de segurança automatizada. A boa notícia é que ative e configure a cópia de segurança automatizada da mesma forma. Veja a secção seguinte para obter estas informações.

> [!NOTE] 
> Se verificar as definições imediatamente depois de fazer uma alteração, é possível que obterá volta os valores de configuração antigo. Aguarde alguns minutos e verifique as definições novamente para certificar-se de que as suas alterações foram aplicadas.

### <a name="configure-automated-backup"></a>Configurar a cópia de segurança automatizada
Pode utilizar o PowerShell para ativar a cópia de segurança automatizada, bem como para alterar sua configuração e seu comportamento em qualquer altura.

Em primeiro lugar, selecione ou crie uma conta de armazenamento para os ficheiros de cópia de segurança. O seguinte script seleciona uma conta de armazenamento ou cria-se não existir.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Cópia de segurança automatizada não suporta o armazenamento de cópias de segurança no armazenamento premium, mas ele pode fazer cópias de segurança de discos VM que utilizam o armazenamento Premium.

Em seguida, utilize o **New-AzVMSqlServerAutoBackupConfig** comando para ativar e configurar as definições de cópia de segurança automatizada para armazenar cópias de segurança na conta de armazenamento do Azure. Neste exemplo, as cópias de segurança são mantidas durante 10 dias. O segundo comando **Set-AzVMSqlServerExtension**, atualiza a VM do Azure especificado com estas definições.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pode demorar vários minutos para instalar e configurar o agente IaaS do SQL Server.

> [!NOTE]
> Existem outras definições para **New-AzVMSqlServerAutoBackupConfig** que se apliquem apenas a v2 do SQL Server 2016 e cópia de segurança automatizada. SQL Server 2014 não suporta as seguintes definições: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**, e **LogBackupFrequencyInMinutes**. Se está tentando configurar estas definições numa máquina virtual do SQL Server 2014, não há nenhum erro, mas as definições não são aplicadas. Se pretender utilizar estas definições numa máquina virtual do SQL Server 2016, veja [v2 de cópia de segurança automatizada para SQL Server 2016 máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup-v2.md).

Para ativar a encriptação, modifique o script anterior para passar a **EnableEncryption** parâmetro juntamente com uma palavra-passe (cadeia segura) para o **CertificatePassword** parâmetro. O seguinte script permite que as definições de cópia de segurança automatizada no exemplo anterior e adiciona a encriptação.

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

Para confirmar as definições são aplicadas, [verificar a configuração de cópia de segurança automatizada](#verifysettings).

### <a name="disable-automated-backup"></a>Desativar cópia de segurança automatizada

Para desativar a cópia de segurança automatizada, execute o mesmo script sem o **-ative** parâmetro para o **New-AzVMSqlServerAutoBackupConfig** comando. A ausência do **-ative** parâmetro sinaliza o comando para desabilitar o recurso. Tal como acontece com a instalação, pode demorar vários minutos para desativar a cópia de segurança automatizada.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de exemplo

O script seguinte fornece um conjunto de variáveis que pode personalizar para ativar e configurar a cópia de segurança automatizada para a sua VM. No seu caso, poderá ter de personalizar o script com base nos seus requisitos. Por exemplo, teria de fazer alterações, se quiser desativar a cópia de segurança de bases de dados do sistema ou ativar a encriptação.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
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

Para monitorizar a cópia de segurança automatizada no SQL Server 2014, terá duas opções principais. Como cópia de segurança automatizada usa a funcionalidade de cópia de segurança do SQL Server gerida, as mesmas técnicas de monitorização aplicam-se a ambos.

Em primeiro lugar, pode consultar o estado chamando [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consultar a [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) função com valor de tabela.

> [!NOTE]
> É o esquema para cópia de segurança gerida no SQL Server 2014 **msdb.smart_admin**. No SQL Server 2016 isto alterado para **msdb.managed_backup**, e os tópicos de referência utilizam este esquema mais recente. Mas para o SQL Server 2014, tem de continuar a utilizar o **smart_admin** esquema para todos os objetos de cópia de segurança gerida.

Outra opção consiste em tirar partido da funcionalidade de correio de base de dados incorporado para notificações.

1. Chamar o [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) armazenados procedimento para atribuir um endereço de e-mail para o **SSMBackup2WANotificationEmailIds** parâmetro. 
1. Ativar [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os e-mails da VM do Azure.
1. Utilize o nome de utilizador e de servidor de SMTP para configurar o correio de base de dados. Pode configurar o Database Mail no SQL Server Management Studio ou com os comandos de Transact-SQL. Para obter mais informações, consulte [correio de base de dados](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configurar o agente do SQL Server para utilizar o Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Certifique-se de que a porta de SMTP é permitida através da firewall VM local e o grupo de segurança de rede para a VM.

## <a name="next-steps"></a>Passos Seguintes

Cópia de segurança automatizada configura a cópia de segurança gerida em VMs do Azure. Portanto, é importante [reveja a documentação de cópia de segurança gerida no SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Pode encontrar a cópia de segurança adicional e restaurar as orientações para o SQL Server em VMs do Azure no seguinte artigo: [Cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
