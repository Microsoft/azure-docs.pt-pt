---
title: Utilizar o armazenamento de BLOBs para o armazenamento IIS e a tabela de eventos no Log Analytics do Azure | Documentos da Microsoft
description: O log Analytics pode ler os registos para serviços do Azure que escrever diagnósticos no armazenamento de tabelas ou de registos do IIS escritos para armazenamento de Blobs.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: 9f5948887262ae190547c96aa09318a19f64812e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306634"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Utilizar o armazenamento de Blobs do Azure para armazenamento de tabelas IIS e o Azure para eventos com o Log Analytics

O log Analytics pode ler os registos para os seguintes serviços que escrever diagnósticos no armazenamento de tabelas ou de registos do IIS escritos para armazenamento de BLOBs:

* Clusters do Service Fabric (pré-visualização)
* Virtual Machines
* Funções de trabalho/Web

Antes do Log Analytics pode recolher dados para estes recursos, o diagnóstico do Azure tem de estar ativado.

Depois de ativar diagnósticos, pode utilizar o portal do Azure ou PowerShell configurar o Log Analytics para recolher os registos.

Diagnóstico do Azure é uma extensão do Azure que permite recolher dados de diagnóstico de uma função de trabalho, a função da web ou a máquina virtual em execução no Azure. Os dados são armazenados numa conta de armazenamento do Azure e, em seguida, podem ser recolhidos pelo Log Analytics.

Para o Log Analytics recolher esses registos de diagnóstico do Azure, tem de ser os registos nas seguintes localizações:

| Tipo de registo | Tipo de Recurso | Localização |
| --- | --- | --- |
| Registos do IIS |Virtual Machines <br> Funções da Web <br> Funções de trabalho |WAD-iis-logfiles (armazenamento de BLOBs) |
| Syslog |Virtual Machines |LinuxsyslogVer2v0 (armazenamento de tabelas) |
| Eventos operacionais do Service Fabric |Nós do Service Fabric |WADServiceFabricSystemEventTable |
| Eventos de Reliable Actor do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableActorEventTable |
| Eventos de Reliable Services do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableServiceEventTable |
| Registos de Eventos do Windows |Nós do Service Fabric <br> Virtual Machines <br> Funções da Web <br> Funções de trabalho |WADWindowsEventLogsTable (armazenamento de tabelas) |
| Registos ETW do Windows |Nós do Service Fabric <br> Virtual Machines <br> Funções da Web <br> Funções de trabalho |WADETWEventTable (armazenamento de tabelas) |

> [!NOTE]
> Registos do IIS de Web sites do Azure não são atualmente suportados.
>
>

Para máquinas virtuais, tem a opção de instalar o [agente do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) à sua máquina virtual para ativar as informações adicionais. Além de ser capaz de analisar os registos do IIS e registos de eventos, pode executar análises adicionais, incluindo o controlo de alterações de configuração, avaliação de SQL e avaliação da atualização.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Ativar o diagnóstico do Azure numa máquina virtual para o registo de eventos e o IIS de recolha de registos
Utilize o procedimento seguinte para ativar o diagnóstico do Azure numa máquina virtual para a recolha de registos de registo de eventos e o IIS através do portal do Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Para ativar o diagnóstico do Azure numa máquina virtual com o portal do Azure
1. Instale o agente da VM quando cria uma máquina virtual. Se a máquina virtual já existir, certifique-se de que o agente da VM já está instalado.

   * No portal do Azure, navegue para a máquina virtual, selecione **configuração opcional**, em seguida, **diagnóstico** e defina **estado** para **no** .

     Após a conclusão, a VM tem a extensão de diagnóstico do Azure instalada e em execução. Esta extensão é responsável por coletar dados de diagnóstico.
2. Ativar a monitorização e configurar uma VM existente registo de eventos. Pode ativar diagnósticos ao nível da VM. Para ativar os diagnósticos e, em seguida, configurar o registo de eventos, execute os seguintes passos:

   1. Selecione a VM.
   2. Clique em **monitorização**.
   3. Clique em **diagnóstico**.
   4. Definir o **Status** ao **ON**.
   5. Selecione cada registo de diagnóstico que pretende recolher.
   6. Clique em **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Ativar o diagnóstico do Azure numa função da Web para a recolha de registos e eventos do IIS
Consulte a [como para ativar o diagnóstico num serviço Cloud](../../cloud-services/cloud-services-dotnet-diagnostics.md) para obter os passos gerais sobre como ativar o diagnóstico do Azure. As instruções abaixo, utilizam estas informações e personalizá-lo para utilização com o Log Analytics.

Com o diagnóstico do Azure ativado:

* Registos do IIS são armazenados por predefinição, com dados de registo transferidos no intervalo de transferência scheduledTransferPeriod.
* Registos de eventos do Windows não são transferidos por predefinição.

### <a name="to-enable-diagnostics"></a>Para ativar o diagnóstico
Para ativar registos de eventos do Windows, ou para alterar o scheduledTransferPeriod, configure o diagnóstico do Azure com o ficheiro de configuração XML (diagnostics.wadcfg), conforme mostrado na [passo 4: Crie o seu ficheiro de configuração de diagnósticos e instalar a extensão](../../cloud-services/cloud-services-dotnet-diagnostics.md)

O ficheiro de configuração de exemplo seguintes recolhe registos do IIS e todos os eventos dos registos de aplicações e do sistema:

```
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

Certifique-se de que sua ConfigurationSettings Especifica uma conta de armazenamento, como no exemplo seguinte:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

O **AccountName** e **AccountKey** valores são encontrados no portal do Azure no dashboard de conta de armazenamento, em Gerir chaves de acesso. O protocolo para a cadeia de ligação tem de ser **https**.

Depois da configuração atualizada de diagnóstico é aplicada ao seu serviço cloud e é escrever diagnósticos no armazenamento do Azure, em seguida, está pronto para configurar o Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Utilizar o portal do Azure para recolher registos de armazenamento do Azure
Pode utilizar o portal do Azure para configurar o Log Analytics para recolher os registos para os serviços do Azure seguintes:

* Clusters do Service Fabric
* Virtual Machines
* Funções de trabalho/Web

No portal do Azure, navegue até à sua área de trabalho do Log Analytics e realizar as seguintes tarefas:

1. Clique em *registos de contas de armazenamento*
2. Clique nas *adicionar* tarefas
3. Selecione a conta de armazenamento que contém os registos de diagnóstico
   * Esta conta pode ser uma conta de armazenamento clássico ou uma conta de armazenamento do Azure Resource Manager
4. Selecione o tipo de dados que pretende recolher registos para
   * As opções são registos do IIS; Eventos; Syslog (Linux); Registos ETW; Eventos de Service Fabric
5. O valor para a origem é preenchido automaticamente com base no tipo de dados e não pode ser alterado
6. Clique em OK para guardar a configuração

Repita os passos 2 a 6 para contas de armazenamento adicionais e tipos de dados que pretende que o Log Analytics para recolher.

Em aproximadamente 30 minutos, é possível ver os dados da conta de armazenamento no Log Analytics. Verá apenas dados que são escritos para o armazenamento depois da configuração é aplicada. O log Analytics não ler os dados já existentes da conta de armazenamento.

> [!NOTE]
> O portal não valida que a origem existe na conta de armazenamento ou se está a ser escritos novos dados.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Ativar o diagnóstico do Azure numa máquina virtual para utilizar o PowerShell de recolha de registos de registo de eventos e o IIS

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Utilize os passos em [configurar o Log Analytics para o diagnóstico do Azure de índice](../../azure-monitor/platform/powershell-workspace-configuration.md#configuring-log-analytics-to-collect-azure-diagnostics-from-storage) para utilizar o PowerShell para ler a partir do diagnóstico do Azure que é escrito no armazenamento de tabelas.

Com o Azure PowerShell pode especificar mais precisamente os eventos que são escritos no armazenamento do Azure.
Para obter mais informações, consulte [ativar diagnósticos em máquinas de virtuais do Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Pode ativar e atualizar o diagnóstico do Azure com o seguinte script do PowerShell.
Também pode utilizar este script com uma configuração de registo personalizado.
Modificar o script para definir a conta de armazenamento, o nome do serviço e o nome da máquina virtual.
O script utiliza os cmdlets para máquinas virtuais clássicas.

Reveja o script de exemplo seguinte, copie-o, modificá-la conforme necessário, guardar o exemplo de como um ficheiro de script do PowerShell e, em seguida, execute o script.

```
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


## <a name="next-steps"></a>Passos Seguintes
* [Recolher registos e métricas para serviços do Azure](collect-azure-metrics-logs.md) para suporte a serviços do Azure.
* [Ativar soluções](../../azure-monitor/insights/solutions.md) para fornecer informações sobre os dados.
* [Utilizar consultas de pesquisa](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados.
