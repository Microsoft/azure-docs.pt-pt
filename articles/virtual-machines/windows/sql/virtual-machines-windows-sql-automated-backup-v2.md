---
title: Backup automatizado v2 para SQL Server VMs do Azure 2016/2017 | Microsoft Docs
description: Explica o recurso de backup automatizado para VMs SQL Server 2016/2017 em execução no Azure. Este artigo é específico para VMs que usam o Gerenciador de recursos.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 009a480add9d808115f24a69a400118fec7cb293
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790591"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Backup automatizado v2 para máquinas virtuais do Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

O backup automatizado v2 configura automaticamente o [backup gerenciado para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os bancos de dados novos e existentes em uma VM do Azure em execução SQL Server as edições Standard, Enterprise ou developer do 2016/2017. Isso permite que você configure backups de banco de dados regulares que utilizam o armazenamento de BLOBs do Azure durável. O backup automatizado v2 depende da [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para usar o backup automatizado v2, examine os seguintes pré-requisitos:

**Sistema operacional**:

- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server**:

- SQL Server 2016: Developer, Standard ou Enterprise
- SQL Server 2017: Developer, Standard ou Enterprise

> [!IMPORTANT]
> O backup automatizado v2 funciona com o SQL Server 2016 ou superior. Se você estiver usando o SQL Server 2014, poderá usar o backup automatizado v1 para fazer backup de seus bancos de dados. Para obter mais informações, consulte [backup automatizado para máquinas virtuais do SQL Server 2014 do Azure](virtual-machines-windows-sql-automated-backup.md).

**Configuração do banco de dados**:

- Os bancos de dados de destino devem usar o modelo de recuperação completa. Para obter mais informações sobre o impacto do modelo de recuperação completa em backups, consulte [backup no modelo de recuperação completa](https://technet.microsoft.com/library/ms190217.aspx).
- Os bancos de dados do sistema não precisam usar o modelo de recuperação completa. No entanto, se você precisar que os backups de log sejam feitos para o modelo ou MSDB, você deverá usar o modelo de recuperação completa.
- Os bancos de dados de destino devem estar na instância de SQL Server padrão ou em uma instância nomeada [corretamente instalada](virtual-machines-windows-sql-server-iaas-faq.md#administration) . 

> [!NOTE]
> O backup automatizado depende da **extensão do agente IaaS SQL Server**. As imagens atuais da Galeria de máquinas virtuais do SQL adicionam essa extensão por padrão. Para obter mais informações, consulte [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela a seguir descreve as opções que podem ser configuradas para o backup automatizado v2. As etapas de configuração reais variam dependendo se você usar o portal do Azure ou os comandos do Windows PowerShell do Azure.

### <a name="basic-settings"></a>Configurações básicas

| Definição | Intervalo (padrão) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Habilitar/desabilitar (desabilitado) | Habilita ou desabilita o backup automatizado para uma VM do Azure que executa o SQL Server 2016/2017 Developer, Standard ou Enterprise. |
| **Período de retenção** | 1-30 dias (30 dias) | O número de dias para manter os backups. |
| **Storage Account** | Conta de armazenamento do Azure | Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de backup automatizados no armazenamento de BLOBs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A Convenção de nomenclatura do arquivo de backup inclui a data, a hora e o GUID do banco de dados. |
| **Encriptação** |Habilitar/desabilitar (desabilitado) | Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificada. Ele usa o mesmo contêiner de **backup automático** com a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanecerá para restaurar os backups anteriores. |
| **Palavra-passe** |Texto da senha | Uma senha para chaves de criptografia. Essa senha só será necessária se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deve ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito. |

### <a name="advanced-settings"></a>Configurações avançadas

| Definição | Intervalo (padrão) | Descrição |
| --- | --- | --- |
| **Backups de banco de dados do sistema** | Habilitar/desabilitar (desabilitado) | Quando habilitado, esse recurso também faz backup dos bancos de dados do sistema: mestre, MSDB e modelo. Para os bancos de dados MSDB e modelo, verifique se eles estão no modo de recuperação completa se você quiser que os backups de log sejam feitos. Os backups de log nunca são feitos para o mestre. E nenhum backup é feito para o TempDB. |
| **Agendamento de backup** | Manual/automatizado (automatizado) | Por padrão, o agendamento de backup é determinado automaticamente com base no crescimento do log. Agendamento de backup manual permite que o usuário especifique a janela de tempo para backups. Nesse caso, os backups ocorrem apenas na frequência especificada e durante a janela de tempo especificada de um determinado dia. |
| **Frequência de backup completo** | Diária/semanal | Frequência de backups completos. Em ambos os casos, os backups completos começam durante a próxima janela de tempo agendada. Quando semanal é selecionado, os backups podem abranger vários dias até que todos os bancos de dados tenham sido submetidos a backup com êxito. |
| **Hora de início do backup completo** | 00:00 – 23:00 (01:00) | Hora de início de um determinado dia durante o qual os backups completos podem ocorrer. |
| **Janela de tempo de backup completo** | 1 a 23 horas (1 hora) | Duração da janela de tempo de um determinado dia durante o qual os backups completos podem ocorrer. |
| **Frequência de backup de log** | 5 a 60 minutos (60 minutos) | Frequência de backups de log. |

## <a name="understanding-full-backup-frequency"></a>Noções básicas sobre a frequência de backup completo
É importante entender a diferença entre backups completos diários e semanais. Considere os dois cenários de exemplo a seguir.

### <a name="scenario-1-weekly-backups"></a>Cenário 1: backups semanais
Você tem uma VM SQL Server que contém vários bancos de dados grandes.

Na segunda-feira, você habilita o backup automatizado V2 com as seguintes configurações:

- Agendamento de backup: **manual**
- Frequência de backup completo: **semanalmente**
- Hora de início do backup completo: **01:00**
- Janela de tempo de backup completo: **1 hora**

Isso significa que a próxima janela de backup disponível é terça-feira às 9h por 1 hora. Nesse momento, o backup automatizado começa a fazer backup de seus bancos de dados um de cada vez. Nesse cenário, os bancos de dados são grandes o suficiente para que os backups completos sejam concluídos para os primeiros bancos de dados. No entanto, após uma hora, não foi feito o backup de todos os bancos de dados.

Quando isso acontece, o backup automatizado começa a fazer backup dos bancos de dados restantes no dia seguinte, quarta-feira às 9h, em uma hora. Se nem todos os bancos de dados tiverem sido submetidos a backup nesse momento, ele tentará novamente no dia seguinte ao mesmo tempo. Isso continuará até que todos os bancos de dados tenham sido submetidos a backup com êxito.

Depois de atingir a terça-feira novamente, o backup automatizado começa a fazer o backup de todos os bancos de dados novamente.

Esse cenário mostra que o backup automatizado funciona apenas dentro da janela de tempo especificada e cada banco de dados é submetido a backup uma vez por semana. Isso também mostra que é possível que os backups abranjam vários dias no caso em que não é possível concluir todos os backups em um único dia.

### <a name="scenario-2-daily-backups"></a>Cenário 2: backups diários
Você tem uma VM SQL Server que contém vários bancos de dados grandes.

Na segunda-feira, você habilita o backup automatizado V2 com as seguintes configurações:

- Agendamento de backup: manual
- Frequência de backup completo: diariamente
- Hora de início do backup completo: 22:00
- Janela de tempo de backup completo: 6 horas

Isso significa que a próxima janela de backup disponível é segunda-feira às 17h por 6 horas. Nesse momento, o backup automatizado começa a fazer backup de seus bancos de dados um de cada vez.

Em seguida, na terça-feira às 10 por 6 horas, os backups completos de todos os bancos de dados são iniciados novamente.

> [!IMPORTANT]
> Ao agendar backups diários, é recomendável agendar uma janela de tempo ampla para garantir que possa ser feito o backup de todos os bancos de dados nesse momento. Isso é especialmente importante no caso em que você tem uma grande quantidade de dados para fazer backup.

## <a name="configure-in-the-portal"></a>Configurar no portal

Você pode usar o portal do Azure para configurar o backup automatizado v2 durante o provisionamento ou para VMs existentes do SQL Server 2016/2017.

## <a name="configure-for-new-vms"></a>Configurar para novas VMs

Use o portal do Azure para configurar o backup automatizado v2 ao criar uma nova máquina virtual SQL Server 2016 ou 2017 no modelo de implantação do Gerenciador de recursos.

Na guia **configurações de SQL Server** , selecione **habilitar** em **backup automatizado**. A captura de tela a seguir portal do Azure mostra as configurações de **backup automatizado do SQL** .

![Configuração de backup automatizado do SQL no portal do Azure](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> O backup automatizado V2 é desabilitado por padrão.

## <a name="configure-existing-vms"></a>Configurar VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para máquinas virtuais SQL Server existentes, navegue até o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e, em seguida, selecione **backups** para configurar os backups automatizados.

![Backup automatizado do SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Quando terminar, clique no botão **aplicar** na parte inferior da página Configurações de **backups** para salvar as alterações.

Se você estiver habilitando o backup automatizado pela primeira vez, o Azure configurará o SQL Server agente IaaS em segundo plano. Durante esse tempo, o portal do Azure pode não mostrar que o backup automatizado está configurado. Aguarde alguns minutos para que o agente seja instalado, configurado. Depois disso, o portal do Azure refletirá as novas configurações.

## <a name="configure-with-powershell"></a>Configurar com o PowerShell

Você pode usar o PowerShell para configurar o backup automatizado v2. Antes de começar, você deve:

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
    -Version "2.0" -Location $region 
```

### <a id="verifysettings"></a>Verificar as configurações atuais
Se você habilitou o backup automatizado durante o provisionamento, poderá usar o PowerShell para verificar a configuração atual. Execute o comando **Get-AzVMSqlServerExtension** e examine a propriedade **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Você deve obter uma saída semelhante à seguinte:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Se a saída mostrar que **habilitar** está definido como **false**, você precisará habilitar o backup automatizado. A boa notícia é que você habilita e configura o backup automatizado da mesma maneira. Consulte a próxima seção para obter essas informações.

> [!NOTE] 
> Se você verificar as configurações imediatamente após fazer uma alteração, será possível que você obtenha os valores de configuração antigos. Aguarde alguns minutos e verifique as configurações novamente para certificar-se de que as alterações foram aplicadas.

### <a name="configure-automated-backup-v2"></a>Configurar backup automatizado v2
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

Em seguida, use o comando **New-AzVMSqlServerAutoBackupConfig** para habilitar e definir as configurações de backup automatizado v2 para armazenar backups na conta de armazenamento do Azure. Neste exemplo, os backups estão definidos para serem retidos por 10 dias. Backups de banco de dados do sistema estão habilitados. Os backups completos são agendados semanalmente com uma janela de tempo que começa às 20:00 por duas horas. Os backups de log são agendados para cada 30 minutos. O segundo comando, **set-AzVMSqlServerExtension**, atualiza a VM do Azure especificada com essas configurações.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Pode levar vários minutos para instalar e configurar o agente IaaS SQL Server. 

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro **enableencryption e** junto com uma senha (cadeia de caracteres segura) para o parâmetro **CertificatePassword** . O script a seguir habilita as configurações de backup automatizado no exemplo anterior e adiciona criptografia.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

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
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorização

Para monitorar o backup automatizado no SQL Server 2016/2017, você tem duas opções principais. Como o backup automatizado usa o SQL Server recurso de backup gerenciado, as mesmas técnicas de monitoramento se aplicam a ambos.

Primeiro, você pode sondar o status chamando [msdb. managed_backup. sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consulte a função com valor de tabela [msdb. managed_backup. fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

Outra opção é aproveitar o recurso interno de Database Mail para notificações.

1. Chame o procedimento armazenado [msdb. managed_backup. sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) para atribuir um endereço de email ao parâmetro **SSMBackup2WANotificationEmailIds** . 
1. Habilite o [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os emails da VM do Azure.
1. Use o servidor SMTP e o nome de usuário para configurar Database Mail. Você pode configurar Database Mail em SQL Server Management Studio ou com comandos Transact-SQL. Para obter mais informações, consulte [Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configure SQL Server Agent para usar Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Verifique se a porta SMTP é permitida por meio do firewall da VM local e do grupo de segurança de rede para a VM.

## <a name="next-steps"></a>Passos seguintes
O backup automatizado v2 configura o backup gerenciado em VMs do Azure. Portanto, é importante [examinar a documentação do backup gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e as implicações.

Você pode encontrar diretrizes adicionais de backup e restauração para SQL Server em VMs do Azure no seguinte artigo: [backup e restauração para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar SQL Server em VMs do Azure, consulte [visão geral de SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

