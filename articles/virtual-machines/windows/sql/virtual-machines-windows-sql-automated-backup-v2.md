---
title: Backup automatizado v2 para SQL Server 2016/2017 VMs Azure [ Backup Automático v2 para SQL Server 2016/2017 VMs ' Microsoft Docs
description: Explica a funcionalidade de backup automatizada para VMs SQL Server 2016/2017 em execução em Azure. Este artigo é específico para VMs usando o Gestor de Recursos.
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
ms.openlocfilehash: 458012982531e228f7c4968f29e79e8b2e29aa48
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77651441"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Backup automático v2 para máquinas virtuais Azure (Gestor de Recursos)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

O Backup V2 automatizado configura automaticamente o [Backup Gerido para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas as bases de dados existentes e novas numa VM Azur que executa as edições SQL Server 2016/2017 Standard, Enterprise ou Developer. Isto permite configurar cópias de dados regulares que utilizam armazenamento de blob Azure durável. A cópia de segurança automática v2 depende da extensão do [agente IaaS do Servidor SQL](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar backup automático v2, reveja os seguintes pré-requisitos:

**Sistema operativo:**

- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server:**

- SQL Server 2016: Developer, Standard ou Enterprise
- SQL Server 2017: Developer, Standard ou Enterprise

> [!IMPORTANT]
> Backup automático v2 funciona com SQL Server 2016 ou superior. Se estiver a utilizar o SQL Server 2014, pode utilizar o V1 de Backup Automatizado para fazer o backup das suas bases de dados. Para mais informações, consulte [Backup Automatizado para Máquinas Virtuais Azure 2014 Azure SQL](virtual-machines-windows-sql-automated-backup.md).

**Configuração da base de dados:**

- As bases de dados-alvo devem utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação completo nas cópias de segurança, consulte [backup sob o modelo de recuperação completa](https://technet.microsoft.com/library/ms190217.aspx).
- As bases de dados do sistema não têm de utilizar um modelo de recuperação completo. No entanto, se necessitar de cópias de segurança para Model ou MSDB, deve utilizar um modelo de recuperação completo.
- As bases de dados de destino devem estar na instância padrão do Servidor SQL ou numa instância de nome [devidamente instalada.](virtual-machines-windows-sql-server-iaas-faq.md#administration) 

> [!NOTE]
> A cópia de segurança automatizada baseia-se na extensão do **agente IaaS do Servidor SQL**. As imagens atuais da galeria de máquinas virtuais SQL adicionam esta extensão por padrão. Para mais informações, consulte a extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para Backup Automático v2. Os passos reais de configuração variam consoante utilize o portal Azure ou os comandos Azure Windows PowerShell.

### <a name="basic-settings"></a>Definições Básicas

| Definição | Gama (Padrão) | Descrição |
| --- | --- | --- |
| **Backup automatizado** | Ativar/Desativar (Desativado) | Permite ou desativa backup automatizado para um Azure VM executando O Programador, Standard ou Enterprise do Azure VM. |
| **Período de Retenção** | 1-30 dias (30 dias) | O número de dias para reter reforços. |
| **Conta de Armazenamento** | Conta de armazenamento do Azure | Uma conta de armazenamento Azure para armazenar ficheiros de backup automatizados no armazenamento de blob. Um recipiente é criado neste local para armazenar todos os ficheiros de reserva. A convenção de nomeação de ficheiros de reserva inclui a data, hora e base de dados GUID. |
| **Encriptação** |Ativar/Desativar (Desativado) | Permite ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada. Usa o mesmo recipiente automático de **reserva** com a mesma convenção de nomeação. Se a palavra-passe mudar, um novo certificado é gerado com essa palavra-passe, mas o certificado antigo permanece para restaurar cópias de segurança prévias. |
| **Palavra-passe** |Texto de palavra-passe | Uma senha para chaves de encriptação. Esta palavra-passe só é necessária se a encriptação estiver ativada. Para restaurar uma cópia de segurança encriptada, deve ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento em que a cópia de segurança foi tomada. |

### <a name="advanced-settings"></a>Definições Avançadas

| Definição | Gama (Padrão) | Descrição |
| --- | --- | --- |
| **Backups de base de dados do sistema** | Ativar/Desativar (Desativado) | Quando ativado, esta funcionalidade também apoia as bases de dados do sistema: Master, MSDB e Model. Para as bases de dados MSDB e Model, verifique se estão em modo de recuperação completa se pretender que sejam tomadas cópias de segurança de log. As cópias de segurança nunca são tomadas para o Mestre. E não são tomadas cópias de segurança para o TempDB. |
| **Agenda de backup** | Manual/Automatizado (Automatizado) | Por predefinição, o calendário de cópia seleções é automaticamente determinado com base no crescimento do registo. O calendário manual de cópias de segurança permite ao utilizador especificar a janela de tempo para cópias de segurança. Neste caso, as cópias de segurança só ocorrem na frequência especificada e durante a janela de tempo especificada de um determinado dia. |
| **Frequência de backup completa** | Diariamente/Semanal | Frequência de cópias de segurança completas. Em ambos os casos, os backups completos começam durante a próxima janela de tempo programada. Quando semanalmente é selecionada, as cópias de segurança podem durar vários dias até que todas as bases de dados tenham suportado com sucesso. |
| **Hora de início de backup completo** | 00:00 – 23:00 (01:00) | Hora de início de um determinado dia durante o qual podem ocorrer todos os backups. |
| **Janela de tempo de backup completa** | 1 - 23 horas (1 hora) | Duração da janela de tempo de um determinado dia durante o qual podem ser efetuadas cópias de segurança completas. |
| **Frequência de backup de log** | 5 – 60 minutos (60 minutos) | Frequência de cópias de segurança de registo. |

## <a name="understanding-full-backup-frequency"></a>Compreender a frequência de backup completa
É importante compreender a diferença entre cópias de segurança diárias e semanais completas. Considere os seguintes dois exemplos.

### <a name="scenario-1-weekly-backups"></a>Cenário 1: Backups semanais
Tem um VM de servidor SQL que contém uma série de grandes bases de dados.

Na segunda-feira, ativa o Backup Automático v2 com as seguintes definições:

- Horário de cópia de segurança: **Manual**
- Frequência de backup completa: **Semanal**
- Hora de início do backup completo: **01:00**
- Janela de tempo de backup completo: **1 hora**

Isto significa que a próxima janela de reserva disponível é terça-feira à 1 da manhã por 1 hora. Nessa altura, o Backup Automatizado começa a fazer backup nas suas bases de dados uma de cada vez. Neste cenário, as suas bases de dados são suficientemente grandes para que as cópias de segurança completas sejam completadas para as bases de dados dos primeiros pares. No entanto, após uma hora, nem todas as bases de dados foram apoiadas.

Quando isso acontece, o Backup Automatizado começa a fazer o backup das restantes bases de dados no dia seguinte, quarta-feira à 1 da manhã durante uma hora. Se nem todas as bases de dados foram apoiadas nesse tempo, tenta novamente no dia seguinte, ao mesmo tempo. Isto continua até que todas as bases de dados tenham sido apoiadas com sucesso.

Depois de chegar novamente na terça-feira, o Backup Automatizado volta a fazer backup em todas as bases de dados.

Este cenário mostra que a Cópia de Segurança Automatizada funciona apenas dentro da janela de tempo especificada, e cada base de dados é apoiada uma vez por semana. Isto também mostra que é possível que os backups se estem por vários dias no caso de não ser possível completar todas as cópias de segurança num só dia.

### <a name="scenario-2-daily-backups"></a>Cenário 2: Backups diários
Tem um VM de servidor SQL que contém uma série de grandes bases de dados.

Na segunda-feira, ativa o Backup Automático v2 com as seguintes definições:

- Horário de cópia de segurança: Manual
- Frequência de backup completa: Diariamente
- Hora de início do backup completo: 22:00
- Janela de tempo de backup completa: 6 horas

Isto significa que a próxima janela de reserva disponível é segunda-feira às 22:00 durante 6 horas. Nessa altura, o Backup Automatizado começa a fazer backup nas suas bases de dados uma de cada vez.

Depois, na terça-feira, às 10 horas, durante 6 horas, os backups completos de todas as bases de dados recomeçam.

> [!IMPORTANT]
> Ao agendar cópias de segurança diárias, recomenda-se que marque uma janela de tempo alargada para garantir que todas as bases de dados podem ser apoiadas dentro deste tempo. Isto é especialmente importante no caso em que você tem uma grande quantidade de dados para fazer apoio.

## <a name="configure-new-vms"></a>Configurar novos VMs

Utilize o portal Azure para configurar o Backup Automatizado v2 quando criar uma nova Máquina Virtual SQL Server 2016 ou 2017 no modelo de implementação do Gestor de Recursos.

No **separador de definições do Servidor SQL,** selecione **Ativar** sob **a cópia de segurança automatizada**. A seguinte imagem do portal Azure mostra as definições de **backup automatizada SQL.**

![Configuração de backup automatizado SQL no portal Azure](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> O Backup V2 automatizado é desativado por defeito.

## <a name="configure-existing-vms"></a>Configurar vMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais existentes do SQL Server, navegue para o recurso das [máquinas virtuais SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e, em seguida, selecione **Backups** para configurar as suas cópias de segurança automatizadas.

![Backup automatizado SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Quando terminar, clique no botão **Aplicar** na parte inferior da página de definições de **Backups** para guardar as alterações.

Se estiver a ativar o Backup Automatizado pela primeira vez, o Azure configura o Agente IaaS do Servidor SQL em segundo plano. Durante este tempo, o portal Azure pode não mostrar que o Backup Automatizado está configurado. Aguarde vários minutos para que o agente seja instalado, configurado. Depois disso, o portal Azure refletirá as novas definições.

## <a name="configure-with-powershell"></a>Configure com PowerShell

Pode utilizar o PowerShell para configurar o Backup Automatizado v2. Antes de começar, deve:

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

Se não estiver instalado ou não for aprovisionado, pode instalá-lo com o seguinte comando. Além do nome vm e do grupo de recursos, deve também especificar a região (**$region**) em que o seu VM está localizado.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Verifique as definições atuais
Se ativou a cópia de segurança automática durante o fornecimento, pode utilizar o PowerShell para verificar a sua configuração atual. Executar o comando **De extensão Get-AzVMSqlServer** e examinar a propriedade **AutoBackupSettings:**

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

Se a sua saída mostrar que **o Enable** está definido para **Falso,** então tem de ativar a cópia de segurança automatizada. A boa notícia é que ativa e configura o Backup Automatizado da mesma forma. Consulte a secção seguinte para obter esta informação.

> [!NOTE] 
> Se verificar as definições imediatamente após a alteração, é possível que recupere os valores de configuração antigos. Aguarde alguns minutos e verifique novamente as definições para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup-v2"></a>Configurar backup automatizado v2
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

Em seguida, utilize o comando **New-AzVMSqlServerAutoBackupConfig** para ativar e configurar as definições automáticas de backup v2 para armazenar cópias de segurança na conta de armazenamento Azure. Neste exemplo, as cópias de segurança devem ser retidas durante 10 dias. As cópias de segurança da base de dados do sistema estão ativadas. Os backups completos estão agendados para o semanário com uma janela de tempo a partir das 20:00 durante duas horas. Os backups de registo estão agendados para cada 30 minutos. O segundo comando, **Set-AzVMSqlServerExtension,** atualiza o Específico Azure VM com estas definições.

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

Pode levar vários minutos para instalar e configurar o Agente IaaS do Servidor SQL. 

Para permitir a encriptação, modifique o script anterior para passar o parâmetro **EnableEncryption** juntamente com uma palavra-passe (string segura) para o parâmetro **CertificatePassword.** O seguinte script permite as definições de Backup Automatizada no exemplo anterior e adiciona encriptação.

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

Para monitorizar a Cópia de Segurança Automatizada no SQL Server 2016/2017, tem duas opções principais. Uma vez que a Cópia de Segurança Automatizada utiliza a função de backup gerida pelo Servidor SQL, as mesmas técnicas de monitorização aplicam-se a ambos.

Primeiro, pode sondar o estado chamando [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consultar a função [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) função de valor de mesa.

Outra opção é aproveitar a funcionalidade de Correio de Base de Dados incorporada para notificações.

1. Ligue para o procedimento [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) armazenado para atribuir um endereço de e-mail ao parâmetro **SSMBackup2WANotificationEmailIds.** 
1. Ativar [o SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os e-mails do Azure VM.
1. Utilize o servidor SMTP e o nome do utilizador para configurar o Database Mail. Pode configurar o Database Mail no Estúdio de Gestão de Servidores SQL ou com comandos Transact-SQL. Para mais informações, consulte [Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configure o Agente servidor SQL para utilizar](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)o Database Mail .
1. Verifique se a porta SMTP é permitida tanto através da firewall VM local como do grupo de segurança da rede para o VM.

## <a name="next-steps"></a>Passos seguintes
Backup automático v2 configures Managed Backup em VMs Azure. Por isso, é importante [rever a documentação para Backup Gerido](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e implicações.

Pode encontrar uma cópia de segurança adicional e restaurar a orientação para o SQL Server em VMs Azure no seguinte artigo: [Backup e Restore for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte a extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre a execução do Servidor SQL em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](virtual-machines-windows-sql-server-iaas-overview.md).

