---
title: Enviar métricas de SO convidado para a métrica do Azure Monitor armazenam clássico dos serviços de Cloud
description: Enviar métricas de SO convidado para a métrica do Azure Monitor armazenam serviços Cloud
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: dba1f66be91d8fa8e151a2771bad70b721af02dc
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313077"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Enviar métricas de SO convidado para a métrica do Azure Monitor armazenam clássico dos serviços de Cloud 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Com o Azure Monitor [extensão de diagnóstico](diagnostics-extension-overview.md), pode coletar métricas e registos do sistema operativo convidado (SO convidado) em execução como parte de uma máquina virtual, um serviço em nuvem ou um cluster do Service Fabric. A extensão pode enviar telemetria para [vários locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo para o envio de métricas de desempenho do SO convidado para serviços Cloud clássico do Azure para o arquivo de métrica do Azure Monitor. A partir do diagnóstico versão 1.11, pode escrever métricas diretamente para o Azure Monitor armazenam métricas, onde as métricas de plataforma padrão já são recolhidas. 

Armazená-los nesta localização permite que acesse as mesmas ações que pode fazer para métricas de plataforma. As ações incluem quase em tempo real alerta, a criação de gráficos, encaminhamento, acesso a partir de uma API REST e muito mais.  No passado, a extensão de diagnóstico escreveu para o armazenamento do Azure, mas não para o arquivo de dados do Azure Monitor.  

O processo que está descrito em funciona este artigo apenas para contadores de desempenho em serviços Cloud do Azure. Ele não funciona para outras métricas personalizadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ser um [administrador de serviços ou coadministrador](~/articles/billing/billing-add-change-azure-subscription-administrator.md) na sua subscrição do Azure. 

- A sua subscrição tem de estar registrada com [Microsoft. insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Tem de ter [do Azure PowerShell](/powershell/azure) ou [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.

## <a name="provision-a-cloud-service-and-storage-account"></a>Aprovisionar uma conta de serviço e o armazenamento na cloud 

1. Criar e implementar um serviço cloud clássico. Um exemplo clássico de aplicativo de serviços Cloud e a implementação podem ser encontrados em [introdução aos serviços Cloud do Azure e ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Pode utilizar uma conta de armazenamento existente ou implementar uma nova conta de armazenamento. É melhor se a conta de armazenamento está na mesma região que o serviço cloud clássico que criou. No portal do Azure, vá para o **contas de armazenamento** painel de recursos e, em seguida, selecione **chaves**. Anote o nome da conta de armazenamento e a chave de conta de armazenamento. Precisará essas informações em passos posteriores.

   ![Chaves de contas de armazenamento](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Criar um principal de serviço 

Criar um principal de serviço no seu inquilino do Azure Active Directory com as instruções em [utilize o portal para criar um Azure Active Directory principal de aplicações e serviço que pode aceder a recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Enquanto vai por meio desse processo, tenha em atenção o seguinte: 

- Pode colocar qualquer URL para o URL de início de sessão.  
- Crie novo segredo do cliente para esta aplicação.  
- Guarde a chave e o ID de cliente para uso em passos posteriores.  

Dê à aplicação criada no passo anterior *Editor de métricas de monitorização* permissões para o recurso que pretende emitir métricas contra. Se planeia utilizar a aplicação para emitir métricas personalizadas em relação a muitos recursos, pode conceder estas permissões ao nível de grupo ou uma subscrição do recurso.  

> [!NOTE]
> A extensão de diagnóstico utiliza o principal de serviço para autenticar no Azure Monitor e emite métricas do serviço em nuvem.

## <a name="author-diagnostics-extension-configuration"></a>Configuração de extensão de diagnóstico do autor 

Prepare o ficheiro de configuração de extensão de diagnóstico. Este ficheiro determina quais os registos e contadores de desempenho a extensão de diagnóstico deve recolher do serviço em nuvem. Segue-se um ficheiro de configuração de diagnósticos de exemplo:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

Na secção "SinksConfig" do seu ficheiro de diagnóstico, defina um novo sink do Azure Monitor: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

A secção do ficheiro de configuração onde lista os contadores de desempenho para recolher, adicione o sink do Azure Monitor. Esta entrada garante que todos os contadores de desempenho que especificou são encaminhados para o Azure Monitor, como métricas. Pode adicionar ou remover contadores de desempenho, de acordo com suas necessidades. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Por fim, a configuração privada, adicione uma *conta do Azure Monitor* secção. Introduza o ID de cliente do principal de serviço e o segredo que criou anteriormente. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Guarde este ficheiro de diagnóstico localmente.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implementar a extensão de diagnóstico ao seu serviço cloud 

Inicie o PowerShell e inicie sessão no Azure. 

```PowerShell
Login-AzAccount 
```

Utilize os seguintes comandos para armazenar os detalhes da conta de armazenamento que criou anteriormente. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Da mesma forma, defina o caminho do ficheiro de diagnóstico a uma variável com o seguinte comando:

```PowerShell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Implemente a extensão de diagnóstico ao seu serviço cloud com o arquivo de diagnóstico com o sink de Monitor do Azure configurado com o seguinte comando:  

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> É ainda obrigatório para fornecer uma conta de armazenamento como parte da instalação da extensão do diagnóstico. Quaisquer registos ou contadores de desempenho que são especificados no ficheiro de configuração de diagnóstico são escritos para a conta de armazenamento especificada.  

## <a name="plot-metrics-in-the-azure-portal"></a>Gráfico de métricas no portal do Azure 

1. Aceda ao portal do Azure. 

   ![Métricas de portal do Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. No menu da esquerda, selecione **Monitor.**

3. Sobre o **Monitor** painel, selecione a **pré-visualização de métricas** separador.

4. No menu de lista pendente de recursos, selecione o seu serviço cloud clássico.

5. No menu de lista pendente de espaços de nomes, selecione **azure.vm.windows.guest**. 

6. No menu de lista pendente de métricas, selecione **Memory\Committed Bytes em utilização**. 

Utilize a dimensão de filtragem e a divisão de recursos para ver o total de memória que é utilizado por uma função específica ou uma instância de função. 

 ![Métricas de portal do Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

