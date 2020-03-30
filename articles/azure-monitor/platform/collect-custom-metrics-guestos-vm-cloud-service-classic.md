---
title: Envie métricas clássicas de Serviços cloud para base de dados de métricas Do Monitor Azure
description: Descreve o processo de envio de métricas de desempenho do Os do Hóspede para os Serviços de Cloud clássicos do Azure para a loja métrica Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3b390ffa20cf3cf79b8fb6311ad05b2978bd5d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655805"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Envie métricas de Os convidados para a loja métrica Azure Monitor serviços clássicos cloud 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Com a [extensão](diagnostics-extension-overview.md)de Diagnóstico do Monitor Azure, pode recolher métricas e registos do sistema operativo convidado (Guest OS) que funciona como parte de uma máquina virtual, serviço em nuvem ou cluster de Tecido de Serviço. A extensão pode enviar telemetria para [muitos locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo de envio de métricas de desempenho do Os para os Serviços de Cloud clássicos do Azure para a loja métrica Azure Monitor. A partir da versão 1.11 do Diagnostics, pode escrever métricas diretamente na loja de métricas Do Monitor Do Azure, onde as métricas padrão da plataforma já são recolhidas. 

Guardá-los neste local permite-lhe aceder às mesmas ações que pode para métricas de plataforma. As ações incluem alerta em tempo quase real, gráficos, encaminhamento, acesso a partir de uma API REST, e muito mais.  No passado, a extensão de Diagnóstico supor ao Armazenamento Azure, mas não à loja de dados Azure Monitor.  

O processo que está delineado neste artigo funciona apenas para contadores de desempenho nos Serviços Azure Cloud. Não funciona para outras métricas personalizadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Deve ser [administrador de serviçoou coadministrador](../../cost-management-billing/manage/add-change-subscription-administrator.md) na sua subscrição Azure. 

- A sua subscrição deve ser registada na [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- É necessário instalar o [Azure PowerShell](/powershell/azure) ou o [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- O seu Serviço cloud deve estar numa [região que suporta métricas personalizadas.](metrics-custom-overview.md#supported-regions)

## <a name="provision-a-cloud-service-and-storage-account"></a>Fornecer uma conta de serviço e armazenamento na nuvem 

1. Crie e implante um serviço clássico de nuvem. Uma amostra clássica de aplicação e implantação de Serviços cloud pode ser encontrada no [Get started com Azure Cloud Services e ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Pode utilizar uma conta de armazenamento existente ou implementar uma nova conta de armazenamento. É melhor se a conta de armazenamento estiver na mesma região que o serviço de nuvem clássico que criou. No portal Azure, vá à lâmina de recursos das contas de **Armazenamento** e, em seguida, selecione **Keys**. Tome nota do nome da conta de armazenamento e da chave da conta de armazenamento. Vai precisar desta informação em passos posteriores.

   ![Chaves de contas de armazenamento](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Criar um principal de serviço 

Crie um princípio de serviço no seu inquilino do Diretório Ativo Azure utilizando as instruções do [portal Use para criar um diretor de diretório ativo Azure e um diretor](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)de serviço que possa aceder a recursos. Note o seguinte enquanto está a passar por este processo: 

- Pode colocar qualquer URL para o URL de entrada.  
- Crie um novo segredo de cliente para esta aplicação.  
- Guarde a chave e o ID do cliente para utilização em etapas posteriores.  

Dê à aplicação criada no passo anterior *a monitorização* das permissões da Editora de Métricas para o recurso contra o que pretende emitir métricas. Se planeia utilizar a app para emitir métricas personalizadas contra muitos recursos, pode conceder essas permissões ao nível de recursos ou subscrição.  

> [!NOTE]
> A extensão de Diagnóstico utiliza o principal de serviço para autenticar contra o Monitor Azure e emitir métricas para o seu serviço na nuvem.

## <a name="author-diagnostics-extension-configuration"></a>Configuração de extensão de diagnóstico de autor 

Prepare o ficheiro de configuração de extensão de Diagnóstico. Este ficheiro dita quais os registos e os contadores de desempenho que a extensão de Diagnóstico deve recolher para o seu serviço na nuvem. Segue-se um ficheiro de configuração de diagnóstico de amostra:  

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

Na secção "SinksConfig" do seu ficheiro de diagnóstico, defina um novo lavatório Do Monitor Azure: 

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

Na secção do seu ficheiro de configuração onde lista os contadores de desempenho para recolher, adicione o lavatório Azure Monitor. Esta entrada garante que todos os contadores de desempenho especificados são encaminhados para o Monitor Azure como métricas. Pode adicionar ou remover contadores de desempenho de acordo com as suas necessidades. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Finalmente, na configuração privada, adicione uma secção *de Conta De Monitor Azure.* Insira o id principal do cliente do serviço e segredo que criou anteriormente. 

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implemente a extensão de Diagnósticos ao seu serviço na nuvem 

Inicie powerShell e faça login no Azure. 

```powershell
Login-AzAccount 
```

Utilize os seguintes comandos para armazenar os detalhes da conta de armazenamento que criou anteriormente. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Da mesma forma, detete o caminho do ficheiro de diagnóstico para uma variável utilizando o seguinte comando:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Implemente a extensão de Diagnóstico ao seu serviço na nuvem com o ficheiro de diagnóstico com o lavatório Do Monitor Azure configurado utilizando o seguinte comando:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Ainda é obrigatório fornecer uma conta de armazenamento como parte da instalação da extensão de Diagnósticos. Quaisquer registos ou contadores de desempenho especificados no ficheiro de diagnóstico config estão escritos na conta de armazenamento especificada.  

## <a name="plot-metrics-in-the-azure-portal"></a>Métricas de enredo no portal Azure 

1. Aceda ao portal do Azure. 

   ![Portal Metrics Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. No menu esquerdo, selecione **Monitor.**

3. Na lâmina **monitora,** selecione o separador **'Pré-visualização de métricas'.**

4. No menu de drop-down de recursos, selecione o seu serviço de nuvem clássica.

5. No menu de lançamento dos espaços de nome, **selecione azure.vm.windows.guest**. 

6. No menu de entrega de métricas, selecione **Memory\Committed Bytes in Use**. 

Usa a dimensão filtrando e dividindo capacidades para ver a memória total que é usada por uma função ou exemplo de papel específico. 

 ![Portal Metrics Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [métricas personalizadas.](metrics-custom-overview.md)

