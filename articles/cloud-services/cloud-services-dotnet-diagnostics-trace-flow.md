---
title: Trace o fluxo em Cloud Services (clássico) Aplicação com DiagnósticoS Azure
description: Adicione mensagens de rastreio a uma aplicação Azure para ajudar a depurar, medir o desempenho, monitorizar, analisar o tráfego, e muito mais.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b00bb28128cfe9a2e701647ad174ea2c9dd458e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742130"
---
# <a name="trace-the-flow-of-a-cloud-services-classic-application-with-azure-diagnostics"></a>Trace o fluxo de uma aplicação cloud Services (clássica) com Azure Diagnostics

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

O rastreio é uma forma de monitorizar a execução da aplicação enquanto esta ocorre. Pode utilizar as classes [System.Diagnostics.Trace,](/dotnet/api/system.diagnostics.trace) [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug)e [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) para registar informações sobre erros e execução de aplicações em registos, ficheiros de texto ou outros dispositivos para posterior análise. Para obter mais informações sobre o rastreio, consulte [aplicações de rastreio e instrumentação.](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications)

## <a name="use-trace-statements-and-trace-switches"></a>Use traços e comutadores de vestígios
Implementar o rastreio na sua aplicação Cloud Services adicionando o [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) à configuração da aplicação e fazendo chamadas para System.Diagnostics.Trace ou System.Diagnostics.Debug no seu código de aplicação. Utilize o ficheiro de configuração *app.config* para funções de trabalhador e *oweb.config* para funções web. Quando cria um novo serviço hospedado utilizando um modelo de Estúdio Visual, o Azure Diagnostics é automaticamente adicionado ao projeto e o DiagnosticMonitorTraceListener é adicionado ao ficheiro de configuração apropriado para as funções que adiciona.

Para obter informações sobre a colocação de declarações de rastreio, consulte [Como: Adicionar declarações de rastreio ao Código de Aplicação](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Ao colocar [Trace Switches](/dotnet/framework/debug-trace-profile/trace-switches) no seu código, pode controlar se o rastreio ocorre e quão extenso é. Isto permite-lhe monitorizar o estado da sua aplicação num ambiente de produção. Isto é especialmente importante numa aplicação de negócio que utiliza vários componentes em funcionamento em vários computadores. Para obter mais informações, consulte [Como: Configurar os interruptores de rastreio](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configure o ouvinte de vestígios numa aplicação Azure
Trace, Debug e TraceSource, exigem que crie "ouvintes" para recolher e gravar as mensagens que são enviadas. Os ouvintes recolhem, armazenam e encaminham mensagens. Direcionam a saída de rastreio para um alvo apropriado, como um registo, janela ou ficheiro de texto. A Azure Diagnostics utiliza a classe [DiagnosticMonitorTraceListener.](/previous-versions/azure/reference/ee758610(v=azure.100))

Antes de completar o seguinte procedimento, deve rubricar o monitor de diagnóstico Azure. Para isso, consulte [Ativar diagnósticos no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Note que se utilizar os modelos fornecidos pelo Visual Studio, a configuração do ouvinte é adicionada automaticamente para si.

### <a name="add-a-trace-listener"></a>Adicione um rastreador
1. Abra o ficheiro web.config ou app.config para o seu papel.
2. Adicione o seguinte código ao ficheiro. Altere o atributo Versão para utilizar o número de versão do conjunto a que se refere. A versão de montagem não muda necessariamente com cada versão Azure SDK, a menos que existam atualizações para a presente.
   
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
   > Certifique-se de que tem uma referência de projeto à montagem Microsoft.WindowsAzure.Diagnostics. Atualize o número da versão no xml acima para corresponder à versão do conjunto de diagnósticos do Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Guarde o ficheiro config.

Para obter mais informações sobre os ouvintes, consulte [Trace Listeners](/dotnet/framework/debug-trace-profile/trace-listeners).

Depois de completar os passos para adicionar o ouvinte, pode adicionar declarações de rastreio ao seu código.

### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a declaração de rastreio ao seu código
1. Abra um ficheiro de origem para a sua aplicação. Por exemplo, o \<RoleName> ficheiro .cs para o papel do trabalhador ou para a web.
2. Adicione a seguinte diretiva de utilização se ainda não tiver sido adicionada:
    ```
        using System.Diagnostics;
    ```
3. Adicione declarações de rastreio onde pretende capturar informações sobre o estado da sua aplicação. Pode utilizar uma variedade de métodos para formatar a saída da declaração Trace. Para obter mais informações, consulte [Como: Adicionar declarações de rastreio ao Código de Aplicação.](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)
4. Guarde o ficheiro de origem.




