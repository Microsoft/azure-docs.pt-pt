---
title: Cópia de segurança automatizada v2 para SQL Server 2016/2017 VMs Azure | Microsoft Docs
description: Este artigo explica a funcionalidade de Backup Automatizada para SQL Server 2016/2017 VMs em execução no Azure. Este artigo é específico dos VMs utilizando o Gestor de Recursos.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f41614d54dc4320f683f406b2882a7b388bb4c3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358423"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Cópia de segurança automática v2 para máquinas virtuais Azure (Gestor de Recursos)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016 +](automated-backup.md)

Cópia de segurança automatizada v2 configura automaticamente [Cópia de Segurança Gerida para o Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) para todas as bases de dados existentes e novas num Azure VM que executa edições SQL Server 2016 ou posteriormente Standard, Enterprise ou Developer. Isto permite-lhe configurar cópias de dados regulares que utilizam o armazenamento de bolhas Azure duráveis. O backup automatizado v2 depende da [infraestrutura do SQL Server como extensão do agente de serviço (IaaS).](sql-server-iaas-agent-extension-automate-management.md)

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar o Backup Automatizado v2, reveja os seguintes pré-requisitos:

**Sistema operativo:**

- Windows Server 2012 R2 ou posterior

**Versão/edição do SQL Server:**

- SQL Server 2016 ou superior: Developer, Standard ou Enterprise

> [!NOTE]
> Para o SQL Server 2014, consulte [backup automatizado para SQL Server 2014](automated-backup-sql-2014.md).

**Configuração da base de dados:**

- As bases de dados dos _utilizadores-alvo_ devem utilizar o modelo de recuperação total. As bases de dados do sistema não têm de utilizar o modelo de recuperação completo. No entanto, se necessitar de cópias de segurança para modelar ou MSDB, deve utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação total nas cópias de segurança, consulte [backup no modelo de recuperação completo](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- O SQL Server VM foi registado com a extensão sql IaaS Agent em [modo de gestão completa](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full). 
-  A cópia de segurança automatizada baseia-se na extensão completa do [agente iaaS do sql Server](sql-server-iaas-agent-extension-automate-management.md). Como tal, a cópia de segurança automatizada é suportada apenas em bases de dados-alvo a partir da instância padrão, ou numa única instância nomeada. Se não houver instância padrão e várias instâncias nomeadas, a extensão SQL IaaS falha e a cópia de segurança automatizada não funcionará. 

## <a name="settings"></a>Definições
A tabela a seguir descreve as opções que podem ser configuradas para cópia de segurança automatizada v2. Os passos de configuração reais variam consoante utilize o portal Azure ou os comandos Azure Windows PowerShell.

### <a name="basic-settings"></a>Definições Básicas

| Definição | Alcance (Padrão) | Description |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Ativar/Desativar (Desativado) | Permite ou desativa backup automatizado para um Azure VM que executa o SQL Server 2016/2017 Developer, Standard ou Enterprise. |
| **Período de Retenção** | 1-30 dias (30 dias) | O número de dias para reter reforços. |
| **Conta de Armazenamento** | Conta de armazenamento do Azure | Uma conta de armazenamento Azure para utilizar para armazenar ficheiros de backup automatizados no armazenamento de bolhas. Neste local é criado um contentor para armazenar todos os ficheiros de backup. A convenção de nomeação de ficheiros de cópia de segurança inclui a data, hora e base de dados GUID. |
| **Encriptação** |Ativar/Desativar (Desativado) | Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada. Utiliza o mesmo recipiente **automático de reserva** com a mesma convenção de nomeação. Se a palavra-passe mudar, é gerado um novo certificado com essa palavra-passe, mas o certificado antigo permanece para restaurar cópias de segurança prévias. |
| **Palavra-passe** |Texto de senha | Uma senha para chaves de encriptação. Esta palavra-passe só é necessária se a encriptação estiver ativada. Para restaurar uma cópia de segurança encriptada, deve ter a senha correta e certificado relacionado que foi usado no momento em que a cópia de segurança foi tomada. |

### <a name="advanced-settings"></a>Definições Avançadas

| Definição | Alcance (Padrão) | Description |
| --- | --- | --- |
| **Backups de bases de dados do sistema** | Ativar/Desativar (Desativado) | Quando ativado, esta funcionalidade também confirma as bases de dados do sistema: Master, MSDB e Model. Para as bases de dados MSDB e Model, verifique se estão em modo de recuperação total se pretender que sejam tomadas cópias de segurança de registo. Os backups de registo nunca são levados para o Mestre. E não há reforços para o TempDB. |
| **Horário de backup** | Manual/Automatizado (Automatizado) | Por predefinição, o calendário de backup é automaticamente determinado com base no crescimento do registo. A agenda manual de cópias de segurança permite ao utilizador especificar a janela de tempo para cópias de segurança. Neste caso, as cópias de segurança só ocorrem na frequência especificada e durante a janela de tempo especificada de um determinado dia. |
| **Frequência de backup completa** | Diariamente/Semanal | Frequência de reforços completos. Em ambos os casos, as cópias de segurança completas começam durante a próxima janela de tempo programada. Quando o semanário é selecionado, as cópias de segurança podem durar vários dias até que todas as bases de dados tenham feito o backup com sucesso. |
| **Tempo de início de backup completo** | 00:00 – 23:00 (01:00) | Hora de início de um determinado dia durante o qual podem ocorrer backups completos. |
| **Janela de tempo de reserva completo** | 1 - 23 horas (1 hora) | Duração da janela de tempo de um determinado dia durante o qual podem ser efetuadas cópias de segurança completas. |
| **Registar frequência de backup** | 5 - 60 minutos (60 minutos) | Frequência de backups de registos. |

## <a name="understanding-full-backup-frequency"></a>Compreender a frequência de backup completa
É importante entender a diferença entre backups diários e semanais completos. Considere os seguintes dois exemplos de cenários.

### <a name="scenario-1-weekly-backups"></a>Cenário 1: Backups semanais
Tem um SQL Server VM que contém várias bases de dados grandes.

Na segunda-feira, ativa o Backup Automático v2 com as seguintes definições:

- Horário de backup: **Manual**
- Frequência de backup completa: **Semanalmente**
- Hora de início de backup completa: **01:00**
- Janela de tempo de reserva completo: **1 hora**

Isto significa que a próxima janela de reserva disponível é terça-feira à 1 da manhã por 1 hora. Nessa altura, a Cópia de Segurança Automática começa a fazer backup das suas bases de dados uma de cada vez. Neste cenário, as suas bases de dados são suficientemente grandes para que as cópias de segurança completas sejam completas para as primeiras bases de dados de casais. No entanto, após uma hora, nem todas as bases de dados foram apoiadas.

Quando isto acontece, a Cópia de Segurança Automática começa a fazer o backup das restantes bases de dados no dia seguinte, quarta-feira à uma da manhã durante uma hora. Se nem todas as bases de dados foram apoiadas nesse tempo, tenta novamente no dia seguinte ao mesmo tempo. Isto continua até que todas as bases de dados tenham sido apoiadas com sucesso.

Depois de chegar novamente a terça-feira, a Cópia de Segurança Automática volta a fazer backup de todas as bases de dados.

Este cenário mostra que a Cópia de Segurança Automatizada funciona apenas dentro da janela de tempo especificada, e cada base de dados é apoiada uma vez por semana. Isto também mostra que é possível que as cópias de segurança se estendem por vários dias no caso de não ser possível completar todas as cópias de segurança num só dia.

### <a name="scenario-2-daily-backups"></a>Cenário 2: Backups diários
Tem um SQL Server VM que contém várias bases de dados grandes.

Na segunda-feira, ativa o Backup Automático v2 com as seguintes definições:

- Horário de backup: Manual
- Frequência de backup completa: Diariamente
- Hora de início de backup completa: 22:00
- Janela de tempo de reserva completa: 6 horas

Isto significa que a próxima janela de reserva disponível é segunda-feira às 22:00 por 6 horas. Nessa altura, a Cópia de Segurança Automática começa a fazer backup das suas bases de dados uma de cada vez.

Então, na terça-feira, às 10:00, por 6 horas, as cópias de segurança completas de todas as bases de dados reem.

> [!IMPORTANT]
> Ao agendar cópias de segurança diárias, recomenda-se que marque uma janela de tempo ampla para garantir que todas as bases de dados podem ser apoiadas dentro deste tempo. Isto é especialmente importante no caso em que você tem uma grande quantidade de dados para fazer o back up.

## <a name="configure-new-vms"></a>Configurar novos VMs

Utilize o portal Azure para configurar o Backup Automatizado v2 quando criar uma nova máquina virtual SQL Server 2016 ou 2017 no modelo de implementação do Gestor de Recursos.

No separador definições do **SQL Server,** selecione **Ative** under **Automated backup**. A imagem do portal Azure que se segue mostra as definições **de Backup Automatizada SQL.**

![Configuração de backup automatizado SQL no portal Azure](./media/automated-backup/automated-backup-blade.png)

> [!NOTE]
> A cópia de segurança automática v2 é desativada por predefinição.

## <a name="configure-existing-vms"></a>Configurar vM existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais do SQL Server existentes, aceda ao [recurso de máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e, em seguida, selecione **Backups** para configurar as suas cópias de segurança automatizadas.

![Backup automatizado SQL para VMs existentes](./media/automated-backup/sql-server-configuration.png)


Quando terminar, clique no botão **Aplicar** na parte inferior da página de definições **de Backups** para guardar as suas alterações.

Se estiver a ativar a Cópia de Segurança Automatizada pela primeira vez, o Azure configura o Agente IAAS do Servidor SQL em segundo plano. Durante este tempo, o portal Azure pode não mostrar que a Cópia de Segurança Automatizada está configurada. Aguarde alguns minutos para que o agente seja instalado, configurado. Depois disso, o portal Azure refletirá as novas definições.

## <a name="configure-with-powershell"></a>Configure com PowerShell

Pode utilizar o PowerShell para configurar a Cópia de Segurança Automatizada v2. Antes de começar, deve:

- [Descarregue e instale o mais recente Azure PowerShell](https://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associe-o à sua conta com o comando **Connect-AzAccount.**

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>Instale a extensão IAAS do Servidor SQL
Se forjado uma máquina virtual SQL Server a partir do portal Azure, a Extensão IAAS do Servidor SQL já deve ser instalada. Pode determinar se está instalado para o seu VM, ligando para o comando **Get-AzVM** e examinando a propriedade **Extensões.**

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Se a extensão do Agente IAAS do SQL Server estiver instalada, deverá vê-la listada como "SqlIaaSAgent" ou "SQLIaaSExtension". **O Estado de Provisioning** para a extensão também deve mostrar "Bem sucedido". 

Se não estiver instalado ou não tiver sido previsto, pode instalá-lo com o seguinte comando. Além do nome VM e do grupo de recursos, deve também especificar a região (**$region**) em que o seu VM está localizado.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Verificar as definições atuais
Se ativar a Cópia de Segurança Automatizada durante o fornecimento, pode utilizar o PowerShell para verificar a sua configuração atual. Executar o comando **Get-AzVMSqlServerExtension** e examinar a propriedade **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Deve obter uma saída semelhante à seguinte:

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

Se a sua saída mostrar que **o Enable** está definido como **Falso,** então tem de ativar a cópia de segurança automatizada. A boa notícia é que você ativa e configurar a Cópia de Segurança Automatizada da mesma forma. Consulte a secção seguinte para obter esta informação.

> [!NOTE] 
> Se verificar as definições imediatamente após a alteração, é possível que recupere os valores de configuração antigos. Aguarde alguns minutos e verifique novamente as definições para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup-v2"></a>Configurar backup automatizado v2
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

Em seguida, utilize o comando **New-AzVMSqlServerAutoBackupConfig** para ativar e configurar as definições de Backup Automático v2 para armazenar cópias de segurança na conta de armazenamento Azure. Neste exemplo, as cópias de segurança devem ser mantidas durante 10 dias. As cópias de segurança da base de dados do sistema estão ativadas. Os backups completos estão agendados para o dia semanal com uma janela de tempo a partir das 20:00 por duas horas. Os backups de registo estão agendados para cada 30 minutos. O segundo comando, **Set-AzVMSqlServerExtension,** atualiza o Azure VM especificado com estas definições.

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

Pode levar vários minutos para instalar e configurar o Agente IAAS do SQL Server. 

Para ativar a encriptação, modifique o script anterior para passar o parâmetro **EnableEncrypation** juntamente com uma palavra-passe (cadeia segura) para o parâmetro **CertificatePassword.** O seguinte script permite as definições de Backup Automatizada no exemplo anterior e adiciona encriptação.

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
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorização

Para monitorizar a Cópia de Segurança Automatizada no SQL Server 2016/2017, tem duas opções principais. Como a Cópia de Segurança Automatizada utiliza a funcionalidade de Backup Gerida pelo Servidor SQL, as mesmas técnicas de monitorização aplicam-se a ambas.

Em primeiro lugar, pode sondar o estado chamando [msdb.managed_backup.sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consultar a função [msdb.managed_backup.fn_get_health_status-valued.](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql)

Outra opção é aproveitar a funcionalidade de Correio da Base de Dados incorporada para notificações.

1. Ligue para o procedimento armazenado [msdb.managed_backup.sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) para atribuir um endereço de e-mail ao parâmetro **SSMBackup2WANotificationEmailIds.** 
1. Ativar [a SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os e-mails do Azure VM.
1. Utilize o servidor SMTP e o nome de utilizador para configurar o Correio da Base de Dados. Pode configurar o Correio da Base de Dados no SQL Server Management Studio ou com comandos Transact-SQL. Para mais informações, consulte [o Correio da Base de Dados.](/sql/relational-databases/database-mail/database-mail)
1. [Configure o agente do servidor SQL para utilizar o Correio da Base de Dados](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Verifique se a porta SMTP é permitida tanto através da firewall VM local como do grupo de segurança da rede para o VM.

## <a name="next-steps"></a>Passos seguintes
Cópia de segurança automatizada v2 configuras Cópias de Segurança Geridas em VMs Azure. Por isso, é importante [rever a documentação para o Managed Backup](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) para entender o comportamento e as implicações.

Pode encontrar cópias de segurança adicionais e restaurar a orientação para o SQL Server em VMs Azure no seguinte artigo: [Cópia de segurança e restauro para o SQL Server em máquinas virtuais Azure](backup-restore.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte [a extensão do agente iaaS do servidor SQL.](sql-server-iaas-agent-extension-automate-management.md)

Para obter mais informações sobre a execução do SQL Server em VMs Azure, consulte [o SQL Server na visão geral das máquinas virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).