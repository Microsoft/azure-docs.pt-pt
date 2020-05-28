---
title: Backup automatizado para Máquinas Virtuais Azure SQL Server 2014
description: Explica a funcionalidade de backup automatizada para VMs SQL Server 2014 em funcionamento em Azure. Este artigo é específico para VMs usando o Gestor de Recursos.
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
ms.openlocfilehash: 553018e23f4cfd23568b4f5e07ef63683366433a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046706"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Backup automatizado para Máquinas Virtuais SQL Server 2014 (Gestor de Recursos)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

Backup automatizado configura automaticamente [Backup Gerido para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas as bases de dados existentes e novas em um Azure VM executando SQL Server 2014 Standard ou Enterprise. Isto permite configurar cópias de dados regulares que utilizam armazenamento de blob Azure durável. A cópia de segurança automatizada depende da extensão do [agente IaaS do Servidor SQL](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar backup automatizado, considere os seguintes pré-requisitos:

**Sistema operativo:**

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server:**

- Padrão SQL Server 2014
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Backup automatizado funciona com O Servidor SQL 2014. Se estiver a utilizar o SQL Server 2016/2017, pode utilizar o Backup Automático v2 para fazer backup nas suas bases de dados. Para mais informações, consulte [Backup Automatizado v2 para Máquinas Virtuais Azure SQL Server 2016](automated-backup.md).

**Configuração da base de dados:**

- As bases de dados-alvo devem utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação completo nas cópias de segurança, consulte [backup sob o modelo de recuperação completa](https://technet.microsoft.com/library/ms190217.aspx).
- As bases de dados de destino devem estar na instância padrão do Servidor SQL. A extensão SQL Server IaaS não suporta instâncias nomeadas.

> [!NOTE]
> A cópia de segurança automatizada baseia-se na extensão do agente IaaS do Servidor SQL. As imagens atuais da galeria de máquinas virtuais SQL adicionam esta extensão por padrão. Para mais informações, consulte a extensão do [agente SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md).

## <a name="settings"></a>Definições

A tabela seguinte descreve as opções que podem ser configuradas para Backup Automatizado. Os passos reais de configuração variam consoante utilize o portal Azure ou os comandos Azure Windows PowerShell.

| Definição | Gama (Padrão) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Ativar/Desativar (Desativado) | Ativa ou desativa backup automatizado para um Azure VM executando SQL Server 2014 Standard ou Enterprise. |
| **Período de Retenção** | 1-30 dias (30 dias) | O número de dias para reter um reforço. |
| **Conta de Armazenamento** | Conta de armazenamento do Azure | Uma conta de armazenamento Azure para armazenar ficheiros de backup automatizados no armazenamento de blob. Um recipiente é criado neste local para armazenar todos os ficheiros de reserva. A convenção de nomeação de ficheiros de reserva inclui a data, hora e nome da máquina. |
| **Encriptação** | Ativar/Desativar (Desativado) | Permite ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada no mesmo `automaticbackup` recipiente utilizando a mesma convenção de nomeação. Se a palavra-passe mudar, um novo certificado é gerado com essa palavra-passe, mas o certificado antigo permanece para restaurar cópias de segurança prévias. |
| **Palavra-passe** | Texto de palavra-passe | Uma senha para chaves de encriptação. Isto só é necessário se a encriptação estiver ativada. Para restaurar uma cópia de segurança encriptada, deve ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento em que a cópia de segurança foi tomada. |


## <a name="configure-new-vms"></a>Configurar novos VMs

Utilize o portal Azure para configurar backup automatizado quando criar uma nova Máquina Virtual SQL Server 2014 no modelo de implementação do Gestor de Recursos.

No **separador de definições do Servidor SQL,** desloque-se para a cópia de **segurança automatizada** e selecione **Enable**. A seguinte imagem do portal Azure mostra as definições de **backup automatizada SQL.**

![Configuração de backup automatizado SQL no portal Azure](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Configurar vMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais existentes do SQL Server, pode ativar e desativar cópias de segurança automatizadas, alterar o período de retenção, especificar a conta de armazenamento e ativar a encriptação do portal Azure. 

Navegue para o recurso das [máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) para a sua máquina virtual SQL Server 2014 e, em seguida, selecione **Backups**. 

![Backup automatizado SQL para VMs existentes](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

Quando terminar, selecione o botão **Aplicar** na parte inferior da página **Backups** para guardar as alterações.

Se estiver a ativar o Backup Automatizado pela primeira vez, o Azure configura o Agente IaaS do Servidor SQL em segundo plano. Durante este tempo, o portal Azure pode não mostrar que o Backup Automatizado está configurado. Aguarde vários minutos para que o agente seja instalado e configurado. Depois disso, o portal Azure refletirá as novas definições.

> [!NOTE]
> Também pode configurar backup automatizado usando um modelo. Para mais informações, consulte o [modelo de arranque rápido do Azure para backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Configure com PowerShell

Pode utilizar o PowerShell para configurar a Cópia de Segurança Automatizada. Antes de começar, deve:

- [Descarregue e instale o mais recente Azure PowerShell.](https://aka.ms/webpi-azps)
- Abra o Windows PowerShell e associe-o à sua conta com o comando **Connect-AzAccount.** 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Instale a extensão SQL IaaS
Se for provisionado uma máquina virtual SQL Server a partir do portal Azure, a extensão SQL Server IaaS já deve ser instalada. Pode determinar se está instalado para o seu VM, ligando para o comando **Get-AzVM** e examinando a propriedade **Extensions.**

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Se a extensão do Agente IaaS do Servidor SQL estiver instalada, deverá vê-la listada como "SqlIaASAgent" ou "SQLIaaSExtension". O Estado de **provisionamento** para a extensão também deve mostrar "Sucesso".

Se não estiver instalado ou não for aprovisionado, pode instalá-lo com o seguinte comando. Além do nome vm e do grupo de recursos, deve também especificar a região (**$region**) em que o seu VM está localizado. Especifique o tipo de licença para o seu VM de Servidor SQL, escolhendo entre pagar-as-você-go ou trazer a sua própria licença através do [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Para mais informações sobre licenciamento, consulte [o modelo de licenciamento.](licensing-model-azure-hybrid-benefit-ahb-change.md) 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Se a extensão ainda não estiver instalada, a instalação da extensão reinicia o serviço SQL Server.

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Verifique as definições atuais

Se ativou a cópia de segurança automática durante o fornecimento, pode utilizar o PowerShell para verificar a sua configuração atual. Executar o comando **De extensão Get-AzVMSqlServer** e examinar a propriedade **AutoBackupSettings:**

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

Se a sua saída mostrar que **o Enable** está definido para **Falso,** então tem de ativar a cópia de segurança automatizada. A boa notícia é que ativa e configura o Backup Automatizado da mesma forma. Consulte a secção seguinte para obter esta informação.

> [!NOTE] 
> Se verificar as definições imediatamente após a alteração, é possível que recupere os valores de configuração antigos. Aguarde alguns minutos e verifique novamente as definições para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup"></a>Configurar cópia de segurança automatizada
Pode utilizar o PowerShell para ativar a Backup Automatizada, bem como modificar a sua configuração e comportamento a qualquer momento.

Primeiro, selecione ou crie uma conta de armazenamento para os ficheiros de backup. O seguinte script seleciona uma conta de armazenamento ou cria-a se não existir.

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
> A Backup Automatizada não suporta armazenar backups em armazenamento premium, mas pode levar cópias de segurança de discos VM que utilizam armazenamento Premium.

Em seguida, utilize o comando **New-AzVMSqlServerAutoBackupConfig** para ativar e configurar as definições de backup automatizada para armazenar cópias de segurança na conta de armazenamento Azure. Neste exemplo, os backups são retidos por 10 dias. O segundo comando, **Set-AzVMSqlServerExtension,** atualiza o Específico Azure VM com estas definições.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pode levar vários minutos para instalar e configurar o Agente IaaS do Servidor SQL.

> [!NOTE]
> Existem outras definições para **New-AzVMSqlServerAutoBackupConfig** que se aplicam apenas ao SQL Server 2016 e ao V2 de Backup Automatizado. O SQL Server 2014 não suporta as seguintes definições: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour,** **FullBackupWindowInHours**e **LogBackupFrequencyInMinutes**. Se tentar configurar estas definições numa máquina virtual SQL Server 2014, não há nenhum erro, mas as definições não são aplicadas. Se pretender utilizar estas definições numa máquina virtual SQL Server 2016, consulte backup [automático v2 para Máquinas Virtuais Azure SQL Server 2016](automated-backup.md).

Para permitir a encriptação, modifique o script anterior para passar o parâmetro **EnableEncryption** juntamente com uma palavra-passe (string segura) para o parâmetro **CertificatePassword.** O seguinte script permite as definições de Backup Automatizada no exemplo anterior e adiciona encriptação.

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

Para confirmar que as suas definições são aplicadas, [verifique a configuração de Backup Automatizada](#verifysettings).

### <a name="disable-automated-backup"></a>Desativar cópia de segurança automatizada

Para desativar a cópia de segurança automatizada, execute o mesmo script sem o parâmetro **-Ativar** o **comando New-AzVMSqlServerAutoBackupConfig.** A ausência do parâmetro **-Ativar** sinaliza o comando para desativar a função. Tal como na instalação, pode levar vários minutos a desativar a Cópia de Segurança Automática.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de exemplo

O seguinte script fornece um conjunto de variáveis que pode personalizar para ativar e configurar backup automatizado para o seu VM. No seu caso, poderá ter de personalizar o script com base nos seus requisitos. Por exemplo, teria de fazer alterações se quisesse desativar a cópia de segurança das bases de dados do sistema ou ativar a encriptação.

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

Para monitorizar a cópia de segurança automatizada no SQL Server 2014, tem duas opções principais. Uma vez que a Cópia de Segurança Automatizada utiliza a função de backup gerida pelo Servidor SQL, as mesmas técnicas de monitorização aplicam-se a ambos.

Primeiro, pode sondar o estado chamando [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consultar a função avaliada em tabela [msdb.smart_admin.fn_get_health_status.](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql)

> [!NOTE]
> O esquema para backup gerido no SQL Server 2014 é **msdb.smart_admin**. No SQL Server 2016 este mudou para **msdb.managed_backup**, e os tópicos de referência usam este novo esquema. Mas para o SQL Server 2014, deve continuar a utilizar o esquema **smart_admin** para todos os objetos de backup geridos.

Outra opção é aproveitar a funcionalidade de Correio de Base de Dados incorporada para notificações.

1. Ligue para o procedimento [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) armazenado para atribuir um endereço de e-mail ao parâmetro **SSMBackup2WANotificationEmailIds.** 
1. Ativar [o SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os e-mails do Azure VM.
1. Utilize o servidor SMTP e o nome do utilizador para configurar o Database Mail. Pode configurar o Database Mail no Estúdio de Gestão de Servidores SQL ou com comandos Transact-SQL. Para mais informações, consulte [Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configure o Agente servidor SQL para utilizar](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)o Database Mail .
1. Verifique se a porta SMTP é permitida tanto através da firewall VM local como do grupo de segurança da rede para o VM.

## <a name="next-steps"></a>Próximos passos

Backup automatizado configures Managed Backup em VMs Azure. Por isso, é importante [rever a documentação para Backup Gerido no SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Pode encontrar uma cópia de segurança adicional e restaurar a orientação para o SQL Server em VMs Azure no seguinte artigo: [Backup e Restore for SQL Server in Azure Virtual Machines](backup-restore.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte a extensão do [agente SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md).

Para obter mais informações sobre a execução do Servidor SQL em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
