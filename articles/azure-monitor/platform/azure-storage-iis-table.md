---
title: Usar o armazenamento de BLOB para o IIS e o armazenamento de tabelas para eventos no Azure Monitor | Microsoft Docs
description: Azure Monitor pode ler os logs dos serviços do Azure que gravam diagnósticos em armazenamento de tabela ou logs do IIS gravados no armazenamento de BLOBs.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 8f70ecc96269783c29c566fb89bd617f034316b1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932679"
---
# <a name="collect-azure-diagnostic-logs-from-azure-storage"></a>Coletar logs de diagnóstico do Azure do armazenamento do Azure

Azure Monitor pode ler os logs para os seguintes serviços que gravam diagnósticos em armazenamento de tabela ou logs do IIS gravados no armazenamento de BLOBs:

* Clusters de Service Fabric (versão prévia)
* Máquinas Virtuais
* Funções Web/de trabalho

Antes que Azure Monitor possa coletar dados em um espaço de trabalho Log Analytics para esses recursos, o diagnóstico do Azure deve ser habilitado.

Depois que o diagnóstico estiver habilitado, você poderá usar o portal do Azure ou o PowerShell configurar o espaço de trabalho para coletar os logs.

Diagnóstico do Azure é uma extensão do Azure que permite coletar dados de diagnóstico de uma função de trabalho, função Web ou máquina virtual em execução no Azure. Os dados são armazenados em uma conta de armazenamento do Azure e, em seguida, podem ser coletados por Azure Monitor.

Para Azure Monitor coletar esses logs de Diagnóstico do Azure, os logs devem estar nos seguintes locais:

| Tipo de log | Tipo de Recurso | Localização |
| --- | --- | --- |
| Registos IIS |Máquinas Virtuais <br> Funções Web <br> Funções de trabalho |wad-IIS-LogFiles (armazenamento de BLOBs) |
| Syslog |Máquinas Virtuais |LinuxsyslogVer2v0 (armazenamento de tabela) |
| Service Fabric eventos operacionais |Nós de Service Fabric |WADServiceFabricSystemEventTable |
| Service Fabric eventos de ator confiáveis |Nós de Service Fabric |WADServiceFabricReliableActorEventTable |
| Service Fabric eventos do Reliable Service |Nós de Service Fabric |WADServiceFabricReliableServiceEventTable |
| Logs de eventos do Windows |Nós de Service Fabric <br> Máquinas Virtuais <br> Funções Web <br> Funções de trabalho |WADWindowsEventLogsTable (armazenamento de tabela) |
| Logs do ETW do Windows |Nós de Service Fabric <br> Máquinas Virtuais <br> Funções Web <br> Funções de trabalho |WADETWEventTable (armazenamento de tabela) |

> [!NOTE]
> Atualmente, não há suporte para logs do IIS de sites do Azure.
>
>

Para máquinas virtuais, você tem a opção de instalar o [agente de log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) em sua máquina virtual para habilitar informações adicionais. Além de ser capaz de analisar logs do IIS e logs de eventos, você pode executar análises adicionais, incluindo controle de alterações de configuração, avaliação de SQL e avaliação de atualização.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Habilitar o diagnóstico do Azure em uma máquina virtual para o log de eventos e a coleta de logs do IIS

Use o procedimento a seguir para habilitar o diagnóstico do Azure em uma máquina virtual para o log de eventos e a coleta de logs do IIS usando o portal do Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Para habilitar o diagnóstico do Azure em uma máquina virtual com o portal do Azure

1. Instale o agente de VM ao criar uma máquina virtual. Se a máquina virtual já existir, verifique se o agente de VM já está instalado.

   * Na portal do Azure, navegue até a máquina virtual, selecione **configuração opcional**, **diagnóstico** e defina o **status** como **ativado**.

     Após a conclusão, a VM tem a extensão de Diagnóstico do Azure instalada e em execução. Essa extensão é responsável por coletar os dados de diagnóstico.
2. Habilite o monitoramento e configure o log de eventos em uma VM existente. Você pode habilitar o diagnóstico no nível da VM. Para habilitar o diagnóstico e configurar o log de eventos, execute as seguintes etapas:

   1. Selecione a VM.
   2. Clique em **monitoramento**.
   3. Clique em **diagnóstico**.
   4. Defina o **status** como **ativado**.
   5. Selecione cada log de diagnóstico que você deseja coletar.
   6. Clique em **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Habilitar o diagnóstico do Azure em uma função Web para coleta de eventos e log do IIS

Consulte [como habilitar o diagnóstico em um serviço de nuvem](../../cloud-services/cloud-services-dotnet-diagnostics.md) para obter as etapas gerais sobre como habilitar o diagnóstico do Azure. As instruções abaixo usam essas informações e as personalizam para uso com Log Analytics.

Com o diagnóstico do Azure habilitado:

* Os logs do IIS são armazenados por padrão, com os dados de log transferidos no intervalo de transferência scheduledTransferPeriod.
* Os logs de eventos do Windows não são transferidos por padrão.

### <a name="to-enable-diagnostics"></a>Para habilitar o diagnóstico

Para habilitar os logs de eventos do Windows ou para alterar o scheduledTransferPeriod, configure Diagnóstico do Azure usando o arquivo de configuração XML (Diagnostics. wadcfg), conforme mostrado na [etapa 4: criar seu arquivo de configuração de diagnóstico e instalar a extensão](../../cloud-services/cloud-services-dotnet-diagnostics.md)

O arquivo de configuração de exemplo a seguir coleta logs do IIS e todos os eventos dos logs do aplicativo e do sistema:

```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Verifique se o ConfigurationSettings especifica uma conta de armazenamento, como no exemplo a seguir:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Os valores **AccountName** e **AccountKey** são encontrados na portal do Azure no painel da conta de armazenamento, em gerenciar chaves de acesso. O protocolo para a cadeia de conexão deve ser **https**.

Depois que a configuração de diagnóstico atualizada for aplicada ao serviço de nuvem e estiver gravando diagnósticos no armazenamento do Azure, você estará pronto para configurar o espaço de trabalho Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Usar o portal do Azure para coletar logs do armazenamento do Azure

Você pode usar o portal do Azure para configurar um espaço de trabalho do Log Analytics no Azure Monitor para coletar os logs para os seguintes serviços do Azure:

* Clusters Service Fabric
* Máquinas Virtuais
* Funções Web/de trabalho

No portal do Azure, navegue até seu espaço de trabalho do Log Analytics e execute as seguintes tarefas:

1. Clique em *logs de contas de armazenamento*
2. Clique na tarefa *Adicionar*
3. Selecione a conta de armazenamento que contém os logs de diagnóstico
   * Essa conta pode ser uma conta de armazenamento clássica ou uma Azure Resource Manager conta de armazenamento
4. Selecione o tipo de dados para o qual você deseja coletar logs
   * As opções são logs do IIS; LostFocus Syslog (Linux); Logs de ETW; Eventos de Service Fabric
5. O valor de source é preenchido automaticamente com base no tipo de dados e não pode ser alterado
6. Clique em OK para salvar a configuração

Repita as etapas 2-6 para contas de armazenamento e tipos de dados adicionais que você deseja coletar no espaço de trabalho.

Em aproximadamente 30 minutos, você poderá ver os dados da conta de armazenamento no espaço de trabalho Log Analytics. Você verá apenas os dados que são gravados no armazenamento depois que a configuração é aplicada. O espaço de trabalho não lê os dados preexistentes da conta de armazenamento.

> [!NOTE]
> O portal não valida se a origem existe na conta de armazenamento ou se novos dados estão sendo gravados.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Habilitar o diagnóstico do Azure em uma máquina virtual para o log de eventos e a coleta de logs do IIS usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Use as etapas em [Configurando Azure monitor para indexar o diagnóstico do Azure](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) para usar o PowerShell para ler do diagnóstico do Azure que são gravados no armazenamento de tabelas.

Usando Azure PowerShell você pode especificar com mais precisão os eventos que são gravados no armazenamento do Azure.
Para obter mais informações, consulte [habilitando o diagnóstico em máquinas virtuais do Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Você pode habilitar e atualizar o diagnóstico do Azure usando o seguinte script do PowerShell.
Você também pode usar esse script com uma configuração de log Personalizada.
Modifique o script para definir a conta de armazenamento, o nome do serviço e o nome da máquina virtual.
O script usa cmdlets para máquinas virtuais clássicas.

Examine o exemplo de script a seguir, copie-o, modifique-o conforme necessário, salve o exemplo como um arquivo de script do PowerShell e, em seguida, execute o script.

```powershell
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Passos seguintes

* [Colete logs e métricas para serviços do Azure](collect-azure-metrics-logs.md) para serviços do Azure com suporte.
* [Habilite soluções](../../azure-monitor/insights/solutions.md) para fornecer informações sobre os dados.
* [Use consultas de pesquisa](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados.