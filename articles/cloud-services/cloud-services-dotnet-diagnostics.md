---
title: Como utilizar o diagnóstico do Azure (.NET) com os serviços Cloud | Documentos da Microsoft
description: Utilizar o diagnóstico do Azure para recolher dados de serviços cloud do Azure para depuração, medir o desempenho, monitorização, análise de tráfego e muito mais.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: jeconnoc
ms.openlocfilehash: 017461d5b73bb48ce61678fbbd795026cef60bc1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536855"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Ativar diagnósticos do Azure nos serviços Cloud do Azure
Ver [descrição geral de diagnóstico do Azure](../azure-diagnostics.md) para um plano de fundo do diagnóstico do Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Como ativar o diagnóstico numa função de trabalho
Este passo a passo descreve como implementar uma função de trabalho do Azure que emite dados de telemetria usando a classe do .NET EventSource. Diagnóstico do Azure é utilizado para recolher os dados de telemetria e o armazenamos numa conta de armazenamento do Azure. Ao criar uma função de trabalho, o Visual Studio permite automaticamente Diagnostics 1.0 como parte da solução no Azure SDK para .NET 2.4 e versões anteriores. As instruções seguintes descrevem o processo de criação da função de trabalho, desabilitar Diagnostics 1.0 da solução e implementação diagnóstico 1.2 ou 1.3 à sua função de trabalho.

### <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que uma subscrição do Azure e estiver a utilizar o Visual Studio com o SDK do Azure. Se não tiver uma subscrição do Azure, pode inscrever-se para o [avaliação gratuita][Free Trial]. Certifique-se de que [instalar e configurar o Azure PowerShell versão 0.8.7 ou mais tarde][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Passo 1: Criar uma função de trabalho
1. Inicie o **Visual Studio**.
2. Criar uma **serviço Cloud do Azure** projeto a **Cloud** modelo que tenha como destino o .NET Framework 4.5.  Nomeie o projeto "WadExample" e clique em Ok.
3. Selecione **função de trabalho** e clique em Ok. O projeto será criado.
4. Na **Explorador de soluções**, clique duas vezes o **WorkerRole1** arquivo de propriedades.
5. Na **Configuration** separador, desmarque **ativar diagnósticos** para desativar Diagnostics 1.0 (Azure SDK 2.4 e anterior).
6. Crie a sua solução para verificar se tem sem erros.

### <a name="step-2-instrument-your-code"></a>Passo 2: Instrumentar o seu código
Substitua o conteúdo do WorkerRole.cs com o código a seguir. A classe SampleEventSourceWriter, herdada a partir do [EventSource classe][EventSource Class], implementa quatro métodos de registo: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o **WriteEvent** método define o ID para o respectivo evento. O método Run implementa um loop infinito que chama cada um dos métodos implementados no Registro em log os **SampleEventSourceWriter** classe cada 10 segundos.

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


### <a name="step-3-deploy-your-worker-role"></a>Passo 3: Implementar a sua função de trabalho

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Implementar função de trabalho no Azure a partir do Visual Studio, selecionando o **WadExample** projeto no Solution Explorer, em seguida, **publicar** partir o **criar** menu.
2. Escolha a sua subscrição.
3. Na **definições de publicação do Microsoft Azure** caixa de diálogo, selecione **criar novo...** .
4. Na **criar serviço de nuvem e de conta de armazenamento** caixa de diálogo, introduza um **nome** (por exemplo, "WadExample") e selecione uma região ou o grupo de afinidade.
5. Definir o **ambiente** ao **teste**.
6. Modificar qualquer outro **configurações** conforme adequado e clique em **Publish**.
7. Depois de concluída a implementação, certifique-se no portal do Azure que seu serviço cloud está numa **em execução** estado.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Passo 4: Crie o seu ficheiro de configuração de diagnósticos e instalar a extensão
1. Transferir a definição de esquema do ficheiro de configuração pública ao executar o seguinte comando do PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Adicionar um arquivo XML para seus **WorkerRole1** projeto clicando no **WorkerRole1** do projeto e selecione **Add** -> **Novo Item...** -> **Visual c# itens** -> **dados** -> **arquivo XML**. Nomeie o arquivo "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associe o WadConfig.xsd o ficheiro de configuração. Certifique-se a janela do editor WadExample.xml a janela ativa. Prima **F4** para abrir o **propriedades** janela. Clique nas **esquemas** propriedade na **propriedades** janela. Clique no **...** na **esquemas** propriedade. Clique no botão **Adicionar…** botão e navegue para a localização onde guardou o ficheiro XSD e selecione o ficheiro WadConfig.xsd. Clique em **OK**.

4. Substitua os conteúdos do ficheiro de configuração WadExample.xml com o seguinte XML e guarde o ficheiro. Este ficheiro de configuração define alguns contadores de desempenho a recolher: um para utilização da CPU e outro para utilização da memória. Em seguida, a configuração define quatro eventos correspondentes para os métodos na classe SampleEventSourceWriter.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Passo 5: Instalar o diagnóstico na sua função de trabalho
Os cmdlets do PowerShell para o gerenciamento de diagnósticos numa função web ou de trabalho são: Conjunto AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1. Abra o PowerShell do Azure.
2. Executar o script para instalar o diagnóstico na sua função de trabalho (substitua *StorageAccountKey* com a chave de conta de armazenamento para a sua conta de armazenamento wadexample e *config_path* com o caminho para o  *WadExample.xml* ficheiros):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Passo 6: Examinar os dados de telemetria
No Visual Studio **Explorador de servidores**, navegue para a conta de armazenamento wadexample. Depois do serviço em nuvem está em execução cerca de cinco (5) minutos, deverá ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para ver a telemetria que tenha sido recolhida.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Esquema de ficheiro de configuração
O ficheiro de configuração de diagnósticos define os valores que são usados para inicializar as definições de diagnóstico de configuração quando o agente de diagnóstico é iniciado. Consulte a [referência de esquema mais recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para valores válidos e exemplos.

## <a name="troubleshooting"></a>Resolução de problemas
Se tiver problemas, consulte [resolução de problemas do diagnóstico do Azure](../azure-diagnostics-troubleshooting.md) para obter ajuda com problemas comuns.

## <a name="next-steps"></a>Próximos Passos
[Ver uma lista de relacionados artigos de diagnóstico de máquinas virtuais do Azure](../azure-monitor/platform/diagnostics-extension-overview.md#cloud-services-using-azure-diagnostics) para alterar os dados que está a recolher, resolução de problemas ou Saiba mais sobre diagnósticos em geral.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/
