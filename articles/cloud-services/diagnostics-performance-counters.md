---
title: Coletar contadores de desempenho nos serviços de nuvem do Azure | Microsoft Docs
description: Saiba como descobrir, usar e criar contadores de desempenho em serviços de nuvem com Diagnóstico do Azure e Application Insights.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 02/02/2018
ms.author: tagore
ms.openlocfilehash: 16b0727a78ad8ad582535fa1f5b0e57079cc4c05
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385591"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Coletar contadores de desempenho para seu serviço de nuvem do Azure

Os contadores de desempenho fornecem uma maneira de controlar o desempenho do aplicativo e do host. O Windows Server fornece vários contadores de desempenho diferentes relacionados a hardware, aplicativos, sistema operacional e muito mais. Ao coletar e enviar contadores de desempenho para o Azure, você pode analisar essas informações para ajudar a tomar decisões melhores. 

## <a name="discover-available-counters"></a>Descobrir contadores disponíveis

Um contador de desempenho é composto de duas partes, um nome de conjunto (também conhecido como categoria) e um ou mais contadores. Você pode usar o PowerShell para obter uma lista de contadores de desempenho disponíveis:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

A propriedade `CounterSetName` representa um conjunto (ou categoria) e é um bom indicador de como os contadores de desempenho estão relacionados. A propriedade `Paths` representa uma coleção de contadores para um conjunto. Você também pode obter a propriedade `Description` para obter mais informações sobre o conjunto de contadores.

Para obter todos os contadores para um conjunto, use o valor `CounterSetName` e expanda a coleção `Paths`. Cada item de caminho é um contador que você pode consultar. Por exemplo, para obter os contadores disponíveis relacionados ao conjunto de `Processor`, expanda a coleção `Paths`:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Esses caminhos de contador individuais podem ser adicionados à estrutura de diagnóstico que seu serviço de nuvem usa. Para obter mais informações sobre como um caminho de contador de desempenho é construído, consulte [especificando um caminho de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Coletar um contador de desempenho

Um contador de desempenho pode ser adicionado ao serviço de nuvem para Diagnóstico do Azure ou Application Insights.

### <a name="application-insights"></a>Estatísticas das Aplicações

Aplicativo Azure insights para serviços de nuvem permite especificar quais contadores de desempenho você deseja coletar. Depois de [adicionar Application insights ao seu projeto](../azure-monitor/app/cloudservices.md#sdk), um arquivo de configuração chamado **ApplicationInsights. config** é adicionado ao seu projeto do Visual Studio. Esse arquivo de configuração define o tipo de informações que Application Insights coleta e envia para o Azure.

Abra o arquivo **ApplicationInsights. config** e localize o elemento **ApplicationInsights** > **TelemetryModules** . Cada elemento filho `<Add>` define um tipo de telemetria para coletar, juntamente com sua configuração. O tipo de módulo de telemetria do contador de desempenho é `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Se esse elemento já estiver definido, não o adicione uma segunda vez. Cada contador de desempenho a ser coletado é definido em um nó chamado `<Counters>`. Aqui está um exemplo que coleta contadores de desempenho da unidade:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Cada contador de desempenho é representado como um elemento de `<Add>` em `<Counters>`. O atributo `PerformanceCounter` define qual contador de desempenho deve ser coletado. O atributo `ReportAs` é o título a ser exibido no portal do Azure para o contador de desempenho. Qualquer contador de desempenho que você coletar é colocado em uma categoria denominada **personalizado** no Portal. Ao contrário de Diagnóstico do Azure, não é possível definir o intervalo que esses contadores de desempenho são coletados e enviados para o Azure. Com Application Insights, os contadores de desempenho são coletados e enviados a cada minuto. 

Application Insights coleta automaticamente os seguintes contadores de desempenho:

* \Process (?? APP_WIN32_PROC??)\% tempo do processador
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Para obter mais informações, consulte [contadores de desempenho do sistema em Application insights](../azure-monitor/app/performance-counters.md) e [Application insights para serviços de nuvem do Azure](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

> [!IMPORTANT]
> Embora todos esses dados sejam agregados na conta de armazenamento, o portal não **fornece uma** maneira nativa de gráfico dos dados. É altamente recomendável que você integre outro serviço de diagnóstico, como Application Insights, em seu aplicativo.

A extensão de Diagnóstico do Azure para serviços de nuvem permite especificar quais contadores de desempenho você deseja coletar. Para configurar Diagnóstico do Azure, consulte [visão geral do monitoramento do serviço de nuvem](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Os contadores de desempenho que você deseja coletar são definidos no arquivo **Diagnostics. wadcfgx** . Abra este arquivo (definido por função) no Visual Studio e localize o elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters** . Adicione um novo elemento **PerformanceCounterConfiguration** como um filho. Esse elemento tem dois atributos: `counterSpecifier` e `sampleRate`. O atributo `counterSpecifier` define qual conjunto de contadores de desempenho do sistema (descrito na seção anterior) a ser coletado. O valor `sampleRate` indica com que frequência esse valor é sondado. Como um todo, todos os contadores de desempenho são transferidos para o Azure de acordo com o valor do atributo de `scheduledTransferPeriod` do elemento de `PerformanceCounters` pai.

Para obter mais informações sobre o elemento de esquema `PerformanceCounters`, consulte o [esquema de diagnóstico do Azure](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element).

O período definido pelo atributo `sampleRate` usa o tipo de dados Duration XML para indicar com que frequência o contador de desempenho é sondado. No exemplo a seguir, a taxa é definida como `PT3M`, o que significa `[P]eriod[T]ime[3][M]inutes`: a cada três minutos.

Para obter mais informações sobre como as `sampleRate` e `scheduledTransferPeriod` são definidas, consulte a seção **tipo de dados Duration** no tutorial de [data e hora XML do W3](https://www.w3schools.com/XML/schema_dtypes_date.asp) .

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Criar um novo contador de desempenho

Um novo contador de desempenho pode ser criado e usado pelo seu código. Seu código que cria um novo contador de desempenho deve ser executado com privilégios elevados, caso contrário, ele falhará. Seu serviço de nuvem `OnStart` código de inicialização pode criar o contador de desempenho, exigindo que você execute a função em um contexto elevado. Ou você pode criar uma tarefa de inicialização que é executada com privilégios elevados e cria o contador de desempenho. Para obter mais informações sobre tarefas de inicialização, consulte [como configurar e executar tarefas de inicialização para um serviço de nuvem](cloud-services-startup-tasks.md).

Para configurar sua função para execução privilegiada, adicione um elemento `<Runtime>` ao arquivo [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) .

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Você pode criar e registrar um novo contador de desempenho com algumas linhas de código. Use a sobrecarga do método `System.Diagnostics.PerformanceCounterCategory.Create` que cria a categoria e o contador. O código a seguir verifica primeiro se a categoria existe e, se ausente, cria a categoria e o contador.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Quando você quiser usar o contador, chame o método `Increment` ou `IncrementBy`.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Agora que seu aplicativo usa seu contador personalizado, você precisa configurar Diagnóstico do Azure ou Application Insights para acompanhar o contador.


### <a name="application-insights"></a>Estatísticas das Aplicações

Conforme indicado anteriormente, os contadores de desempenho para Application Insights são definidos no arquivo **ApplicationInsights. config** . Abra **ApplicationInsights. config** e localize o elemento **ApplicationInsights** > **TelemetryModules** > **Adicionar** > **Counters** . Crie um `<Add>` elemento filho e defina o atributo `PerformanceCounter` como a categoria e o nome do contador de desempenho que você criou em seu código. Defina o atributo `ReportAs` como um nome amigável que você deseja ver no Portal.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

Conforme indicado anteriormente, os contadores de desempenho que você deseja coletar são definidos no arquivo **Diagnostics. wadcfgx** . Abra este arquivo (definido por função) no Visual Studio e localize o elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters** . Adicione um novo elemento **PerformanceCounterConfiguration** como um filho. Defina o atributo `counterSpecifier` como a categoria e o nome do contador de desempenho que você criou em seu código. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Mais informações

- [Application Insights para serviços de nuvem do Azure](../azure-monitor/app/cloudservices.md#performance-counters)
- [Contadores de desempenho do sistema no Application Insights](../azure-monitor/app/performance-counters.md)
- [Especificando um caminho de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Diagnóstico do Azure contadores de desempenho de esquema](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)



