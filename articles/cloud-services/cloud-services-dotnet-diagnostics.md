---
title: Como usar o diagnóstico do Azure (.NET) com os serviços de nuvem | Microsoft Docs
description: Usando o diagnóstico do Azure para coletar dados dos serviços de nuvem do Azure para depuração, medição de desempenho, monitoramento, análise de tráfego e muito mais.
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: tagore
ms.openlocfilehash: d5a4e5ce40726ea36734a0dcf751b79225d5e153
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75361118"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Habilitando Diagnóstico do Azure nos serviços de nuvem do Azure
Confira [diagnóstico do Azure visão geral](../azure-diagnostics.md) de um plano de fundo em diagnóstico do Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Como habilitar o diagnóstico em uma função de trabalho
Este tutorial descreve como implementar uma função de trabalho do Azure que emite dados de telemetria usando a classe EventSource do .NET. Diagnóstico do Azure é usado para coletar os dados de telemetria e armazená-los em uma conta de armazenamento do Azure. Ao criar uma função de trabalho, o Visual Studio habilita automaticamente o diagnóstico 1,0 como parte da solução nos SDKs do Azure para .NET 2,4 e versões anteriores. As instruções a seguir descrevem o processo de criação da função de trabalho, a desabilitação do diagnóstico 1,0 da solução e a implantação de diagnóstico 1,2 ou 1,3 em sua função de trabalho.

### <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha uma assinatura do Azure e esteja usando o Visual Studio com o SDK do Azure. Se você não tiver uma assinatura do Azure, poderá se inscrever para a [avaliação gratuita][Free Trial]. Certifique-se de [instalar e configurar Azure PowerShell versão 0.8.7 ou posterior][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Etapa 1: criar uma função de trabalho
1. Inicie o **Visual Studio**.
2. Crie um projeto de **serviço de nuvem do Azure** a partir do modelo de **nuvem** que tem como destino .NET Framework 4,5.  Nomeie o projeto "WadExample" e clique em OK.
3. Selecione **função de trabalho** e clique em OK. O projeto será criado.
4. Em **Gerenciador de soluções**, clique duas vezes no arquivo de propriedades **WorkerRole1** .
5. Na guia **configuração** , desmarque **habilitar diagnóstico** para desabilitar o diagnóstico 1,0 (SDK 2,4 do Azure e anterior).
6. Crie sua solução para verificar se você não tem erros.

### <a name="step-2-instrument-your-code"></a>Etapa 2: instrumentar seu código
Substitua o conteúdo de WorkerRole.cs pelo código a seguir. A classe SampleEventSourceWriter, herdada da [classe EventSource][EventSource Class], implementa quatro métodos de registro em log: **SendEnums**, **MessageMethod**, **Other** e **HighFreq**. O primeiro parâmetro para o método **WriteEvent** define a ID do respectivo evento. O método Run implementa um loop infinito que chama cada um dos métodos de registro em log implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Etapa 3: implantar sua função de trabalho

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Implante sua função de trabalho no Azure de dentro do Visual Studio selecionando o projeto **WadExample** no Gerenciador de soluções, em seguida, **publicar** no menu **Compilar** .
2. Escolha a sua subscrição.
3. Na caixa de diálogo **Microsoft Azure configurações de publicação** , selecione **criar novo...** .
4. Na caixa de diálogo **Criar serviço de nuvem e conta de armazenamento** , insira um **nome** (por exemplo, "WadExample") e selecione uma região ou grupo de afinidade.
5. Defina o **ambiente** como **preparo**.
6. Modifique todas **as outras configurações** conforme apropriado e clique em **publicar**.
7. Após a conclusão da implantação, verifique na portal do Azure se o serviço de nuvem está em estado de **execução** .

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Etapa 4: criar seu arquivo de configuração de diagnóstico e instalar a extensão
1. Baixe a definição de esquema do arquivo de configuração pública executando o seguinte comando do PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Adicione um arquivo XML ao projeto **WorkerRole1** clicando com o botão direito do mouse no projeto **WorkerRole1** e selecione **Adicionar** -> **novo item...** -> **itens C# visuais** -> **dados** -> **arquivo XML**. Nomeie o arquivo "WadExample. xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associe o WadConfig. xsd ao arquivo de configuração. Verifique se a janela do editor WadExample. xml é a janela ativa. Pressione **F4** para abrir a janela **Propriedades** . Clique na propriedade **esquemas** na janela **Propriedades** . Clique em **...** na propriedade **esquemas** . Clique no botão **Adicionar…** e navegue até o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig. xsd. Clique em **OK**.

4. Substitua o conteúdo do arquivo de configuração WadExample. xml pelo seguinte XML e salve o arquivo. Este arquivo de configuração define alguns contadores de desempenho para coletar: um para utilização da CPU e outro para utilização de memória. Em seguida, a configuração define os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Etapa 5: instalar o diagnóstico em sua função de trabalho
Os cmdlets do PowerShell para gerenciar o diagnóstico em uma função Web ou de trabalho são: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e remove-AzureServiceDiagnosticsExtension.

1. Abra Azure PowerShell.
2. Execute o script para instalar o diagnóstico em sua função de trabalho (substitua *StorageAccountKey* pela chave de conta de armazenamento para sua conta de armazenamento wadexample e *config_path* pelo caminho para o arquivo *wadexample. xml* ):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Etapa 6: examinar seus dados de telemetria
No **Gerenciador de servidores**do Visual Studio, navegue até a conta de armazenamento wadexample. Depois que o serviço de nuvem estiver em execução cerca de cinco (5) minutos, você deverá ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Esquema do arquivo de configuração
O arquivo de configuração de diagnóstico define os valores que são usados para inicializar as definições de configuração de diagnóstico quando o agente de diagnóstico é iniciado. Consulte a [referência de esquema mais recente](/azure/azure-monitor/platform/diagnostics-extension-schema) para obter valores e exemplos válidos.

## <a name="troubleshooting"></a>Resolução de problemas
Se você tiver problemas, consulte [solução de problemas diagnóstico do Azure](../azure-diagnostics-troubleshooting.md) para obter ajuda com problemas comuns.

## <a name="next-steps"></a>Próximos Passos
[Consulte uma lista de artigos de diagnóstico de máquina virtual do Azure relacionados](../azure-monitor/platform/diagnostics-extension-overview.md#cloud-services-using-azure-diagnostics) para alterar os dados que você está coletando, solucionar problemas ou saber mais sobre diagnósticos em geral.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/



