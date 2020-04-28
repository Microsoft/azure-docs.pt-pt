---
title: Trace o fluxo na aplicação de serviços de nuvem com diagnósticos azure
titleSuffix: Azure Cloud Services
description: Adicione mensagens de rastreio a uma aplicação Azure para ajudar a depurar, medir o desempenho, monitorização, análise de tráfego e muito mais.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 47a33ba27dd6d2df626d93695c421303bace6a0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75386515"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Trace o fluxo de uma aplicação de Serviços cloud com diagnósticos azure
Rastrear é uma forma de monitorizar a execução da sua aplicação enquanto está em execução. Pode utilizar o [System.Diagnostics.Trace,](/dotnet/api/system.diagnostics.trace) [System.Diagnostics.Debug,](/dotnet/api/system.diagnostics.debug) [system.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) classes para registar informações sobre erros e execução de aplicações em registos, ficheiros de texto ou outros dispositivos para posterior análise. Para mais informações sobre rastreios, consulte Aplicações de [Rastreio e Instrumentação](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Utilize declarações de vestígios e interruptores de rastreio
Implemente o rastreio na sua aplicação Cloud Services adicionando o [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) à configuração da aplicação e fazendo chamadas para System.Diagnostics.Trace ou System.Diagnostics.Debug no seu código de aplicação. Utilize a app de ficheiros de *configuração.config* para funções de trabalhador e o *web.config* para funções web. Quando cria um novo serviço hospedado utilizando um modelo de Estúdio Visual, o Azure Diagnostics é adicionado automaticamente ao projeto e o DiagnosticMonitorTraceListener é adicionado ao ficheiro de configuração apropriado para as funções que adiciona.

Para obter informações sobre a colocação de declarações de vestígios, consulte [Como: Adicionar declarações de rastreio ao Código de Aplicação](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Ao colocar os [Trace Switches](/dotnet/framework/debug-trace-profile/trace-switches) no seu código, pode controlar se o rastreio ocorre e quão extenso é. Isto permite-lhe monitorizar o estado da sua aplicação num ambiente de produção. Isto é especialmente importante numa aplicação de negócio que utiliza vários componentes em execução em vários computadores. Para mais informações, consulte [Como configurar os interruptores](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches)de rastreio .

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configure o ouvinte de vestígios numa aplicação Azure
Trace, Debug e TraceSource, exigem que instale "ouvintes" para recolher e gravar as mensagens que são enviadas. Os ouvintes recolhem, armazenam e rotas de rastreio de mensagens. Direcionam a saída de rastreio para um alvo apropriado, como um registo, janela ou ficheiro de texto. O Azure Diagnostics utiliza a classe [DiagnosticMonitorTraceListener.](/previous-versions/azure/reference/ee758610(v=azure.100))

Antes de completar o seguinte procedimento, deve inicializar o monitor de diagnóstico Azure. Para isso, consulte A Habilitação de [Diagnósticos no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Note que se utilizar os modelos fornecidos pelo Visual Studio, a configuração do ouvinte é adicionada automaticamente para si.

### <a name="add-a-trace-listener"></a>Adicione um ouvinte de traços
1. Abra o ficheiro web.config ou app.config para o seu papel.
2. Adicione o seguinte código ao ficheiro. Altere o atributo versão para utilizar o número de versão do conjunto a que se refere. A versão de montagem não muda necessariamente com cada versão Do SDK Azure, a menos que existam atualizações.
   
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
   > Certifique-se de que tem uma referência ao projeto de referência para o conjunto Microsoft.WindowsAzure.Diagnostics. Atualize o número da versão no xml acima para corresponder à versão do conjunto de diagnósticos Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Guarde o ficheiro de config.

Para mais informações sobre os ouvintes, consulte [Trace Listeners](/dotnet/framework/debug-trace-profile/trace-listeners).

Depois de completar os passos para adicionar o ouvinte, pode adicionar declarações de rastreio ao seu código.

### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a declaração de rastreio ao seu código
1. Abra um ficheiro de origem para a sua candidatura. Por exemplo, \<o ficheiro RoleName>.cs para o papel do trabalhador ou função web.
2. Adicione a seguinte diretiva utilizando a seguinte diretiva se ainda não tiver sido adicionada:
    ```
        using System.Diagnostics;
    ```
3. Adicione declarações de Trace onde pretende capturar informações sobre o estado da sua aplicação. Pode utilizar uma variedade de métodos para formatar a saída da declaração de Rastreio. Para mais informações, consulte [Como: Adicionar declarações de rastreio ao Código](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)de Aplicação .
4. Guarde o ficheiro de origem.




