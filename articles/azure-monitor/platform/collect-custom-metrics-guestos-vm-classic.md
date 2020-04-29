---
title: Envie métricas vM clássicas do Windows para base de dados de métricas do Monitor Azure
description: Envie métricas de OS do Hóspede para a loja de dados Azure Monitor para uma máquina virtual do Windows (clássica)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 65bb1a3915ece384974da12b4e7a1ad0c1e08133
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655822"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Envie métricas de OS do Hóspede para a base de dados de métricas do Monitor Azure para uma máquina virtual do Windows (clássica)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A [extensão](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) de Diagnóstico do Monitor Azure (conhecida como "WAD" ou "Diagnósticos") permite-lhe recolher métricas e registos do sistema operativo convidado (Os convidados) que funciona como parte de uma máquina virtual, serviço em nuvem ou cluster de Tecido de Serviço. A extensão pode enviar telemetria para [muitos locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo de envio de métricas de desempenho do Guest OS para uma máquina virtual do Windows (clássica) para a base de dados métrica do Monitor Azure. A partir da versão 1.11 do Diagnostics, pode escrever métricas diretamente na loja de métricas Do Monitor Do Azure, onde as métricas padrão da plataforma já são recolhidas. 

Guardá-los neste local permite-lhe aceder às mesmas ações que tem para métricas de plataforma. As ações incluem alerta em tempo quase real, gráficos, encaminhamento, acesso a partir de uma API REST, e muito mais. No passado, a extensão de Diagnóstico supor ao Armazenamento Azure, mas não à loja de dados Azure Monitor. 

O processo que está delineado neste artigo funciona apenas em máquinas virtuais clássicas que estão a executar o sistema operativo Windows.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ser [administrador de serviçoou coadministrador](../../cost-management-billing/manage/add-change-subscription-administrator.md) na sua subscrição Azure. 

- A sua subscrição deve ser registada na [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- É necessário instalar o [Azure PowerShell](/powershell/azure) ou o [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- O seu recurso VM deve estar numa [região que suporte métricas personalizadas.](metrics-custom-overview.md#supported-regions)

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Crie uma conta clássica de máquina virtual e armazenamento

1. Crie um VM clássico utilizando o portal Azure.
   ![Criar VM clássico](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Quando estiver a criar este VM, escolha a opção de criar uma nova conta de armazenamento clássica. Usamos esta conta de armazenamento em etapas posteriores.

1. No portal Azure, vá ao recurso das contas de **armazenamento.** Selecione **Keys**e tome nota do nome da conta de armazenamento e da chave da conta de armazenamento. Precisa desta informação em passos posteriores.
   ![Chaves de acesso ao armazenamento](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Crie um princípio de serviço no seu inquilino de Diretório Ativo Azure utilizando as instruções da [Create a Service principal](../../active-directory/develop/howto-create-service-principal-portal.md). Note o seguinte durante este processo: 
- Crie um novo segredo de cliente para esta aplicação.
- Guarde a chave e o ID do cliente para utilização em etapas posteriores.

Dê a esta aplicação permissões "Monitoring Metrics Publisher" para o recurso contra o que pretende emitir métricas. Pode utilizar um grupo de recursos ou uma subscrição inteira.  

> [!NOTE]
> A extensão de Diagnóstico utiliza o diretor de serviço para autenticar contra o Monitor Azure e emitir métricas para o seu VM clássico.

## <a name="author-diagnostics-extension-configuration"></a>Configuração de extensão de diagnóstico de autor

1. Prepare o ficheiro de configuração de extensão de Diagnóstico. Este ficheiro dita quais os registos e contadores de desempenho que a extensão de Diagnóstico deve recolher para o seu VM clássico. Segue um exemplo:

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
1. Na secção "SinksConfig" do seu ficheiro de diagnóstico, defina um novo lavatório Do Monitor Azure, da seguinte forma:

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

1. Na secção do seu ficheiro de configuração onde está listada a lista dos contadores de desempenho a recolher, encaminhe os contadores de desempenho para o lavatório do Monitor Azure "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Na configuração privada, defina a conta Azure Monitor. Em seguida, adicione a informação principal do serviço para usar para emitir métricas.

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

1. Guarde este ficheiro localmente.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implemente a extensão de Diagnósticos ao seu serviço na nuvem

1. Lançar PowerShell e iniciar sessão.

    ```powershell
    Login-AzAccount
    ```

1. Comece por definir o contexto para o seu VM clássico.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Delineie o contexto da conta de armazenamento clássica que foi criada com o VM.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Desloque o caminho do ficheiro Diagnostics para uma variável utilizando o seguinte comando:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Prepare a atualização para o seu VM clássico com o ficheiro de diagnóstico que tem o lavatório Azure Monitor configurado.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Desloque a atualização para o seu VM executando o seguinte comando:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> É ainda obrigatório fornecer uma conta de armazenamento como parte da instalação da extensão de Diagnósticos. Quaisquer registos ou contadores de desempenho especificados no ficheiro De diagnóstico de config serão escritos na conta de armazenamento especificada.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Traçar as métricas no portal Azure

1.  Aceda ao portal do Azure. 

1.  No menu esquerdo, selecione **Monitor.**

1.  Na lâmina **monitora,** selecione **Métricas**.

    ![Navegar métricas](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. No menu de drop-down de recursos, selecione o seu VM clássico.

1. No menu de lançamento dos espaços de nome, **selecione azure.vm.windows.guest**.

1. No menu de entrega de métricas, selecione **Memory\Committed Bytes in Use**.
   ![Métricas do enredo](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](metrics-custom-overview.md)

