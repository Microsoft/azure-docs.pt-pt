---
title: Recolha em contadores de desempenho em Azure Cloud Services (clássico) | Microsoft Docs
description: Aprenda a descobrir, usar e criar contadores de desempenho em Serviços cloud com Azure Diagnostics e Application Insights.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: fa5dd61c0764be45cdba68b73a4f55745ee5e55a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585510"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service-classic"></a>Colete contadores de desempenho para o seu Azure Cloud Service (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Os contadores de desempenho fornecem uma forma de acompanhar o desempenho da sua aplicação e do anfitrião. O Windows Server fornece muitos contadores de desempenho diferentes relacionados com hardware, aplicações, sistema operativo e muito mais. Ao recolher e enviar contadores de desempenho para o Azure, pode analisar esta informação para ajudar a tomar melhores decisões. 

## <a name="discover-available-counters"></a>Descubra balcões disponíveis

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

A `CounterSetName` propriedade representa um conjunto (ou categoria), e é um bom indicador do que os contadores de desempenho estão relacionados. A `Paths` propriedade representa uma coleção de balcões para um conjunto. Você também pode obter a `Description` propriedade para mais informações sobre o conjunto de contadores.

Para obter todos os balcões para um conjunto, use o `CounterSetName` valor e expanda a `Paths` coleção. Cada item de caminho é um contador que pode consultar. Por exemplo, para obter os balcões disponíveis relacionados com o `Processor` conjunto, expanda a `Paths` coleção:

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

Estes caminhos de contra-ataque individuais podem ser adicionados à estrutura de diagnósticos que o seu serviço de nuvem utiliza. Para obter mais informações sobre como um contra-caminho de desempenho é construído, consulte [especificar um contra-caminho](/windows/win32/perfctrs/specifying-a-counter-path).

## <a name="collect-a-performance-counter"></a>Colete um balcão de performance

Um contador de desempenho pode ser adicionado ao seu serviço de nuvem para Azure Diagnostics ou Application Insights.

### <a name="application-insights"></a>Application Insights

O Azure Application Insights for Cloud Services permite especificar quais os contadores de desempenho que pretende recolher. Depois de [adicionar Application Insights ao seu projeto,](../azure-monitor/app/cloudservices.md#sdk)um ficheiro config chamado **ApplicationInsights.config** é adicionado ao seu projeto Visual Studio. Este ficheiro config define que tipo de informação a Application Insights recolhe e envia para o Azure.

Abra o ficheiro **ApplicationInsights.config** e encontre o elemento Telemetriamodules **ApplicationInsights.**  >   Cada `<Add>` elemento infantil define um tipo de telemetria para recolher, juntamente com a sua configuração. O tipo de módulo de telemetria de contador de desempenho é `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector` . Se este elemento já estiver definido, não o adicione uma segunda vez. Cada contador de desempenho a recolher é definido sob um nó denominado `<Counters>` . Aqui está um exemplo que recolhe contadores de desempenho de unidade:

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

Cada contador de desempenho é representado como um `<Add>` elemento sob `<Counters>` . O `PerformanceCounter` atributo define qual o contador de desempenho a recolher. O `ReportAs` atributo é o título a exibir no portal Azure para o contador de desempenho. Qualquer contador de desempenho que recolher é colocado numa categoria chamada **Custom** no portal. Ao contrário do Azure Diagnostics, não é possível definir o intervalo que estes contadores de desempenho são recolhidos e enviados para a Azure. Com a Application Insights, os contadores de desempenho são recolhidos e enviados a cada minuto. 

O Application Insights recolhe automaticamente os seguintes contadores de desempenho:

* \Processo(?? APP_WIN32_PROC??) \% Tempo do processador
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Para obter mais informações, consulte [os contadores de desempenho do Sistema em Insights de Aplicações](../azure-monitor/app/performance-counters.md) e [Insights de Aplicações para Serviços em Nuvem Azure](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

> [!IMPORTANT]
> Embora todos estes dados são agregados na conta de armazenamento, o portal **não** fornece uma forma nativa de traçar os dados. É altamente recomendável que integre outro serviço de diagnóstico, como o Application Insights, na sua aplicação.

A extensão Azure Diagnostics para Serviços cloud permite-lhe especificar quais os contadores de desempenho que pretende recolher. Para configurar o Azure Diagnostics, consulte [a Visão Geral de Monitorização do Serviço de Nuvem](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Os contadores de desempenho que pretende recolher estão definidos no ficheiro **diagnostics.wadcfgx.** Abra este ficheiro (está definido por função) no Estúdio Visual e encontre o elemento **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **PerformanceCounters.** Adicione um novo elemento **de Configuração do DesempenhoCounter** quando criança. Este elemento tem dois atributos: `counterSpecifier` e `sampleRate` . O `counterSpecifier` atributo define qual o conjunto de contador de desempenho do sistema (delineado na secção anterior) para recolher. O `sampleRate` valor indica a frequência com que esse valor é sondado. No seu conjunto, todos os contadores de desempenho são transferidos para a Azure de acordo com o `PerformanceCounters` valor de atributo do `scheduledTransferPeriod` elemento-mãe.

Para obter mais informações sobre o `PerformanceCounters` elemento esquema, consulte o [Esquema de DiagnósticoS Azure](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element).

O período definido pelo `sampleRate` atributo utiliza o tipo de dados de duração XML para indicar com que frequência o contador de desempenho é sondado. No exemplo abaixo, a taxa é definida para , o que significa : a `PT3M` `[P]eriod[T]ime[3][M]inutes` cada três minutos.

Para obter mais informações sobre como `sampleRate` o e `scheduledTransferPeriod` são definidos, consulte a secção **tipo de dados de duração** no tutorial [de data e data de hora W3 XML.](https://www.w3schools.com/XML/schema_dtypes_date.asp)

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

## <a name="create-a-new-perf-counter"></a>Criar um novo balcão perf

Um novo contador de desempenho pode ser criado e usado pelo seu código. O seu código que cria um novo contador de desempenho deve estar a funcionar elevado, caso contrário falhará. O código de arranque do serviço de nuvem `OnStart` pode criar o contador de desempenho, exigindo que você execute o papel num contexto elevado. Ou pode criar uma tarefa de startup que executa elevada e cria o contador de desempenho. Para obter mais informações sobre tarefas de arranque, consulte [Como configurar e executar tarefas de arranque para um serviço de cloud](cloud-services-startup-tasks.md).

Para configurar o seu papel para correr elevado, adicione um `<Runtime>` elemento ao ficheiro [.csdef.](cloud-services-model-and-package.md#servicedefinitioncsdef)

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

Pode criar e registar um novo contador de desempenho com algumas linhas de código. Utilize a `System.Diagnostics.PerformanceCounterCategory.Create` sobrecarga do método que cria tanto a categoria como o contador. O código a seguir verifica primeiro se a categoria existe, e em caso de falta, cria tanto a categoria como o contador.

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

Quando quiser utilizar o balcão, ligue para o `Increment` ou `IncrementBy` método.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Agora que a sua aplicação utiliza o seu contador personalizado, tem de configurar o Azure Diagnostics ou Application Insights para rastrear o contador.


### <a name="application-insights"></a>Application Insights

Como indicado anteriormente, os contadores de desempenho para Insights de Aplicação são definidos no ficheiro **ApplicationInsights.config.** Abra **ApplicationInsights.config** e encontre o elemento **ApplicationInsights**  >  **TelemetryModules**  >  **Add**  >  **Counters.** Crie um `<Add>` elemento infantil e desaponte o `PerformanceCounter` atributo para a categoria e nome do contador de desempenho que criou no seu código. Desa parte `ReportAs` para um nome amigável que pretende ver no portal.

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

Como indicado anteriormente, os contadores de desempenho que pretende recolher são definidos no ficheiro **diagnostics.wadcfgx.** Abra este ficheiro (está definido por função) no Estúdio Visual e encontre o elemento **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **PerformanceCounters.** Adicione um novo elemento **de Configuração do DesempenhoCounter** quando criança. Desaprote o `counterSpecifier` atributo para a categoria e o nome do contador de desempenho que criou no seu código. 

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
- [Contadores de desempenho do sistema em Insights de Aplicações](../azure-monitor/app/performance-counters.md)
- [Especificando um caminho de contra-ataque](/windows/win32/perfctrs/specifying-a-counter-path)
- [Azure Diagnostics Schema - Contadores de desempenho](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element)