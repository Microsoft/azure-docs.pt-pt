---
title: Automatizada v2 de cópia de segurança para VMs do Azure do SQL Server 2016/2017 | Documentos da Microsoft
description: Explica a funcionalidade de cópia de segurança automatizada para SQL Server 2016/2017 VMs em execução no Azure. Este artigo é específico para VMs com o Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 540acd1735eb539ecaac468e74511ba5f751278f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780019"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>V2 de cópia de segurança automatizada para máquinas virtuais do Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Configura automaticamente a v2 de cópia de segurança automatizada [cópia de segurança gerida para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os novos e existentes bases de dados numa VM do Azure com o SQL Server 2016/2017 Standard, Enterprise ou Developer edições. Isto permite-lhe configurar cópias de segurança regular da base de dados que utilizam o armazenamento de Blobs do Azure durável. V2 de cópia de segurança automatizada depende da [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a cópia de segurança automatizada v2, reveja os seguintes pré-requisitos:

**Sistema operativo**:

- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server**:

- SQL Server 2016: Programador, Standard ou Enterprise
- SQL Server 2017: Programador, Standard ou Enterprise

> [!IMPORTANT]
> V2 de cópia de segurança automatizada funciona com o SQL Server 2016 ou superior. Se estiver a utilizar o SQL Server 2014, pode utilizar a cópia de segurança automatizada v1 para fazer backup de seus bancos de dados. Para obter mais informações, consulte [cópia de segurança automatizada para SQL Server 2014 máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

**Configuração de base de dados**:

- Bases de dados de destino tem de utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação completo sobre as cópias de segurança, consulte [cópia de segurança sob a recuperação modelo completo](https://technet.microsoft.com/library/ms190217.aspx).
- Bases de dados do sistema não é necessário que utilizar o modelo de recuperação completo. No entanto, se necessitar de backups de log Ir para o modelo ou MSDB, tem de utilizar o modelo de recuperação completo.
- Bases de dados de destino tem de estar em qualquer um da padrão instância do SQL Server, ou uma [corretamente instalados](virtual-machines-windows-sql-server-iaas-faq.md#administration) instância nomeada. 

> [!NOTE]
> Depende de cópia de segurança automatizada as **extensão de agente IaaS do SQL Server**. Imagens de Galeria de máquinas virtuais SQL atuais adicione esta extensão por predefinição. Para obter mais informações, consulte [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizada v2. Os passos de configuração real variam consoante utilize ou o portal do Azure ou os comandos do Azure Windows PowerShell.

### <a name="basic-settings"></a>Definições Básicas

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Ativar/desativar (desativado) | Ativa ou desativa a cópia de segurança automatizada para uma VM do Azure a executar o SQL Server 2016/2017 Developer, Standard ou Enterprise. |
| **Período de retenção** | 1-30 dias (30 dias) | O número de dias a manter as cópias de segurança. |
| **Storage Account** | Conta de armazenamento do Azure | Uma conta de armazenamento do Azure a utilizar para armazenar ficheiros de cópia de segurança automatizada no armazenamento de Blobs. É criado um contentor nesta localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e a base de dados GUID. |
| **Encriptação** |Ativar/desativar (desativado) | Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada. Ele usa o mesmo **automaticbackup** contentor com a mesma Convenção de nomenclatura. Se alterar a palavra-passe, um novo certificado é gerado com essa palavra-passe, mas o certificado antigo permanece restaurar cópias de segurança anteriores. |
| **Palavra-passe** |Texto de palavra-passe | Uma palavra-passe para as chaves de encriptação. Esta palavra-passe só é necessário se a encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que da cópia de segurança. |

### <a name="advanced-settings"></a>Definições Avançadas

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópias de segurança do sistema da base de dados** | Ativar/desativar (desativado) | Quando ativada, esta funcionalidade também cria cópias de segurança de bases de dados do sistema: Mestre, MSDB e Model. Para as bases de dados MSDB e Model, certifique-se de que estão no modo de recuperação completo se pretender que os backups de log a tomar. Backups de log nunca serão direcionados para o mestre. E não existem cópias de segurança são encaminhadas para TempDB. |
| **Agenda de cópia de segurança** | Manual/automatizada (automatizada) | Por predefinição, a agenda de cópia de segurança é determinada automaticamente com base no crescimento do registo. Agenda de cópia de segurança manual permite ao utilizador especificar a janela de tempo para cópias de segurança. Neste caso, as cópias de segurança só ocorrem a frequência especificada e durante a janela de tempo especificado de um determinado dia. |
| **Frequência de cópia de segurança completa** | Diários/semanais | Frequência de cópias de segurança completas. Em ambos os casos, as cópias de segurança completas começarem durante a próxima janela de tempo agendado. Quando é selecionado semanais, cópias de segurança foi de duram vários dias até que todas as bases de dados com êxito efetuou uma cópia. |
| **Hora de início de cópia de segurança completa** | 00:00 – 23:00 (01:00) | Hora de início da determinado dia durante os quais podem ocorrer as cópias de segurança completas. |
| **Janela de tempo de cópia de segurança completa** | 1 – 23 horas (1 hora) | Duração da janela de tempo de um determinado dia durante os quais podem ocorrer as cópias de segurança completas. |
| **Frequência de cópia de segurança do registo** | 5 – 60 minutos (60 minutos) | Frequência de cópias de segurança do registo. |

## <a name="understanding-full-backup-frequency"></a>Compreender a frequência de cópia de segurança completa
É importante compreender a diferença entre cópias de segurança completas diárias e semanais. Considere os seguintes cenários de duas exemplo.

### <a name="scenario-1-weekly-backups"></a>Cenário 1: Cópias de segurança semanais
Tem uma VM do SQL Server que contém um número de bases de dados grandes.

Na segunda-feira, ativar a cópia de segurança automatizada v2 com as seguintes definições:

- Agenda de cópia de segurança: **Manual**
- Frequência de cópia de segurança completa: **Semanal**
- Hora de início de cópia de segurança completa: **01:00**
- Janela de tempo de cópia de segurança completa: **1 hora**

Isso significa que a próxima janela de backup disponível é Terça-feira às AM de 1 para 1 hora. Nessa altura, a cópia de segurança automatizada começa a cópia de segurança das suas bases de dados, um por vez. Neste cenário, as suas bases de dados são suficientemente grandes para que as cópias de segurança completas concluam das bases de dados de algumas primeiro. No entanto, depois de uma hora nem todas as bases de dados tem sido submetidas a backup.

Quando isto acontecer, a cópia de segurança automatizada começa a cópia de segurança de bases de dados restantes no dia seguinte, quarta-feira às 1 AM durante uma hora. Se nem todas as bases de dados tem sido submetidas a backup nesse período de tempo, tentará novamente no próximo dia ao mesmo tempo. Isso continua até que todas as bases de dados tem sido com êxito uma cópia de segurança.

Depois de atingir Terça-feira novamente, a cópia de segurança automatizada começa a cópia de segurança de todas as bases de dados novamente.

Este cenário mostra que a cópia de segurança automatizada só opera dentro da janela de tempo especificado, e cada base de dados cópia de segurança uma vez por semana. Isso também mostra que é possível para cópias de segurança duram vários dias, no caso em que não é possível concluir todas as cópias de segurança num único dia.

### <a name="scenario-2-daily-backups"></a>Cenário 2: Cópias de segurança diárias
Tem uma VM do SQL Server que contém um número de bases de dados grandes.

Na segunda-feira, ativar a cópia de segurança automatizada v2 com as seguintes definições:

- Agenda de cópia de segurança: Manual
- Frequência de cópia de segurança completa: Diariamente
- Hora de início de cópia de segurança completa: 22:00
- Janela de tempo de cópia de segurança completa: 6 horas

Isso significa que a próxima janela de backup disponível é a segunda-feira às 22:00 durante 6 horas. Nessa altura, a cópia de segurança automatizada começa a cópia de segurança das suas bases de dados, um por vez.

Em seguida, na Terça-feira, 10 para 6 horas, as cópias de segurança completas de todas as bases de dados começarem novamente.

> [!IMPORTANT]
> Durante o agendamento de cópias de segurança diárias, recomenda-se que agende uma janela de tempo grande para garantir que todas as bases de dados podem ser uma cópia de segurança dentro deste período de tempo. Isso é especialmente importante no caso em que tem uma grande quantidade de dados para criar cópias de segurança.

## <a name="configure-in-the-portal"></a>Configurar no portal

Pode utilizar o portal do Azure para configurar a cópia de segurança automatizada v2 durante o aprovisionamento ou para existente VMs do SQL Server 2016/2017.

## <a name="configure-for-new-vms"></a>Configurar para novas VMs

Utilize o portal do Azure para configurar a cópia de segurança automatizada v2 quando cria um novo SQL Server 2016 ou 2017 numa máquina Virtual no modelo de implementação do Resource Manager.

Na **definições do SQL Server** painel, selecione **cópia de segurança automatizada**. A captura de ecrã de portal do Azure a seguir mostra a **cópia de segurança do SQL automatizada** definições.

![Configuração de cópia de segurança do SQL automatizada no portal do Azure](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> V2 de cópia de segurança automatizada está desativada por predefinição.

## <a name="configure-existing-vms"></a>Configurar as VMs existentes

Para máquinas de virtuais do SQL Server existentes, selecione a máquina virtual do SQL Server. Em seguida, selecione o **configuração do SQL Server** secção da VM **definições**.

![SQL automatizados cópia de segurança de VMs existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

Na **configuração do SQL Server** definições, clique nas **editar** botão na secção de cópia de segurança automatizada.

![Configurar a cópia de segurança do SQL automatizada de VMs existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

Quando terminar, clique nas **OK** botão na parte inferior dos **configuração do SQL Server** definições para guardar as alterações.

Se pretende ativar a cópia de segurança automatizada pela primeira vez, o Azure configura o agente IaaS do SQL Server em segundo plano. Durante este período, o portal do Azure poderá não mostrar que a cópia de segurança automatizada está configurada. Aguarde alguns minutos até que o agente ser instalado, configurado. Depois disso, o portal do Azure irão refletir as novas definições.

## <a name="configure-with-powershell"></a>Configurar com o PowerShell

Pode utilizar o PowerShell para configurar a cópia de segurança automatizada v2. Antes de começar, tem de:

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

Se não está instalado ou falha no aprovisionamento, pode instalá-lo com o seguinte comando. Juntamente com o grupo de recursos e o nome VM, também tem de especificar a região (**$region**) situado na sua VM.

```powershell
$region = “EASTUS2”
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a> Verifique se as definições atuais
Se ativou a cópia de segurança automatizada durante o aprovisionamento, pode utilizar o PowerShell para verificar a configuração atual. Executar o **Get-AzVMSqlServerExtension** comando e examine a **AutoBackupSettings** propriedade:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Obterá saída semelhante ao seguinte:

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

Se o resultado mostra que **ativar** está definida como **falso**, em seguida, tem de ativar cópia de segurança automatizada. A boa notícia é que ative e configure a cópia de segurança automatizada da mesma forma. Veja a secção seguinte para obter estas informações.

> [!NOTE] 
> Se verificar as definições imediatamente depois de fazer uma alteração, é possível que obterá volta os valores de configuração antigo. Aguarde alguns minutos e verifique as definições novamente para certificar-se de que as suas alterações foram aplicadas.

### <a name="configure-automated-backup-v2"></a>Configurar a v2 de cópia de segurança automatizada
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

Em seguida, utilize o **New-AzVMSqlServerAutoBackupConfig** comando para ativar e configurar as definições de v2 de cópia de segurança automatizada para armazenar cópias de segurança na conta de armazenamento do Azure. Neste exemplo, as cópias de segurança são definidas para ser retidos durante 10 dias. Cópias de segurança do sistema da base de dados estão ativadas. Cópias de segurança completas estão programadas para semanal com uma janela de tempo, começando às 9:00 20 de duas horas. Backups de log estão agendados para cada 30 minutos. O segundo comando **Set-AzVMSqlServerExtension**, atualiza a VM do Azure especificado com estas definições.

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

Pode demorar vários minutos para instalar e configurar o agente IaaS do SQL Server. 

Para ativar a encriptação, modifique o script anterior para passar a **EnableEncryption** parâmetro juntamente com uma palavra-passe (cadeia segura) para o **CertificatePassword** parâmetro. O seguinte script permite que as definições de cópia de segurança automatizada no exemplo anterior e adiciona a encriptação.

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
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorização

Para monitorizar a cópia de segurança automatizada no SQL Server 2016/2017, tem duas opções principais. Como cópia de segurança automatizada usa a funcionalidade de cópia de segurança do SQL Server gerida, as mesmas técnicas de monitorização aplicam-se a ambos.

Em primeiro lugar, pode consultar o estado chamando [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consultar a [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) função com valor de tabela.

Outra opção consiste em tirar partido da funcionalidade de correio de base de dados incorporado para notificações.

1. Chamar o [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) armazenados procedimento para atribuir um endereço de e-mail para o **SSMBackup2WANotificationEmailIds** parâmetro. 
1. Ativar [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os e-mails da VM do Azure.
1. Utilize o nome de utilizador e de servidor de SMTP para configurar o correio de base de dados. Pode configurar o Database Mail no SQL Server Management Studio ou com os comandos de Transact-SQL. Para obter mais informações, consulte [correio de base de dados](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configurar o agente do SQL Server para utilizar o Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Certifique-se de que a porta de SMTP é permitida através da firewall VM local e o grupo de segurança de rede para a VM.

## <a name="next-steps"></a>Passos Seguintes
V2 de cópia de segurança automatizada configura a cópia de segurança gerida em VMs do Azure. Portanto, é importante [reveja a documentação de cópia de segurança gerida](https://msdn.microsoft.com/library/dn449496.aspx) para compreender o comportamento e as implicações.

Pode encontrar a cópia de segurança adicional e restaurar as orientações para o SQL Server em VMs do Azure no seguinte artigo: [Cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

