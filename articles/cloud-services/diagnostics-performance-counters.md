---
title: Recolher contadores de desempenho nos serviços Cloud do Azure | Documentos da Microsoft
description: Saiba como detetar, utilizar e criar contadores de desempenho nos serviços de Cloud com o diagnóstico do Azure e o Application Insights.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: jeconnoc
ms.openlocfilehash: 7713b449d5e5291ce1dd6c9b814ebefd07bc53a9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737676"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Recolher contadores de desempenho para o seu serviço Cloud do Azure

Contadores de desempenho proporcionam uma forma para controlar a eficiência com que seu aplicativo e o anfitrião estão a efetuar. Windows Server fornece muitos diferentes contadores de desempenho relacionados com o hardware, aplicativos, o sistema operativo e muito mais. Ao recolher e enviar os contadores de desempenho para o Azure, pode analisar estas informações para ajudar a tomar melhores decisões. 

## <a name="discover-available-counters"></a>Detetar contadores disponíveis

Um contador de desempenho é constituído por duas partes: um nome de conjunto (também conhecido como uma categoria) e um ou mais contadores. Pode utilizar o PowerShell para obter uma lista de contadores de desempenho disponíveis:

```PowerShell
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

O `CounterSetName` propriedade representa um conjunto (ou uma categoria) e é um bom indicador de que os contadores de desempenho relacionados com. O `Paths` propriedade representa uma coleção de contadores para um conjunto. Também pode obter o `Description` propriedade para obter mais informações sobre o conjunto de contadores.

Para obter todos os contadores para um conjunto, utilize o `CounterSetName` valor e expanda o `Paths` coleção. Cada item do caminho é um contador, pode consultar. Por exemplo, para obter os contadores disponíveis relacionados com o `Processor` definido, expanda o `Paths` coleção:

```PowerShell
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

Esses caminhos podem ser adicionados para a estrutura de diagnóstico de contador individuais utiliza o seu serviço cloud. Para obter mais informações sobre como um caminho do contador de desempenho é construído, consulte [especificando um caminho de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Recolher um contador de desempenho

Um contador de desempenho pode ser adicionado ao seu serviço cloud para o diagnóstico do Azure ou para o Application Insights.

### <a name="application-insights"></a>Application Insights

O Azure Application Insights para serviços em nuvem permite que especifique quais contadores de desempenho que pretende recolher. Depois de [adicionar o Application Insights ao seu projeto](../azure-monitor/app/cloudservices.md#sdk), um arquivo de configuração chamado **applicationinsights. config** é adicionado ao seu projeto do Visual Studio. Este ficheiro de configuração define o tipo de informações Application Insights recolhe e envia para o Azure.

Abra o **applicationinsights. config** de ficheiros e encontre a **Application Insights** > **TelemetryModules** elemento. Cada `<Add>` elemento subordinado define um tipo de telemetria para recolher, juntamente com a respetiva configuração. O tipo de módulo de telemetria de contador de desempenho é `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Se já está definido neste elemento, não o adicione uma segunda vez. Cada contador de desempenho para recolher é definido num nó denominado `<Counters>`. Eis um exemplo que recolhe os contadores de desempenho de unidade:

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

Cada contador de desempenho é representada como um `<Add>` elemento em `<Counters>`. O `PerformanceCounter` atributo define o contador de desempenho a recolher. O `ReportAs` atributo é o título para apresentar no portal do Azure para o contador de desempenho. Qualquer contador de desempenho recolhidos é colocado numa categoria com o nome **personalizado** no portal. Ao contrário do diagnóstico do Azure, não é possível definir o intervalo estes contadores de desempenho são recolhidos e enviados para o Azure. Com o Application Insights, contadores de desempenho são recolhidos e enviados a cada minuto. 

O Application Insights recolhe automaticamente os seguintes contadores de desempenho:

* \Process(??APP_WIN32_PROC??)\% Hora do Processador
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Para obter mais informações, consulte [contadores de desempenho do sistema no Application Insights](../azure-monitor/app/performance-counters.md) e [Application Insights para serviços Cloud do Azure](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

> [!IMPORTANT]
> Embora todos estes dados são agregados na conta de armazenamento, o portal faz **não** proporcionam uma forma de nativa para os dados do gráfico. É altamente recomendável que integrar o outro serviço de diagnóstico, como o Application Insights, na sua aplicação.

A extensão de diagnóstico do Azure para serviços em nuvem permite que especifique quais contadores de desempenho que pretende recolher. Para configurar o diagnóstico do Azure, veja [Cloud Service descrição geral da monitorização](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Os contadores de desempenho que pretende recolher são definidos na **diagnostics.wadcfgx** ficheiro. Abra este ficheiro (ele é definido por função) no Visual Studio e localize a **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **PerformanceCounters** elemento. Adicionar um novo **PerformanceCounterConfiguration** elemento como filho. Esse elemento possui dois atributos: `counterSpecifier` e `sampleRate`. O `counterSpecifier` atributo define qual contador definido (descrito na secção anterior) para recolher o desempenho do sistema. O `sampleRate` valor indica a frequência com que esse valor é consultado. Como um todo, todos os contadores de desempenho são transferidos para o Azure, de acordo com o elemento principal `PerformanceCounters` do elemento `scheduledTransferPeriod` valor do atributo.

Para obter mais informações sobre o `PerformanceCounters` elemento do esquema, consulte a [esquema de diagnóstico do Azure](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element).

O período definido pelo `sampleRate` usos de atributo tipo dos dados de duração do XML para indicar com que frequência é consultado o contador de desempenho. No exemplo abaixo, a taxa é definida como `PT3M`, que significa que `[P]eriod[T]ime[3][M]inutes`: a cada três minutos.

Para obter mais informações sobre como o `sampleRate` e `scheduledTransferPeriod` são definidas, consulte a **tipo de dados de duração** secção o [W3 XML data e hora data tipos](https://www.w3schools.com/XML/schema_dtypes_date.asp) tutorial.

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

Um novo contador de desempenho pode ser criado e usado pelo seu código. O código que cria um novo contador de desempenho tem de executar elevado, caso contrário, ocorrerá uma falha. Seu serviço cloud `OnStart` código de inicialização pode criar o contador de desempenho, que seja necessário executar a função num contexto elevado. Ou pode criar uma tarefa de arranque que é executado elevada e cria o contador de desempenho. Para obter mais informações sobre tarefas de arranque, consulte [como configurar e executar tarefas de arranque para um serviço cloud](cloud-services-startup-tasks.md).

Para configurar a sua função para execução privilegiada, adicione uma `<Runtime>` elemento para a [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) ficheiro.

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

Pode criar e registar um novo contador de desempenho com algumas linhas de código. Utilize o `System.Diagnostics.PerformanceCounterCategory.Create` sobrecarga do método que cria a categoria e o contador. O código a seguir verifica primeiro se a categoria existe e se estiver em falta, cria a categoria e o contador.

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

Quando deseja usar o contador, chamar o `Increment` ou `IncrementBy` método.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Agora que a sua aplicação utilizar o contador personalizado, tem de configurar o diagnóstico do Azure ou o Application Insights para controlar o contador.


### <a name="application-insights"></a>Application Insights

Conforme anteriormente mencionado, os contadores de desempenho para o Application Insights é definido na **applicationinsights. config** ficheiro. Open **applicationinsights. config** e localize a **Application Insights** > **TelemetryModules** > **adicionar**  >  **Contadores** elemento. Criar uma `<Add>` elemento subordinado e o conjunto a `PerformanceCounter` atributo para a categoria e o nome do contador de desempenho que criou no seu código. Definir o `ReportAs` atributo para um nome amigável que deseja ver no portal.

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

Conforme indicado anteriormente, os contadores de desempenho que pretende recolher são definidos na **diagnostics.wadcfgx** ficheiro. Abra este ficheiro (ele é definido por função) no Visual Studio e localize a **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **PerformanceCounters** elemento. Adicionar um novo **PerformanceCounterConfiguration** elemento como filho. Definir o `counterSpecifier` atributo para a categoria e o nome do contador de desempenho que criou no seu código. 

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

- [Application Insights para serviços Cloud do Azure](../azure-monitor/app/cloudservices.md#performance-counters)
- [Contadores de desempenho do sistema no Application Insights](../azure-monitor/app/performance-counters.md)
- [Especificar um caminho de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Esquema de diagnóstico do Azure - contadores de desempenho](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)
