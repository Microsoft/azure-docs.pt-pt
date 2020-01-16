---
title: Enviar métricas clássicas dos serviços de nuvem para Azure Monitor banco de dados de métricas
description: Descreve o processo para enviar métricas de desempenho do SO convidado para serviços de nuvem clássicos do Azure para o repositório de métrica Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: fb13bb7ec2de2633796aecb5216ae8b9e2574a57
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75971195"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Enviar métricas do SO convidado para os serviços de nuvem clássicos do repositório de métricas de Azure Monitor 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Com a [extensão de diagnóstico](diagnostics-extension-overview.md)de Azure monitor, você pode coletar métricas e logs do sistema operacional convidado (SO convidado) em execução como parte de uma máquina virtual, serviço de nuvem ou cluster de Service Fabric. A extensão pode enviar telemetria para [vários locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado para serviços de nuvem clássicos do Azure para o repositório de métrica Azure Monitor. A partir da versão de diagnóstico 1,11, você pode gravar métricas diretamente no repositório de métricas de Azure Monitor, em que as métricas de plataforma padrão já são coletadas. 

Armazená-los nesse local permite que você acesse as mesmas ações que você pode para métricas de plataforma. As ações incluem alertas quase em tempo real, criação de gráficos, roteamento, acesso de uma API REST e muito mais.  No passado, a extensão de diagnóstico gravou no armazenamento do Azure, mas não no armazenamento de dados Azure Monitor.  

O processo descrito neste artigo funciona apenas para contadores de desempenho nos serviços de nuvem do Azure. Ele não funciona para outras métricas personalizadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um [administrador de serviços ou coadministrador](../../cost-management-billing/manage/add-change-subscription-administrator.md) em sua assinatura do Azure. 

- Sua assinatura deve ser registrada com [Microsoft. insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Você precisa ter o [Azure PowerShell](/powershell/azure) ou [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.

- Seu serviço de nuvem deve estar em uma [região que dá suporte a métricas personalizadas](metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Provisionar um serviço de nuvem e uma conta de armazenamento 

1. Crie e implante um serviço de nuvem clássico. Um exemplo de aplicativo e implantação de serviços de nuvem clássicos pode ser encontrado em [introdução aos serviços de nuvem do Azure e ASP.net](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Você pode usar uma conta de armazenamento existente ou implantar uma nova conta de armazenamento. É melhor se a conta de armazenamento estiver na mesma região que o serviço de nuvem clássico que você criou. Na portal do Azure, vá para a folha de recursos **contas de armazenamento** e, em seguida, selecione **chaves**. Anote o nome da conta de armazenamento e a chave da conta de armazenamento. Você precisará dessas informações em etapas posteriores.

   ![Chaves de contas de armazenamento](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Criar um principal de serviço 

Crie um princípio de serviço em seu locatário Azure Active Directory usando as instruções em [usar o portal para criar um aplicativo Azure Active Directory e uma entidade de serviço que possa acessar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Observe o seguinte enquanto você está passando por esse processo: 

- Você pode colocar em qualquer URL para a URL de entrada.  
- Crie um novo segredo do cliente para este aplicativo.  
- Salve a chave e a ID do cliente para uso em etapas posteriores.  

Forneça ao aplicativo criado na etapa anterior o monitoramento de permissões de *Editor de métricas* para o recurso do qual você deseja emitir métricas. Se você planeja usar o aplicativo para emitir métricas personalizadas em vários recursos, você pode conceder essas permissões no nível do grupo de recursos ou da assinatura.  

> [!NOTE]
> A extensão de diagnóstico usa a entidade de serviço para se autenticar em Azure Monitor e emitir métricas para seu serviço de nuvem.

## <a name="author-diagnostics-extension-configuration"></a>Configuração de extensão de diagnóstico de autor 

Prepare o arquivo de configuração da extensão de diagnóstico. Esse arquivo determina quais logs e contadores de desempenho a extensão de diagnóstico deve coletar para seu serviço de nuvem. A seguir está um arquivo de configuração de diagnóstico de exemplo:  

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

Na seção "SinksConfig" do arquivo de diagnóstico, defina um novo coletor de Azure Monitor: 

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

Na seção do arquivo de configuração em que você lista os contadores de desempenho a serem coletados, adicione o coletor de Azure Monitor. Essa entrada garante que todos os contadores de desempenho especificados sejam roteados para Azure Monitor como métricas. Você pode adicionar ou remover contadores de desempenho de acordo com suas necessidades. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Por fim, na seção configuração privada, adicione uma *conta de Azure monitor* . Insira a ID do cliente da entidade de serviço e o segredo que você criou anteriormente. 

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

Salve esse arquivo de diagnóstico localmente.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implantar a extensão de diagnóstico em seu serviço de nuvem 

Inicie o PowerShell e faça logon no Azure. 

```powershell
Login-AzAccount 
```

Use os comandos a seguir para armazenar os detalhes da conta de armazenamento que você criou anteriormente. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Da mesma forma, defina o caminho do arquivo de diagnóstico para uma variável usando o seguinte comando:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Implante a extensão de diagnóstico em seu serviço de nuvem com o arquivo de diagnóstico com o coletor de Azure Monitor configurado usando o seguinte comando:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Ainda é obrigatório fornecer uma conta de armazenamento como parte da instalação da extensão de diagnóstico. Todos os logs ou contadores de desempenho especificados no arquivo de configuração de diagnóstico são gravados na conta de armazenamento especificada.  

## <a name="plot-metrics-in-the-azure-portal"></a>Plotar métricas no portal do Azure 

1. Aceda ao portal do Azure. 

   ![Métricas portal do Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. No menu à esquerda, selecione **Monitor.**

3. Na folha **Monitor** , selecione a guia **visualização de métricas** .

4. No menu suspenso recursos, selecione seu serviço de nuvem clássico.

5. No menu suspenso namespaces, selecione **Azure. VM. Windows. Guest**. 

6. No menu suspenso métricas, selecione **confirmados bytes em uso**. 

Você usa os recursos de filtragem e divisão de dimensão para exibir a memória total usada por uma função ou instância de função específica. 

 ![Métricas portal do Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

