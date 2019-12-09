---
title: Criar perfil de aplicativos de produção no Azure com Application Insights Profiler
description: Identifique o Hot Path no código do servidor Web com um criador de perfil de baixa ocupação.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 64e7cc0afa4051836d38c885dff0bc0a0e0b08c6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922082"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Perfil de aplicativos de produção no Azure com Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Habilitar Application Insights Profiler para seu aplicativo

O Aplicativo Azure insights Profiler fornece rastreamentos de desempenho para aplicativos que estão sendo executados em produção no Azure. O criador de perfil captura os dados automaticamente em escala sem afetar negativamente os usuários. O profiler ajuda a identificar o caminho de código "quente" que leva o tempo mais longo quando trata de uma solicitação da Web específica. 

O profiler funciona com aplicativos .NET que são implantados nos seguintes serviços do Azure. Instruções específicas para habilitar o Profiler para cada tipo de serviço estão nos links abaixo.

* [App Service do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [Versão **prévia** ASP.NET Core aplicativos Web Linux do Azure](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Se você tiver habilitado o Profiler, mas não estiver vendo rastreamentos, consulte nosso [Guia de solução de problemas](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Exibir dados do criador de perfil

Para que o profiler carregue rastreamentos, seu aplicativo deve estar manipulando ativamente as solicitações. Se você estiver fazendo um experimento, poderá gerar solicitações para seu aplicativo Web usando [Application insights teste de desempenho](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Se você tiver habilitado o profiler recentemente, poderá executar um teste de carga curto. Enquanto o teste de carga estiver em execução, selecione o botão **perfil agora** no [painel **configurações do profiler** ](profiler-settings.md). Quando o profiler está em execução, ele faz o perfil aleatoriamente de uma vez por hora e por uma duração de dois minutos. Se seu aplicativo estiver manipulando um fluxo constante de solicitações, o profiler carregará rastreamentos a cada hora.

Depois que o aplicativo recebe algum tráfego e o criador de perfil teve tempo para carregar os rastreamentos, você deve ter rastreamentos para exibir. Esse processo pode levar de 5 a 10 minutos. Para exibir os rastreamentos, no painel **desempenho** , selecione **executar ações**e, em seguida, selecione o botão **rastreamentos do criador de perfil** .

![Painel de desempenho do Application Insights Visualizar rastreamentos do criador de perfil][performance-blade]

Selecione um exemplo para exibir uma divisão de nível de código de tempo gasto na execução da solicitação.

![Gerenciador de rastreamento do Application Insights][trace-explorer]

O Gerenciador de rastreamento exibe as seguintes informações:

* **Mostrar Hot Path**: abre o maior nó folha ou pelo menos algo próximo. Na maioria dos casos, esse nó está perto de um afunilamento de desempenho.
* **Rótulo**: o nome da função ou do evento. A árvore exibe uma mistura de código e eventos que ocorreram, como eventos SQL e HTTP. O evento superior representa a duração geral da solicitação.
* **Decorrido**: o intervalo de tempo entre o início da operação e o fim da operação.
* **Quando**: a hora em que a função ou evento estava sendo executado em relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

O criador de perfil de serviço da Microsoft usa uma combinação de métodos de amostragem e instrumentação para analisar o desempenho do seu aplicativo. Quando a coleta detalhada está em andamento, o criador de perfil de serviço amostra o ponteiro de instrução de cada CPU da máquina a cada milissegundo. Cada exemplo captura a pilha de chamadas completa do thread que está sendo executado no momento. Ele fornece informações detalhadas sobre o que o thread estava fazendo, em um nível alto e um nível baixo de abstração. O criador de perfil de serviço também coleta outros eventos para acompanhar a correlação de atividades e causalidade, incluindo eventos de alternância de contexto, eventos TPL (biblioteca paralela de tarefas) e eventos de pool de threads.

A pilha de chamadas exibida na exibição da linha do tempo é o resultado da amostragem e da instrumentação. Como cada amostra captura a pilha de chamadas completa do thread, ela inclui código de Microsoft .NET Framework e de outras estruturas que você referencia.

### <a id="jitnewobj"></a>Alocação de objeto (CLR! JIT\_novo ou CLR! Newarr1 JIT\_)

**CLR!\_JIT New** e **CLR! JIT\_Newarr1** são funções auxiliares em .NET Framework que alocam memória de um heap gerenciado. **CLR! O JIT\_novo** é invocado quando um objeto é alocado. **CLR! JIT\_Newarr1** é invocado quando uma matriz de objeto é alocada. Essas duas funções geralmente são rápidas e levam quantidades relativamente pequenas de tempo. Se o **CLR! JIT\_novo** ou **CLR! O JIT\_Newarr1** leva muito tempo em sua linha do tempo, o código pode estar alocando muitos objetos e consumindo quantidades significativas de memória.

### <a id="theprestub"></a>Carregando código (CLR! ThePreStub

**CLR! ThePreStub** é uma função auxiliar no .NET Framework que prepara o código para ser executado pela primeira vez. Essa execução geralmente inclui, mas não se limita à compilação JIT (just-in-time). Para cada C# método, **CLR! ThePreStub** deve ser invocado no máximo uma vez durante um processo.

Se o **CLR! ThePreStub** leva muito tempo para uma solicitação, a solicitação é a primeira a executar esse método. O tempo para o tempo de execução de .NET Framework carregar o primeiro método é significativo. Você pode considerar o uso de um processo aquecimento que executa essa parte do código antes que os usuários o acessem, ou considere executar o gerador de imagem nativa (NGen. exe) em seus assemblies.

### <a id="lockcontention"></a>Contenção de bloqueio (CLR! JITutil\_MonContention ou CLR! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** ou **CLR! JITutil\_MonEnterWorker** indica que o thread atual está aguardando a liberação de um bloqueio. Esse texto geralmente é exibido quando você executa uma C# instrução **Lock** , invoca o método **Monitor. Enter** ou invoca um método com o atributo **MethodImplOptions. Synchronized** . A contenção de bloqueio geralmente ocorre quando o thread _a_ adquire um bloqueio e o thread _B_ tenta adquirir o mesmo bloqueio antes que _o thread a o_ libere.

### <a id="ngencold"></a>Carregando código ([frio])

Se o nome do método contiver **[frio]** , como **mscorlib.ni! [ FRIO] System. Reflection. CustomAttribute. IsDefined**, .NET Framework tempo de execução está executando o código pela primeira vez que não é otimizado pela [otimização guiada por perfil](/cpp/build/profile-guided-optimizations). Para cada método, ele deve ser exibido no máximo uma vez durante o processo.

Se o carregamento de código demorar uma quantidade significativa de tempo para uma solicitação, a solicitação será a primeira a executar a parte não otimizada do método. Considere usar um processo aquecimento que execute essa parte do código antes que os usuários o acessem.

### <a id="httpclientsend"></a>Enviar solicitação HTTP

Métodos como **HttpClient. Send** indicam que o código está aguardando a conclusão de uma solicitação HTTP.

### <a id="sqlcommand"></a>Operação de banco de dados

Métodos como **SqlCommand. Execute** indicam que o código está aguardando a conclusão de uma operação de banco de dados.

### <a id="await"></a>Aguardando (AWAIT\_tempo)

**AWAIT\_time** indica que o código está aguardando a conclusão de outra tarefa. Geralmente, esse atraso ocorre com C# a instrução **AWAIT** . Quando o código faz um C# **AWAIT**, o thread desenrola e retorna o controle para o pool de threads, e não há nenhum thread que esteja bloqueado aguardando a conclusão do **AWAIT** . No entanto, logicamente, o thread que fez o **AWAIT** é "bloqueado" e está aguardando a conclusão da operação. A instrução **AWAIT\_time** indica o tempo bloqueado aguardando a conclusão da tarefa.

### <a id="block"></a>Tempo bloqueado

**BLOCKED_TIME** indica que o código está aguardando que outro recurso esteja disponível. Por exemplo, ele pode estar aguardando um objeto de sincronização, para que um thread esteja disponível ou para que uma solicitação seja concluída.

### <a name="unmanaged-async"></a>Async não gerenciado

O .NET Framework emite eventos ETW e passa IDs de atividade entre threads para que as chamadas assíncronas possam ser controladas entre threads. O código não gerenciado (código nativo) e alguns estilos mais antigos de código assíncrono estão sem esses eventos e IDs de atividade, portanto, o criador de perfil não pode informar o thread e quais funções estão em execução no thread. Isso é rotulado como ' async não gerenciado ' na pilha de chamadas. Se você baixar o arquivo ETW, talvez seja possível usar o [Perfview](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) para obter mais informações sobre o que está acontecendo.

### <a id="cpu"></a>Tempo de CPU

A CPU está ocupada executando as instruções.

### <a id="disk"></a>Tempo do disco

O aplicativo está executando operações de disco.

### <a id="network"></a>Hora da rede

O aplicativo está executando operações de rede.

### <a id="when"></a>Quando coluna

A coluna **When** é uma visualização de como as amostras inclusivas coletadas para um nó variam ao longo do tempo. O intervalo total da solicitação é dividido em 32 buckets de tempo. Os exemplos inclusivos para esse nó são acumulados nesses 32 buckets. Cada bucket é representado como uma barra. A altura da barra representa um valor dimensionado. Para nós marcados como **CPU_TIME** ou **BLOCKED_TIME**, ou onde há uma relação óbvia para consumir um recurso (por exemplo, CPU, disco ou thread), a barra representa o consumo de um dos recursos durante o Bucket. Para essas métricas, é possível obter um valor maior que 100% consumindo vários recursos. Por exemplo, se você usar, em média, duas CPUs durante um intervalo, obterá 200 por cento.

## <a name="limitations"></a>Limitações

O período de retenção de dados padrão é de cinco dias. O máximo de dados ingeridos por dia é de 10 GB.

Não há encargos para usar o serviço Profiler. Para você usá-lo, seu aplicativo Web deve ser hospedado em pelo menos a camada básica do recurso de aplicativos Web do serviço de Azure App.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmo de sobrecarga e amostragem

O profiler é executado aleatoriamente dois minutos a cada hora em cada máquina virtual que hospeda o aplicativo que tem o criador de perfil habilitado para capturar rastreamentos. Quando o profiler está em execução, ele adiciona de 5 a 15% da sobrecarga de CPU ao servidor.

## <a name="next-steps"></a>Passos seguintes
Habilite Application Insights Profiler para seu aplicativo do Azure. Veja também:
* [Serviços Aplicacionais](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
