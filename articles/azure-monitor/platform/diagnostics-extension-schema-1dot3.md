---
title: Diagnóstico do Azure a extensão 1,3 e o esquema de configuração posterior
description: A versão de esquema 1,3 e posterior do diagnóstico do Azure foi fornecida como parte do Microsoft Azure SDK 2,4 e posterior.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 09/20/2018
ms.openlocfilehash: 2a3ee9731ebeb3b002f4dd9f5b856e720bf719d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395098"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Diagnóstico do Azure 1,3 e esquema de configuração posterior
> [!NOTE]
> A extensão de Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de:
> - Máquinas Virtuais do Azure
> - Conjuntos de Dimensionamento de Máquinas Virtuais
> - Service Fabric
> - Serviços em Nuvem
> - Grupos de Segurança de Rede
>
> Essa página só será relevante se você estiver usando um desses serviços.

Esta página é válida para as versões 1,3 e mais recentes (SDK do Azure 2,4 e mais recente). As seções de configuração mais recentes são comentadas para mostrar em qual versão elas foram adicionadas. A versão 1,0 e 1,2 do esquema foram arquivadas e não estão mais disponíveis. 

O arquivo de configuração descrito aqui é usado para definir as definições de configuração de diagnóstico quando o monitor de diagnóstico é iniciado.  

A extensão é usada em conjunto com outros produtos de diagnóstico da Microsoft, como Azure Monitor, que inclui Application Insights e Log Analytics.

Baixe a definição de esquema do arquivo de configuração pública executando o seguinte comando do PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Para obter mais informações sobre como usar Diagnóstico do Azure, consulte [diagnóstico do Azure extensão](diagnostics-extension-overview.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo do arquivo de configuração de diagnóstico  
 O exemplo a seguir mostra um arquivo de configuração de diagnóstico típico:  

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
> A definição pública de Azure Monitor de configuração do coletor tem duas propriedades, ResourceId e região. Eles só são necessários para VMs clássicas e serviços de nuvem clássicos. Essas propriedades não devem ser usadas para máquinas virtuais do Resource Manager ou conjuntos de dimensionamento de máquinas virtuais.
> Há também um elemento de configuração particular adicional para o coletor de Azure Monitor, que passa uma ID de entidade de segurança e um segredo. Isso só é necessário para VMs clássicas e serviços de nuvem clássicos. Para VMs do Gerenciador de recursos e VMSS, a definição de Azure Monitor no elemento de configuração particular pode ser excluída.
>

Equivalente JSON do arquivo de configuração XML anterior.

Os PublicConfig e PrivateConfig são separados porque, na maioria dos casos de uso de JSON, eles são passados como variáveis diferentes. Esses casos incluem modelos do Resource Manager, conjunto de dimensionamento de máquinas virtuais do PowerShell e do Visual Studio.

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
> A definição pública de Azure Monitor de configuração do coletor tem duas propriedades, ResourceId e região. Eles só são necessários para VMs clássicas e serviços de nuvem clássicos.
> Essas propriedades não devem ser usadas para máquinas virtuais do Resource Manager ou conjuntos de dimensionamento de máquinas virtuais.
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
> Há um elemento de configuração particular adicional para o coletor de Azure Monitor, que passa uma ID de entidade de segurança e um segredo. Isso só é necessário para VMs clássicas e serviços de nuvem clássicos. Para VMs do Gerenciador de recursos e VMSS, a definição de Azure Monitor no elemento de configuração particular pode ser excluída.
>


## <a name="reading-this-page"></a>Lendo esta página  
 As marcas a seguir estão aproximadamente na ordem mostrada no exemplo anterior.  Se você não vir uma descrição completa onde espera, pesquise o elemento ou o atributo na página.  

## <a name="common-attribute-types"></a>Tipos de atributo comuns  
 o atributo **scheduledTransferPeriod** aparece em vários elementos. É o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. O valor é um [XML "tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Elemento DiagnosticsConfiguration  
 *Árvore: raiz-DiagnosticsConfiguration*

Adicionado na versão 1,3.  

O elemento de nível superior do arquivo de configuração de diagnóstico.  

**Atributo** xmlns-o namespace XML para o arquivo de configuração de diagnóstico é:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**PublicConfig**|Necessário. Consulte a descrição em outro lugar nesta página.|  
|**PrivateConfig**|Opcional. Consulte a descrição em outro lugar nesta página.|  
|**IsEnabled**|Boolean. Consulte a descrição em outro lugar nesta página.|  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig*

 Descreve a configuração de diagnóstico público.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**WadCfg**|Necessário. Consulte a descrição em outro lugar nesta página.|  
|**StorageAccount**|O nome da conta de armazenamento do Azure na qual armazenar os dados. Também pode ser especificado como um parâmetro ao executar o cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Pode ser *Table*, *blob*ou *TableAndBlob*. A tabela é padrão. Quando TableAndBlob é escolhido, os dados de diagnóstico são gravados duas vezes, uma vez para cada tipo.|  
|**LocalResourceDirectory**|O diretório na máquina virtual onde o agente de monitoramento armazena dados de evento. Se não estiver, defina, o diretório padrão será usado:<br /><br /> Para uma função de trabalho/Web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma máquina virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Os atributos necessários são:<br /><br /> **caminho** de - -o diretório no sistema a ser usado pelo diagnóstico do Azure.<br /><br /> - **expandEnvironment** -controla se as variáveis de ambiente são expandidas no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG*

 Identifica e configura os dados de telemetria a serem coletados.  


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration*

 Obrigatório

|Atributos|Descrição|  
|----------------|-----------------|  
| **overallQuotaInMB** | A quantidade máxima de espaço em disco local que pode ser consumida pelos vários tipos de dados de diagnóstico coletados pelo Diagnóstico do Azure. A configuração padrão é 4096 MB.<br />
|**useProxyServer** | Configure Diagnóstico do Azure para usar as configurações do servidor proxy, conforme definido nas configurações do IE.|
|**coletores** | Adicionado em 1,5. Opcional. Aponta para um local de coletor para também enviar dados de diagnóstico para todos os elementos filho que dão suporte a coletores. O exemplo de coletor é Application Insights ou hubs de eventos.|  


<br /> <br />

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**CrashDumps**|Consulte a descrição em outro lugar nesta página.|  
|**DiagnosticInfrastructureLogs**|Habilite a coleta de logs gerados por Diagnóstico do Azure. Os logs de infraestrutura de diagnóstico são úteis para solucionar problemas do próprio sistema de diagnóstico. Os atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** – configura o nível mínimo de severidade dos logs coletados.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. O valor é um [XML "tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Diretórios**|Consulte a descrição em outro lugar nesta página.|  
|**EtwProviders**|Consulte a descrição em outro lugar nesta página.|  
|**Métricas**|Consulte a descrição em outro lugar nesta página.|  
|**PerformanceCounters**|Consulte a descrição em outro lugar nesta página.|  
|**WindowsEventLog**|Consulte a descrição em outro lugar nesta página.|
|**DockerSources**|Consulte a descrição em outro lugar nesta página. |



## <a name="crashdumps-element"></a>Elemento CrashDumps  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-CrashDumps*

 Habilite a coleção de despejos de memória.  

|Atributos|Descrição|  
|----------------|-----------------|  
|**containerName**|Opcional. O nome do contêiner de BLOB em sua conta de armazenamento do Azure a ser usado para armazenar despejos de memória.|  
|**crashDumpType**|Opcional.  Configura Diagnóstico do Azure para coletar mini ou despejos de memória completos.|  
|**directoryQuotaPercentage**|Opcional.  Configura a porcentagem de **overallQuotaInMB** a ser reservada para despejos de memória na VM.|  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Necessário. Define valores de configuração para cada processo.<br /><br /> O atributo a seguir também é necessário:<br /><br /> **ProcessName** -o nome do processo para o qual você deseja diagnóstico do Azure coletar um despejo de memória.|  

## <a name="directories-element"></a>Elemento de diretórios
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-diretórios*

 Habilita a coleta do conteúdo de um diretório, o IIS falhou em logs de solicitação de acesso e/ou logs do IIS.  

 Atributo **scheduledTransferPeriod** opcional. Consulte a explicação anterior.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**IISLogs**|A inclusão desse elemento na configuração habilita a coleta de logs do IIS:<br /><br /> **ContainerName** -o nome do contêiner de BLOB em sua conta de armazenamento do Azure a ser usado para armazenar os logs do IIS.|   
|**FailedRequestLogs**|A inclusão desse elemento na configuração habilita a coleta de logs sobre solicitações com falha para um site ou aplicativo do IIS. Você também deve habilitar as opções de rastreamento em **sistema. Webserver** em **Web. config**.|  
|**DataSources**|Uma lista de diretórios a serem monitorados.|




## <a name="datasources-element"></a>Elemento dataSources  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-diretórios-fontes de fonte*

 Uma lista de diretórios a serem monitorados.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Necessário. Atributo obrigatório:<br /><br /> **ContainerName** -o nome do contêiner de BLOB em sua conta de armazenamento do Azure que será usado para armazenar os arquivos de log.|  





## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-diretórios-DataSources-DirectoryConfiguration*

 Pode incluir o elemento **Absolute** ou **LocalResource** , mas não ambos.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**Altos**|O caminho absoluto para o diretório a ser monitorado. Os seguintes atributos são necessários:<br /><br /> **caminho** de - -o caminho absoluto para o diretório a ser monitorado.<br /><br /> - **expandEnvironment** – configura se as variáveis de ambiente no caminho são expandidas.|  
|**LocalResource**|O caminho relativo a um recurso local a ser monitorado. Os atributos necessários são:<br /><br /> **nome** do - -o recurso local que contém o diretório a ser monitorado<br /><br /> - **RelativePath** -o caminho relativo ao nome que contém o diretório a ser monitorado|  



## <a name="etwproviders-element"></a>Elemento EtwProviders  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders*

 Configura a coleta de eventos ETW dos provedores baseados em EventSource e/ou de manifesto ETW.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a coleção de eventos gerados da [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo obrigatório:<br /><br /> **provedor** -o nome da classe do evento EventSource.<br /><br /> Os atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de severidade mínimo a ser transferido para sua conta de armazenamento.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. O valor é um [XML "tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Atributo obrigatório:<br /><br /> **provedor** -o GUID do provedor de eventos<br /><br /> Os atributos opcionais são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de severidade mínimo a ser transferido para sua conta de armazenamento.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. O valor é um [XML "tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwEventSourceProviderConfiguration*

 Configura a coleção de eventos gerados da [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br/><br/> **eventDestination** -o nome da tabela na qual armazenar os eventos|  
|**Evento**|Atributo obrigatório:<br /><br /> **ID** -a ID do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela na qual armazenar os eventos|  



## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwManifestProviderConfiguration*

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela na qual armazenar os eventos|  
|**Evento**|Atributo obrigatório:<br /><br /> **ID** -a ID do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela na qual armazenar os eventos|  



## <a name="metrics-element"></a>Elemento de métricas  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Metrics*

 Permite que você gere uma tabela de contador de desempenho otimizada para consultas rápidas. Cada contador de desempenho definido no elemento **PerformanceCounters** é armazenado na tabela de métricas, além da tabela de contador de desempenho.  

 O atributo **ResourceId** é necessário.  A ID de recurso da máquina virtual ou do conjunto de dimensionamento de máquinas virtuais em que você está implantando Diagnóstico do Azure. Obtenha o **ResourceId** do [portal do Azure](https://portal.azure.com). Selecione **procurar** -> **grupos de recursos** -> **nome do <\>** . Clique no bloco **Propriedades** e copie o valor do campo **ID** .  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**MetricAggregation**|Atributo obrigatório:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para o armazenamento arredondado para o minuto mais próximo. O valor é um [XML "tipo de dados de duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-PerformanceCounters*

 Habilita a coleta de contadores de desempenho.  

 Atributo opcional:  

 Atributo **scheduledTransferPeriod** opcional. Consulte a explicação anterior.

|Elemento filho|Descrição|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Os seguintes atributos são necessários:<br /><br /> -  counterer-o nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de contadores de desempenho em seu host, execute o comando `typeperf`.<br /><br /> - **amostrar** -a frequência com que o contador deve ser amostrado.<br /><br /> Atributo opcional:<br /><br /> **unidade** -a unidade de medida do contador.|
|**coletores** | Adicionado em 1,5. Opcional. Aponta para um local de coletor para também enviar dados de diagnóstico. Por exemplo, Azure Monitor ou hubs de eventos.|    




## <a name="windowseventlog-element"></a>Elemento WindowsEventLog
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-WindowsEventLog*

 Habilita a coleta de logs de eventos do Windows.  

 Atributo **scheduledTransferPeriod** opcional. Consulte a explicação anterior.  

|Elemento filho|Descrição|  
|-------------------|-----------------|  
|**DataSource**|Os logs de eventos do Windows a serem coletados. Atributo obrigatório:<br /><br /> **nome** -a consulta XPath que descreve os eventos do Windows a serem coletados. Por exemplo:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Para coletar todos os eventos, especifique "*"|  




## <a name="logs-element"></a>Elemento logs  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-logs*

 Presente na versão 1,0 e 1,1. Ausente em 1,2. Adicionado novamente em 1,3.  

 Define a configuração de buffer para logs básicos do Azure.  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Opcional. Especifica a quantidade máxima de armazenamento do sistema de arquivos que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilter**|**string**|Opcional. Especifica o nível de severidade mínimo para entradas de log que são transferidas. O valor padrão é **indefinido**, o que transfere todos os logs. Outros valores possíveis (em ordem de mais de menos informações) são **detalhados**, **informações**, **aviso**, **erro**e **crítico**.|  
|**scheduledTransferPeriod**|**Duração**|Opcional. Especifica o intervalo entre as transferências agendadas de dados, arredondado para o minuto mais próximo.<br /><br /> O padrão é PT0S.|  
|**coletores** |**string**| Adicionado em 1,5. Opcional. Aponta para um local de coletor para também enviar dados de diagnóstico. Por exemplo, Application Insights ou hubs de eventos.|  

## <a name="dockersources"></a>DockerSources
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-DockerSources*

 Adicionado em 1,9.

|Nome do Elemento|Descrição|  
|------------------|-----------------|  
|**Estatísticas**|Instrui o sistema a coletar estatísticas para contêineres do Docker|  

## <a name="sinksconfig-element"></a>Elemento SinksConfig  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig*

 Uma lista de locais para os quais enviar dados de diagnóstico e a configuração associada a esses locais.  

|Nome do Elemento|Descrição|  
|------------------|-----------------|  
|**sink**|Consulte a descrição em outro lugar nesta página.|  

## <a name="sink-element"></a>Elemento Sink
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink*

 Adicionado na versão 1,5.  

 Define os locais para os quais enviar dados de diagnóstico. Por exemplo, o serviço Application Insights.  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**name**|string|Uma cadeia de caracteres que identifica o sinkname.|  

|Elemento|Tipo|Descrição|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Usado somente ao enviar dados para Application Insights. Conter a chave de instrumentação para uma conta do Active Application Insights à qual você tem acesso.|  
|**Channels** (Canais)|string|Uma para cada filtragem adicional que é transmitida para você|  

## <a name="channels-element"></a>Elemento Channels  
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-Channels*

 Adicionado na versão 1,5.  

 Define filtros para fluxos de dados de log que passam por um coletor.  

|Elemento|Tipo|Descrição|  
|-------------|----------|-----------------|  
|**Canaliza**|string|Consulte a descrição em outro lugar nesta página.|  

## <a name="channel-element"></a>Elemento Channel
 *Árvore: raiz-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-canais-Channel*

 Adicionado na versão 1,5.  

 Define os locais para os quais enviar dados de diagnóstico. Por exemplo, o serviço Application Insights.  

|Atributos|Tipo|Descrição|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Especifica o nível de severidade mínimo para entradas de log que são transferidas. O valor padrão é **indefinido**, o que transfere todos os logs. Outros valores possíveis (em ordem de mais de menos informações) são **detalhados**, **informações**, **aviso**, **erro**e **crítico**.|  
|**name**|**string**|Um nome exclusivo do canal ao qual se refere|  


## <a name="privateconfig-element"></a>Elemento PrivateConfig
 *Árvore: raiz-DiagnosticsConfiguration-PrivateConfig*

 Adicionado na versão 1,3.  

 Opcional  

 Armazena os detalhes particulares da conta de armazenamento (nome, chave e ponto de extremidade). Essas informações são enviadas para a máquina virtual, mas não podem ser recuperadas a partir dela.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**StorageAccount**|A conta de armazenamento a ser usada. Os atributos a seguir são necessários<br /><br /> **nome** do - -o nome da conta de armazenamento.<br /><br /> **chave** de - -a chave para a conta de armazenamento.<br /><br /> **ponto de extremidade** - -o ponto de extremidade para acessar a conta de armazenamento. <br /><br /> -**sasToken** (adicionado 1.8.1)-você pode especificar um token SAS em vez de uma chave de conta de armazenamento na configuração particular. Se fornecido, a chave da conta de armazenamento será ignorada. <br />Requisitos para o token SAS: <br />-Dá suporte somente ao token SAS da conta <br />- *b*, os tipos de serviço *t* são necessários. <br /> - as permissões *a*, *c*, *u*, *w* são necessárias. <br /> os tipos de recurso - *c*e *o* são necessários. <br /> – Dá suporte apenas ao protocolo HTTPS <br /> -A hora de início e de expiração deve ser válida.|  


## <a name="isenabled-element"></a>Elemento IsEnabled  
 *Árvore: raiz-DiagnosticsConfiguration-IsEnabled*

 Boolean. Use `true` para habilitar o diagnóstico ou `false` para desabilitar o diagnóstico.

