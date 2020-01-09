---
title: Rastrear o fluxo no aplicativo de serviços de nuvem com Diagnóstico do Azure
titleSuffix: Azure Cloud Services
description: Adicione mensagens de rastreamento a um aplicativo do Azure para ajudar a depurar, medir o desempenho, o monitoramento, a análise de tráfego e muito mais.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 47a33ba27dd6d2df626d93695c421303bace6a0b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75386515"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Rastrear o fluxo de um aplicativo de serviços de nuvem com Diagnóstico do Azure
O rastreamento é uma maneira de monitorar a execução do seu aplicativo enquanto ele está em execução. Você pode usar as classes [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace), [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug)e [System. Diagnostics. traceexception](/dotnet/api/system.diagnostics.tracesource) para registrar informações sobre erros e a execução de aplicativos em logs, arquivos de texto ou outros dispositivos para análise posterior. Para obter mais informações sobre rastreamento, consulte [rastreamento e instrumentação de aplicativos](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Usar instruções de rastreamento e opções de rastreamento
Implemente o rastreamento em seu aplicativo de serviços de nuvem adicionando o [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) à configuração do aplicativo e fazendo chamadas para System. Diagnostics. Trace ou System. Diagnostics. Debug no código do aplicativo. Use o arquivo de configuração *app. config* para funções de trabalho e o *Web. config* para funções Web. Quando você cria um novo serviço hospedado usando um modelo do Visual Studio, Diagnóstico do Azure é automaticamente adicionado ao projeto e o DiagnosticMonitorTraceListener é adicionado ao arquivo de configuração apropriado para as funções que você adiciona.

Para obter informações sobre como colocar instruções de rastreamento, consulte [como: Adicionar instruções de rastreamento ao código do aplicativo](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Ao colocar as [Opções de rastreamento](/dotnet/framework/debug-trace-profile/trace-switches) em seu código, você pode controlar se o rastreamento ocorre e quão extensiva é. Isso permite que você monitore o status do seu aplicativo em um ambiente de produção. Isso é especialmente importante em um aplicativo de negócios que usa vários componentes em execução em vários computadores. Para obter mais informações, consulte [como: configurar opções de rastreamento](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurar o ouvinte de rastreamento em um aplicativo do Azure
Trace, Debug e Rastreáte exigem que você configure "ouvintes" para coletar e registrar as mensagens que são enviadas. Os ouvintes coletam, armazenam e roteiam mensagens de rastreamento. Eles direcionam a saída de rastreamento para um destino apropriado, como um log, uma janela ou um arquivo de texto. Diagnóstico do Azure usa a classe [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) .

Antes de concluir o procedimento a seguir, você deve inicializar o monitor de diagnóstico do Azure. Para fazer isso, consulte [habilitando o diagnóstico no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Observe que, se você usar os modelos fornecidos pelo Visual Studio, a configuração do ouvinte será adicionada automaticamente para você.

### <a name="add-a-trace-listener"></a>Adicionar um ouvinte de rastreamento
1. Abra o arquivo Web. config ou app. config para sua função.
2. Adicione o código a seguir ao arquivo. Altere o atributo Version para usar o número de versão do assembly que você está referenciando. A versão do assembly não é necessariamente alterada com cada versão do SDK do Azure, a menos que haja atualizações para ela.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Verifique se você tem uma referência de projeto para o assembly Microsoft. WindowsAzure. Diagnostics. Atualize o número de versão no XML acima para corresponder à versão do assembly Microsoft. WindowsAzure. Diagnostics referenciado.
   > 
   > 
3. Salve o arquivo de configuração.

Para obter mais informações sobre ouvintes, consulte [ouvintes de rastreamento](/dotnet/framework/debug-trace-profile/trace-listeners).

Depois de concluir as etapas para adicionar o ouvinte, você pode adicionar instruções de rastreamento ao seu código.

### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a instrução trace ao seu código
1. Abra um arquivo de origem para seu aplicativo. Por exemplo, o \<RoleName > arquivo. cs para a função de trabalho ou função Web.
2. Adicione a seguinte diretiva using se ela ainda não tiver sido adicionada:
    ```
        using System.Diagnostics;
    ```
3. Adicione instruções de rastreamento em que você deseja capturar informações sobre o estado do seu aplicativo. Você pode usar uma variedade de métodos para formatar a saída da instrução trace. Para obter mais informações, consulte [como: Adicionar instruções de rastreamento ao código do aplicativo](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Salve o arquivo de origem.




