---
title: Recolha em Balcões de Performance nos Serviços Azure Cloud [ Microsoft Docs
description: Saiba como descobrir, usar e criar contadores de desempenho em Cloud Services com Diagnósticos Azure e Insights de Aplicação.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 02/02/2018
ms.author: tagore
ms.openlocfilehash: 3b4028a09f69acd5d7a6579b4610785ed32e227d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77469532"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Colete contadores de desempenho para o seu Serviço Azure Cloud

Os contadores de desempenho fornecem uma forma de rastrear o desempenho da sua aplicação e do anfitrião. O Windows Server fornece muitos contadores de desempenho diferentes relacionados com hardware, aplicações, o sistema operativo e muito mais. Ao recolher e enviar contadores de desempenho para o Azure, pode analisar esta informação para ajudar a tomar melhores decisões. 

## <a name="discover-available-counters"></a>Descubra os balcões disponíveis

Um contador de desempenho é composto por duas partes, um nome definido (também conhecido como categoria) e um ou mais contadores. Pode utilizar o PowerShell para obter uma lista de contadores de desempenho disponíveis:

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

O `CounterSetName` imóvel representa um conjunto (ou categoria), e é um bom indicador do que os contadores de desempenho estão relacionados. A `Paths` propriedade representa uma coleção de balcões para um conjunto. Você também poderia `Description` obter a propriedade para mais informações sobre o conjunto de balcão.

Para obter todos os balcões para `CounterSetName` um conjunto, `Paths` use o valor e expanda a coleção. Cada item de caminho é um contador que você pode consultar. Por exemplo, para obter os contadores `Processor` disponíveis relacionados com o conjunto, expandir a `Paths` coleção:

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

Estes contra-caminhos individuais podem ser adicionados à estrutura de diagnóstico sintetizador que o seu serviço de nuvem utiliza. Para obter mais informações sobre como é construído um contra-caminho de desempenho, consulte [especificar um Contra-Caminho](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Recolher um contador de desempenho

Um contador de desempenho pode ser adicionado ao seu serviço de nuvem para o Azure Diagnostics ou para application Insights.

### <a name="application-insights"></a>Application Insights

Os Insights de Aplicação Azure para serviços na nuvem permitem especificar quais os contadores de desempenho que pretende recolher. Depois de adicionar Insights de [Aplicação ao seu projeto,](../azure-monitor/app/cloudservices.md#sdk)um ficheiro config chamado **ApplicationInsights.config** é adicionado ao seu projeto Visual Studio. Este ficheiro config define que tipo de informação a Application Insights recolhe e envia para o Azure.

Abra o ficheiro **ApplicationInsights.config** e encontre o elemento **ApplicationInsights** > **TelemettryModules.** Cada `<Add>` elemento infantil define um tipo de telemetria para recolher, juntamente com a sua configuração. O tipo de módulo de `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`telemetria de contador de desempenho é . Se este elemento já estiver definido, não o adicione uma segunda vez. Cada contador de desempenho para recolher `<Counters>`é definido sob um nó chamado . Aqui está um exemplo que recolhe contadores de desempenho de unidade:

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

Cada contador de desempenho `<Add>` é `<Counters>`representado como um elemento abaixo de . O `PerformanceCounter` atributo define qual contador de desempenho recolher. O `ReportAs` atributo é o título a exibir no portal Azure para o contador de desempenho. Qualquer contador de desempenho que recolher é colocado numa categoria chamada **Custom** no portal. Ao contrário do Azure Diagnostics, não é possível definir o intervalo que estes contadores de desempenho são recolhidos e enviados para o Azure. Com os Insights de Aplicação, os contadores de desempenho são recolhidos e enviados a cada minuto. 

Os Insights de Aplicação recolhem automaticamente os seguintes contadores de desempenho:

* \Processo(?? APP_WIN32_PROC??) \% Tempo do processador
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Para mais informações, consulte os contadores de desempenho do Sistema em Insights de [Aplicação](../azure-monitor/app/performance-counters.md) e Insights de [Aplicação para Serviços de Nuvem Azure](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

> [!IMPORTANT]
> Embora todos estes dados sejam agregados na conta de armazenamento, o portal **não** fornece uma forma nativa de traçar os dados. É altamente recomendável que integre outro serviço de diagnóstico, como o Application Insights, na sua aplicação.

A extensão de Diagnóstico Azure para Serviços na Nuvem permite especificar quais os contadores de desempenho que pretende recolher. Para configurar o Azure Diagnostics, consulte a [visão geral](cloud-services-how-to-monitor.md#setup-diagnostics-extension)do serviço de nuvem .

Os contadores de desempenho que pretende recolher são definidos no ficheiro **diagnostics.wadcfgx.** Abra este ficheiro (é definido por função) no Estúdio Visual e encontre o elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Adicione um novo elemento **PerformanceCounterConfiguration** em criança. Este elemento tem `counterSpecifier` dois `sampleRate`atributos: e . O `counterSpecifier` atributo define qual o conjunto de contador de desempenho do sistema (delineado na secção anterior) para recolher. O `sampleRate` valor indica quantas vezes esse valor é sondado. No seu conjunto, todos os contadores de desempenho `PerformanceCounters` são transferidos para OAzure de acordo com o valor do atributo do `scheduledTransferPeriod` elemento-mãe.

Para obter mais `PerformanceCounters` informações sobre o elemento esquema, consulte o [Azure Diagnostics Schema](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element).

O período definido `sampleRate` pelo atributo utiliza o tipo de dados de duração XML para indicar com que frequência o contador de desempenho é inquirido. No exemplo abaixo, a taxa `PT3M`é `[P]eriod[T]ime[3][M]inutes`fixada para , o que significa : a cada três minutos.

Para obter mais `sampleRate` informações sobre como e stão `scheduledTransferPeriod` definidos, consulte a secção Tipo de Dados de **Duração** no tutorial [de data seleções W3 XML e data](https://www.w3schools.com/XML/schema_dtypes_date.asp) de tempo.

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

## <a name="create-a-new-perf-counter"></a>Criar um novo contador perf

Um novo contador de desempenho pode ser criado e usado pelo seu código. O seu código que cria um novo contador de desempenho deve estar a funcionar elevado, caso contrário falhará. O seu `OnStart` código de arranque de serviço na nuvem pode criar o contador de desempenho, exigindo que você execute o papel num contexto elevado. Ou pode criar uma tarefa de arranque que seja elevada e crie o contador de desempenho. Para obter mais informações sobre as tarefas de arranque, consulte [como configurar e executar tarefas](cloud-services-startup-tasks.md)de arranque para um serviço na nuvem.

Para configurar a sua função `<Runtime>` para ser elevado, adicione um elemento ao ficheiro [.csdef.](cloud-services-model-and-package.md#servicedefinitioncsdef)

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

Pode criar e registar um novo contador de desempenho com algumas linhas de código. Utilize `System.Diagnostics.PerformanceCounterCategory.Create` a sobrecarga do método que cria tanto a categoria como o contador. O código seguinte verifica primeiro se a categoria existe e, se falta, cria tanto a categoria como o contador.

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

Quando quiser utilizar o contador, `Increment` `IncrementBy` ligue para o ou o método.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Agora que a sua aplicação utiliza o seu contador personalizado, precisa de configurar o Azure Diagnostics ou a Application Insights para rastrear o contador.


### <a name="application-insights"></a>Application Insights

Como indicado anteriormente, os contadores de desempenho para Insights de Aplicação são definidos no ficheiro **ApplicationInsights.config.** Abra **applicationInsights.config** e encontre o elemento **ApplicationInsights** > **TelemettryModules** > **Add** > **Counters.** Crie `<Add>` um elemento `PerformanceCounter` infantil e detete o atributo à categoria e nome do contador de desempenho que criou no seu código. Detete o `ReportAs` atributo para um nome amigável que deseja ver no portal.

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

Como indicado anteriormente, os contadores de desempenho que pretende recolher são definidos no ficheiro **diagnostics.wadcfgx.** Abra este ficheiro (é definido por função) no Estúdio Visual e encontre o elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Adicione um novo elemento **PerformanceCounterConfiguration** em criança. Detete o `counterSpecifier` atributo para a categoria e nome do contador de desempenho que criou no seu código. 

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

- [Application Insights para os Serviços Cloud do Azure](../azure-monitor/app/cloudservices.md#performance-counters)
- [Contadores de desempenho do sistema em Insights de Aplicação](../azure-monitor/app/performance-counters.md)
- [Especificação de um contra-caminho](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure Diagnostics Schema - Contadores de Desempenho](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)



