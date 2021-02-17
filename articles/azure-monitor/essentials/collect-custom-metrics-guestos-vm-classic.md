---
title: Envie métricas clássicas do Windows VM para a base de dados de métricas do Azure Monitor
description: Envie métricas de Clientes OS para a loja de dados do Azure Monitor para uma máquina virtual Windows (clássica)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 48c7323a630f5c79c7ecb16e26c9ec79ec2014a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613338"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Envie métricas de OS para a base de dados de métricas do Azure Monitor para uma máquina virtual do Windows (clássica)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A extensão Azure Monitor [Diagnostics](../agents/diagnostics-extension-overview.md) (conhecida como "WAD" ou "Diagnostics") permite-lhe recolher métricas e registos do sistema operativo convidado (Guest OS) em funcionamento como parte de uma máquina virtual, serviço de nuvem ou cluster de Tecido de Serviço. A extensão pode enviar telemetria para [vários locais diferentes.](../platform/data-platform.md?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo de envio de métricas de desempenho do Guest OS para uma máquina virtual do Windows (clássica) para a base de dados métrica do Azure Monitor. A partir da versão 1.11 do Diagnostics, pode escrever métricas diretamente na loja de métricas do Azure Monitor, onde já são recolhidas métricas padrão da plataforma. 

Guardá-los neste local permite-lhe aceder às mesmas ações que tem para as métricas da plataforma. As ações incluem alerta em tempo real, gráfico, encaminhamento, acesso a partir de uma API REST, e muito mais. No passado, a extensão de Diagnóstico escreveu ao Azure Storage, mas não à loja de dados do Azure Monitor. 

O processo que está delineado neste artigo funciona apenas em máquinas virtuais clássicas que estão a executar o sistema operativo Windows.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ser [administrador de serviço ou coadministrador](../../cost-management-billing/manage/add-change-subscription-administrator.md) na sua assinatura Azure. 

- A sua subscrição deve estar registada no [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Precisa de ter [a Azure PowerShell](/powershell/azure) ou [a Azure Cloud Shell](../../cloud-shell/overview.md) instaladas.

- O seu recurso VM deve estar numa [região que suporte métricas personalizadas.](../platform/metrics-custom-overview.md#supported-regions)

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Crie uma clássica caixa de máquinas virtuais e uma conta de armazenamento

1. Crie um VM clássico utilizando o portal Azure.
   ![Criar VM Clássico](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Quando estiver a criar este VM, escolha a opção de criar uma nova conta de armazenamento clássico. Usamos esta conta de armazenamento em etapas posteriores.

1. No portal Azure, aceda à lâmina de recursos das **contas de Armazenamento.** Selecione **Keys**, e tome nota do nome da conta de armazenamento e da chave da conta de armazenamento. Precisa desta informação em etapas posteriores.
   ![Chaves de acesso ao armazenamento](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Crie um diretor de serviço no seu inquilino Azure Ative Directory utilizando as instruções da [Create a service principal.](../../active-directory/develop/howto-create-service-principal-portal.md) Note o seguinte durante este processo: 
- Crie um novo segredo de cliente para esta aplicação.
- Guarde a chave e o ID do cliente para utilização em etapas posteriores.

Dê a esta aplicação permissões "Monitoring Metrics Publisher" ao recurso contra o quais pretende emitir métricas. Pode utilizar um grupo de recursos ou uma subscrição completa.  

> [!NOTE]
> A extensão de Diagnóstico utiliza o principal de serviço para autenticar contra o Azure Monitor e emitir métricas para o seu VM clássico.

## <a name="author-diagnostics-extension-configuration"></a>Configuração da extensão de Diagnóstico de Autor

1. Prepare o seu ficheiro de configuração de extensão de Diagnóstico. Este ficheiro dita quais os registos e os contadores de desempenho que a extensão de Diagnóstico deve recolher para o seu VM clássico. Segue um exemplo:

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
1. Na secção "SinksConfig" do seu ficheiro de diagnóstico, defina uma nova pia do Azure Monitor, da seguinte forma:

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

1. Na secção do seu ficheiro de configuração onde está listada a lista de contadores de desempenho a recolher, encaminhe os contadores de desempenho para o lavatório "AzMonSink" do Monitor Azure.

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implemente a extensão de Diagnóstico para o seu serviço na nuvem

1. Lançar PowerShell e iniciar s.a.

    ```powershell
    Login-AzAccount
    ```

1. Comece por definir o contexto para o seu VM clássico.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Desajuste o contexto da conta de armazenamento clássica que foi criada com o VM.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Desacorda o percurso do ficheiro De diagnóstico para uma variável utilizando o seguinte comando:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Prepare a atualização para o seu VM clássico com o ficheiro de diagnóstico que tem a pia do Azure Monitor configurada.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Implemente a atualização para o seu VM executando o seguinte comando:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> É ainda obrigatório fornecer uma conta de armazenamento como parte da instalação da extensão de Diagnóstico. Quaisquer registos ou contadores de desempenho especificados no ficheiro Diagnóstico config serão escritos na conta de armazenamento especificada.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Traçar as métricas no portal Azure

1.  Aceda ao portal do Azure. 

1.  No menu esquerdo, selecione **Monitor.**

1.  Na lâmina **monitor,** selecione **Métricas**.

    ![Navegar métricas](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. No menu de entrega de recursos, selecione o seu VM clássico.

1. No menu drop-down dos espaços de nome, selecione **azure.vm.windows.guest**.

1. No menu suspenso de métricas, selecione **Memory\Committed Bytes in Use**.
   ![Métricas de enredo](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](../platform/metrics-custom-overview.md)
