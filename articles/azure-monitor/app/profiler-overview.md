---
title: Perfil de aplicativos de produção no Azure com o Application Insights Profiler | Documentos da Microsoft
description: Identifique o caminho instantâneo em seu código de servidor web com um criador de perfil de baixa capacidade.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: c42de8cf189c0ebaf5f13ef5971ad91d14d862fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850280"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Aplicações de produção do perfil no Azure com o Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Ativar o Application Insights Profiler para a sua aplicação

De Azure Application Insights Profiler fornece rastreios de desempenho para aplicativos em execução em produção no Azure. Profiler captura os dados automaticamente à escala sem afetar negativamente os seus utilizadores. Profiler ajuda a identificar o caminho de códigos "ativos" que demora muito tempo quando ele está a processar um pedido web específico. 

Profiler funciona com aplicações de .NET que são implementadas nos seguintes serviços do Azure. Instruções específicas para ativar o Profiler para cada tipo de serviço estão nos links a seguir.

* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Conjuntos de dimensionamento de máquinas virtuais e máquinas virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**Pré-visualização** aplicações ASP.NET Core a Web do Linux do Azure](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Se tiver ativado o Profiler, mas não vê rastreios, verifique nossos [guia de resolução de problemas](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Ver dados do Profiler

Para o Profiler carregar os rastreios, seu aplicativo deve ser ativamente processar pedidos. Se estiver fazendo uma experimentação, pode gerar solicitações à sua aplicação web, utilizando [testes de desempenho do Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Se ativou Profiler recentemente, pode executar um teste de carga curto. Enquanto o teste de carga está em execução, selecione o **perfil agora** botão a [ **Profiler definições** painel](profiler-settings.md#profiler-settings-pane). Quando o Profiler está em execução, perfis aleatoriamente aproximadamente uma vez por hora e durante um período de dois minutos. Se a sua aplicação está a processar um fluxo constante de pedidos, o Profiler carrega rastreios a cada hora.

Depois da aplicação recebe algum tráfego e Profiler tenha tido tempo para carregar os rastreios, deve ter rastreios para ver. Este processo pode demorar 5 a 10 minutos. Para ver os rastreios, além da **desempenho** painel, selecione **tomar ações**e, em seguida, selecione o **rastreios do Profiler** botão.

![Rastreios do Profiler de visualização de painel de informações de desempenho do aplicativo][performance-blade]

Selecione um exemplo para exibir uma divisão de nível de código de tempo gasto para executar o pedido.

![Explorador de rastreio do Application Insights][trace-explorer]

O Explorador de rastreio apresenta as seguintes informações:

* **Mostrar caminho mais utilizado**: É aberto o maior folha nó, ou, pelo menos, algo fechar. Na maioria dos casos, este nó está perto de um estrangulamento do desempenho.
* **Etiqueta**: O nome da função ou eventos. A árvore apresenta uma combinação de código e eventos que ocorreram, como o SQL e eventos HTTP. O evento superior representa a duração do pedido geral.
* **Decorrido**: O intervalo de tempo entre o início da operação e o final da operação.
* **When**: A hora quando a função ou o evento estava em execução em relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

O criador de perfil de serviço do Microsoft utiliza uma combinação de métodos de amostragem e instrumentação para analisar o desempenho da sua aplicação. Quando a coleção detalhada está em curso, o criador de perfil de serviço copia o ponteiro de instrução de cada CPU do computador cada milissegundo. Cada exemplo captura a pilha de chamadas completa do thread que está sendo executado. Ele fornece informações detalhadas sobre o que esse thread estava fazendo, num alto nível e um baixo nível de abstração. O criador de perfil de serviço também recolhe outros eventos para controlar a correlação da atividade e causalidade, incluindo eventos, eventos de tarefas TPL (biblioteca paralela) e eventos de pool de threads de alternância de contexto.

A pilha de chamadas que é apresentada na vista de linha de tempo é o resultado da amostragem e instrumentação. Uma vez que cada exemplo captura a pilha de chamadas completa do thread, inclui código de Microsoft .NET Framework e de outras estruturas que referencia.

### <a id="jitnewobj"></a>Alocação de objeto (clr! JIT\_novo ou clr! JIT\_Newarr1)

**CLR! JIT\_New** e **clr! JIT\_Newarr1** são funções auxiliares no .NET Framework que alocar a memória de um heap gerenciado. **CLR! JIT\_New** é invocado quando um objeto é alocado. **CLR! JIT\_Newarr1** é invocado quando uma matriz de objeto é alocada. Essas duas funções são, normalmente, rápidas e levar quantidades relativamente pequenas de tempo. Se **clr! JIT\_New** ou **clr! JIT\_Newarr1** assume um muito tempo na sua linha de tempo, o código poderá ser alocando muitos objetos e consumir quantidades significativas de memória.

### <a id="theprestub"></a>Carregamento de código (clr! ThePreStub)

**CLR! ThePreStub** é uma função de auxiliar no .NET Framework, que prepara o código seja executado pela primeira vez. Normalmente, esta execução inclui, mas não está limitada a compilação do just-in-time (JIT). Para cada C# método, **clr! ThePreStub** deve ser invocado no máximo uma vez durante um processo.

Se **clr! ThePreStub** demora muito tempo para um pedido, o pedido é o primeiro para executar esse método. A hora de tempo de execução do .NET Framework carregar o primeiro método é significativa. Considere o uso de um processo de aquecimento que executa essa parte do código antes dos utilizadores aceder ou considere executar o gerador de imagem nativa (ngen.exe) em seus assemblies.

### <a id="lockcontention"></a>Contenção de bloqueio (clr! JITutil\_MonContention ou clr! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** ou **clr! JITutil\_MonEnterWorker** indica que o thread atual está a aguardar um bloqueio ser lançado. Este texto, muitas vezes, é apresentado quando executa um C# **bloqueio** instrução, invocar o **ENTER** método, ou invocar um método com o **Methodimploptions**atributo. Contenção de bloqueio geralmente ocorre quando thread _um_ adquire um bloqueio e o thread _B_ tenta adquirir o bloqueio mesmo antes de thread _A_ libera-lo.

### <a id="ngencold"></a>Carregamento de código ([frio])

Se o nome do método contiver **[frio]**, tal como **mscorlib.ni! [ COLD]System.Reflection.CustomAttribute.IsDefined**, o tempo de execução do .NET Framework esteja executando código pela primeira vez que não está otimizada por [otimização orientada por perfil](https://msdn.microsoft.com/library/e7k32f4k.aspx). Para cada método, que deve ser exibida no máximo uma vez durante o processo.

Se o carregamento de código leva uma quantidade substancial de tempo para um pedido, o pedido é o primeiro para executar a parte não otimizada do método. Considere a utilização de um processo de aquecimento que executa essa parte do código antes dos utilizadores aceder a ele.

### <a id="httpclientsend"></a>Enviar o pedido HTTP

Métodos como **HttpClient.Send** indicar que o código está aguardando uma solicitação HTTP para ser concluído.

### <a id="sqlcommand"></a>Operação de base de dados

Métodos como **SqlCommand.Execute** indicar que o código está à espera que uma operação de base de dados concluir.

### <a id="await"></a>A aguardar (AWAIT\_tempo)

**AWAIT\_tempo** indica que o código está a aguardar conclusão da outra tarefa. Este atraso normalmente acontece com o C# **AWAIT** declaração. Quando o código faz uma C# **AWAIT**, o thread se Desdobra e retorna o controle ao pool de segmentos, e não há nenhum thread que está bloqueada aguardando o **AWAIT** para concluir. No entanto, logicamente, o thread que fez a **AWAIT** for "bloqueado," e está a aguardar a conclusão da operação. O **AWAIT\_tempo** instrução indica o tempo bloqueado aguardando a conclusão da tarefa.

### <a id="block"></a>Tempo bloqueado

**BLOCKED_TIME** indica que o código está a aguardar outro recurso esteja disponível. Por exemplo, pode aguardar para um objeto de sincronização, para um thread esteja disponível ou para um pedido para concluir.

### <a name="unmanaged-async"></a>Assíncrono Não Gerido

.NET framework emite eventos do ETW e transmite os ids de atividade entre threads para que as chamadas assíncronas podem ser controladas entre threads. Código não gerenciado (código nativo) e alguns estilos mais antigos do código assíncrono estão em falta esses eventos e ids de atividade, para que o criador de perfil não é possível dizer qual thread e quais funções estão sendo executados no thread. Isso tem o nome "Async não gerenciado" na pilha de chamadas. Se baixar o arquivo do ETW, poderá conseguir utilizar [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) para obter mais informações sobre o que está acontecendo.

### <a id="cpu"></a>Tempo de CPU

A CPU está ocupada executando as instruções.

### <a id="disk"></a>Tempo de disco

A aplicação está a efetuar operações de disco.

### <a id="network"></a>Tempo da rede

A aplicação está a efetuar operações de rede.

### <a id="when"></a>Quando coluna

O **quando** coluna é uma visualização de como os exemplos, INCLUSIVE recolhidos para um nó variam ao longo do tempo. O intervalo total do pedido está dividido em 32 registos de tempo. Os exemplos, inclusivos para esse nó são acumulados nessas 32 partições. Cada classificação é representada como uma barra. A altura da barra de representa um valor dimensionado. Para nós que estão marcados **CPU_TIME** ou **BLOCKED_TIME**, ou onde existe uma relação óbvia a consumindo um recurso (por exemplo, uma CPU, disco ou thread), a barra representa o consumo de um dos os recursos durante o registo. Para estas métricas, é possível obter um valor superior a 100 por cento de consumir vários recursos. Por exemplo, se usar, em média, duas CPUs durante um intervalo, terá 200 por cento.

## <a name="limitations"></a>Limitações

O período de retenção de dados predefinido é de cinco dias. A máxima de dados que é ingerida por dia é de 10 GB.

Não existem custos para utilizar o serviço do Profiler. Para poder utilizá-lo, a aplicação web tem de ser hospedado em, pelo menos, o escalão básico da funcionalidade Web Apps do App Service do Azure.

## <a name="overhead-and-sampling-algorithm"></a>Sobrecarga e algoritmo de amostragem

Profiler aleatoriamente executa dois minutos por hora em cada máquina virtual que aloja a aplicação que tenha ativado para capturar rastreios do Profiler. Quando o Profiler está em execução, ele adiciona entre 5 para 15% a sobrecarga da CPU para o servidor.

## <a name="next-steps"></a>Passos Seguintes
Ative o Application Insights Profiler para a sua aplicação do Azure. Consulte também:
* [Serviços Aplicacionais](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Conjuntos de dimensionamento de máquinas virtuais e máquinas virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
