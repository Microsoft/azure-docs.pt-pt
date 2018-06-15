---
title: Cópia de segurança automatizada para o SQL Server 2014 Virtual Machines do Azure | Microsoft Docs
description: Explica a funcionalidade de cópia de segurança automatizada para as VMs de 2014 do SQL Server em execução no Azure. Este artigo é específico para VMs com o Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 43ce94653197933a13830003dd07e5b21be2a585
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895012"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Cópia de segurança automatizada para máquinas de virtuais do SQL Server 2014 (Gestor de recursos)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Configura automaticamente a cópia de segurança automatizada [cópia de segurança gerida no Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os existentes e novas bases de dados numa VM do Azure a executar o SQL Server 2014 Standard ou Enterprise. Isto permite-lhe configurar cópias de segurança regular da base de dados que utilizam o armazenamento de Blobs do Azure durável. Cópia de segurança automatizada depende o [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

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
> Automatizada funciona de cópia de segurança com o SQL Server 2014. Se estiver a utilizar o SQL Server 2016/2017, pode utilizar a cópia de segurança automatizada v2 para cópia de segurança das bases de dados. Para obter mais informações, consulte [v2 de cópia de segurança automatizada para o SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuração de base de dados**:

- Bases de dados de destino tem de utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação completa em cópias de segurança, consulte [cópia de segurança nos completo modelo de recuperação](https://technet.microsoft.com/library/ms190217.aspx).
- Tem de estar bases de dados de destino na instância predefinida do SQL Server. A extensão de IaaS do SQL Server não suporta instâncias nomeadas.

> [!NOTE]
> Cópia de segurança automatizada baseia-se na extensão de agente do SQL Server IaaS. Imagens de galeria da máquina virtual SQL atuais adicionar esta extensão, por predefinição. Para obter mais informações, consulte [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições

A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizada. Os passos de configuração reais variam dependendo se utilizar o portal do Azure ou os comandos do Azure Windows PowerShell.

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Ativar/desativar (desativada) | Ativa ou desativa a cópia de segurança automatizada para uma VM do Azure a executar o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** | 1-30 dias (30 dias) | O número de dias a manter uma cópia de segurança. |
| **Storage Account** | Conta de armazenamento do Azure | Uma conta de armazenamento do Azure a utilizar para armazenar ficheiros de cópia de segurança automatizada no blob storage. Um contentor é criado nesta localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e nome da máquina. |
| **Encriptação** | Ativar/desativar (desativada) | Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada na mesma `automaticbackup` contentor utilizando a mesma Convenção de nomenclatura. Se alterar a palavra-passe, é gerado um novo certificado com essa palavra-passe, mas o certificado antigo permanece restaurar cópias de segurança anteriores. |
| **Palavra-passe** | Texto da palavra-passe | Uma palavra-passe para as chaves de encriptação. Isto apenas é necessário se a encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que da cópia de segurança foi efetuada. |

## <a name="configure-in-the-portal"></a>Configurar no portal

Pode utilizar o portal do Azure para configurar a cópia de segurança automatizada durante o aprovisionamento de ou para VMs de 2014 existente do SQL Server.

## <a name="configure-new-vms"></a>Configurar novas VMs

Utilize o portal do Azure para configurar a cópia de segurança automatizada quando cria um novo SQL Server 2014 máquina no modelo de implementação Resource Manager.

No **definições do SQL Server** painel, selecione **cópia de segurança automatizada**. A seguinte captura de ecrã de portal do Azure mostra o **cópia de segurança do SQL Server automatizada** definições.

![Configuração de cópia de segurança do SQL Server automatizada no portal do Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Configurar VMs existentes

Para máquinas virtuais do SQL Server existentes, selecione a máquina virtual do SQL Server. Em seguida, selecione o **configuração do SQL Server** secção da VM **definições**.

![SQL Server cópia de segurança automatizada para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

No **configuração do SQL Server** painel, clique em de **editar** botão na secção de cópia de segurança automatizada.

![Configurar a cópia de segurança do SQL Server automatizada para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Quando terminar, clique em de **OK** botão na parte inferior do **configuração do SQL Server** definições para guardar as alterações.

Se pretende ativar a cópia de segurança automatizada pela primeira vez, o Azure configura o agente do SQL Server IaaS em segundo plano. Durante este período, o portal do Azure poderá não mostrar que a cópia de segurança automatizada está configurada. Aguarde alguns minutos para que o agente ser instalado, configurado. Depois de que o portal do Azure irá refletir as novas definições.

> [!NOTE]
> Também pode configurar a cópia de segurança automatizada através de um modelo. Para obter mais informações, consulte [modelo de início rápido do Azure para cópia de segurança automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Configurar com o PowerShell

Pode utilizar o PowerShell para configurar a cópia de segurança automatizada. Antes de começar, tem de:

- [Transfira e instale o Azure PowerShell mais recente](http://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associá-lo com a sua conta com o **Connect-AzureRmAccount** comando.

### <a name="install-the-sql-iaas-extension"></a>Instale a extensão de IaaS do SQL Server
Se aprovisionar uma máquina virtual do SQL Server a partir do portal do Azure, a extensão de IaaS do SQL Server já deve estar instalada. Pode determinar se este é instalado para a VM ao chamar **Get-AzureRmVM** comando e examinar o **extensões** propriedade.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Se a extensão de agente do IaaS do SQL Server estiver instalada, deve ver, listado como "SqlIaaSAgent" ou "SQLIaaSExtension". **ProvisioningState** para a extensão também deve mostrar "Com êxito".

Se não está instalado ou falha no aprovisionamento, pode instalá-lo com o seguinte comando. Para além do grupo de recursos e nome VM, também tem de especificar a região (**$region**) que a VM está localizada em.

```powershell
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Se a extensão já não estiver instalada, a instalação da extensão reinicia o serviço SQL Server.

### <a id="verifysettings"></a> Verifique as definições atuais

Se ativou a cópia de segurança automatizada durante o aprovisionamento, pode utilizar o PowerShell para verificar a configuração atual. Execute o **Get-AzureRmVMSqlServerExtension** de comandos e examine a **AutoBackupSettings** propriedade:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Deve obter resultado semelhante ao seguinte:

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

Se o resultado mostra que **ativar** está definido como **falso**, terá de ativar a cópia de segurança automatizada. Boas notícias são que pode ativar e configurar cópia de segurança automatizada da mesma forma. Consulte a secção seguinte para obter estas informações.

> [!NOTE] 
> Se verifique as definições de imediatamente depois de efetuar uma alteração, é possível que irá obter novamente os valores de configuração antigo. Aguarde alguns minutos e verifique as definições novamente para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup"></a>Configurar a cópia de segurança automatizada
Pode utilizar o PowerShell para ativar a cópia de segurança automatizada, bem como para modificar a configuração e o comportamento em qualquer altura.

Em primeiro lugar, selecione ou crie uma conta de armazenamento para os ficheiros de cópia de segurança. O script seguinte seleciona uma conta de armazenamento ou criá-lo se não existir.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Cópia de segurança automatizada não suporta cópias de segurança armazenar no armazenamento premium, mas pode demorar cópias de segurança de discos VM que utilizam o armazenamento Premium.

Em seguida, utilize o **New-AzureRmVMSqlServerAutoBackupConfig** comando para ativar e configurar as definições de cópia de segurança automatizada para armazenar as cópias de segurança na conta do storage do Azure. Neste exemplo, as cópias de segurança são retidas durante 10 dias. O segundo comando, **conjunto AzureRmVMSqlServerExtension**, atualiza a VM do Azure especificada com estas definições.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pode demorar alguns minutos para instalar e configurar o agente do SQL Server IaaS.

> [!NOTE]
> Existem outras definições para **New-AzureRmVMSqlServerAutoBackupConfig** que se apliquem apenas a SQL Server 2016 e cópia de segurança automatizada v2. SQL Server 2014 não suporta as seguintes definições: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**, e **LogBackupFrequencyInMinutes**. Se tentar configurar estas definições numa máquina virtual do SQL Server 2014, não há nenhum erro, mas não ser aplicadas as definições. Se pretender utilizar estas definições numa máquina virtual do SQL Server 2016, consulte [v2 de cópia de segurança automatizada para o SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

Para ativar a encriptação, modificar o script anterior para passar o **EnableEncryption** parâmetro juntamente com uma palavra-passe (cadeia segura) para o **CertificatePassword** parâmetro. O script seguinte Ativa as definições de cópia de segurança automatizada no exemplo anterior e adiciona a encriptação.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Para confirmar as definições são aplicadas, [verificar a configuração de cópia de segurança automatizada](#verifysettings).

### <a name="disable-automated-backup"></a>Desativar a cópia de segurança automatizada

Para desativar a cópia de segurança automatizada, execute o script mesmo sem a **-ativar** parâmetro para o **New-AzureRmVMSqlServerAutoBackupConfig** comando. A ausência de **-ativar** parâmetro sinalizar o comando para desativar esta funcionalidade. Tal como acontece com a instalação, pode demorar alguns minutos para desativar a cópia de segurança automatizada.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de exemplo

O script seguinte fornece um conjunto de variáveis que pode personalizar para ativar e configurar a cópia de segurança automatizada para a VM. No seu caso, poderá ter de personalizar o script com base nos seus requisitos. Por exemplo, terá de efetuar alterações, se pretendesse desativar a cópia de segurança das bases de dados do sistema ou ativar a encriptação.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorização

Para monitorizar a cópia de segurança automatizada no SQL Server 2014, tem duas opções principais. Porque a cópia de segurança automatizada utiliza a funcionalidade de cópia de segurança do SQL Server geridos, as mesmas técnicas de monitorização aplicam-se tanto.

Em primeiro lugar, pode consultar o estado ao chamar [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consultar o [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) função de valor de tabela.

> [!NOTE]
> O esquema para cópia de segurança gerida no SQL Server 2014 é **msdb.smart_admin**. No SQL Server 2016 isto alterado para **msdb.managed_backup**, e os tópicos de referência utilizam este esquema mais recente. Mas para SQL Server 2014, tem de continuar a utilizar o **smart_admin** esquema para todos os objetos de cópia de segurança gerida.

Outra opção consiste em tirar partido da funcionalidade de correio de base de dados incorporado para notificações.

1. Chamar o [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) armazenados procedimento para atribuir um endereço de e-mail para o **SSMBackup2WANotificationEmailIds** parâmetro. 
1. Ativar [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) enviar e-mails a partir da VM do Azure.
1. Utilize o nome de utilizador e de servidor de SMTP para configurar o correio de base de dados. Pode configurar o correio de base de dados no SQL Server Management Studio ou com os comandos de Transact-SQL. Para obter mais informações, consulte [correio de base de dados](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configurar o agente do SQL Server para utilizar o correio de base de dados](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Certifique-se de que a porta SMTP é permitida através da firewall da VM local e o grupo de segurança de rede para a VM.

## <a name="next-steps"></a>Passos Seguintes

Cópia de segurança automatizada configura a cópia de segurança gerida em VMs do Azure. Pelo que é importante para [consulte a documentação para cópia de segurança gerida no SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Pode encontrar a cópia de segurança adicional e restaurar as orientações para o SQL Server em VMs do Azure no seguinte artigo: [cópia de segurança e restaurar para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Virtual Machines do Azure descrição-geral](virtual-machines-windows-sql-server-iaas-overview.md).
