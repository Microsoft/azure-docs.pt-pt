---
title: Aplicativos de produção de perfil em Azure com Perfil de Insights de Aplicação
description: Identifique o caminho quente no seu código de servidor web com um perfil de baixa pegada.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0d3074d58560df5cb5bd6bdc2c0437a4be828918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86499397"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Aplicações de produção de perfis em Azure com Insights de Aplicação
## <a name="enable-application-insights-profiler-for-your-application"></a>Ativar o Profiler de Insights de Aplicação para a sua aplicação

O Azure Application Insights Profiler fornece traços de desempenho para aplicações que estão em execução em produção em Azure. O profiler captura os dados automaticamente em escala sem afetar negativamente os seus utilizadores. O profiler ajuda-o a identificar o caminho do código "quente" que demora mais tempo quando está a lidar com um determinado pedido web. 

Profiler trabalha com aplicações .NET que são implantadas nos seguintes serviços Azure. Instruções específicas para permitir o Profiler para cada tipo de serviço estão nos links abaixo.

* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais Azure e conjuntos de escala de máquinas virtuais](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**PRÉ-VISUALIZAÇÃO** ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Se ativou o Profiler mas não está a ver vestígios, consulte o nosso [guia de resolução de problemas.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

## <a name="view-profiler-data"></a>Ver dados do Profiler

Para o Profiler carregar vestígios, a sua aplicação deve estar a lidar ativamente com pedidos. Se estiver a fazer uma experiência, pode gerar pedidos para a sua aplicação web utilizando [testes de desempenho do Application Insights.](/vsts/load-test/app-service-web-app-performance-test) Se tiver o Profiler recentemente ativado, pode fazer um teste de carga curto. Enquanto o teste de carga estiver em execução, selecione o botão **Profile Now** no painel [ **de definições**](profiler-settings.md)do perfil . Quando o Profiler está a funcionar, perfis aleatoriamente cerca de uma vez por hora e durante uma duração de dois minutos. Se a sua aplicação estiver a lidar com um fluxo constante de pedidos, o Profiler envia vestígios a cada hora.

Depois de a sua aplicação receber algum tráfego e o Profiler ter tido tempo para carregar os vestígios, deverá ter vestígios para visualizar. Este processo pode demorar 5 a 10 minutos. Para visualizar vestígios, no painel **performance,** selecione **Take Actions** e, em seguida, selecione o botão **Traces Profiler.**

![Rastreios de desempenho de insights de aplicação][performance-blade]

Selecione uma amostra para apresentar uma desagregação de nível de código do tempo gasto a executar o pedido.

![Rastreador de aplicações trace explorer][trace-explorer]

O explorador de vestígios apresenta as seguintes informações:

* **Show Hot Path**: Abre o maior nó de folha, ou pelo menos algo próximo. Na maioria dos casos, este nó está perto de um estrangulamento de desempenho.
* **Etiqueta**: O nome da função ou evento. A árvore apresenta uma mistura de código e eventos que ocorreram, tais como eventos SQL e HTTP. O evento de topo representa a duração global do pedido.
* **Decorrido**: O intervalo de tempo entre o início da operação e o fim da operação.
* **Quando**: O momento em que a função ou evento estava em funcionamento em relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

O perfil de serviço da Microsoft utiliza uma combinação de métodos de amostragem e instrumentação para analisar o desempenho da sua aplicação. Quando a recolha detalhada está em curso, o perfilador de serviço mostra o ponteiro de instruções de cada cpu de cada milésimo de segundo. Cada amostra captura a pilha de chamadas completa do fio que está atualmente a ser executado. Dá informações detalhadas sobre o que esse fio estava a fazer, tanto a um nível elevado como a um baixo nível de abstração. O profiler de serviço também recolhe outros eventos para acompanhar a correlação de atividade e causalidade, incluindo eventos de comutação de contexto, eventos da Task Parallel Library (TPL) e eventos de piscina de fios.

A pilha de chamadas que é exibida na vista da linha do tempo é o resultado da amostragem e instrumentação. Como cada amostra captura a pilha de chamadas completa do fio, inclui código do Microsoft .NET Framework e de outras estruturas que você refere.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Alocação de objetos (clr! JIT \_ Novo ou clr! JIT \_ Newarr1)

**clr! JIT \_ Novo** e **clr! JIT \_ Newarr1** são funções auxiliares em .NET Framework que alocam a memória de uma pilha gerida. **clr! JIT \_ New** é invocado quando um objeto é atribuído. **clr! JIT \_ Newarr1** é invocado quando um conjunto de objetos é atribuído. Estas duas funções são geralmente rápidas e demoram quantidades relativamente pequenas de tempo. Se **clr! JIT \_ Novo** ou **clr! JIT \_ Newarr1** leva muito tempo na sua linha temporal, o código pode estar a alocar muitos objetos e a consumir quantidades significativas de memória.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Código de carregamento (clr! ThePreStub)

**clr! OPreStub** é uma função de ajudante no Quadro .NET que prepara o código para executar pela primeira vez. Esta execução geralmente inclui, mas não se limita a, just-in-time compilação (JIT). Para cada método **C#, clr! OPreStub** deve ser invocado no máximo uma vez durante um processo.

Se **clr! OPreStub** demora muito tempo a pedir um pedido, o pedido é o primeiro a executar esse método. O tempo de funcionamento do quadro .NET para carregar o primeiro método é significativo. Pode considerar a utilização de um processo de aquecimento que execute essa parte do código antes de os seus utilizadores acederem ao mesmo, ou considerar executar o Gerador de Imagem Nativa (ngen.exe) nos seus conjuntos.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Contenção de bloqueio (clr! JITutil \_ MonContention ou clr! JITutil \_ MonEnterWorker)

**clr! JITutil \_ MonContention** ou **clr! JITutil \_ MonEnterWorker** indica que o fio atual está à espera que seja lançado um bloqueio. Este texto é frequentemente exibido quando executa uma declaração **de BLOQUEIO** C#, invoca o método **Monitor.Enter** ou invoca um método com o atributo **MethodImplOptions.Synchronized.** A contenção do bloqueio ocorre geralmente quando o fio _A_ adquire uma fechadura e o fio _B_ tenta adquirir a mesma fechadura antes que o fio _A_ o liberte.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Código de carregamento ([COLD])

Se o nome do método contiver **[COLD]**, como **mscorlib.ni!. COLD]System.Reflection.CustomAttribute.IsDefined**, .NET Framework runtime is executing code for the first time that is optimized by [profile-guided optimization](/cpp/build/profile-guided-optimizations). Para cada método, deve ser exibido no máximo uma vez durante o processo.

Se o código de carregamento demorar um tempo substancial para um pedido, o pedido é o primeiro a executar a parte nãotimizada do método. Considere utilizar um processo de aquecimento que execute essa parte do código antes de os seus utilizadores acederem ao mesmo.

### <a name="send-http-request"></a><a id="httpclientsend"></a>Enviar pedido HTTP

Métodos como **HttpClient.Send** indicam que o código está à espera que um pedido HTTP seja concluído.

### <a name="database-operation"></a><a id="sqlcommand"></a>Operação de base de dados

Métodos como **SqlCommand.Exebonitos** indicam que o código está à espera que uma operação de base de dados termine.

### <a name="waiting-await_time"></a><a id="await"></a>Espera (TEMPO DE \_ ESPERA)

**AGUARDE \_ O TEMPO** indica que o código está à espera que outra tarefa termine. Este atraso costuma acontecer com a declaração C# **AWAIT.** Quando o código faz um C# **AWAIT,** o fio desenrola-se e devolve o controlo à piscina de roscas, e não há fio que esteja bloqueado à espera que o **ESPERA** termine. No entanto, logicamente, o fio que fez o **ESPERA** está "bloqueado", e está à espera que a operação termine. A declaração **\_ do TEMPO DE ESPERA** indica o tempo bloqueado à espera que a tarefa termine.

### <a name="blocked-time"></a><a id="block"></a>Tempo bloqueado

**BLOCKED_TIME** indica que o código está à espera que outro recurso esteja disponível. Por exemplo, pode estar à espera de um objeto de sincronização, de um fio disponível, ou de um pedido de acabamento.

### <a name="unmanaged-async"></a>Async não gerido

.Net framework emite eventos ETW e passa ids de atividade entre fios para que as chamadas de async possam ser rastreadas através de threads. O código não gerido (código nativo) e alguns estilos mais antigos de código assíncronos estão a perder estes eventos e ids de atividade, pelo que o profiler não consegue dizer que fio e que funções estão a funcionar no fio. Isto é rotulado como "Async não gerido" na pilha de chamadas. Se descarregar o ficheiro ETW, poderá utilizar o [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md)  para obter mais informações sobre o que está a acontecer.

### <a name="cpu-time"></a><a id="cpu"></a>Hora do CPU

O CPU está ocupado a executar as instruções.

### <a name="disk-time"></a><a id="disk"></a>Tempo do disco

A aplicação está a realizar operações de disco.

### <a name="network-time"></a><a id="network"></a>Tempo de rede

A aplicação está a realizar operações de rede.

### <a name="when-column"></a><a id="when"></a>Quando coluna

A coluna **When** é uma visualização de como as amostras INCLUSIVAs recolhidas para um nó variam ao longo do tempo. O intervalo total do pedido é dividido em 32 baldes de tempo. As amostras inclusivas para esse nó são acumuladas nesses 32 baldes. Cada balde é representado como um bar. A altura da barra representa um valor escalonado. Para nós marcados **CPU_TIME** ou **BLOCKED_TIME,** ou quando existe uma relação óbvia para consumir um recurso (por exemplo, cpu, disco ou fio), a barra representa o consumo de um dos recursos durante o balde. Para estas métricas, é possível obter um valor superior a 100 por cento consumindo múltiplos recursos. Por exemplo, se usar, em média, dois CPUs durante um intervalo, obtém-se 200%.

## <a name="limitations"></a>Limitações

O período de retenção de dados predefinidos é de cinco dias. Os dados máximos que são ingeridos por dia são de 10 GB.

Não há encargos para a utilização do serviço Profiler. Para que possa usá-lo, a sua aplicação web deve ser hospedada pelo menos no nível básico da funcionalidade de Aplicações Web do Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Sobrecarga e algoritmo de amostragem

O profiler funciona aleatoriamente dois minutos a cada hora em cada máquina virtual que hospeda a aplicação que tem o Profiler ativado para capturar vestígios. Quando o Profiler está em execução, adiciona de 5 a 15% de CPU para o servidor.

## <a name="next-steps"></a>Passos seguintes
Ativar o Profiler de Insights de Aplicação para a sua aplicação Azure. Veja também:
* [Serviços Aplicacionais](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais Azure e conjuntos de escala de máquinas virtuais](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
