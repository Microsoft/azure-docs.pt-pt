---
title: Backup automatizado para máquinas virtuais SQL Server 2014 Azure
description: Explica a função de cópia de segurança automatizada para VMs SQL Server 2014 em execução em Azure. Este artigo é específico dos VMs utilizando o Gestor de Recursos.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ccd998bc2f6e2771ff4dd1bedfa2213af7573102
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556595"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Backup automatizado para máquinas virtuais SQL Server 2014 (Gestor de Recursos)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

Cópia de segurança automatizada configura automaticamente [o Backup Gerido para o Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) para todas as bases de dados existentes e novas num VM Azure que executa o SQL Server 2014 Standard ou Enterprise. Isto permite-lhe configurar cópias de dados regulares que utilizam o armazenamento durável de Azure Blob. A Cópia de Segurança Automatizada depende da [infraestrutura do SqL Server como extensão do agente de serviço (IaaS).](sql-server-iaas-agent-extension-automate-management.md)

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a cópia de segurança automatizada, considere os seguintes pré-requisitos:


**Sistema operativo:**

- Windows Server 2012 e maior 

**Versão/edição do SQL Server:**

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!NOTE]
> Para SQL 2016 e maior, consulte [Cópia de Segurança Automatizada para SQL Server 2016](automated-backup.md).

**Configuração da base de dados:**

- As bases de dados dos _utilizadores-alvo_ devem utilizar o modelo de recuperação total. As bases de dados do sistema não têm de utilizar o modelo de recuperação completo. No entanto, se necessitar de cópias de segurança para modelar ou MSDB, deve utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação total nas cópias de segurança, consulte [backup no modelo de recuperação completo](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- O SQL Server VM foi registado com a extensão sql IaaS Agent em [modo de gestão completa](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full). 
-  A cópia de segurança automatizada baseia-se na extensão completa do [agente iaaS do sql Server](sql-server-iaas-agent-extension-automate-management.md). Como tal, a cópia de segurança automatizada é suportada apenas em bases de dados-alvo a partir da instância padrão, ou numa única instância nomeada. Se não houver instância padrão e várias instâncias nomeadas, a extensão SQL IaaS falha e a cópia de segurança automatizada não funcionará. 

## <a name="settings"></a>Definições

A tabela a seguir descreve as opções que podem ser configuradas para cópia de segurança automatizada. Os passos de configuração reais variam consoante utilize o portal Azure ou os comandos Azure Windows PowerShell.

| Definição | Alcance (Padrão) | Description |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Ativar/Desativar (Desativado) | Ativa ou desativa a Cópia de Segurança Automatizada para um Azure VM que executa o SQL Server 2014 Standard ou Enterprise. |
| **Período de Retenção** | 1-30 dias (30 dias) | O número de dias para reter um reforço. |
| **Conta de Armazenamento** | Conta de armazenamento do Azure | Uma conta de armazenamento Azure para utilizar para armazenar ficheiros de backup automatizados no armazenamento de bolhas. Neste local é criado um contentor para armazenar todos os ficheiros de backup. A convenção de nomeação de ficheiros de reserva inclui a data, hora e nome da máquina. |
| **Encriptação** | Ativar/Desativar (Desativado) | Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada no mesmo `automaticbackup` recipiente utilizando a mesma convenção de nomeação. Se a palavra-passe mudar, é gerado um novo certificado com essa palavra-passe, mas o certificado antigo permanece para restaurar cópias de segurança prévias. |
| **Palavra-passe** | Texto de senha | Uma senha para chaves de encriptação. Isto só é necessário se a encriptação estiver ativada. Para restaurar uma cópia de segurança encriptada, deve ter a senha correta e certificado relacionado que foi usado no momento em que a cópia de segurança foi tomada. |


## <a name="configure-new-vms"></a>Configurar novos VMs

Utilize o portal Azure para configurar a Cópia de Segurança Automatizada quando criar uma nova máquina virtual SQL Server 2014 no modelo de implementação do Gestor de Recursos.

No separador de definições do **SQL Server,** desloque-se para **a cópia de segurança automatizada** e selecione **Ative**. A imagem do portal Azure que se segue mostra as definições **de Backup Automatizada SQL.**

![Configuração de backup automatizado SQL no portal Azure](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Configurar vM existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para os VMs do servidor SQL existentes, pode ativar e desativar cópias de segurança automatizadas, alterar o período de retenção, especificar a conta de armazenamento e ativar a encriptação a partir do portal Azure. 

Navegue para o [recurso de máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) para a sua máquina virtual SQL Server 2014 e, em seguida, selecione **Backups**. 

![Backup automatizado SQL para VMs existentes](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

Quando terminar, **selecione** o botão Aplicar na parte inferior da página **'Backups'** para guardar as alterações.

Se estiver a ativar a Cópia de Segurança Automatizada pela primeira vez, o Azure configura o Agente IAAS do Servidor SQL em segundo plano. Durante este tempo, o portal Azure pode não mostrar que a Cópia de Segurança Automatizada está configurada. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal Azure refletirá as novas definições.

> [!NOTE]
> Também pode configurar backup automatizado usando um modelo. Para obter mais informações, consulte [o modelo de arranque rápido do Azure para cópia de segurança automatizada.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)

## <a name="configure-with-powershell"></a>Configure com PowerShell

Pode utilizar o PowerShell para configurar a cópia de segurança automatizada. Antes de começar, deve:

- [Descarregue e instale o mais recente Azure PowerShell](https://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associe-o à sua conta com o comando **Connect-AzAccount.** 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>Instale a extensão IAAS do Servidor SQL
Se forjado um SQL Server VM a partir do portal Azure, a Extensão IAAS do Servidor SQL já deve ser instalada. Pode determinar se está instalado para o seu VM, ligando para o comando **Get-AzVM** e examinando a propriedade **Extensões.**

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Se a extensão do Agente IAAS do SQL Server estiver instalada, deverá vê-la listada como "SqlIaaSAgent" ou "SQLIaaSExtension". **O Estado de Provisioning** para a extensão também deve mostrar "Bem sucedido".

Se não estiver instalado ou não tiver sido previsto, pode instalá-lo com o seguinte comando. Além do nome VM e do grupo de recursos, deve também especificar a região ( **$region** ) em que o seu VM está localizado. Especifique o tipo de licença para o seu SQL Server VM, escolhendo entre o pay-as-you-go ou trazer a sua própria licença através do [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Para obter mais informações sobre o licenciamento, consulte [o modelo de licenciamento.](licensing-model-azure-hybrid-benefit-ahb-change.md) 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Se a extensão ainda não estiver instalada, a instalação da extensão reinicia o SQL Server.

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Verificar as definições atuais

Se tiver ativado a cópia de segurança automatizada durante o fornecimento, pode utilizar o PowerShell para verificar a sua configuração atual. Executar o comando **Get-AzVMSqlServerExtension** e examinar a propriedade **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Deve obter uma saída semelhante à seguinte:

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

Se a sua saída mostrar que **o Enable** está definido como **Falso,** então tem de ativar a cópia de segurança automatizada. A boa notícia é que você ativa e configurar a Cópia de Segurança Automatizada da mesma forma. Consulte a secção seguinte para obter esta informação.

> [!NOTE] 
> Se verificar as definições imediatamente após a alteração, é possível que recupere os valores de configuração antigos. Aguarde alguns minutos e verifique novamente as definições para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup"></a>Configurar backup automatizado
Pode utilizar o PowerShell para ativar a Cópia de Segurança Automatizada, bem como para modificar a sua configuração e comportamento a qualquer momento.

Em primeiro lugar, selecione ou crie uma conta de armazenamento para os ficheiros de backup. O seguinte script seleciona uma conta de armazenamento ou cria-a se não existir.

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
> A Cópia de Segurança Automatizada não suporta o armazenamento de backups no armazenamento premium, mas pode ser necessário fazer cópias de segurança dos discos VM que utilizam o Premium Storage.

Em seguida, utilize o comando **New-AzVMSqlServerAutoBackupConfig** para ativar e configurar as definições de Cópia de Segurança Automatizada para armazenar cópias de segurança na conta de armazenamento Azure. Neste exemplo, as cópias de segurança são mantidas por 10 dias. O segundo comando, **Set-AzVMSqlServerExtension,** atualiza o Azure VM especificado com estas definições.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pode levar vários minutos para instalar e configurar o Agente IAAS do SQL Server.

> [!NOTE]
> Existem outras definições para **New-AzVMSqlServerAutoBackupConfig** que se aplicam apenas ao SQL Server 2016 e ao Backup Automático v2. O SQL Server 2014 não suporta as seguintes definições: **BackupSystemDbs** , **BackupScheduleType** , **FullBackupFrequency** , **FullBackupStartHour** , **FullBackupWindowInHours** e **LogBackupFrequencyInMinutes**. Se tentar configurar estas definições numa máquina virtual SQL Server 2014, não há erro, mas as definições não são aplicadas. Se pretender utilizar estas definições numa máquina virtual SQL Server 2016, consulte [cópias de segurança automatizada v2 para máquinas virtuais SQL Server 2016 Azure](automated-backup.md).

Para ativar a encriptação, modifique o script anterior para passar o parâmetro **EnableEncrypation** juntamente com uma palavra-passe (cadeia segura) para o parâmetro **CertificatePassword.** O seguinte script permite as definições de Backup Automatizada no exemplo anterior e adiciona encriptação.

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

Para confirmar a aplicação das suas definições, [verifique a configuração de Backup Automatizada](#verifysettings).

### <a name="disable-automated-backup"></a>Desativar backup automatizado

Para desativar a Cópia de Segurança Automatizada, execute o mesmo script sem o parâmetro **-Ativar** o comando **New-AzVMSqlServerAutoBackupConfig.** A ausência do parâmetro **-Ativar** sinaliza o comando para desativar a função. Tal como na instalação, pode levar vários minutos a desativar a cópia de segurança automatizada.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de exemplo

O seguinte script fornece um conjunto de variáveis que pode personalizar para ativar e configurar a Cópia de Segurança Automatizada para o seu VM. No seu caso, poderá ter de personalizar o script com base nos seus requisitos. Por exemplo, teria de fazer alterações se quisesse desativar a cópia de segurança das bases de dados do sistema ou ativar a encriptação.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

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

Para monitorizar a cópia de segurança automatizada no SQL Server 2014, tem duas opções principais. Como a Cópia de Segurança Automatizada utiliza a funcionalidade de Backup Gerida pelo Servidor SQL, as mesmas técnicas de monitorização aplicam-se a ambas.

Em primeiro lugar, pode sondar o estado chamando [msdb.smart_admin.sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consultar a função [msdb.smart_admin.fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) valued.

> [!NOTE]
> O esquema de Cópia de Segurança Gerida no SQL Server 2014 é **msdb.smart_admin**. No SQL Server 2016 isto mudou para **msdb.managed_backup** , e os tópicos de referência usam este esquema mais recente. Mas para o SQL Server 2014, deve continuar a utilizar o esquema **smart_admin** para todos os objetos de backup geridos.

Outra opção é aproveitar a funcionalidade de Correio da Base de Dados incorporada para notificações.

1. Ligue para o procedimento armazenado [msdb.smart_admin.sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) para atribuir um endereço de e-mail ao parâmetro **SSMBackup2WANotificationEmailIds.** 
1. Ativar [a SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os e-mails do Azure VM.
1. Utilize o servidor SMTP e o nome de utilizador para configurar o Correio da Base de Dados. Pode configurar o Correio da Base de Dados no SQL Server Management Studio ou com comandos Transact-SQL. Para mais informações, consulte [o Correio da Base de Dados.](/sql/relational-databases/database-mail/database-mail)
1. [Configure o agente do servidor SQL para utilizar o Correio da Base de Dados](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Verifique se a porta SMTP é permitida tanto através da firewall VM local como do grupo de segurança da rede para o VM.

## <a name="next-steps"></a>Passos seguintes

Cópia de segurança automatizada configura cópia de segurança gerida em VMs Azure. Por isso, é importante [rever a documentação para cópia de segurança gerida no SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure).

Pode encontrar cópias de segurança adicionais e restaurar a orientação para o SQL Server em VMs Azure no seguinte artigo: [Cópia de segurança e restauro para o SQL Server em máquinas virtuais Azure](backup-restore.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte [a extensão do agente iaaS do servidor SQL.](sql-server-iaas-agent-extension-automate-management.md)

Para obter mais informações sobre a execução do SQL Server em VMs Azure, consulte [o SQL Server na visão geral das máquinas virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).