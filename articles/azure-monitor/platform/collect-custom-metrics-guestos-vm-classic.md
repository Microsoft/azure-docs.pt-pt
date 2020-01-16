---
title: Enviar métricas de VM do Windows clássicas para Azure Monitor banco de dados de métricas
description: Enviar métricas do sistema operacional convidado para o armazenamento de dados Azure Monitor para uma máquina virtual do Windows (clássica)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: d911a6b65ba0d63bca09437f6451019addcbc0b3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977626"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Enviar métricas do sistema operacional convidado para o banco de dados de métricas de Azure Monitor para uma máquina virtual do Windows (clássica)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A [extensão de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) de Azure monitor (conhecida como "wad" ou "diagnóstico") permite coletar métricas e logs do sistema operacional convidado (SO convidado) em execução como parte de uma máquina virtual, serviço de nuvem ou cluster de Service Fabric. A extensão pode enviar telemetria para [vários locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado para uma máquina virtual do Windows (clássica) para o banco de dados de métricas Azure Monitor. A partir da versão de diagnóstico 1,11, você pode gravar métricas diretamente no repositório de métricas de Azure Monitor, em que as métricas de plataforma padrão já são coletadas. 

Armazená-los nesse local permite que você acesse as mesmas ações que você faz para métricas de plataforma. As ações incluem alertas quase em tempo real, criação de gráficos, roteamento, acesso de uma API REST e muito mais. No passado, a extensão de diagnóstico gravou no armazenamento do Azure, mas não no armazenamento de dados Azure Monitor. 

O processo descrito neste artigo só funciona em máquinas virtuais clássicas que executam o sistema operacional Windows.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um [administrador de serviços ou coadministrador](../../cost-management-billing/manage/add-change-subscription-administrator.md) em sua assinatura do Azure. 

- Sua assinatura deve ser registrada com [Microsoft. insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Você precisa ter o [Azure PowerShell](/powershell/azure) ou [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.

- O recurso da VM deve estar em uma [região que ofereça suporte a métricas personalizadas](metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Criar uma máquina virtual clássica e uma conta de armazenamento

1. Crie uma VM clássica usando o portal do Azure.
   ![criar uma VM clássica](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Quando você estiver criando essa VM, escolha a opção para criar uma nova conta de armazenamento clássico. Usamos essa conta de armazenamento em etapas posteriores.

1. Na portal do Azure, vá para a folha de recursos de **contas de armazenamento** . Selecione **chaves**e anote o nome da conta de armazenamento e a chave da conta de armazenamento. Você precisa dessas informações em etapas posteriores.
   ![chaves de acesso de armazenamento](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Crie um princípio de serviço em seu locatário Azure Active Directory usando as instruções em [criar uma entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md). Observe o seguinte ao passar pelo processo: 
- Crie um novo segredo do cliente para este aplicativo.
- Salve a chave e a ID do cliente para uso em etapas posteriores.

Conceda a esse aplicativo as permissões de "editor de métricas de monitoramento" ao recurso do qual você deseja emitir métricas. Você pode usar um grupo de recursos ou uma assinatura inteira.  

> [!NOTE]
> A extensão de diagnóstico usa a entidade de serviço para se autenticar em Azure Monitor e emitir métricas para sua VM clássica.

## <a name="author-diagnostics-extension-configuration"></a>Configuração de extensão de diagnóstico de autor

1. Prepare o arquivo de configuração da extensão de diagnóstico. Esse arquivo determina quais logs e contadores de desempenho a extensão de diagnóstico deve coletar para sua VM clássica. Segue um exemplo:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
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
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
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
1. Na seção "SinksConfig" do arquivo de diagnóstico, defina um novo coletor de Azure Monitor, da seguinte maneira:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Na seção do arquivo de configuração em que a lista de contadores de desempenho a serem coletados está listada, encaminhe os contadores de desempenho para o coletor de Azure Monitor "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Na configuração privada, defina a conta de Azure Monitor. Em seguida, adicione as informações da entidade de serviço a serem usadas para emitir métricas.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Salve esse arquivo localmente.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implantar a extensão de diagnóstico em seu serviço de nuvem

1. Inicie o PowerShell e entre.

    ```powershell
    Login-AzAccount
    ```

1. Comece definindo o contexto para sua VM clássica.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Defina o contexto da conta de armazenamento clássica que foi criada com a VM.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Defina o caminho do arquivo de diagnóstico para uma variável usando o seguinte comando:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Prepare a atualização para sua VM clássica com o arquivo de diagnóstico que tem o coletor de Azure Monitor configurado.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Implante a atualização em sua VM executando o seguinte comando:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Ainda é obrigatório fornecer uma conta de armazenamento como parte da instalação da extensão de diagnóstico. Todos os logs ou contadores de desempenho especificados no arquivo de configuração de diagnóstico serão gravados na conta de armazenamento especificada.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Plotar as métricas no portal do Azure

1.  Aceda ao portal do Azure. 

1.  No menu à esquerda, selecione **Monitor.**

1.  Na folha **Monitor** , selecione **métricas**.

    ![Navegar pelas métricas](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. No menu suspenso recursos, selecione sua VM clássica.

1. No menu suspenso namespaces, selecione **Azure. VM. Windows. Guest**.

1. No menu suspenso métricas, selecione **confirmados bytes em uso**.
   ![](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png) métricas de plotagem


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

