---
title: Envie métricas clássicas de Serviços cloud para base de dados métricas do Azure Monitor
description: Descreve o processo de envio de métricas de desempenho do Guest OS para a Azure classic Cloud Services para a loja métrica Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: 0ef9d8118a8ff1d9fdd69566dd60033f5847f810
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048969"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Envie métricas de SO para a loja métrica Azure Monitor clássico Cloud Services 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Com a extensão Azure Monitor [Diagnostics,](../agents/diagnostics-extension-overview.md)pode recolher métricas e registos do sistema operativo convidado (Guest OS) em funcionamento como parte de uma máquina virtual, serviço de nuvem ou cluster de Tecido de Serviço. A extensão pode enviar telemetria para [vários locais diferentes.](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Este artigo descreve o processo de envio de métricas de desempenho do Guest OS para a Azure classic Cloud Services para a loja métrica Azure Monitor. A partir da versão 1.11 do Diagnostics, pode escrever métricas diretamente na loja de métricas do Azure Monitor, onde já são recolhidas métricas padrão da plataforma. 

Guardá-los neste local permite-lhe aceder às mesmas ações que pode para as métricas da plataforma. As ações incluem alerta em tempo real, gráfico, encaminhamento, acesso a partir de uma API REST, e muito mais.  No passado, a extensão de Diagnóstico escreveu ao Azure Storage, mas não à loja de dados do Azure Monitor.  

O processo que está delineado neste artigo funciona apenas para contadores de desempenho nos Serviços Azure Cloud. Não funciona para outras métricas personalizadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Deve ser [administrador de serviço ou coadministrador](../../cost-management-billing/manage/add-change-subscription-administrator.md) na sua assinatura Azure. 

- A sua subscrição deve estar registada no [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Precisa de ter [a Azure PowerShell](/powershell/azure) ou [a Azure Cloud Shell](../../cloud-shell/overview.md) instaladas.

- O seu Serviço cloud deve estar numa [região que suporte métricas personalizadas.](./metrics-custom-overview.md#supported-regions)

## <a name="provision-a-cloud-service-and-storage-account"></a>Fornecimento de um serviço em nuvem e conta de armazenamento 

1. Crie e implemente um serviço de nuvem clássico. Uma amostra clássica de aplicação e implementação de Cloud Services pode ser encontrada no [Get start with Azure Cloud Services and ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Pode utilizar uma conta de armazenamento existente ou implementar uma nova conta de armazenamento. É melhor se a conta de armazenamento estiver na mesma região que o serviço de nuvem clássico que criou. No portal Azure, aceda à lâmina de recursos das **contas de Armazenamento** e, em seguida, selecione **Keys**. Tome nota do nome da conta de armazenamento e da chave da conta de armazenamento. Estas informações são necessárias nos passos posteriores.

   ![Chaves de contas de armazenamento](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Criar um principal de serviço 

Crie um diretor de serviço no seu inquilino Azure Ative Directory utilizando as instruções no [portal Use para criar uma aplicação e um diretor de serviço azure ative que possa aceder aos recursos.](../../active-directory/develop/howto-create-service-principal-portal.md) Note o seguinte enquanto está a passar por este processo: 

- Pode colocar qualquer URL para a URL de inscrição.  
- Crie um novo segredo de cliente para esta aplicação.  
- Guarde a chave e o ID do cliente para utilização em etapas posteriores.  

Dê a aplicação criada no passo anterior *Monitoring Metrics Publisher* permissões para o recurso contra o quais pretende emitir métricas. Se pretender utilizar a app para emitir métricas personalizadas contra muitos recursos, pode conceder essas permissões ao nível do grupo de recursos ou da subscrição.  

> [!NOTE]
> A extensão de Diagnóstico utiliza o principal de serviço para autenticar contra o Azure Monitor e emitir métricas para o seu serviço na nuvem.

## <a name="author-diagnostics-extension-configuration"></a>Configuração da extensão de Diagnóstico de Autor 

Prepare o seu ficheiro de configuração de extensão de Diagnóstico. Este ficheiro dita quais os registos e os contadores de desempenho que a extensão de Diagnóstico deve recolher para o seu serviço na nuvem. Segue-se um ficheiro de configuração de diagnóstico de amostra:  

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

Na secção "SinksConfig" do seu ficheiro de diagnóstico, defina um novo lavatório Azure Monitor: 

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

Na secção do seu ficheiro de configuração onde lista os contadores de desempenho a recolher, adicione a pia do Azure Monitor. Esta entrada garante que todos os contadores de desempenho especificados sejam encaminhados para O Azure Monitor como métricas. Pode adicionar ou remover contadores de desempenho de acordo com as suas necessidades. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Finalmente, na configuração privada, adicione uma secção *de Conta Azure Monitor.* Insira o iD do cliente principal de serviço e o segredo que criou anteriormente. 

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implemente a extensão de Diagnóstico para o seu serviço na nuvem 

Lançar PowerShell e iniciar sessão no Azure. 

```powershell
Login-AzAccount 
```

Utilize os seguintes comandos para armazenar os detalhes da conta de armazenamento que criou anteriormente. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Da mesma forma, desacorda o caminho do ficheiro de diagnóstico para uma variável utilizando o seguinte comando:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Implemente a extensão de Diagnóstico ao seu serviço de nuvem com o ficheiro de diagnóstico com o lavatório Azure Monitor configurado utilizando o seguinte comando:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> É ainda obrigatório fornecer uma conta de armazenamento como parte da instalação da extensão de Diagnóstico. Quaisquer registos ou contadores de desempenho especificados no ficheiro config de diagnóstico são escritos na conta de armazenamento especificada.  

## <a name="plot-metrics-in-the-azure-portal"></a>Métricas de enredo no portal Azure 

1. Aceda ao portal do Azure. 

   ![A screenshot mostra o portal Azure com Monitor, em seguida, métricas selecionadas.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. No menu esquerdo, selecione **Monitor.**

3. Na lâmina **Monitor,** selecione o **separador De pré-visualização métrica.**

4. No menu suspenso de recursos, selecione o seu serviço clássico de nuvem.

5. No menu drop-down dos espaços de nome, selecione **azure.vm.windows.guest**. 

6. No menu suspenso de métricas, selecione **Memory\Committed Bytes in Use**. 

Utiliza-se a capacidade de filtragem e divisão de dimensões para visualizar a memória total que é usada por um papel específico ou por uma instância de função. 

 ![A screenshot mostra dados de métricas.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [métricas personalizadas.](./metrics-custom-overview.md)