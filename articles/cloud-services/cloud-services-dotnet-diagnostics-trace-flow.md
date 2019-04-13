---
title: Rastrear o fluxo numa aplicação de serviços Cloud com o diagnóstico do Azure | Documentos da Microsoft
description: Adicione mensagens de rastreio para uma aplicação do Azure para o ajudar a depuração, medir o desempenho, monitorização, análise de tráfego e muito mais.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: jeconnoc
ms.openlocfilehash: f597bc760a3f3825416912642ee66a53dfb91696
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525566"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Rastrear o fluxo de um aplicativo de serviços em nuvem com o diagnóstico do Azure
O rastreamento é uma forma monitorizar a execução do seu aplicativo enquanto ele estiver em execução. Pode utilizar o [Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug), e [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) classes para registar informações sobre erros e execução do aplicativo em registos, ficheiros de texto ou outros dispositivos para análise posterior. Para obter mais informações sobre o rastreio, consulte [rastreamento e Instrumentar aplicativos](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Utilizar declarações de rastreio e comutadores de rastreio
Rastreamento de implementar na sua aplicação de serviços Cloud, adicionando a [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) para a configuração da aplicação e fazer chamadas para TRACE ou System.Diagnostics.Debug no seu código da aplicação. Utilize o ficheiro de configuração *App. config* para funções de trabalho e o *Web. config* para funções da web. Quando cria um novo serviço hospedado, através de um modelo do Visual Studio, o diagnóstico do Azure é automaticamente adicionado ao projeto e o DiagnosticMonitorTraceListener é adicionado ao arquivo de configuração adequado para as funções que adicionar.

Para obter informações sobre o posicionamento de declarações de rastreio, consulte [como: Adicionar declarações de rastreio para o código da aplicação](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Colocando [comutadores de rastreio](/dotnet/framework/debug-trace-profile/trace-switches) em seu código, pode controlar se o rastreio ocorre e extensão é. Isto permite-lhe monitorizar o estado do aplicativo num ambiente de produção. Isso é especialmente importante num aplicativo de negócios que utiliza vários componentes em execução em vários computadores. Para obter mais informações, consulte [como: Configure os comutadores de rastreio](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurar o serviço de escuta de rastreamento num aplicativo do Azure
Rastreio, depuração e TraceSource, tem de configurar "serviços de escuta" para recolher e registar as mensagens que são enviadas. Serviços de escuta recolhem, armazenam e encaminham mensagens de rastreio. Eles direcionar a saída de rastreamento para um destino apropriado, como um registo, a janela ou o arquivo de texto. Diagnóstico do Azure utiliza o [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) classe.

Antes de concluir o procedimento seguinte, o monitor de diagnóstico do Azure tem de ser inicializado. Para tal, veja [ativar diagnósticos no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Tenha em atenção que se usar os modelos que são fornecidos pelo Visual Studio, a configuração do serviço de escuta é adicionada automaticamente para.

### <a name="add-a-trace-listener"></a>Adicionar um serviço de escuta de rastreio
1. Abra o ficheiro Web. config ou App. config para a sua função.
2. Adicione o seguinte código para o ficheiro. Altere o atributo de versão para utilizar o número de versão do assembly que está a referenciar. A versão da assemblagem não necessariamente se altera com cada versão do SDK do Azure, a menos que existam atualizações ao mesmo.
   
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
   > Certifique-se de que tem uma referência de projeto ao Microsoft.WindowsAzure.Diagnostics assembly. Atualize o número de versão no xml acima para corresponder à versão do assembly Microsoft.WindowsAzure.Diagnostics referenciado.
   > 
   > 
3. Guarde o ficheiro de configuração.

Para obter mais informações sobre serviços de escuta, consulte [ouvintes de rastreamento](/dotnet/framework/debug-trace-profile/trace-listeners).

Depois de concluir os passos para adicionar o serviço de escuta, pode adicionar declarações de rastreio para o seu código.

### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a declaração de rastreio para o seu código
1. Abra um ficheiro de origem para a sua aplicação. Por exemplo, o \<RoleName > arquivo. CS para a função de trabalho ou a função da web.
2. Adicione o seguinte usando a instrução se já não foi adicionado:
    ```
        using System.Diagnostics;
    ```
3. Adicione declarações de rastreio onde pretende capturar informações sobre o estado do seu aplicativo. Pode usar uma variedade de métodos para formatar a saída da declaração de rastreio. Para obter mais informações, consulte [como: Adicionar declarações de rastreio para o código da aplicação](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Guarde o ficheiro de origem.

