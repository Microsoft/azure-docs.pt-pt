---
title: Esquema de extensão de diagnóstico do Windows
description: Referência de esquema de configuração para extensão de diagnóstico do Windows (WAD) no Monitor Azure.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 4c711e1b0a63fbcf978c0e4467eadaed8d91f3de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274713"
---
# <a name="windows-diagnostics-extension-schema"></a>Esquema de extensão de diagnóstico do Windows
A extensão Azure Diagnostics é um agente no Azure Monitor que recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho dos recursos da computação Azure. Este artigo detalha o esquema utilizado para a configuração da extensão de diagnóstico em máquinas virtuais do Windows e outros recursos computacionais.

> [!NOTE]
> O esquema neste artigo é válido para as versões 1.3 e mais recentes (Azure SDK 2.4 e mais recente). As secções de configuração mais recentes são comentadas para mostrar em que versão foram adicionadas. As versões 1.0 e 1.2 do esquema foram arquivadas e já não estão disponíveis. 

## <a name="public-configuration-file-schema"></a>Esquema de ficheiro de configuração pública

Descarregue a definição de esquema de ficheiro de configuração pública executando o seguinte comando PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Tipos comuns de atributos  
 **atributo agendado TransferPeriod** aparece em vários elementos. É o intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um XML "Tipo de Dados de [Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Elemento de Configuração de Diagnóstico  
 *Árvore: Raiz - Configuração de Diagnóstico*

Adicionado na versão 1.3.  

O elemento de nível superior do ficheiro de configuração de diagnóstico.  

**Atributo** xmlns - O espaço de nome XML para o ficheiro de configuração de diagnóstico é:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**PublicConfig**|Necessário. Consulte a descrição em outro lugar nesta página.|  
|**PrivateConfig**|Opcional. Consulte a descrição em outro lugar nesta página.|  
|**IsEnabled**|Boolean. Consulte a descrição em outro lugar nesta página.|  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 *Árvore: Raiz - DiagnósticosConfiguração - PublicConfig*

 Descreve a configuração de diagnósticos públicos.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**WadCfg**|Necessário. Consulte a descrição em outro lugar nesta página.|  
|**StorageAccount**|O nome da conta De armazenamento Azure para armazenar os dados. Também pode ser especificado como parâmetro na execução do cmdlet set-AzureServiceDiagnosticsExtension.|  
|**Tipo de armazenamento**|Pode ser *Mesa,* *Blob*ou *TableAndBlob*. A mesa está em incumprimento. Quando o TableAndBlob é escolhido, os dados de diagnóstico são escritos duas vezes - uma para cada tipo.|  
|**LocalResourceDirectory**|O diretório na máquina virtual onde o Agente de Monitorização armazena dados de eventos. Caso contrário, configurar, o diretório predefinido é utilizado:<br /><br /> Para um papel de Trabalhador/web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma máquina virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Os atributos necessários são:<br /><br /> - **caminho** - O diretório no sistema a utilizar pela Azure Diagnostics.<br /><br /> - **expandirOAmbiente** - Controla se as variáveis ambientais são expandidas no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG*

 Identifica e confunde os dados da telemetria a recolher.  


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento de configuração de monitorização de diagnóstico
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Necessário

|Atributos|Descrição|  
|----------------|-----------------|  
| **globalQuotaInMB** | A quantidade máxima de espaço de disco local que pode ser consumido pelos vários tipos de dados de diagnóstico recolhidos pela Azure Diagnostics. A definição predefinida é de 4096 MB.<br />
|**usarProxyServer** | Configure o Azure Diagnostics para utilizar as definições do servidor proxy conforme definido nas definições IE.|
|**lava-se** | Adicionado em 1,5. Opcional. Aponta para um local de pia para também enviar dados de diagnóstico para todos os elementos infantis que suportam pias. O exemplo do sink é Application Insights ou Event Hubs.|  


<br /> <br />

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**CrashDumps**|Consulte a descrição em outro lugar nesta página.|  
|**DiagnosticInfrastructureLogs**|Ativar a recolha de registos gerados pela Azure Diagnostics. Os registos de infraestruturas de diagnóstico são úteis para resolver problemas no próprio sistema de diagnóstico. Os atributos opcionais são:<br /><br /> - **programadoTransferLogLevelFilter** - Configura o nível mínimo de gravidade dos registos recolhidos.<br /><br /> - **transferperiod programado** - O intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um XML "Tipo de Dados de [Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Diretórios**|Consulte a descrição em outro lugar nesta página.|  
|**EtwProviders**|Consulte a descrição em outro lugar nesta página.|  
|**Métricas**|Consulte a descrição em outro lugar nesta página.|  
|**Contadores de Desempenho**|Consulte a descrição em outro lugar nesta página.|  
|**WindowsEventLog**|Consulte a descrição em outro lugar nesta página.|
|**Fontes de Docker**|Consulte a descrição em outro lugar nesta página. |



## <a name="crashdumps-element"></a>Elemento crashdumps  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Ative a recolha de lixeiras.  

|Atributos|Descrição|  
|----------------|-----------------|  
|**nome do recipiente**|Opcional. O nome do recipiente de bolhas na sua conta De armazenamento Azure para ser usado para armazenar lixeiras de colisão.|  
|**crashDumpType**|Opcional.  Configures Azure Diagnostics para recolher mini ou lixeiras completas.|  
|**diretórioQuotasPercentage**|Opcional.  Configura a percentagem de **totalDoInMB** a reservar para despejos de colisões no VM.|  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**Configuração crashdump**|Necessário. Define valores de configuração para cada processo.<br /><br /> É também necessário o seguinte atributo:<br /><br /> **processName** - O nome do processo que pretende que a Azure Diagnostics recolha uma lixeira para acidentes.|  

## <a name="directories-element"></a>Elemento de Diretórios
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Diretórios*

 Permite a recolha do conteúdo de um diretório, registos de pedidos de acesso falhados do IIS e registos de IIS.  

 Atributo opcional **agendado TransferPeriod.** Veja a explicação mais cedo.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**IISLogs**|A inclusão deste elemento na configuração permite a recolha de registos IIS:<br /><br /> **nome** do recipiente de bolhas na sua conta De armazenamento Azure a utilizar para armazenar os registos IIS.|   
|**Registos de Pedidos falhados**|Incluir este elemento na configuração permite a recolha de registos sobre pedidos falhados a um site ou aplicação IIS. Também deve ativar as opções de rastreio no **sistema. WebServer** em **Web.config**.|  
|**DataSources**|Uma lista de diretórios para monitorizar.|




## <a name="datasources-element"></a>Elemento DataSources  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Diretórios - DataSources*

 Uma lista de diretórios para monitorizar.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**Configuração de Diretório**|Necessário. Atributo obrigatório:<br /><br /> **nome** do recipiente de bolhas na sua conta De armazenamento Azure que deve ser utilizado para armazenar os ficheiros de registo.|  





## <a name="directoryconfiguration-element"></a>Elemento de Configuração de Diretório  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Diretórios - DataSources - Diretórios*

 Pode incluir o elemento **Absolute** ou **LocalResource,** mas não ambos.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**Absoluto**|O caminho absoluto para o diretório para monitorizar. São necessários os seguintes atributos:<br /><br /> - **Caminho** - O caminho absoluto para o diretório para monitorizar.<br /><br /> - **expandirO Ambiente** - Configura se as variáveis ambientais no Caminho são expandidas.|  
|**Recursos Locais**|O caminho relativo a um recurso local para monitorizar. Os atributos necessários são:<br /><br /> - **Nome** - O recurso local que contém o diretório para monitorizar<br /><br /> - **relativoCaminho** - O caminho relativo ao Nome que contém o diretório para monitorizar|  



## <a name="etwproviders-element"></a>Elemento EtwProviders  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Recolha de configures de eventos ETW de fornecedores baseados em EventSource e/ou ETW Manifest.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguração**|Coleção de eventos gerados a partir da [Classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo obrigatório:<br /><br /> **fornecedor** - O nome de classe do evento EventSource.<br /><br /> Os atributos opcionais são:<br /><br /> - **programadoTransferLogLevelFilter** - O nível mínimo de gravidade para transferir para a sua conta de armazenamento.<br /><br /> - **transferperiod programado** - O intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um XML "Tipo de Dados de [Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Configuração EtwManifestProvider**|Atributo obrigatório:<br /><br /> **fornecedor** - O GUID do prestador de eventos<br /><br /> Os atributos opcionais são:<br /><br /> - **programadoTransferLogLevelFilter** - O nível mínimo de gravidade para transferir para a sua conta de armazenamento.<br /><br /> - **transferperiod programado** - O intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um XML "Tipo de Dados de [Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>Elemento de Configuração EtwEventSourceProvider  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Coleção de eventos gerados a partir da [Classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**Eventos Padrão**|Atributo opcional:<br/><br/> **eventoDestino** - O nome da tabela para armazenar os eventos em|  
|**Evento**|Atributo obrigatório:<br /><br /> **id** - A identificação do evento.<br /><br /> Atributo opcional:<br /><br /> **eventoDestino** - O nome da tabela para armazenar os eventos em|  



## <a name="etwmanifestproviderconfiguration-element"></a>Elemento de Configuração EtwManifestProvider  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**Eventos Padrão**|Atributo opcional:<br /><br /> **eventoDestino** - O nome da tabela para armazenar os eventos em|  
|**Evento**|Atributo obrigatório:<br /><br /> **id** - A identificação do evento.<br /><br /> Atributo opcional:<br /><br /> **eventoDestino** - O nome da tabela para armazenar os eventos em|  



## <a name="metrics-element"></a>Elemento de Métricas  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Métricas*

 Permite-lhe gerar uma contramesa de desempenho otimizada para consultas rápidas. Cada contador de desempenho definido no elemento **PerformanceCounters** é armazenado na tabela Métricas para além da tabela Performance Counter.  

 O atributo de **recurso Id** é necessário.  O ID de recurso da Máquina Virtual ou conjunto de escala de máquina virtual para o conjunto para o que está a implementar o Azure Diagnostics. Obtenha o ID de **recursos** do [portal Azure.](https://portal.azure.com) **Selecione Browse** -> **Grupos** de Recursos ->  **<Nome\>** . Clique no azulejo **Properties** e copie o valor do campo **ID.**  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**Agregação Métrica**|Atributo obrigatório:<br /><br /> **TransferPeriod agendado** - O intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um XML "Tipo de Dados de [Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Elemento de Contrapontos de Desempenho  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Permite a recolha de contadores de desempenho.  

 Atributo opcional:  

 Atributo opcional **agendado TransferPeriod.** Veja a explicação mais cedo.

|Elemento Infantil|Descrição|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|São necessários os seguintes atributos:<br /><br /> **- contraespecificador** - O nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de contadores de desempenho no seu anfitrião, faça o comando `typeperf`.<br /><br /> - **amostraGem** - Quantas vezes o contador deve ser amostrado.<br /><br /> Atributo opcional:<br /><br /> **unidade** - A unidade de medida do balcão. Os valores estão disponíveis na [Classe UnitType](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet) |
|**lava-se** | Adicionado em 1,5. Opcional. Aponta para um local de pia para também enviar dados de diagnóstico. Por exemplo, Monitor Azure ou Centros de Eventos.|    




## <a name="windowseventlog-element"></a>Elemento WindowsEventLog
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Permite a recolha de Registos de Eventos windows.  

 Atributo opcional **agendado TransferPeriod.** Veja a explicação mais cedo.  

|Elemento Infantil|Descrição|  
|-------------------|-----------------|  
|**DataSource**|Os registos do Windows Event para recolher. Atributo obrigatório:<br /><br /> **nome** - A consulta XPath descrevendo os eventos das janelas a recolher. Por exemplo:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Para recolher todos os eventos, especifique "*"|  




## <a name="logs-element"></a>Elemento de logs  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 Presente nos versões 1.0 e 1.1. Desaparecido em 1.2. Adicionado de volta em 1.3.  

 Define a configuração do tampão para os registos básicos do Azure.  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**não assinadoInt**|Opcional. Especifica a quantidade máxima de armazenamento do sistema de ficheiros disponível para os dados especificados.<br /><br /> O padrão é 0.|  
|**logilogLogLevelFilter agendado**|**cadeia**|Opcional. Especifica o nível mínimo de gravidade para as entradas de registo que são transferidas. O valor predefinido é **Indefinido,** que transfere todos os registos. Outros valores possíveis (por ordem da maior parte das informações) são **Verbose,** **Informação,** **Aviso,** **Erro**e **Crítico.**|  
|**período de transferência programado**|**duração**|Opcional. Especifica o intervalo entre transferências programadas de dados, arredondadas até ao minuto mais próximo.<br /><br /> O padrão é PT0S.|  
|**lava-se** |**cadeia**| Adicionado em 1,5. Opcional. Aponta para um local de pia para também enviar dados de diagnóstico. Por exemplo, Insights de Aplicação ou Centros de Eventos.|  

## <a name="dockersources"></a>DockerSources
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Adicionado em 1.9.

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Estatísticas**|Diz ao sistema para recolher estatísticas para contentores do Docker|  

## <a name="sinksconfig-element"></a>Elemento SinksConfig  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Uma lista de locais para enviar dados de diagnóstico e a configuração associada a esses locais.  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Pia**|Consulte a descrição em outro lugar nesta página.|  

## <a name="sink-element"></a>Elemento de pia
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - SinksConfig - Sink*

 Adicionado na versão 1.5.  

 Define locais para enviar dados de diagnóstico para. Por exemplo, o serviço Application Insights.  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**nome**|string|Uma corda identificando o nome da pia.|  

|Elemento|Tipo|Descrição|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Utilizado apenas no envio de dados para Insights de Aplicação. Contenha a Chave de Instrumentação para uma conta de Insights de Aplicação ativa a que tem acesso.|  
|**Canais**|string|Um para cada filtragem adicional que fluxo que você|  

## <a name="channels-element"></a>Elemento canais  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Sink - Canais*

 Adicionado na versão 1.5.  

 Define filtros para fluxos de dados de registo que passam por um lavatório.  

|Elemento|Tipo|Descrição|  
|-------------|----------|-----------------|  
|**Canal**|string|Consulte a descrição em outro lugar nesta página.|  

## <a name="channel-element"></a>Elemento do Canal
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Canais - Canal*

 Adicionado na versão 1.5.  

 Define locais para enviar dados de diagnóstico para. Por exemplo, o serviço Application Insights.  

|Atributos|Tipo|Descrição|  
|----------------|----------|-----------------|  
|**logLevel**|**cadeia**|Especifica o nível mínimo de gravidade para as entradas de registo que são transferidas. O valor predefinido é **Indefinido,** que transfere todos os registos. Outros valores possíveis (por ordem da maior parte das informações) são **Verbose,** **Informação,** **Aviso,** **Erro**e **Crítico.**|  
|**nome**|**cadeia**|Um nome único do canal para se referir|  


## <a name="privateconfig-element"></a>Elemento PrivateConfig
 *Árvore: Raiz - DiagnósticosConfiguração - PrivateConfig*

 Adicionado na versão 1.3.  

 Opcional  

 Armazene os detalhes privados da conta de armazenamento (nome, chave e ponto final). Esta informação é enviada para a máquina virtual, mas não pode ser recuperada dela.  

|Elementos Subordinados|Descrição|  
|--------------------|-----------------|  
|**StorageAccount**|A conta de armazenamento a usar. São necessários os seguintes atributos<br /><br /> - **nome** - O nome da conta de armazenamento.<br /><br /> - **chave** - A chave da conta de armazenamento.<br /><br /> - **ponto final** - O ponto final para aceder à conta de armazenamento. <br /><br /> -**sasToken** (adicionado 1.8.1)- Pode especificar um token SAS em vez de uma chave de conta de armazenamento no config privado. Se fornecido, a chave da conta de armazenamento é ignorada. <br />Requisitos para o Token SAS: <br />- Suporta apenas a conta SAS token <br />- *b*, são necessários tipos de serviço *t.* <br /> - a, *c,* *u,* *são* necessárias permissões. <br /> - *c*, *são* necessários tipos de recursos. <br /> - Suporta apenas o protocolo HTTPS <br /> - O tempo de início e de validade deve ser válido.|  


## <a name="isenabled-element"></a>Elemento Isenabled  
 *Árvore: Raiz - Configuração de Diagnósticos - IsEnabled*

 Boolean. Utilize `true` para permitir que os diagnósticos ou `false` desativem os diagnósticos.

## <a name="example-configuration"></a>Configuração de exemplo
 Segue-se uma configuração completa da amostra para a extensão de diagnóstico do Windows mostrada tanto na JSON como na XML.

 
### <a name="json"></a>JSON

O *PublicConfig* e o *PrivateConfig* são separados porque na maioria dos casos de utilização da JSON, são passados como variáveis diferentes. Estes casos incluem modelos de Gestor de Recursos, PowerShell e Estúdio Visual.

> [!NOTE]
> A definição de pia do Monitor De figo Público Azure tem duas propriedades, *recursos Id* e *região.* Estes são necessários apenas para VMs clássicos e serviços de Nuvem Clássica. Estas propriedades não devem ser utilizadas para outros recursos.

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
> A definição privada de sumidouro do Monitor De figo Azure tem duas propriedades, *Principado e* *Secreto.* Estes são necessários apenas para VMs clássicos e serviços de Nuvem Clássica. Estas propriedades não devem ser utilizadas para outros recursos.


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

### <a name="xml"></a>XML

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
> A definição de pia do Monitor Config Azure público tem duas propriedades, recursos Id e região. Estes são necessários apenas para VMs clássicos e serviços de Nuvem Clássica. Estas propriedades não devem ser utilizadas para máquinas virtuais do Gestor de Recursos ou conjuntos de escala de máquina virtual.
> Há também um elemento Particular Config adicional para o lavatório Azure Monitor, que passa em um Id principal e segredo. Isto só é necessário para VMs clássicos e serviços clássicos de nuvem. Para VMs e VMSS do Gestor de Recursos, a definição do Monitor Azure no elemento config privado pode ser excluída.
>
