---
title: Extensão de diagnóstico do Azure 1.2 esquema de configuração
description: Relevante apenas se estiver a utilizar o Azure SDK 2.5 com máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric ou serviços Cloud.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496493"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Esquema de configuração de diagnóstico 1.2 do Azure
> [!NOTE]
> Diagnóstico do Azure é o componente utilizado para recolher contadores de desempenho e outras estatísticas de máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric e serviços em nuvem.  Esta página só é relevante se estiver a utilizar um destes serviços.
>

Diagnóstico do Azure é utilizado com outros produtos de diagnóstico da Microsoft, como o Azure Monitor, que inclui o Application Insights e o Log Analytics.

Este esquema define os valores possíveis que pode utilizar para inicializar as definições de diagnóstico de configuração quando o monitor de diagnóstico é iniciado.  


 Transferir a definição de esquema do ficheiro de configuração pública ao executar o seguinte comando do PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Para obter mais informações sobre como utilizar o diagnóstico do Azure, consulte [ativar diagnósticos nos serviços Cloud do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do ficheiro de configuração de diagnósticos  
 O exemplo seguinte mostra um ficheiro de configuração de diagnósticos típico:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
      </PerformanceCounters>  
      <Directories scheduledTransferPeriod="PT5M">  
        <IISLogs containerName="iislogs" />  
        <FailedRequestLogs containerName="iisfailed" />  
        <DataSources>  
          <DirectoryConfiguration containerName="mynewprocess">  
            <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="badapp">  
            <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="goodapp">  
            <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
          </DirectoryConfiguration>  
        </DataSources>  
      </Directories>  
      <EtwProviders>  
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
          <Event id="0"/>  
          <Event id="1" eventDestination="errorTable"/>  
          <DefaultEvents />  
        </EtwEventSourceProviderConfiguration>  
        <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
          <Event id="0"/>  
          <DefaultEvents eventDestination="defaultTable"/>  
        </EtwManifestProviderConfiguration>  
      </EtwProviders>  
      <WindowsEventLog scheduledTransferPeriod="PT5M">  
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
        <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
        <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
      </WindowsEventLog>  
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Espaço de nomes de configuração de diagnósticos  
 O espaço de nomes XML para o ficheiro de configuração de diagnósticos é:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Elemento de PublicConfig  
 Elemento de nível superior do ficheiro de configuração de diagnósticos. A tabela seguinte descreve os elementos do ficheiro de configuração.  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**WadCfg**|Necessário. Definições de configuração para os dados de telemetria recolha.|  
|**StorageAccount**|O nome da conta para armazenar os dados no armazenamento do Azure. Isso também pode ser especificado como um parâmetro ao executar o cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|O diretório na máquina virtual a ser utilizado pelo agente de monitorização para armazenar dados de eventos. Se não é utilizado o conjunto, o diretório predefinido:<br /><br /> Para uma função de trabalho/web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma Máquina Virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Atributos necessários são:<br /><br /> -                      **caminho** -o diretório do sistema a ser utilizado pelo diagnóstico do Azure.<br /><br /> -                      **expandEnvironment** -controla se as variáveis de ambiente são expandidas no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento de WadCFG  
Define as configurações para os dados de telemetria recolha. A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Necessário. Atributos opcionais são:<br /><br /> -                     **overallQuotaInMB** -a quantidade máxima de espaço no disco local que pode ser utilizada por vários tipos de dados de diagnóstico recolhidos pelo diagnóstico do Azure. O valor predefinido é 5120MB.<br /><br /> -                     **useProxyServer** -configurar o diagnóstico do Azure para utilizar as definições do servidor proxy, conforme definido nas definições do IE.|  
|**CrashDumps**|Ative a recolha de informações de falhas. Atributos opcionais são:<br /><br /> -                     **containerName** -o nome do contentor de BLOBs na sua conta de armazenamento do Azure a ser utilizado para armazenar despejos de memória.<br /><br /> -                     **crashDumpType** -despeja configura o diagnóstico do Azure para recolher Mini ou completo poderá falhar.<br /><br /> -                     **directoryQuotaPercentage**-configura a percentagem de **overallQuotaInMB** a reservar para informações de falhas na VM.|  
|**DiagnosticInfrastructureLogs**|Ative a recolha de registos gerados pelo diagnóstico do Azure. Os registos de diagnóstico de infraestrutura são úteis para resolver o próprio sistema de diagnóstico. Atributos opcionais são:<br /><br /> -                     **scheduledTransferLogLevelFilter** -configura o nível de gravidade mínimo dos registos recolhidos.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Diretórios**|Permite a recolha do conteúdo de um diretório, o IIS não conseguiu aceder aos registos de pedido e/ou registos do IIS. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Configura a recolha de eventos do ETW do EventSource de e/ou o manifesto do ETW com base em provedores.|  
|**Métricas**|Esse elemento permite-lhe gerar uma tabela de contador de desempenho otimizado para consultas rápidas. Cada contador de desempenho que está definido no **PerformanceCounters** elemento é armazenado na tabela de métricas para além da tabela de contador de desempenho. Atributo necessário:<br /><br /> **resourceId** -este é o ID de recurso da Máquina Virtual está a implementar o diagnóstico do Azure para. Obter o **resourceID** partir do [portal do Azure](https://portal.azure.com). Selecione **navegue** -> **grupos de recursos** -> **< nome\>**. Clique no **propriedades** mosaico e copie o valor a partir do **ID** campo.|  
|**PerformanceCounters**|Permite a recolha de contadores de desempenho. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Permite a recolha de registos de eventos do Windows. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Elemento de CrashDumps  
 Permite a recolha de informações de falhas. A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Necessário. Atributo necessário:<br /><br /> **processName** -o nome do processo de que pretende que o diagnóstico do Azure para recolher um despejo de falha para.|  
|**crashDumpType**|Configura o diagnóstico do Azure para coletar despejos de mini ou falha completa do sistema.|  
|**directoryQuotaPercentage**|Configura a percentagem de **overallQuotaInMB** a reservar para informações de falhas na VM.|  

## <a name="directories-element"></a>Elemento de diretórios  
 Permite a recolha do conteúdo de um diretório, o IIS não conseguiu aceder aos registos de pedido e/ou registos do IIS. A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DataSources**|Uma lista de diretórios a monitorizar.|  
|**FailedRequestLogs**|Incluindo esse elemento na configuração permite a recolha de registos de pedidos falhados para um aplicativo ou site do IIS. Também tem de ativar as opções de rastreamento em **system. Servidor Web** no **Web. config**.|  
|**IISLogs**|Incluindo esse elemento na configuração permite que a coleção de registos do IIS:<br /><br /> **containerName** -o nome do contentor de BLOBs na sua conta de armazenamento do Azure a ser utilizado para armazenar os registos IIS.|  

## <a name="datasources-element"></a>Elemento de origens de dados  
 Uma lista de diretórios a monitorizar. A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Necessário. Atributo necessário:<br /><br /> **containerName** -o nome do contentor de BLOBs na sua conta de armazenamento do Azure a ser utilizado para armazenar os ficheiros de registo.|  

## <a name="directoryconfiguration-element"></a>Elemento de DirectoryConfiguration  
 **DirectoryConfiguration** podem incluir qualquer um de **absoluto** ou **LocalResource** elemento, mas não ambos. A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Absoluto**|O caminho absoluto para o diretório a monitorizar. Os seguintes atributos são necessários:<br /><br /> -                     **Caminho** -o caminho absoluto para o diretório a monitorizar.<br /><br /> -                      **expandEnvironment** -configura se as variáveis de ambiente no caminho são expandidas.|  
|**LocalResource**|O caminho relativo para um recurso local para monitorizar. Atributos necessários são:<br /><br /> -                     **Nome** -o recurso local que contém o diretório para monitorizar<br /><br /> -                     **relativePath** -o caminho relativo ao nome que contém o diretório para monitorizar|  

## <a name="etwproviders-element"></a>Elemento de EtwProviders  
 Configura a recolha de eventos do ETW do EventSource de e/ou o manifesto do ETW com base em provedores. A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a recolha de eventos gerados a partir [EventSource classe](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo necessário:<br /><br /> **fornecedor** -o nome da classe do evento EventSource.<br /><br /> Atributos opcionais são:<br /><br /> -                     **scheduledTransferLogLevelFilter** -o nível de gravidade mínimo para transferir a sua conta de armazenamento.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [tipo de dados de duração do XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Atributo necessário:<br /><br /> **fornecedor** -o GUID do provedor do evento<br /><br /> Atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de gravidade mínimo para transferir a sua conta de armazenamento.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [tipo de dados de duração do XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>Elemento de EtwEventSourceProviderConfiguration  
 Configura a recolha de eventos gerados a partir [EventSource classe](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar os eventos em|  
|**Evento**|Atributo necessário:<br /><br /> **ID** -o id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar os eventos em|  

## <a name="etwmanifestproviderconfiguration-element"></a>Elemento de EtwManifestProviderConfiguration  
 A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar os eventos em|  
|**Evento**|Atributo necessário:<br /><br /> **ID** -o id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar os eventos em|  

## <a name="metrics-element"></a>Elemento de métricas  
 Permite-lhe gerar uma tabela de contador de desempenho otimizado para consultas rápidas. A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**MetricAggregation**|Atributo necessário:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [tipo de dados de duração do XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters elemento  
 Permite a recolha de contadores de desempenho. A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Os seguintes atributos são necessários:<br /><br /> -                     **counterSpecifier** -o nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de desempenho contadores no seu anfitrião execute o comando `typeperf`.<br /><br /> -                     **SampleRate como sendo** -a frequência com que o contador de amostragem.<br /><br /> Atributo opcional:<br /><br /> **unidade** -a unidade de medida do contador.|  

## <a name="performancecounterconfiguration-element"></a>Elemento de PerformanceCounterConfiguration  
 A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**annotation**|Atributo necessário:<br /><br /> **displayName** -o nome a apresentar para o contador<br /><br /> Atributo opcional:<br /><br /> **Localidade** -a região a utilizar quando se apresenta o nome do contador|  

## <a name="windowseventlog-element"></a>Elemento de WindowsEventLog  
 A tabela seguinte descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DataSource**|Os registos de eventos do Windows para recolher. Atributo necessário:<br /><br /> **nome** - a consulta de XPath que descrevem os eventos do windows a serem recolhidos. Por exemplo:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Para recolher todos os eventos, especificar "*".|

