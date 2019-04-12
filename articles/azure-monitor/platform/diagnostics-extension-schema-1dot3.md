---
title: Extensão de diagnóstico do Azure 1.3 e posterior esquema de configuração
description: Versão de esquema 1.3 e posterior diagnóstico do Azure fornecidos como parte do Microsoft Azure SDK 2.4 e mais tarde.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: fa03017c35c76d986139eeee00eea8a9b4a00e62
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59497088"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>1.3 de diagnóstico do Azure e o esquema de configuração posterior
> [!NOTE]
> A extensão de diagnóstico do Azure é o componente utilizado para recolher contadores de desempenho e outras estatísticas de:
> - Máquinas Virtuais do Azure
> - Conjuntos de Dimensionamento de Máquinas Virtuais
> - Service Fabric
> - Serviços Cloud
> - Grupos de Segurança de Rede
>
> Esta página só é relevante se estiver a utilizar um destes serviços.

Esta página é válida para versões 1.3 e mais recente (Azure SDK 2.4 e mais recente). As seções de configuração mais recente são inserido um comentário para mostrar no qual a versão terem sido adicionados.  

O ficheiro de configuração descrito aqui é usado para definir as definições de diagnóstico de configuração, quando o monitor de diagnóstico é iniciado.  

A extensão é utilizada em conjunto com outros produtos de diagnóstico da Microsoft, como o Azure Monitor, que inclui o Application Insights e o Log Analytics.



Transferir a definição de esquema do ficheiro de configuração pública ao executar o seguinte comando do PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Para obter mais informações sobre como utilizar o diagnóstico do Azure, consulte [extensão de diagnóstico do Azure](diagnostics-extension-overview.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do ficheiro de configuração de diagnósticos  
 O exemplo seguinte mostra um ficheiro de configuração de diagnósticos típico:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
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
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
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

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> A configuração pública de definição de sink do Azure Monitor tem duas propriedades, resourceId e região. Só são necessárias para serviços Cloud clássico e de VMs clássicas. Estas propriedades não devem ser utilizadas para máquinas de virtuais do Resource Manager ou conjuntos de dimensionamento de Máquina Virtual.
> Também há um elemento de configuração privada adicional para o sink do Azure Monitor, que passa um Principal de Id e segredo. Isto só é necessária para as VMs clássicas e de serviços de Cloud clássico. Para VMs do Resource Manager e o Azure Monitor VMSS a definição do elemento de configuração privada pode ser excluída.
>

Equivalente JSON do ficheiro de configuração XML anterior.

O PublicConfig e PrivateConfig estão separadas porque, na maioria dos casos de utilização de json, elas são passadas como variáveis diferentes. Nestes casos incluem modelos do Resource Manager, PowerShell e o Visual Studio do conjunto de dimensionamento de Máquina Virtual.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> A configuração pública de definição de sink do Azure Monitor tem duas propriedades, resourceId e região. Só são necessárias para serviços Cloud clássico e de VMs clássicas.
> Estas propriedades não devem ser utilizadas para máquinas de virtuais do Resource Manager ou conjuntos de dimensionamento de Máquina Virtual.
>

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

> [!NOTE]
> Há um elemento de configuração privada adicional para o sink do Azure Monitor, que passa um Principal de Id e segredo. Isto só é necessária para as VMs clássicas e de serviços de Cloud clássico. Para VMs do Resource Manager e o Azure Monitor VMSS a definição do elemento de configuração privada pode ser excluída.
>


## <a name="reading-this-page"></a>Ler esta página  
 As etiquetas seguintes são mais ou menos em ordem mostrada no exemplo anterior.  Se não vir uma descrição completa em que espera, procure a página para o elemento ou atributo.  

## <a name="common-attribute-types"></a>Tipos de atributos comuns  
 **scheduledTransferPeriod** atributo é apresentado em vários elementos. É o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Elemento de DiagnosticsConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration*

Adicionado na versão 1.3.  

O elemento de nível superior do ficheiro de configuração de diagnósticos.  

**Atributo** xmlns - o espaço de nomes XML para o ficheiro de configuração de diagnósticos é:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**PublicConfig**|Necessário. Ver descrição noutro local nesta página.|  
|**PrivateConfig**|Opcional. Ver descrição noutro local nesta página.|  
|**IsEnabled**|Valor booleano. Ver descrição noutro local nesta página.|  

## <a name="publicconfig-element"></a>Elemento de PublicConfig  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig*

 Descreve a configuração de diagnósticos pública.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**WadCfg**|Necessário. Ver descrição noutro local nesta página.|  
|**StorageAccount**|O nome da conta para armazenar os dados no armazenamento do Azure. Também pode ser especificado como um parâmetro ao executar o cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Pode ser *tabela*, *Blob*, ou *TableAndBlob*. A tabela é a predefinição. Quando TableAndBlob for escolhida, dados de diagnóstico são escritos em duas vezes – uma vez para cada tipo.|  
|**LocalResourceDirectory**|O diretório na máquina virtual em que o agente de monitorização armazena dados de eventos. Se não, definido, é utilizado o diretório predefinido:<br /><br /> Para uma função de trabalho/web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma Máquina Virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Atributos necessários são:<br /><br /> - **caminho** -o diretório do sistema a ser utilizado pelo diagnóstico do Azure.<br /><br /> - **expandEnvironment** -controla se as variáveis de ambiente são expandidas no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento de WadCFG  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG*

 Identifica e configura os dados de telemetria recolha.  


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento de DiagnosticMonitorConfiguration
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Necessário

|Atributos|Descrição|  
|----------------|-----------------|  
| **overallQuotaInMB** | A quantidade máxima de espaço em disco local que pode ser utilizada por vários tipos de dados de diagnóstico recolhidos pelo diagnóstico do Azure. A configuração padrão é 4096 MB.<br />
|**useProxyServer** | Configure o diagnóstico do Azure para utilizar as definições do servidor proxy, conforme definido nas definições do IE.|
|**Coletores de** | Adicionado 1.5. Opcional. Aponta para uma localização de sink para também enviar dados de diagnóstico para todos os elementos filho que suportam sinks. Exemplo de sink é o Application Insights ou Hubs de eventos.|  


<br /> <br />

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**CrashDumps**|Ver descrição noutro local nesta página.|  
|**DiagnosticInfrastructureLogs**|Ative a recolha de registos gerados pelo diagnóstico do Azure. Os registos de diagnóstico de infraestrutura são úteis para resolver o próprio sistema de diagnóstico. Atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** -configura o nível de gravidade mínimo dos registos recolhidos.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Diretórios**|Ver descrição noutro local nesta página.|  
|**EtwProviders**|Ver descrição noutro local nesta página.|  
|**Métricas**|Ver descrição noutro local nesta página.|  
|**PerformanceCounters**|Ver descrição noutro local nesta página.|  
|**WindowsEventLog**|Ver descrição noutro local nesta página.|
|**DockerSources**|Ver descrição noutro local nesta página. |



## <a name="crashdumps-element"></a>Elemento de CrashDumps  
 *Árvore: -DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps de raiz*

 Ative a recolha de informações de falhas.  

|Atributos|Descrição|  
|----------------|-----------------|  
|**containerName**|Opcional. O nome do contentor de BLOBs na sua conta de armazenamento do Azure a ser utilizado para armazenar despejos de memória.|  
|**crashDumpType**|Opcional.  Configura o diagnóstico do Azure para coletar despejos de mini ou falha completa do sistema.|  
|**directoryQuotaPercentage**|Opcional.  Configura a percentagem de **overallQuotaInMB** a reservar para informações de falhas na VM.|  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Necessário. Define os valores de configuração para cada processo.<br /><br /> Também é necessário o seguinte atributo:<br /><br /> **processName** -o nome do processo de que pretende que o diagnóstico do Azure para recolher um despejo de falha para.|  

## <a name="directories-element"></a>Elemento de diretórios
 *Árvore: -DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - diretórios de raiz*

 Permite a recolha do conteúdo de um diretório, o IIS não conseguiu aceder aos registos de pedido e/ou registos do IIS.  

 Opcional **scheduledTransferPeriod** atributo. Ver a explicação anteriormente.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**IISLogs**|Incluindo esse elemento na configuração permite que a coleção de registos do IIS:<br /><br /> **containerName** -o nome do contentor de BLOBs na sua conta de armazenamento do Azure a ser utilizado para armazenar os registos IIS.|   
|**FailedRequestLogs**|Incluindo esse elemento na configuração permite a recolha de registos de pedidos falhados para um aplicativo ou site do IIS. Também tem de ativar as opções de rastreamento em **system. Servidor Web** no **Web. config**.|  
|**DataSources**|Uma lista de diretórios a monitorizar.|




## <a name="datasources-element"></a>Elemento de origens de dados  
 *Árvore: Origens de dados do raiz - DiagnosticsConfiguration - PublicConfig - diretórios WadCFG - DiagnosticMonitorConfiguration-*

 Uma lista de diretórios a monitorizar.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Necessário. Atributo necessário:<br /><br /> **containerName** -o nome do contentor de BLOBs no armazenamento do Azure da conta que será utilizada para armazenar os ficheiros de registo.|  





## <a name="directoryconfiguration-element"></a>Elemento de DirectoryConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - diretórios - origens de dados - DirectoryConfiguration*

 Pode incluir qualquer um de **absoluto** ou **LocalResource** elemento, mas não ambos.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**Absoluto**|O caminho absoluto para o diretório a monitorizar. Os seguintes atributos são necessários:<br /><br /> - **Caminho** -o caminho absoluto para o diretório a monitorizar.<br /><br /> - **expandEnvironment** -configura se as variáveis de ambiente no caminho são expandidas.|  
|**LocalResource**|O caminho relativo para um recurso local para monitorizar. Atributos necessários são:<br /><br /> - **Nome** -o recurso local que contém o diretório para monitorizar<br /><br /> - **relativePath** -o caminho relativo ao nome que contém o diretório para monitorizar|  



## <a name="etwproviders-element"></a>Elemento de EtwProviders  
 *Árvore: -DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders de raiz*

 Configura a recolha de eventos do ETW do EventSource de e/ou o manifesto do ETW com base em provedores.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a recolha de eventos gerados a partir [EventSource classe](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo necessário:<br /><br /> **fornecedor** -o nome da classe do evento EventSource.<br /><br /> Atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de gravidade mínimo para transferir a sua conta de armazenamento.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Atributo necessário:<br /><br /> **fornecedor** -o GUID do provedor do evento<br /><br /> Atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de gravidade mínimo para transferir a sua conta de armazenamento.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>Elemento de EtwEventSourceProviderConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwEventSourceProviderConfiguration*

 Configura a recolha de eventos gerados a partir [EventSource classe](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br/><br/> **eventDestination** -o nome da tabela para armazenar os eventos em|  
|**Evento**|Atributo necessário:<br /><br /> **ID** -o id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar os eventos em|  



## <a name="etwmanifestproviderconfiguration-element"></a>Elemento de EtwManifestProviderConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar os eventos em|  
|**Evento**|Atributo necessário:<br /><br /> **ID** -o id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar os eventos em|  



## <a name="metrics-element"></a>Elemento de métricas  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - métricas*

 Permite-lhe gerar uma tabela de contador de desempenho otimizado para consultas rápidas. Cada contador de desempenho que está definido no **PerformanceCounters** elemento é armazenado na tabela de métricas para além da tabela de contador de desempenho.  

 O **resourceId** atributo é necessário.  O ID de recurso da Máquina Virtual ou o conjunto de dimensionamento de Máquina Virtual que está a implementar o diagnóstico do Azure para. Obter o **resourceID** partir do [portal do Azure](https://portal.azure.com). Selecione **navegue** -> **grupos de recursos** -> **< nome\>**. Clique no **propriedades** mosaico e copie o valor a partir do **ID** campo.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**MetricAggregation**|Atributo necessário:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências de agendada para o armazenamento, arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters elemento  
 *Árvore: -DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters de raiz*

 Permite a recolha de contadores de desempenho.  

 Atributo opcional:  

 Opcional **scheduledTransferPeriod** atributo. Ver a explicação anteriormente.

|Elemento subordinado|Descrição|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Os seguintes atributos são necessários:<br /><br /> - **counterSpecifier** -o nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de contadores de desempenho no seu anfitrião, execute o comando `typeperf`.<br /><br /> - **SampleRate como sendo** -a frequência com que o contador de amostragem.<br /><br /> Atributo opcional:<br /><br /> **unidade** -a unidade de medida do contador.|
|**Coletores de** | Adicionado 1.5. Opcional. Aponta para uma localização de sink para também enviar dados de diagnóstico. Por exemplo, do Azure Monitor ou Hubs de eventos.|    




## <a name="windowseventlog-element"></a>Elemento de WindowsEventLog
 *Árvore: -DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog de raiz*

 Permite a recolha de registos de eventos do Windows.  

 Opcional **scheduledTransferPeriod** atributo. Ver a explicação anteriormente.  

|Elemento subordinado|Descrição|  
|-------------------|-----------------|  
|**Origem de dados**|Os registos de eventos do Windows para recolher. Atributo necessário:<br /><br /> **nome** - a consulta de XPath que descrevem os eventos do windows a serem recolhidos. Por exemplo:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Para recolher todos os eventos, especificar "*"|  




## <a name="logs-element"></a>Elemento de registos  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 Apresentar na versão 1.0 e 1.1. Em falta no 1.2. Adicionado novamente no 1.3.  

 Define a configuração de memória intermédia para registos do Azure básicas.  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Opcional. Especifica a quantidade máxima de armazenamento de sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilter**|**string**|Opcional. Especifica o nível de gravidade mínimo para entradas de registo que são transferidos. O valor predefinido é **indefinido**, que transfere todos os registos. Outros valores possíveis (na ordem da maioria às informações do menor) são **verboso**, **informações**, **aviso**, **erro**e o **Críticos**.|  
|**scheduledTransferPeriod**|**duração**|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  
|**Coletores de** |**string**| Adicionado 1.5. Opcional. Aponta para uma localização de sink para também enviar dados de diagnóstico. Por exemplo, Application Insights ou Hubs de eventos.|  

## <a name="dockersources"></a>DockerSources
 *Árvore: -DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources de raiz*

 Adicionado no 1.9.

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Estatísticas**|Informa ao sistema para coletar estatísticas para contentores do Docker|  

## <a name="sinksconfig-element"></a>Elemento de SinksConfig  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Uma lista de localizações para enviar dados de diagnóstico para e a configuração associada nessas localizações.  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Sink**|Ver descrição noutro local nesta página.|  

## <a name="sink-element"></a>Elemento de sink
 *Árvore: SinksConfig raiz - WadCFG DiagnosticsConfiguration - PublicConfig - - - Sink*

 Adicionado na versão 1.5.  

 Define as localizações para enviar dados de diagnóstico. Por exemplo, o serviço Application Insights.  

|Atributo|Type|Descrição|  
|---------------|----------|-----------------|  
|**nome**|string|Uma cadeia de identificação a sinkname.|  

|Elemento|Type|Descrição|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Utilizado apenas quando enviar dados para o Application Insights. Contém a chave de instrumentação para uma conta ativa do Application Insights tem acesso.|  
|**Canais**|string|Um para cada adicional de filtragem que do stream|  

## <a name="channels-element"></a>Elemento de canais  
 *Árvore: Canais de SinksConfig - Sink - raiz - WadCFG DiagnosticsConfiguration - PublicConfig-*

 Adicionado na versão 1.5.  

 Define os filtros para fluxos de dados de registo, passando por meio de um sink.  

|Elemento|Type|Descrição|  
|-------------|----------|-----------------|  
|**Canal**|string|Ver descrição noutro local nesta página.|  

## <a name="channel-element"></a>Elemento de canal
 *Árvore: Canais de SinksConfig - Sink - raiz - WadCFG DiagnosticsConfiguration - PublicConfig - - - canal*

 Adicionado na versão 1.5.  

 Define as localizações para enviar dados de diagnóstico. Por exemplo, o serviço Application Insights.  

|Atributos|Type|Descrição|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Especifica o nível de gravidade mínimo para entradas de registo que são transferidos. O valor predefinido é **indefinido**, que transfere todos os registos. Outros valores possíveis (na ordem da maioria às informações do menor) são **verboso**, **informações**, **aviso**, **erro**e o **Críticos**.|  
|**nome**|**string**|Um nome exclusivo do canal para fazer referência a|  


## <a name="privateconfig-element"></a>Elemento de PrivateConfig
 *Árvore: Raiz - DiagnosticsConfiguration - PrivateConfig*

 Adicionado na versão 1.3.  

 Opcional  

 Armazena os detalhes privados da conta de armazenamento (nome, a chave e ponto final). Estas informações são enviadas para a máquina virtual, mas não não possível obter a partir do mesmo.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**StorageAccount**|A conta de armazenamento a utilizar. Os seguintes atributos são necessários<br /><br /> - **nome** -o nome da conta de armazenamento.<br /><br /> - **chave** -a chave para a conta de armazenamento.<br /><br /> - **ponto final** -o ponto final para aceder à conta de armazenamento. <br /><br /> -**sasToken** (adicionado 1.8.1)-pode especificar um token SAS em vez de uma chave de conta de armazenamento a configuração privada. Se for fornecido, a chave de conta de armazenamento é ignorada. <br />Requisitos para o Token SAS: <br />-Suporta apenas o token SAS conta <br />- *b*, *t* tipos de serviço são necessários. <br /> - *um*, *c*, *u*, *w* são necessárias permissões. <br /> - *c*, *s* tipos de recursos são necessários. <br /> – Suporta o protocolo HTTPS apenas <br /> – Começar e a hora de expiração tem de ser válida.|  


## <a name="isenabled-element"></a>Elemento de IsEnabled  
 *Árvore: Raiz - DiagnosticsConfiguration - IsEnabled*

 Valor booleano. Uso `true` para ativar o diagnóstico ou `false` para desativar o diagnóstico.

