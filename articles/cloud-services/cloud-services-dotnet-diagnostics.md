---
title: Como utilizar diagnósticos Azure (.NET) com Serviços cloud / Microsoft Docs
description: Utilizando diagnósticos Azure para recolher dados dos Serviços de nuvem Azure para depuração, medição do desempenho, monitorização, análise de tráfego, entre outros.
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: tagore
ms.openlocfilehash: 1e49a0935a70a2470267e5458fa1f55e3059e965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469770"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Habilitar diagnósticos azure em serviços de nuvem azure
Consulte a visão geral do [Azure Diagnostics](../azure-diagnostics.md) para obter um fundo sobre o Azure Diagnostics.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Como ativar diagnósticos em um papel de trabalhador
Este walkthrough descreve como implementar uma função de trabalhador Azure que emite dados de telemetria usando a classe .NET EventSource. O Azure Diagnostics é utilizado para recolher os dados da telemetria e armazená-lo numa conta de armazenamento Azure. Ao criar uma função de trabalhador, o Visual Studio permite automaticamente o Diagnóstico 1.0 como parte da solução em SDKs Azure para .NET 2.4 e anterior. As seguintes instruções descrevem o processo de criação da função do trabalhador, desativando diagnósticos 1.0 da solução e implantando diagnósticos 1.2 ou 1.3 para o seu papel de trabalhador.

### <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem uma subscrição Azure e está a usar o Estúdio Visual com o Azure SDK. Se não tiver uma subscrição Azure, pode inscrever-se no [Free Trial][Free Trial]. Certifique-se de instalar e configurar a [versão Azure PowerShell 0.8.7 ou posterior][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Passo 1: Criar um papel de trabalhador
1. Inicie o **Visual Studio**.
2. Crie um projeto **de Serviço de Nuvem Azure** a partir do modelo **Cloud** que visa .NET Framework 4.5.  Nomeie o projeto "WadExample" e clique em Ok.
3. Selecione **Função de Trabalhador** e clique em Ok. O projeto será criado.
4. No **Solution Explorer,** clique duas vezes no ficheiro de propriedades **do WorkerRole1.**
5. No separador **Configuração,** desverifique **o Enable Diagnostics** para desativar os diagnósticos 1.0 (Azure SDK 2.4 e anterior).
6. Construa a sua solução para verificar se não tem erros.

### <a name="step-2-instrument-your-code"></a>Passo 2: Instrumente o seu código
Substitua o conteúdo do WorkerRole.cs pelo seguinte código. A classe SampleEventSourceWriter, herdada da [Classe EventSource,][EventSource Class]implementa quatro métodos de exploração de madeira: **SendEnums,** **MessageMethod,** **SetOther** e **HighFreq**. O primeiro parâmetro do método **WriteEvent** define o ID para o respetivo evento. O método Run implementa um ciclo infinito que chama cada um dos métodos de registo implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

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


### <a name="step-3-deploy-your-worker-role"></a>Passo 3: Implementar a sua função de trabalhador

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Implemente o seu papel de trabalhador para o Azure a partir do Visual Studio selecionando o projeto **WadExample** no Solution Explorer **e depois publique** a partir do menu **Build.**
2. Escolha a sua subscrição.
3. No diálogo **Microsoft Azure Publish Settings,** selecione **Create New...**.
4. No diálogo **create Cloud Service and Storage Account,** introduza um **Nome** (por exemplo, "WadExample") e selecione uma região ou grupo de afinidade.
5. Desloque o **Ambiente** à **Encenação**.
6. Modifique quaisquer outras **Definições** conforme apropriado e clique **em Publicar**.
7. Depois de concluída a implantação, verifique no portal Azure que o seu serviço de cloud está em estado de **Execução.**

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Passo 4: Crie o ficheiro de configuração de Diagnósticos e instale a extensão
1. Descarregue a definição de esquema de ficheiro de configuração pública executando o seguinte comando PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Adicione um ficheiro XML ao seu projeto **WorkerRole1** clicando no projeto **WorkerRole1** e selecione **Adicionar** -> **Novo Item...** -> **Itens visuais C# itens** -> **Data** -> **XML File**. Nomeie o ficheiro "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associe o WadConfig.xsd ao ficheiro de configuração. Certifique-se de que a janela do editor WadExample.xml é a janela ativa. Pressione **F4** para abrir a janela **Propriedades.** Clique na propriedade **Schemas** na janela **Propriedades.** Clique no **...** na propriedade **de Schemas.** Clique no botão **Adicionar…** botão e navegar para o local onde guardou o ficheiro XSD e selecione o ficheiro WadConfig.xsd. Clique em **OK**.

4. Substitua o conteúdo do ficheiro de configuração WadExample.xml pelo seguinte XML e guarde o ficheiro. Este ficheiro de configuração define um par de contadores de desempenho para recolher: um para utilização de CPU e outro para utilização da memória. Em seguida, a configuração define os quatro eventos correspondentes aos métodos da classe SampleEventSourceWriter.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Passo 5: Instale diagnósticos na sua função de trabalhador
Os cmdlets PowerShell para gerir diagnósticos numa função web ou de trabalhador são: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1. Open Azure PowerShell.
2. Execute o script para instalar Diagnósticos na sua função de trabalhador (substitua *storageAccountKey* com a chave da conta de armazenamento para a sua conta de armazenamento wadexample e *config_path* com o caminho para o ficheiro *WadExample.xml):*

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Passo 6: Olhe para os seus dados de telemetria
No Visual Studio **Server Explorer,** navegue para a conta de armazenamento wadexample. Depois de o serviço de nuvem ter funcionado cerca de cinco (5) minutos, deverá ver as **tabelas WADEnumsTable**, **WADHighFreqTable,** **WADMessageTable,** **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes numa das tabelas para ver a telemetria que foi recolhida.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Esquema de Arquivo de Configuração
O ficheiro de configuração de Diagnóstico define valores que são usados para inicializar as definições de configuração de diagnóstico quando o agente de diagnóstico começa. Consulte a [mais recente referência de esquemas](/azure/azure-monitor/platform/diagnostics-extension-schema) para valores e exemplos válidos.

## <a name="troubleshooting"></a>Resolução de problemas
Se tiver problemas, consulte a [Troubleshooting Azure Diagnostics](../azure-diagnostics-troubleshooting.md) para obter ajuda com problemas comuns.

## <a name="next-steps"></a>Passos Seguintes
[Consulte uma lista de artigos de diagnóstico de máquinas virtuais relacionadas azure](../azure-monitor/platform/diagnostics-extension-overview.md) para alterar os dados que está a recolher, problemas de resolução de problemas ou saber mais sobre diagnósticos em geral.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/



