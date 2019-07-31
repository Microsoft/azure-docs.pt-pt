---
title: Esquema de configuração do Diagnóstico do Azure 1,0
description: Relevante apenas se você estiver usando o SDK do Azure 2,4 e versões anteriores com máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric ou serviços de nuvem.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237847"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Esquema de configuração do Diagnóstico do Azure 1,0
> [!NOTE]
> Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric e serviços de nuvem.  Essa página só será relevante se você estiver usando um desses serviços.
>

O Diagnóstico do Azure é usado com outros produtos de diagnóstico da Microsoft, como Azure Monitor, que inclui Application Insights e Log Analytics.

O arquivo de configuração Diagnóstico do Azure define os valores que são usados para inicializar o monitor de diagnóstico. Esse arquivo é usado para inicializar as definições de configuração de diagnóstico quando o monitor de diagnóstico é iniciado.  

 Por padrão, o arquivo de esquema de configuração diagnóstico do Azure é instalado `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` no diretório. Substitua `<version>` pela versão instalada do SDK do [Azure](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  O arquivo de configuração de diagnóstico normalmente é usado com tarefas de inicialização que exigem que os dados de diagnóstico sejam coletados anteriormente no processo de inicialização. Para obter mais informações sobre como usar Diagnóstico do Azure, consulte [coletar dados de log usando diagnóstico do Azure](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do arquivo de configuração de diagnóstico  
 O exemplo a seguir mostra um arquivo de configuração de diagnóstico típico:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>Namespace DiagnosticsConfiguration  
 O namespace XML para o arquivo de configuração de diagnóstico é:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementos de esquema  
 O arquivo de configuração de diagnóstico inclui os elementos a seguir.


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration  
O elemento de nível superior do arquivo de configuração de diagnóstico.  

Atributos:

|Atributo  |Type   |Requerido| Predefinição | Descrição|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Opcional | PT1M| Especifica o intervalo no qual o monitor de diagnóstico sonda as alterações de configuração de diagnóstico.|  
|**overallQuotaInMB**|unsignedInt|Opcional| 4000 MB. Se você fornecer um valor, ele não deve exceder esse valor |A quantidade total de armazenamento do sistema de arquivos alocada para todos os buffers de log.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Elemento DiagnosticInfrastructureLogs  
Define a configuração de buffer para os logs gerados pela infraestrutura de diagnóstico subjacente.

Elemento pai: Elemento DiagnosticMonitorConfiguration.  

Atributos:

|Atributo|Type|Descrição|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos que está disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferLogLevelFilter**|cadeia|Opcional. Especifica o nível de severidade mínimo para entradas de log que são transferidas. O valor padrão é **indefinido**. Outros valores possíveis são **detalhado**, **informações**, **aviso**, **erro**e **crítico**.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="logs-element"></a>Elemento logs  
 Define a configuração de buffer para logs básicos do Azure.

 Elemento pai: Elemento DiagnosticMonitorConfiguration.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos que está disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferLogLevelFilter**|Cadeia de caracteres|Opcional. Especifica o nível de severidade mínimo para entradas de log que são transferidas. O valor padrão é **indefinido**. Outros valores possíveis são **detalhado**, **informações**, **aviso**, **erro**e **crítico**.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="directories-element"></a>Elemento de diretórios  
Define a configuração de buffer para logs baseados em arquivo que você pode definir.

Elemento pai: Elemento DiagnosticMonitorConfiguration.  


Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos que está disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Define o diretório de despejos de memória.

 Elemento pai: Elemento de diretórios.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**container**|cadeia|O nome do contêiner em que o conteúdo do diretório deve ser transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> O padrão é 0.|  

## <a name="failedrequestlogs-element"></a>Elemento FailedRequestLogs  
 Define o diretório de log de solicitações com falha.

 Elemento de diretórios do elemento pai.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**container**|Cadeia de caracteres|O nome do contêiner em que o conteúdo do diretório deve ser transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> O padrão é 0.|  

##  <a name="iislogs-element"></a>Elemento IISLogs  
 Define o diretório de log do IIS.

 Elemento de diretórios do elemento pai.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**container**|Cadeia de caracteres|O nome do contêiner em que o conteúdo do diretório deve ser transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> O padrão é 0.|  

## <a name="datasources-element"></a>Elemento dataSources  
 Define zero ou mais diretórios de log adicionais.

 Elemento pai: Elemento de diretórios.

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 Define o diretório de arquivos de log a serem monitorados.

 Elemento pai: Elemento dataSources.

Atributos:

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**container**|cadeia|O nome do contêiner em que o conteúdo do diretório deve ser transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> O padrão é 0.|  

## <a name="absolute-element"></a>Elemento absoluto  
 Define um caminho absoluto do diretório a ser monitorado com expansão de ambiente opcional.

 Elemento pai: Elemento DirectoryConfiguration.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**path**|Cadeia de caracteres|Necessário. O caminho absoluto para o diretório a ser monitorado.|  
|**expandEnvironment**|boolean|Necessário. Se definido como **true**, as variáveis de ambiente no caminho serão expandidas.|  

## <a name="localresource-element"></a>Elemento LocalResource  
 Define um caminho relativo a um recurso local definido na definição de serviço.

 Elemento pai: Elemento DirectoryConfiguration.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**name**|Cadeia de caracteres|Necessário. O nome do recurso local que contém o diretório a ser monitorado.|  
|**relativePath**|cadeia|Necessário. O caminho relativo ao recurso local a ser monitorado.|  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Define o caminho para o contador de desempenho a ser coletado.

 Elemento pai: Elemento DiagnosticMonitorConfiguration.


 Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos que está disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 Define o contador de desempenho a ser coletado.

 Elemento pai: Elemento PerformanceCounters.  

 Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**counterSpecifier**|cadeia|Necessário. O caminho para o contador de desempenho a ser coletado.|  
|**sampleRate**|duration|Necessário. A taxa na qual o contador de desempenho deve ser coletado.|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 Define os logs de eventos a serem monitorados.

 Elemento pai: Elemento DiagnosticMonitorConfiguration.

  Atributos:

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos que está disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**scheduledTransferLogLevelFilter**|Cadeia de caracteres|Opcional. Especifica o nível de severidade mínimo para entradas de log que são transferidas. O valor padrão é **indefinido**. Outros valores possíveis são **detalhado**, **informações**, **aviso**, **erro**e **crítico**.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  

## <a name="datasource-element"></a>Elemento DataSource  
 Define o log de eventos a ser monitorado.

 Elemento pai: Elemento WindowsEventLog.  

 Atributos:

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**name**|Cadeia de caracteres|Necessário. Uma expressão XPath que especifica o log a ser coletado.|  

