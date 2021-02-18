---
title: Esquema de extensão de diagnóstico do Windows
description: Referência de esquema de configuração para extensão de diagnóstico do Windows (WAD) no Azure Monitor.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: eccd4010d796e541e4a0a2c0b0c485b5f18f0366
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618171"
---
# <a name="windows-diagnostics-extension-schema"></a>Esquema de extensão de diagnóstico do Windows
A extensão Azure Diagnostics é um agente no Azure Monitor que recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho dos recursos de computação Azure. Este artigo detalha o esquema utilizado para a configuração da extensão de diagnóstico em máquinas virtuais do Windows e outros recursos computacional.

> [!NOTE]
> O esquema deste artigo é válido para as versões 1.3 e mais recentes (Azure SDK 2.4 e mais recente). As secções de configuração mais recentes são comentadas para mostrar em que versão foram adicionadas. As versões 1.0 e 1.2 do esquema foram arquivadas e já não estão disponíveis. 

## <a name="public-configuration-file-schema"></a>Esquema de ficheiro de configuração pública

Descarregue a definição de esquema de ficheiro de configuração pública executando o seguinte comando PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Tipos de atributos comuns  
 **atributo programadoTransferPeriod** aparece em vários elementos. É o intervalo entre as transferências programadas para o armazenamento arredondada até ao minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Elemento deConfiguação de Diagnósticos  
 *Árvore: Raiz - Confirmação de Diagnósticos*

Adicionado na versão 1.3.  

O elemento de nível superior do ficheiro de configuração de diagnóstico.  

**Atributo**  xmlns - O espaço de nome XML para o ficheiro de configuração de diagnósticos é:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**Config público**|Obrigatório. Consulte a descrição em outro lugar nesta página.|  
|**Config Privado**|Opcional. Consulte a descrição em outro lugar nesta página.|  
|**IsEnabled**|O Boolean. Consulte a descrição em outro lugar nesta página.|  

## <a name="publicconfig-element"></a>ElementoConfig Público  
 *Árvore: Raiz - DiagnósticoConfiguration - PublicConfig*

 Descreve a configuração de diagnóstico público.  

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**WadCfg**|Obrigatório. Consulte a descrição em outro lugar nesta página.|  
|**StorageAccount**|O nome da conta de Armazenamento Azure para armazenar os dados dentro Também pode ser especificado como um parâmetro ao executar o Set-AzureServiceDiagnosticsExtension cmdlet.|  
|**Dispositivo de armazenamento**|Pode ser *Mesa,* *Blob,* ou *TableAndBlob*. A tabela é padrão. Quando o TableAndBlob é escolhido, os dados de diagnóstico são escritos duas vezes - uma para cada tipo.|  
|**LocalResourceDirectory**|O diretório na máquina virtual onde o Agente de Monitorização armazena dados de eventos. Caso contrário, definido, o diretório predefinido é utilizado:<br /><br /> Para um papel trabalhador/web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma máquina virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Os atributos necessários são:<br /><br /> - **caminho** - O diretório no sistema a ser utilizado pela Azure Diagnostics.<br /><br /> - **expandir O ambiente** - Controla se as variáveis ambientais são expandidas no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
 *Árvore: Raiz - DiagnósticoConfiguration - PublicConfig - WadCFG*

 Identifica e configura os dados de telemetria a recolher.  


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento deConfiguação do DiagnósticoMonitor
 *Árvore: Raiz - Confirmação de Diagnóstico - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Necessário

|Atributos|Description|  
|----------------|-----------------|  
| **globalQuotaInMB** | A quantidade máxima de espaço em disco local que pode ser consumida pelos vários tipos de dados de diagnóstico recolhidos pela Azure Diagnostics. A definição predefinida é de 4096 MB.<br />
|**useProxyServer** | Configure Azure Diagnostics para utilizar as definições do servidor proxy como definido nas definições de IE.|
|**pias** | Adicionado em 1,5. Opcional. Aponta para um local de pia para também enviar dados de diagnóstico para todos os elementos infantis que suportam pias. O exemplo da pia é Application Insights ou Event Hubs. Note que precisa adicionar a propriedade *resourceId* sob o elemento *Metrics* se quiser que os eventos enviados para Os Centros de Eventos tenham um ID de recursos. |  


<br /> <br />

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**CrashDumps**|Consulte a descrição em outro lugar nesta página.|  
|**DiagnósticoInfrastructureLogs**|Ativar a recolha de registos gerados pela Azure Diagnostics. Os registos de infraestrutura de diagnóstico são úteis para resolver problemas no próprio sistema de diagnóstico. Os atributos opcionais são:<br /><br /> - **ProgramadoTransferLogLevelFilter** - Configura o nível mínimo de gravidade dos registos recolhidos.<br /><br /> - **ProgramadoTransferPeriod** - O intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Diretórios**|Consulte a descrição em outro lugar nesta página.|  
|**EtwProviders**|Consulte a descrição em outro lugar nesta página.|  
|**Métricas**|Consulte a descrição em outro lugar nesta página.|  
|**PerformanceCounters**|Consulte a descrição em outro lugar nesta página.|  
|**WindowsEventLog**|Consulte a descrição em outro lugar nesta página.|
|**DockerSources**|Consulte a descrição em outro lugar nesta página. |



## <a name="crashdumps-element"></a>Elemento CrashDumps  
 *Árvore: Raiz - Confirmação de Diagnóstico - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Habilitar a recolha de depósitos de colisão.  

|Atributos|Description|  
|----------------|-----------------|  
|**nome de contentor**|Opcional. O nome do recipiente blob na sua conta de armazenamento Azure para ser usado para armazenar depósitos de acidentes.|  
|**crashDumpType**|Opcional.  Configura a Azure Diagnostics para recolher mini ou lixeiras completas.|  
|**directyQuotaPercentage**|Opcional.  Configura a percentagem de **GlobalQuotaInMB** a ser reservada para despejos de colisão no VM.|  

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Obrigatório. Define valores de configuração para cada processo.<br /><br /> É igualmente necessário o seguinte atributo:<br /><br /> **nome de processo** - O nome do processo para o quais pretende que o Azure Diagnostics recolha uma lixeira de colisão.|  

## <a name="directories-element"></a>Elemento de diretórios
 *Árvore: Raiz - DiagnósticosConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Diretórios*

 Permite a recolha do conteúdo de um diretório, registos de pedidos de acesso falhados do IIS e/ou registos IIS.  

 Atributo **opcional Deperiod programado.** Veja a explicação mais cedo.  

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**IISLogs**|A inclusão deste elemento na configuração permite a recolha de registos IIS:<br /><br /> **containerName** - O nome do recipiente blob na sua conta de armazenamento Azure para ser utilizado para armazenar os registos IIS.|   
|**RequestLogs falhados**|A inclusão deste elemento na configuração permite a recolha de registos sobre pedidos falhados a um site ou aplicação do IIS. Também deve ativar opções de rastreio no **sistema. WebServer** em **Web.config**.|  
|**Fontes de Dados**|Uma lista de diretórios para monitorizar.|




## <a name="datasources-element"></a>Elemento DataSources  
 *Árvore: Raiz - Confirmação de Diagnósticos - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Diretórios - DataSources*

 Uma lista de diretórios para monitorizar.  

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**Configuação do Diretório**|Obrigatório. Atributo requerido:<br /><br /> **containerName** - O nome do recipiente blob na sua conta de Armazenamento Azure que deve ser utilizado para armazenar os ficheiros de registo.|  





## <a name="directoryconfiguration-element"></a>Elemento deConfiguação do Diretório  
 *Árvore: Raiz - Confirmação de Diagnóstico - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Diretórios - DataSources - Diretório*

 Pode incluir o elemento **Absolute** ou **LocalResource,** mas não ambos.  

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**Absoluto**|O caminho absoluto para o diretório para monitorizar. São necessários os seguintes atributos:<br /><br /> - **Caminho** - O caminho absoluto para o diretório para monitorizar.<br /><br /> - **expandEnvironment** - Configura se as variáveis ambientais em Path são expandidas.|  
|**Fonte local**|O caminho relativo a um recurso local para monitorizar. Os atributos necessários são:<br /><br /> - **Nome** - O recurso local que contém o diretório para monitorizar<br /><br /> - **relativaPath** - O caminho relativo ao Nome que contém o diretório para monitorizar|  



## <a name="etwproviders-element"></a>Elemento EtwProviders  
 *Árvore: Raiz - Confirmação de Diagnósticos - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Configura a recolha de eventos ETW de fornecedores baseados em EventSource e/ou ETW Manifest.  

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a recolha de eventos gerados a partir da [Classe EventSource.](/dotnet/api/system.diagnostics.tracing.eventsource) Atributo requerido:<br /><br /> **fornecedor** - O nome de classe do evento EventSource.<br /><br /> Os atributos opcionais são:<br /><br /> - **ProgramadoTransferLogLevelFilter** - O nível mínimo de gravidade para transferir para a sua conta de armazenamento.<br /><br /> - **ProgramadoTransferPeriod** - O intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Atributo requerido:<br /><br /> **fornecedor** - O GUIA do fornecedor de eventos<br /><br /> Os atributos opcionais são:<br /><br /> - **ProgramadoTransferLogLevelFilter** - O nível mínimo de gravidade para transferir para a sua conta de armazenamento.<br /><br /> - **ProgramadoTransferPeriod** - O intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Árvore: Raiz -Configuration de Diagnóstico - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Configura a recolha de eventos gerados a partir da [Classe EventSource.](/dotnet/api/system.diagnostics.tracing.eventsource)  

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br/><br/> **eventDestination** - O nome da mesa para armazenar os eventos em|  
|**Evento**|Atributo requerido:<br /><br /> **Id** - O id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** - O nome da mesa para armazenar os eventos em|  



## <a name="etwmanifestproviderconfiguration-element"></a>Elemento deConfiguação EtwManifestProvider  
 *Árvore: Raiz - Confirmação de Diagnóstico - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** - O nome da mesa para armazenar os eventos em|  
|**Evento**|Atributo requerido:<br /><br /> **Id** - O id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** - O nome da mesa para armazenar os eventos em|  



## <a name="metrics-element"></a>Elemento métricas  
 *Árvore: Raiz - DiagnósticoConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Métricas*

 Permite-lhe gerar uma tabela de contadores de desempenho que é otimizada para consultas rápidas. Cada contador de desempenho definido no elemento **PerformanceCounters** é armazenado na tabela Métricas, além da tabela 'Contador de Desempenho'.  

 O atributo **resourceId** é necessário.  O ID de recursos da Máquina Virtual ou do Conjunto de Escala de Máquina Virtual para o que está a implementar Azure Diagnostics. Obtenha os **recursosID** do [portal Azure.](https://portal.azure.com) **Selecione**  ->  **Grupos de Recursos de**  ->  **Navegação<Nome \>**. Clique no azulejo **propriedades** e copie o valor do campo **ID.**  Esta propriedade de recursosID é usada tanto para o envio de métricas personalizadas como para adicionar uma propriedade de RECURSOSID aos dados enviados para Os Centros de Eventos. Note que precisa adicionar a propriedade *resourceId* sob o elemento *Metrics* se quiser que os eventos enviados para Os Centros de Eventos tenham um ID de recursos.

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**MétricaAggregação**|Atributo requerido:<br /><br /> **ProgramadoTransferPeriod** - O intervalo entre transferências programadas para armazenamento arredondado até ao minuto mais próximo. O valor é um [XML "Tipo de Dados de Duração".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 *Árvore: Raiz - DiagnósticosConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Permite a recolha de contadores de desempenho.  

 Atributo opcional:  

 Atributo **opcional Deperiod programado.** Veja a explicação mais cedo.

|Elemento infantil|Description|  
|-------------------|-----------------|  
|**Realização de DesempenhoConiguration**|São necessários os seguintes atributos:<br /><br /> - **contraSpecificador** - O nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de contadores de desempenho no seu anfitrião, faça o comando `typeperf` .<br /><br /> - **sampleRate** - Com que frequência o contador deve ser amostrado.<br /><br /> Atributo opcional:<br /><br /> **unidade** - A unidade de medida do balcão. Os valores estão disponíveis na [Classe UnitType](/dotnet/api/microsoft.azure.management.sql.models.unittype) |
|**pias** | Adicionado em 1,5. Opcional. Aponta para um local de pia para também enviar dados de diagnóstico. Por exemplo, Azure Monitor ou Centros de Eventos. Note que precisa adicionar a propriedade *resourceId* sob o elemento *Metrics* se quiser que os eventos enviados para Os Centros de Eventos tenham um ID de recursos.|    




## <a name="windowseventlog-element"></a>Elemento WindowsEventLog
 *Árvore: Raiz - Confirmação de Diagnóstico - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Permite a recolha de Registos de Eventos do Windows.  

 Atributo **opcional Deperiod programado.** Veja a explicação mais cedo.  

|Elemento infantil|Description|  
|-------------------|-----------------|  
|**Fonte de Dados**|Os registos do Evento Windows para recolher. Atributo requerido:<br /><br /> **nome** - A consulta XPath que descreve os eventos das janelas a recolher. Por exemplo:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Para recolher todos os eventos, especifique "*" |
|**pias** | Adicionado em 1,5. Opcional. Aponta para um local de pia para também enviar dados de diagnóstico para todos os elementos infantis que suportam pias. O exemplo da pia é Application Insights ou Event Hubs.|  


## <a name="logs-element"></a>Elemento de registos  
 *Árvore: Raiz - Configuração de Diagnóstico - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 Presente na versão 1.0 e 1.1. Desaparecido em 1.2. Adicionado em 1.3.  

 Define a configuração do tampão para registos Azure básicos.  

|Atributo|Tipo|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**Não assinadoInt**|Opcional. Especifica a quantidade máxima de armazenamento do sistema de ficheiros disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**ProgramadoTransferLogLevelFilter**|**string**|Opcional. Especifica o nível mínimo de gravidade para as entradas de registo que são transferidas. O valor **predefinido é Indefinido,** que transfere todos os registos. Outros valores possíveis (por ordem da maioria das informações menores) são **Verbose,** **Informação,** **Aviso,** **Erro** e **Crítico**.|  
|**programadoTransferPeriod**|**duração**|Opcional. Especifica o intervalo entre transferências programadas de dados, arredondadas até ao minuto mais próximo.<br /><br /> O padrão é PT0S.|  
|**pias** |**string**| Adicionado em 1,5. Opcional. Aponta para um local de pia para também enviar dados de diagnóstico. Por exemplo, Application Insights ou Event Hubs. Note que precisa adicionar a propriedade *resourceId* sob o elemento *Metrics* se quiser que os eventos enviados para Os Centros de Eventos tenham um ID de recursos.|  

## <a name="dockersources"></a>DockerSources
 *Árvore: Raiz - Confirmação de Diagnóstico - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Adicionado em 1.9.

|Nome do Elemento|Description|  
|------------------|-----------------|  
|**Estatísticas**|Diz ao sistema para recolher estatísticas para os contentores do Docker|  

## <a name="sinksconfig-element"></a>ElementoConfig de Sinks  
 *Árvore: Raiz - DiagnósticoConfiguration - PublicConfig - WadCFG - SinksConfig*

 Uma lista de locais para enviar dados de diagnóstico e a configuração associada a esses locais.  

|Nome do Elemento|Description|  
|------------------|-----------------|  
|**Sink**|Consulte a descrição em outro lugar nesta página.|  

## <a name="sink-element"></a>Elemento de pia
 *Árvore: Raiz - DiagnósticoConfiguration - PublicConfig - WadCFG - SinksConfig - SinksConfig - Sink*

 Adicionado na versão 1.5.  

 Define localizações para enviar dados de diagnóstico para. Por exemplo, o serviço Application Insights.  

|Atributo|Tipo|Description|  
|---------------|----------|-----------------|  
|**nome**|string|Uma corda que identifica o nome da pia.|  

|Elemento|Tipo|Description|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Utilizado apenas no envio de dados para Insights de Aplicação. Contiver a Chave de Instrumentação para uma conta de Insights de Aplicação ativa a que tenha acesso.|  
|**Channels** (Canais)|string|Um para cada filtragem adicional que o fluxo que você|  

## <a name="channels-element"></a>Elemento canais  
 *Árvore: Raiz - DiagnósticosConfiguration - PublicConfig - WadCFG - SinksConfig - SinksConfig - Sink - Canais*

 Adicionado na versão 1.5.  

 Define filtros para fluxos de dados de registo que passam por uma pia.  

|Elemento|Tipo|Description|  
|-------------|----------|-----------------|  
|**Canal**|string|Consulte a descrição em outro lugar nesta página.|  

## <a name="channel-element"></a>Elemento do Canal
 *Árvore: Raiz - DiagnósticoConfiguration - PublicConfig - WadCFG - SinksConfig - SinksConfig - Sink - Canais - Canal*

 Adicionado na versão 1.5.  

 Define localizações para enviar dados de diagnóstico para. Por exemplo, o serviço Application Insights.  

|Atributos|Tipo|Description|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Especifica o nível mínimo de gravidade para as entradas de registo que são transferidas. O valor **predefinido é Indefinido,** que transfere todos os registos. Outros valores possíveis (por ordem da maioria das informações menores) são **Verbose,** **Informação,** **Aviso,** **Erro** e **Crítico**.|  
|**nome**|**string**|Um nome único do canal a que se refere|  


## <a name="privateconfig-element"></a>ElementoConfig Privado
 *Árvore: Raiz - Confirmação de Diagnóstico -Config Privado*

 Adicionado na versão 1.3.  

 Opcional  

 Armazena os detalhes privados da conta de armazenamento (nome, chave e ponto final). Esta informação é enviada para a máquina virtual, mas não pode ser recuperada daí.  

|Elementos Subordinados|Description|  
|--------------------|-----------------|  
|**StorageAccount**|A conta de armazenamento a utilizar. São necessários os seguintes atributos<br /><br /> - **nome** - O nome da conta de armazenamento.<br /><br /> - **chave** - A chave da conta de armazenamento.<br /><br /> - **ponto final** - O ponto final para aceder à conta de armazenamento. <br /><br /> -**sasToken** (adicionado 1.8.1)- Pode especificar um token SAS em vez de uma chave de conta de armazenamento no config privado. Se for fornecida, a chave da conta de armazenamento é ignorada. <br />Requisitos para o Token SAS: <br />- Suporta apenas o token sas da conta SAS <br />- *b* são necessários tipos de serviço *t.* <br /> - *a*, *c,* *u,* *w* permissões são necessárias. <br /> - *c*, *o* tipos de recursos são necessários. <br /> - Suporta apenas o protocolo HTTPS <br /> - O tempo de início e validade deve ser válido.|  


## <a name="isenabled-element"></a>Elemento IsEnabled  
 *Árvore: Raiz - Configuração de Diagnóstico - IsEnabled*

 O Boolean. Utilize `true` para ativar os diagnósticos ou `false` para desativar os diagnósticos.

## <a name="example-configuration"></a>Configuração de exemplo
 Segue-se uma configuração completa da amostra para a extensão de diagnóstico do Windows mostrada tanto no JSON como no XML.

 
### <a name="json"></a>JSON

O *PublicConfig* e *o PrivateConfig* são separados porque na maioria dos casos de utilização do JSON, são passados como variáveis diferentes. Estes casos incluem modelos de Gestor de Recursos, PowerShell e Visual Studio.

> [!NOTE]
> A definição de pia do Azure Monitor tem duas propriedades, *recursosId* e *região.* Estes são apenas necessários para vMs clássicos e serviços Classic Cloud. A propriedade *da região* não deve ser usada para outros recursos, a propriedade *de recursosId* é usada em VMs ARM para povoar o campo de RECURSOSID em registos enviados para Centros de Eventos.

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
> A definição privada de pia config Azure Monitor tem duas propriedades, *PrincipalId* e *Secret.* Estes são apenas necessários para vMs clássicos e serviços Classic Cloud. Estas propriedades não devem ser utilizadas para outros recursos.


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
> A definição de pia do Azure Monitor do público tem duas propriedades, recursosId e região. Estes são apenas necessários para vMs clássicos e serviços Classic Cloud. Estas propriedades não devem ser utilizadas para máquinas virtuais do Gestor de Recursos ou conjuntos de escala de máquina virtual.
> Há também um elemento config privado adicional para a pia do Monitor Azure, que passa em um ID principal e segredo. Isto só é necessário para VMs Clássicos e Serviços De Nuvem Clássica. Para o Gestor de Recursos VMs e VMSS, a definição do Monitor Azure no elemento configurar privado pode ser excluída.
>
