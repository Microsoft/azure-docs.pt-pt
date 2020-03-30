---
title: Aplicativos de produção de perfil em Azure com Perfilde Insights de Aplicação
description: Identifique o caminho quente no seu código de servidor web com um perfil de baixa pegada.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ce952bd248640d03fcff43284707614577df8469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671652"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Aplicações de produção de perfil em Azure com Insights de Aplicação
## <a name="enable-application-insights-profiler-for-your-application"></a>Ativar o Profiler de Insights de Aplicação para a sua aplicação

O Perfil de Insights de Aplicação Azure fornece vestígios de desempenho para aplicações que estão em execução em produção no Azure. O Profiler captura automaticamente os dados em escala sem afetar negativamente os seus utilizadores. O Profiler ajuda-o a identificar o código "quente" que demora mais tempo a lidar com um pedido web específico. 

O Profiler trabalha com aplicações .NET que são implantadas nos seguintes serviços Azure. Instruções específicas para ativar o Profiler para cada tipo de serviço estão nos links abaixo.

* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços Cloud do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais azure e conjuntos de escala de máquinas virtuais](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**PRÉ-VISUALIZAÇÃO** ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Se ativou o Profiler mas não está a ver vestígios, consulte o nosso guia de [resolução de problemas.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

## <a name="view-profiler-data"></a>Ver dados do Profiler

Para que o Profiler faça o upload de vestígios, a sua aplicação deve estar a tratar ativamente os pedidos. Se estiver a fazer uma experiência, pode gerar pedidos para a sua aplicação web utilizando testes de desempenho do [Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Se tiver o Profiler recentemente ativado, pode fazer um teste de carga curta. Enquanto o teste de carga estiver em execução, selecione o botão **Profile Now** no painel [ **definições** ](profiler-settings.md)do perfil . Quando o Profiler está em execução, perfis aleatoriamente cerca de uma vez por hora e por uma duração de dois minutos. Se a sua aplicação estiver a lidar com um fluxo constante de pedidos, o Profiler envia vestígios a cada hora.

Depois de a sua aplicação receber algum tráfego e o Profiler tiver tido tempo para carregar os vestígios, deve ter vestígios para visualizar. Este processo pode demorar 5 a 10 minutos. Para visualizar vestígios, no painel **performance,** selecione **Take Actions**, e, em seguida, selecione o botão **Traces do Profiler.**

![Rastreios de perfil de visualização do painel de desempenho insights de aplicação][performance-blade]

Selecione uma amostra para apresentar uma repartição de tempo gasto a executar o pedido pelo nível de código.

![Aplicação Insights trace explorer][trace-explorer]

O explorador de vestígios apresenta as seguintes informações:

* **Show Hot Path**: Abre o maior nó de folhas, ou pelo menos algo próximo. Na maioria dos casos, este nó está perto de um estrangulamento de desempenho.
* **Etiqueta**: O nome da função ou evento. A árvore exibe uma mistura de código e eventos que ocorreram, tais como eventos SQL e HTTP. O evento de topo representa a duração total do pedido.
* **Decorrido**: Intervalo de tempo entre o início da operação e o fim da operação.
* **Quando**: O momento em que a função ou evento estava em execução em relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

O perfilador de serviço da Microsoft utiliza uma combinação de métodos de amostragem e instrumentação para analisar o desempenho da sua aplicação. Quando a recolha detalhada está em andamento, o perfilde serviço mostra o ponteiro de instruções de cada cpu da máquina a cada milésimo de segundo. Cada amostra captura a pilha de chamadas completa do fio que está atualmente a executar. Dá informações detalhadas sobre o que aquele fio estava a fazer, tanto a um nível elevado como a um baixo nível de abstração. O profiler de serviço também recolhe outros eventos para rastrear a correlação de atividade e a causalidade, incluindo eventos de comutação de contexto, eventos da Biblioteca Paralela de Tarefas (TPL) e eventos de thread pool.

A pilha de chamadas que é exibida na vista da linha do tempo é o resultado da amostragem e instrumentação. Como cada amostra captura a pilha completa de chamadas do fio, inclui código da Microsoft .NET Framework e de outros quadros que você refere.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Alocação de objetos (clr! JIT\_Novo ou clr! JIT\_Newarr1)

**Clr! JIT\_Novo** e **clr! JIT\_Newarr1** são funções auxiliares em .NET Framework que alocam a memória de um monte gerido. **Clr! JIT\_New** é invocado quando um objeto é atribuído. **Clr! JIT\_Newarr1** é invocado quando uma matriz de objetos é atribuída. Estas duas funções são geralmente rápidas e demoram quantidades relativamente pequenas de tempo. Se **clr! JIT\_Novo** ou **clr! JIT\_Newarr1** leva muito tempo na sua linha temporal, o código pode estar a alocar muitos objetos e a consumir quantidades significativas de memória.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Código de carregamento (clr! ThePreStub)

**Clr! O PreStub** é uma função de ajudante em .NET Framework que prepara o código para executar pela primeira vez. Esta execução geralmente inclui, mas não se limita a, just-in-time (JIT) compilação. Para cada método C#, **clr! O PreStub** deve ser invocado no máximo uma vez durante um processo.

Se **clr! O PreStub** demora muito tempo a pedir um pedido, o pedido é o primeiro a executar este método. O tempo de execução do quadro .NET para carregar o primeiro método é significativo. Pode considerar usar um processo de aquecimento que execute essa parte do código antes de os seus utilizadores acederem ao mesmo, ou considerar executar o Native Image Generator (ngen.exe) nos seus conjuntos.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Bloqueio de contenção (clr! JITutil\_MonContention ou clr! JITutil\_MonEnterWorker)

**Clr! JITutil\_MonContention** ou **clr! JiTutil\_MonEnterWorker** indica que o fio de corrente está à espera que um bloqueio seja libertado. Este texto é frequentemente apresentado quando executa uma declaração c# **LOCK,** invoca o método **Monitor.Enter** ou invoca um método com o **atributo MethodImplOptions.Synchronized.** A contenção de bloqueio ocorre geralmente quando o fio _A_ adquire um cadeado e o fio _B_ tenta adquirir a mesma fechadura antes do fio _A_ soltá-lo.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Código de carga ([COLD])

Se o nome do método contiver **[COLD],** como **mscorlib.ni!] COLD]System.Reflection.CustomAttribute.IsDefined**, .NET O tempo de execução do quadro está a executar código pela primeira vez que não é otimizado pela [otimização guiada pelo perfil](/cpp/build/profile-guided-optimizations). Para cada método, deve ser exibido no máximo uma vez durante o processo.

Se o código de carregamento demorar um tempo substancial para um pedido, o pedido é o primeiro a executar a parte não otimizada do método. Considere utilizar um processo de aquecimento que execute essa parte do código antes de os seus utilizadores acederem ao mesmo.

### <a name="send-http-request"></a><a id="httpclientsend"></a>Enviar pedido http

Métodos como **httpClient.Send** indicam que o código está à espera que um pedido http seja concluído.

### <a name="database-operation"></a><a id="sqlcommand"></a>Operação de base de dados

Métodos como **O SqlCommand.Execute** indicam que o código está à espera que uma operação de base de dados termine.

### <a name="waiting-await_time"></a><a id="await"></a>Espera (TEMPO DE ESPERA)\_

**Aguardação\_TIME** indica que o código está à espera que outra tarefa termine. Este atraso geralmente acontece com a declaração c# **AWAIT.** Quando o código faz um C# **AWAIT,** o fio desenrola-se e devolve o controlo à piscina de roscas, e não há nenhum fio que esteja bloqueado à espera que o **AWAIT** termine. No entanto, logicamente, o fio que fez o **AWAIT** está "bloqueado", e está à espera que a operação termine. A declaração **DE TEMPO AGUARDAÇÃO\_** indica o tempo bloqueado à espera que a tarefa termine.

### <a name="blocked-time"></a><a id="block"></a>Tempo bloqueado

**BLOCKED_TIME** indica que o código está à espera que outro recurso esteja disponível. Por exemplo, pode estar à espera de um objeto de sincronização, de um fio disponível, ou de um pedido para terminar.

### <a name="unmanaged-async"></a>Async não gerido

A estrutura .NET emite eventos ETW e passa ids de atividade entre fios para que as chamadas assinantes possam ser rastreadas através de fios. Código não gerido (código nativo) e alguns estilos mais antigos de código assíncrono estão faltando estes eventos e ids de atividade, por isso o perfil não pode dizer que fio e que funções estão a funcionar no fio. Isto é rotulado "Async Não Gerido" na pilha de chamadas. Se descarregar o ficheiro ETW, poderá utilizar o [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) para obter mais informações sobre o que está a acontecer.

### <a name="cpu-time"></a><a id="cpu"></a>Tempo cpu

A CPU está ocupada a executar as instruções.

### <a name="disk-time"></a><a id="disk"></a>Tempo do disco

A aplicação está a realizar operações em disco.

### <a name="network-time"></a><a id="network"></a>Tempo de rede

A aplicação está a realizar operações de rede.

### <a name="when-column"></a><a id="when"></a>Quando coluna

A coluna **Quando** é uma visualização de como as amostras INCLUSIVAs recolhidas para um nó variam ao longo do tempo. A gama total do pedido é dividida em 32 baldes de tempo. As amostras inclusivas para esse nó são acumuladas nesses 32 baldes. Cada balde é representado como um bar. A altura da barra representa um valor escalonado. Para nós marcados **CPU_TIME** ou **BLOCKED_TIME,** ou onde existe uma relação óbvia com o consumo de um recurso (por exemplo, um CPU, disco ou fio), a barra representa o consumo de um dos recursos durante o balde. Para estas métricas, é possível obter um valor superior a 100 por cento consumindo vários recursos. Por exemplo, se utilizar, em média, dois CPUs durante um intervalo, obtém-se 200%.

## <a name="limitations"></a>Limitações

O período de retenção de dados por defeito é de cinco dias. Os dados máximos que são ingeridos por dia são de 10 GB.

Não há taxas para o uso do serviço Profiler. Para que a utilize, a sua aplicação web deve ser alojada no nível básico da funcionalidade de Aplicações Web do Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmo de sobrecarga e amostragem

O profiler executa aleatoriamente dois minutos por hora em cada máquina virtual que acolhe a aplicação que tem o Profiler habilitado para capturar vestígios. Quando o Profiler está em execução, adiciona 5 a 15% de sobrecarga de CPU ao servidor.

## <a name="next-steps"></a>Passos seguintes
Ativar o Perfil de Insights de Aplicação para a sua aplicação Azure. Veja também:
* [Serviços Aplicacionais](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços Cloud do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais azure e conjuntos de escala de máquinas virtuais](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
