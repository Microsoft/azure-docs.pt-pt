---
title: Como utilizar diagnósticos Azure (.NET) com Serviços cloud (clássicos) | Microsoft Docs
description: Usando diagnósticos Azure para recolher dados dos Serviços de nuvem Azure para depuração, medição de desempenho, monitorização, análise de tráfego, entre outros.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 97e68d338580132b6927c4cc8b206db60fe93ba2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703512"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services-classic"></a>Habilitar diagnósticos Azure em Azure Cloud Services (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Consulte [a visão geral do Azure Diagnostics](../azure-monitor/agents/diagnostics-extension-overview.md) para obter um fundo sobre O Diagnóstico Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Como permitir diagnósticos numa função de trabalhador
Este walkthrough descreve como implementar um papel de trabalhador Azure que emite dados de telemetria usando a classe .NET EventSource. O Azure Diagnostics é utilizado para recolher os dados de telemetria e armazená-los numa conta de armazenamento Azure. Ao criar uma função de trabalhador, o Visual Studio ativa automaticamente o Diagnóstico 1.0 como parte da solução em Azure SDKs para .NET 2.4 e anteriores. As seguintes instruções descrevem o processo de criação do papel do trabalhador, desativando o Diagnóstico 1.0 da solução e implantando os Diagnósticos 1.2 ou 1.3 para o seu papel de trabalhador.

### <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem uma subscrição do Azure e está a usar o Visual Studio com o Azure SDK. Se não tiver uma subscrição do Azure, pode inscrever-se no [Teste Gratuito.][Free Trial] Certifique-se de [instalar e configurar a versão Azure PowerShell 0.8.7 ou posterior][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Passo 1: Criar um papel de trabalhador
1. Lançamento **Visual Studio**.
2. Crie um projeto **Azure Cloud Service** a partir do modelo **Cloud** que visa .NET Framework 4.5.  Nomeie o projeto "WadExample" e clique em Ok.
3. Selecione **Função de Trabalhador** e clique em Ok. O projeto será criado.
4. No **Solution Explorer,** clique duas vezes no ficheiro **propriedades WorkerRole1.**
5. No **separador Configuração,** **desative o Enable Diagnostics** para desativar o Diagnóstico 1.0 (Azure SDK 2.4 e anterior).
6. Construa a sua solução para verificar se não tem erros.

### <a name="step-2-instrument-your-code"></a>Passo 2: Instrumento o seu código
Substitua o conteúdo do .cs WorkerRole pelo seguinte código. A classe SampleEventSourceWriter, herdada da [Classe EventSource,][EventSource Class]implementa quatro métodos de registo: **SendEnums,** **MessageMethod,** **SetOther** e **HighFreq**. O primeiro parâmetro do método **WriteEvent** define o ID para o evento respetivo. O método Run implementa um loop infinito que chama cada um dos métodos de registo implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

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


### <a name="step-3-deploy-your-worker-role"></a>Passo 3: Implementar o seu papel de trabalhador

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Desdobre o seu papel de trabalhador para a Azure a partir de dentro do Visual Studio, selecionando o projeto **WadExample** no Solution Explorer **e,** em seguida, publique a partir do menu **Build.**
2. Escolha a sua subscrição.
3. No diálogo microsoft **Azure Publish Settings,** selecione **Create New...**.
4. No diálogo **de serviço de nuvem e armazenamento Create,** insira um **Nome** (por exemplo, "WadExample") e selecione uma região ou grupo de afinidade.
5. Definir o **Ambiente** para **encenar**.
6. Modificar quaisquer **outras Definições** conforme apropriado e clicar **em Publicar.**
7. Depois de concluída a implementação, verifique no portal Azure que o seu serviço em nuvem está em **funcionamento.**

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Passo 4: Criar o seu ficheiro de configuração de Diagnóstico e instalar a extensão
1. Descarregue a definição de esquema de ficheiro de configuração pública executando o seguinte comando PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Adicione um ficheiro XML ao seu projeto **WorkerRole1** clicando à direita no projeto **WorkerRole1** e selecione **Add**  ->  **New Item...** -> **Itens visuais**  ->  C# **Dados**  ->  **Ficheiro XML**. Diga o nome do ficheiro "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associar o WadConfig.xsd com o ficheiro de configuração. Certifique-se de que a janela do editor WadExample.xml é a janela ativa. Prima **F4** para abrir a janela **Propriedades.** Clique na propriedade **Schemas** na janela **Propriedades.** Clique no **...** na propriedade **Schemas.** Clique no botão **Adicionar…** botão e navegue para o local onde guardou o ficheiro XSD e selecione o ficheiro WadConfig.xsd. Clique em **OK**.

4. Substitua o conteúdo do ficheiro de configuração WadExample.xml pelo seguinte XML e guarde o ficheiro. Este ficheiro de configuração define um par de contadores de desempenho para recolher: um para utilização do CPU e outro para utilização da memória. Em seguida, a configuração define os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Passo 5: Instalar diagnósticos na sua função de trabalhador
Os cmdlets PowerShell para gerir diagnósticos numa função web ou de trabalhador são: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1. Abra o Azure PowerShell.
2. Execute o script para instalar Diagnósticos na sua função de trabalhador (substitua o *StorageAccountKey* pela chave de conta de armazenamento da sua conta de armazenamento de wadexample e *config_path* com o caminho para o ficheiro *WadExample.xml):*

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Passo 6: Veja os seus dados de telemetria
No Visual Studio **Server Explorer,** navegue para a conta de armazenamento wadexample. Depois de o serviço de nuvem estar a funcionar cerca de cinco (5) minutos, deverá ver as **tabelas WADEnumsTable**, **WADHighFreqTable,** **WADMessageTable,** **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes numa das tabelas para ver a telemetria que foi recolhida.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Esquema de ficheiro de configuração
O ficheiro de configuração de Diagnóstico define valores que são utilizados para inicializar as definições de configuração de diagnóstico quando o agente de diagnóstico começa. Consulte a [última referência de esquema](../azure-monitor/agents/diagnostics-extension-versions.md) para valores e exemplos válidos.

## <a name="troubleshooting"></a>Resolução de problemas
Se tiver problemas, consulte [o Troubleshooting Azure Diagnostics](../azure-monitor/agents/diagnostics-extension-troubleshooting.md) para obter ajuda com problemas comuns.

## <a name="next-steps"></a>Passos Seguintes
[Consulte uma lista de artigos de diagnóstico de máquina virtual Azure relacionados](../azure-monitor/agents/diagnostics-extension-overview.md) para alterar os dados que está a recolher, problemas de resolução de problemas ou saiba mais sobre diagnósticos em geral.

[EventSource Class]: /dotnet/api/system.diagnostics.tracing.eventsource

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: /previous-versions/azure/gg433048(v=azure.100)
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: /powershell/azure/