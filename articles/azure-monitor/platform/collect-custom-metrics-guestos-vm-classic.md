---
title: Enviar métricas de SO convidado para o arquivo de dados do Azure Monitor para uma máquina virtual de Windows (clássico)
description: Enviar métricas de SO convidado para o arquivo de dados do Azure Monitor para uma máquina virtual de Windows (clássico)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 57212da1a8da7ee6c57faf2413b88a413df04817
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315134"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Enviar métricas de SO convidado para o arquivo de dados do Azure Monitor para uma máquina virtual de Windows (clássico)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O Azure Monitor [extensão de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (também conhecida como "WAD" ou "Diagnóstico") permite-lhe recolher métricas e registos do sistema operativo convidado (SO convidado) em execução como parte de uma máquina virtual, o serviço em nuvem ou o Service Fabric cluster. A extensão pode enviar telemetria para [vários locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo para o envio de métricas de desempenho do SO convidado para uma máquina virtual do Windows (clássico) para o arquivo de métrica do Azure Monitor. A partir do diagnóstico versão 1.11, pode escrever métricas diretamente para o Azure Monitor armazenam métricas, onde as métricas de plataforma padrão já são recolhidas. 

Armazená-los nesta localização permite que acesse as mesmas ações, tal como sucede para métricas de plataforma. As ações incluem quase em tempo real alerta, a criação de gráficos, encaminhamento, acesso a partir de uma API REST e muito mais. No passado, a extensão de diagnóstico escreveu para o armazenamento do Azure, mas não para o arquivo de dados do Azure Monitor. 

O processo que é descrito neste artigo só funciona em máquinas virtuais clássicas que estejam a executar o sistema operativo do Windows.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ser um [administrador de serviços ou coadministrador](../../billing/billing-add-change-azure-subscription-administrator.md) na sua subscrição do Azure. 

- A sua subscrição tem de estar registrada com [Microsoft. insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Tem de ter [do Azure PowerShell](/powershell/azure) ou [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Criar uma máquina virtual clássica e uma conta de armazenamento

1. Crie uma VM clássica com o portal do Azure.
   ![Criar VM clássica](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Quando estiver a criar esta VM, escolha a opção para criar uma nova conta de armazenamento clássicas. Utilizamos esta conta de armazenamento em passos posteriores.

1. No portal do Azure, vá para o **contas de armazenamento** painel de recursos. Selecione **chaves**e anote o nome da conta de armazenamento e a chave da conta de armazenamento. Precisa essas informações em passos posteriores.
   ![Chaves de acesso de armazenamento](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Criar um principal de serviço no seu inquilino do Azure Active Directory com as instruções em [criar um principal de serviço](../../active-directory/develop/howto-create-service-principal-portal.md). Tenha em atenção o seguinte ao percorrer este processo: 
- Crie novo segredo do cliente para esta aplicação.
- Guarde a chave e o ID de cliente para uso em passos posteriores.

Conceda esta aplicação permissões de "Editor de métricas de monitorização" para o recurso que pretende emitir métricas relativamente a. Pode utilizar um grupo de recursos ou uma subscrição completa.  

> [!NOTE]
> A extensão de diagnóstico utiliza o principal de serviço para autenticar no Azure Monitor e emite métricas para a sua VM clássica.

## <a name="author-diagnostics-extension-configuration"></a>Configuração de extensão de diagnóstico do autor

1. Prepare o ficheiro de configuração de extensão de diagnóstico. Este ficheiro determina quais os registos e contadores de desempenho a extensão de diagnóstico deve recolher para a sua VM clássica. Segue-se um exemplo:

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
1. Na secção "SinksConfig" do seu ficheiro de diagnóstico, defina um novo sink do Azure Monitor, da seguinte forma:

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

1. Na secção do ficheiro de configuração, onde a lista dos contadores de desempenho a recolher está listada, encaminhe os contadores de desempenho para o sink do Azure Monitor "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. A configuração privada, defina a conta do Azure Monitor. Em seguida, adicione as informações do principal de serviço a utilizar para emitir métricas.

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implementar a extensão de diagnóstico ao seu serviço cloud

1. Inicie o PowerShell e inicie sessão.

    ```powershell
    Login-AzAccount
    ```

1. Comece por definir o contexto para a sua VM clássica.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Defina o contexto da conta de armazenamento clássicas que foi criado com a VM.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Defina o caminho do ficheiro de diagnóstico para uma variável com o seguinte comando:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Prepare a atualização para a sua VM clássica com o ficheiro de diagnóstico que tem o sink do Azure Monitor configurado.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Implantar a atualização para a VM ao executar o seguinte comando:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> É ainda obrigatório para fornecer uma conta de armazenamento como parte da instalação da extensão do diagnóstico. Quaisquer registos ou contadores de desempenho que são especificados no ficheiro de configuração de diagnósticos de escrita para a conta de armazenamento especificada.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Desenhar as métricas no portal do Azure

1.  Aceda ao portal do Azure. 

1.  No menu da esquerda, selecione **Monitor.**

1.  Sobre o **Monitor** painel, selecione **métricas**.

    ![Navegue de métricas](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. No menu de lista pendente de recursos, selecione a sua VM clássica.

1. No menu de lista pendente de espaços de nomes, selecione **azure.vm.windows.guest**.

1. No menu de lista pendente de métricas, selecione **Memory\Committed Bytes em utilização**.
   ![Gráfico de métricas](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

