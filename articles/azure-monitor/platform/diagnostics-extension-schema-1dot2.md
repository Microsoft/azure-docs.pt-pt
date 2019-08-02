---
title: Esquema de configuração do Diagnóstico do Azure Extension 1,2
description: Relevante apenas se você estiver usando o SDK do Azure 2,5 com máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric ou serviços de nuvem.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237842"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Esquema de configuração do Diagnóstico do Azure 1,2
> [!NOTE]
> Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric e serviços de nuvem.  Essa página só será relevante se você estiver usando um desses serviços.
>

O Diagnóstico do Azure é usado com outros produtos de diagnóstico da Microsoft, como Azure Monitor, que inclui Application Insights e Log Analytics.

Esse esquema define os valores possíveis que você pode usar para inicializar as definições de configuração de diagnóstico quando o monitor de diagnóstico é iniciado.  


 Baixe a definição de esquema do arquivo de configuração pública executando o seguinte comando do PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Para obter mais informações sobre como usar Diagnóstico do Azure, consulte Habilitando o [diagnóstico nos serviços de nuvem do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do arquivo de configuração de diagnóstico  
 O exemplo a seguir mostra um arquivo de configuração de diagnóstico típico:  

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

## <a name="diagnostics-configuration-namespace"></a>Namespace de configuração de diagnóstico  
 O namespace XML para o arquivo de configuração de diagnóstico é:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 Elemento de nível superior do arquivo de configuração de diagnóstico. A tabela a seguir descreve os elementos do arquivo de configuração.  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**WadCfg**|Necessário. Definições de configuração para os dados de telemetria a serem coletados.|  
|**StorageAccount**|O nome da conta de armazenamento do Azure na qual armazenar os dados. Isso também pode ser especificado como um parâmetro ao executar o cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|O diretório na máquina virtual a ser usado pelo agente de monitoramento para armazenar dados de evento. Se não estiver definido, o diretório padrão será usado:<br /><br /> Para uma função de trabalho/Web:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma máquina virtual:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Os atributos necessários são:<br /><br /> -                      **caminho** -o diretório no sistema a ser usado pelo diagnóstico do Azure.<br /><br /> -                      **expandEnvironment** -controla se as variáveis de ambiente são expandidas no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
Define as definições de configuração dos dados de telemetria a serem coletados. A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Necessário. Os atributos opcionais são:<br /><br /> -                     **overallQuotaInMB** -a quantidade máxima de espaço em disco local que pode ser consumida pelos vários tipos de dados de diagnóstico coletados pelo diagnóstico do Azure. A configuração padrão é 5120MB.<br /><br /> -                     **useProxyServer** -configure diagnóstico do Azure para usar as configurações do servidor proxy, conforme definido nas configurações do IE.|  
|**CrashDumps**|Habilitar a coleta de despejos de memória. Os atributos opcionais são:<br /><br /> -                     **ContainerName** -o nome do contêiner de BLOB em sua conta de armazenamento do Azure a ser usado para armazenar despejos de memória.<br /><br /> -                     **crashDumpType** – configura diagnóstico do Azure para coletar mini ou despejos de memória completos.<br /><br /> -                     **directoryQuotaPercentage**– configura a porcentagem de **overallQuotaInMB** a ser reservada para despejos de memória na VM.|  
|**DiagnosticInfrastructureLogs**|Habilite a coleta de logs gerados por Diagnóstico do Azure. Os logs de infraestrutura de diagnóstico são úteis para solucionar problemas do próprio sistema de diagnóstico. Os atributos opcionais são:<br /><br /> -                     **scheduledTransferLogLevelFilter** – configura o nível de severidade mínimo dos logs coletados.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. O valor é um [XML "tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Diretórios**|Habilita a coleta do conteúdo de um diretório, o IIS falhou em logs de solicitação de acesso e/ou logs do IIS. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. O valor é um [XML "tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Configura a coleta de eventos ETW dos provedores baseados em EventSource e/ou de manifesto ETW.|  
|**Métricas**|Esse elemento permite que você gere uma tabela de contador de desempenho otimizada para consultas rápidas. Cada contador de desempenho definido no elemento **PerformanceCounters** é armazenado na tabela de métricas, além da tabela de contador de desempenho. Atributo obrigatório:<br /><br /> ResourceId-essa é a ID de recurso da máquina virtual na qual você está implantando diagnóstico do Azure. Obtenha o ResourceId do [portal do Azure](https://portal.azure.com). Selecione **procurar** ->  **** gruposde -> recursos **<nome\>** . Clique no bloco **Propriedades** e copie o valor do campo **ID** .|  
|**PerformanceCounters**|Habilita a coleta de contadores de desempenho. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. Value é um [XML "tipo de dados Duration".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Habilita a coleta de logs de eventos do Windows. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. Value é um [XML "tipo de dados Duration".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Habilita a coleta de despejos de memória. A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Necessário. Atributo obrigatório:<br /><br /> **ProcessName** -o nome do processo para o qual você deseja diagnóstico do Azure coletar um despejo de memória.|  
|**crashDumpType**|Configura Diagnóstico do Azure para coletar mini ou despejos de memória completos.|  
|**directoryQuotaPercentage**|Configura a porcentagem de **overallQuotaInMB** a ser reservada para despejos de memória na VM.|  

## <a name="directories-element"></a>Elemento de diretórios  
 Habilita a coleta do conteúdo de um diretório, o IIS falhou em logs de solicitação de acesso e/ou logs do IIS. A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DataSources**|Uma lista de diretórios a serem monitorados.|  
|**FailedRequestLogs**|A inclusão desse elemento na configuração habilita a coleta de logs sobre solicitações com falha para um site ou aplicativo do IIS. Você também deve habilitar as opções de rastreamento em **sistema. Webserver** em **Web. config**.|  
|**IISLogs**|A inclusão desse elemento na configuração habilita a coleta de logs do IIS:<br /><br /> **ContainerName** -o nome do contêiner de BLOB em sua conta de armazenamento do Azure a ser usado para armazenar os logs do IIS.|  

## <a name="datasources-element"></a>Elemento dataSources  
 Uma lista de diretórios a serem monitorados. A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Necessário. Atributo obrigatório:<br /><br /> **ContainerName** -o nome do contêiner de BLOB em sua conta de armazenamento do Azure a ser usado para armazenar os arquivos de log.|  

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 **DirectoryConfiguration** pode incluir o elemento **Absolute** ou **LocalResource** , mas não ambos. A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Altos**|O caminho absoluto para o diretório a ser monitorado. Os seguintes atributos são necessários:<br /><br /> -                     **Caminho** -o caminho absoluto para o diretório a ser monitorado.<br /><br /> -                      **expandEnvironment** – define se as variáveis de ambiente no caminho são expandidas.|  
|**LocalResource**|O caminho relativo a um recurso local a ser monitorado. Os atributos necessários são:<br /><br /> -                     **Nome** -o recurso local que contém o diretório a ser monitorado<br /><br /> -                     **RelativePath** -o caminho relativo ao nome que contém o diretório a ser monitorado|  

## <a name="etwproviders-element"></a>Elemento EtwProviders  
 Configura a coleta de eventos ETW dos provedores baseados em EventSource e/ou de manifesto ETW. A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a coleção de eventos gerados da [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo obrigatório:<br /><br /> **provedor** -o nome da classe do evento EventSource.<br /><br /> Os atributos opcionais são:<br /><br /> -                     **scheduledTransferLogLevelFilter** -o nível de severidade mínimo a ser transferido para sua conta de armazenamento.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. Valor é um [tipo de dados de duração XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Atributo obrigatório:<br /><br /> **provedor** -o GUID do provedor de eventos<br /><br /> Os atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de severidade mínimo a ser transferido para sua conta de armazenamento.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. Valor é um [tipo de dados de duração XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 Configura a coleção de eventos gerados da [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela na qual armazenar os eventos|  
|**Evento**|Atributo obrigatório:<br /><br /> **ID** -a ID do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela na qual armazenar os eventos|  

## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela na qual armazenar os eventos|  
|**Evento**|Atributo obrigatório:<br /><br /> **ID** -a ID do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela na qual armazenar os eventos|  

## <a name="metrics-element"></a>Elemento de métricas  
 Permite que você gere uma tabela de contador de desempenho otimizada para consultas rápidas. A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**MetricAggregation**|Atributo obrigatório:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. Valor é um [tipo de dados de duração XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Habilita a coleta de contadores de desempenho. A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Os seguintes atributos são necessários:<br /><br /> -                     **CounterSet-o** nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de contadores de desempenho em seu host, execute `typeperf`o comando.<br /><br /> -                     **amostrar** -a frequência com que o contador deve ser amostrado.<br /><br /> Atributo opcional:<br /><br /> **unidade** -a unidade de medida do contador.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**annotation**|Atributo obrigatório:<br /><br /> **DisplayName** -o nome para exibição do contador<br /><br /> Atributo opcional:<br /><br /> **localidade** -a localidade a ser usada ao exibir o nome do contador|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 A tabela a seguir descreve os elementos filho:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DataSource**|Os logs de eventos do Windows a serem coletados. Atributo obrigatório:<br /><br /> **nome** -a consulta XPath que descreve os eventos do Windows a serem coletados. Por exemplo:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Para coletar todos os eventos, especifique "*".|

