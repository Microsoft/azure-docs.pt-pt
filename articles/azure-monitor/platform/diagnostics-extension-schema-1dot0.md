---
title: Esquema de configuração de diagnósticos do Azure 1.0
description: Relevante apenas se estiver a utilizar o Azure SDK 2.4 e abaixo com máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric ou serviços Cloud.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237847"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Esquema de configuração de diagnósticos do Azure 1.0
> [!NOTE]
> Diagnóstico do Azure é o componente utilizado para recolher contadores de desempenho e outras estatísticas de máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Service Fabric e serviços em nuvem.  Esta página só é relevante se estiver a utilizar um destes serviços.
>

Diagnóstico do Azure é utilizado com outros produtos de diagnóstico da Microsoft, como o Azure Monitor, que inclui o Application Insights e o Log Analytics.

O ficheiro de configuração de diagnósticos do Azure define os valores que são usados para inicializar o Monitor de diagnóstico. Esse arquivo é usado para inicializar as definições de diagnóstico de configuração, quando o monitor de diagnóstico é iniciado.  

 Por predefinição, o ficheiro de esquema de configuração de diagnósticos do Azure está instalado para o `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` diretório. Substitua `<version>` com a versão instalada do [SDK do Azure](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  O ficheiro de configuração de diagnósticos é normalmente utilizado com as tarefas de arranque que necessitam de dados de diagnóstico a serem recolhidos anteriormente no processo de inicialização. Para obter mais informações sobre como utilizar o diagnóstico do Azure, consulte [recolher dados do registo de diagnóstico do Azure utilizando](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do ficheiro de configuração de diagnósticos  
 O exemplo seguinte mostra um ficheiro de configuração de diagnósticos típico:  

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

## <a name="diagnosticsconfiguration-namespace"></a>Espaço de nomes de DiagnosticsConfiguration  
 O espaço de nomes XML para o ficheiro de configuração de diagnósticos é:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementos de esquema  
 O ficheiro de configuração de diagnósticos inclui os seguintes elementos.


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento de DiagnosticMonitorConfiguration  
O elemento de nível superior do ficheiro de configuração de diagnósticos.  

Atributos:

|Atributo  |Tipo   |Necessário| Predefinição | Descrição|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|Duração|Opcional | PT1M| Especifica o intervalo a que consulta o monitor de diagnóstico para alterações de configuração de diagnóstico.|  
|**overallQuotaInMB**|unsignedInt|Opcional| 4000 MB. Se fornecer um valor, não deve exceder este montante |A quantidade total de armazenamento alocado para todos os buffers de log do sistema de ficheiros.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Elemento de DiagnosticInfrastructureLogs  
Define a configuração de memória intermédia para os registos gerados pela infraestrutura subjacente de diagnóstico.

Elemento principal: Elemento de DiagnosticMonitorConfiguration.  

Atributos:

|Atributo|Type|Descrição|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento de sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilter**|string|Opcional. Especifica o nível de gravidade mínimo para entradas de registo que são transferidos. O valor predefinido é **indefinido**. Outros valores possíveis são **verboso**, **informações**, **aviso**, **erro**, e **crítico**.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="logs-element"></a>Elemento de registos  
 Define a configuração de memória intermédia para registos do Azure básicas.

 Elemento principal: Elemento de DiagnosticMonitorConfiguration.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento de sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilter**|string|Opcional. Especifica o nível de gravidade mínimo para entradas de registo que são transferidos. O valor predefinido é **indefinido**. Outros valores possíveis são **verboso**, **informações**, **aviso**, **erro**, e **crítico**.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="directories-element"></a>Elemento de diretórios  
Define a configuração de memória intermédia para os registos baseados em ficheiros, que pode definir.

Elemento principal: Elemento de DiagnosticMonitorConfiguration.  


Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento de sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="crashdumps-element"></a>Elemento de CrashDumps  
 Define o diretório de despejos de falhas.

 Elemento principal: Elemento de diretórios.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**container**|string|O nome do contentor onde o conteúdo do diretório está a ser transferidos.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> A predefinição é 0.|  

## <a name="failedrequestlogs-element"></a>Elemento de FailedRequestLogs  
 Define o diretório de registo de pedido falhado.

 Elemento de diretórios de elemento principal.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**container**|string|O nome do contentor onde o conteúdo do diretório está a ser transferidos.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> A predefinição é 0.|  

##  <a name="iislogs-element"></a>Elemento de IISLogs  
 Define o diretório de registo do IIS.

 Elemento de diretórios de elemento principal.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**container**|string|O nome do contentor onde o conteúdo do diretório está a ser transferidos.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> A predefinição é 0.|  

## <a name="datasources-element"></a>Elemento de origens de dados  
 Define a zero ou mais diretórios de registo adicionais.

 Elemento principal: Elemento de diretórios.

## <a name="directoryconfiguration-element"></a>Elemento de DirectoryConfiguration  
 Define o diretório de ficheiros de registo para monitorizar.

 Elemento principal: Elemento de origens de dados.

Atributos:

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**container**|string|O nome do contentor onde o conteúdo do diretório está a ser transferidos.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> A predefinição é 0.|  

## <a name="absolute-element"></a>Elemento absoluto  
 Define um caminho absoluto do diretório para monitorizar com a expansão de ambiente opcionais.

 Elemento principal: Elemento de DirectoryConfiguration.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**path**|string|Necessário. O caminho absoluto para o diretório a monitorizar.|  
|**expandEnvironment**|boolean|Necessário. Se definido como **true**, variáveis de ambiente no caminho são expandidas.|  

## <a name="localresource-element"></a>Elemento de LocalResource  
 Define um caminho relativo para um recurso local definido na definição de serviço.

 Elemento principal: Elemento de DirectoryConfiguration.  

Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**name**|string|Necessário. O nome do recurso local que contém o diretório para monitorizar.|  
|**relativePath**|string|Necessário. O caminho relativo para o recurso local para monitorizar.|  

## <a name="performancecounters-element"></a>PerformanceCounters elemento  
 Define o caminho para o contador de desempenho a recolher.

 Elemento principal: Elemento de DiagnosticMonitorConfiguration.


 Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento de sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Elemento de PerformanceCounterConfiguration  
 Define o contador de desempenho a recolher.

 Elemento principal: PerformanceCounters elemento.  

 Atributos:  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**counterSpecifier**|string|Necessário. O caminho para o contador de desempenho a recolher.|  
|**sampleRate**|Duração|Necessário. A taxa em que o contador de desempenho deve ser recolhido.|  

## <a name="windowseventlog-element"></a>Elemento de WindowsEventLog  
 Define os registos de eventos a monitorizar.

 Elemento principal: Elemento de DiagnosticMonitorConfiguration.

  Atributos:

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento de sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilter**|string|Opcional. Especifica o nível de gravidade mínimo para entradas de registo que são transferidos. O valor predefinido é **indefinido**. Outros valores possíveis são **verboso**, **informações**, **aviso**, **erro**, e **crítico**.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="datasource-element"></a>Elemento de origem de dados  
 Define o registo de eventos a monitorizar.

 Elemento principal: Elemento de WindowsEventLog.  

 Atributos:

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**name**|string|Necessário. Uma expressão XPath especificando o registo para recolher.|  

