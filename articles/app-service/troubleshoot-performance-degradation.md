---
title: Solucionar problemas de degradação de desempenho-serviço de Azure App | Microsoft Docs
description: Este artigo ajuda a solucionar problemas de desempenho de aplicativo lento no serviço Azure App.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: desempenho do aplicativo Web, aplicativo lento, lento do aplicativo
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 02a214c10c5b111070127e4a823682989f24b20b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637729"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Solucionar problemas de desempenho de aplicativo lento no serviço Azure App
Este artigo ajuda a solucionar problemas de desempenho de aplicativo lento no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714).

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure no [msdn do Azure e nos fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**.

## <a name="symptom"></a>Sintoma
Quando você navega no aplicativo, as páginas são carregadas lentamente e, às vezes, o tempo limite.

## <a name="cause"></a>Causa
Esse problema geralmente é causado por problemas no nível do aplicativo, como:

* solicitações de rede demorando muito
* o código do aplicativo ou as consultas do banco de dados são ineficientes
* aplicativo usando memória/CPU alta
* falha no aplicativo devido a uma exceção

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A solução de problemas pode ser dividida em três tarefas distintas, em ordem sequencial:

1. [Observar e monitorar o comportamento do aplicativo](#observe)
2. [Coletar dados](#collect)
3. [Atenuar o problema](#mitigate)

O [serviço de aplicativo](overview.md) oferece várias opções em cada etapa.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorar o comportamento do aplicativo
#### <a name="track-service-health"></a>Acompanhar a integridade do serviço
Microsoft Azure publicizes cada vez que houver uma interrupção de serviço ou degradação de desempenho. Você pode acompanhar a integridade do serviço no [portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [acompanhar a integridade do serviço](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorar seu aplicativo
Essa opção permite que você descubra se o aplicativo está tendo problemas. Na folha do aplicativo, clique no bloco **solicitações e erros** . A folha **métrica** mostra todas as métricas que você pode adicionar.

Algumas das métricas que você pode querer monitorar para seu aplicativo são

* Média do conjunto de trabalho de memória
* Tempo médio de resposta
* Tempo de CPU
* Conjunto de trabalho de memória
* Pedidos

![monitorar o desempenho do aplicativo](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitorar aplicativos no serviço Azure App](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorar o status do ponto de extremidade da Web
Se você estiver executando seu aplicativo no tipo de preço **Standard** , o serviço de aplicativo permitirá que você monitore dois pontos de extremidade de três locais geográficos.

O monitoramento de ponto de extremidade configura os testes da Web de locais distribuídos geograficamente que testam o tempo de resposta e a atividade da Web. O teste executa uma operação HTTP GET na URL da Web para determinar o tempo de resposta e a duração de cada local. Cada local configurado executa um teste a cada cinco minutos.

O tempo de atividade é monitorado usando códigos de resposta HTTP, e é medido em milissegundos. Um teste de monitoramento falhará se o código de resposta HTTP for maior ou igual a 400 ou se a resposta demorar mais de 30 segundos. Um ponto de extremidade será considerado disponível se seus testes de monitoramento forem bem-sucedidos de todos os locais especificados.

Para configurá-lo, consulte [monitorar aplicativos no serviço Azure app](web-sites-monitor.md).

Além disso, veja [mantendo os sites do Azure ativos mais o monitoramento de ponto de extremidade-com Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) para um vídeo sobre monitoramento de ponto de extremidade.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitoramento do desempenho de aplicativos usando extensões
Você também pode monitorar o desempenho do aplicativo usando uma *extensão de site*.

Cada aplicativo do serviço de aplicativo fornece um ponto de extremidade de gerenciamento extensível que permite que você use um conjunto poderoso de ferramentas implantadas como extensões de site. As extensões incluem: 

- Editores de código-fonte como o [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Ferramentas de gerenciamento para recursos conectados, como um banco de dados MySQL conectado a um aplicativo.

O [aplicativo Azure](https://azure.microsoft.com/services/application-insights/) insights é uma extensão de site de monitoramento de desempenho que também está disponível. Para usar Application Insights, recompile seu código com um SDK. Você também pode instalar uma extensão que fornece acesso a dados adicionais. O SDK permite que você escreva código para monitorar o uso e o desempenho do seu aplicativo com mais detalhes. Para obter mais informações, consulte [monitorar o desempenho em aplicativos Web](../azure-monitor/app/web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Recolher dados
O serviço de aplicativo fornece a funcionalidade de diagnóstico para registrar informações do servidor Web e do aplicativo Web. As informações são separadas no diagnóstico do servidor Web e no Application Diagnostics.

#### <a name="enable-web-server-diagnostics"></a>Habilitar o diagnóstico do servidor Web
Você pode habilitar ou desabilitar os seguintes tipos de logs:

* **Log de erros detalhado** -informações detalhadas de erro para códigos de status HTTP que indicam uma falha (código de status 400 ou superior). Isso pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro.
* **Rastreamento de solicitação com falha** -informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes do IIS usados para processar a solicitação e o tempo gasto em cada componente. Isso pode ser útil se você estiver tentando melhorar o desempenho do aplicativo ou isolar o que está causando um erro de HTTP específico.
* **Log de servidor Web** -informações sobre transações http usando o formato de arquivo de log estendido W3C. Isso é útil ao determinar as métricas gerais do aplicativo, como o número de solicitações tratadas ou quantas solicitações são de um endereço IP específico.

#### <a name="enable-application-diagnostics"></a>Habilitar o Application Diagnostics
Há várias opções para coletar dados de desempenho do aplicativo do serviço de aplicativo, criar o perfil de seu aplicativo ao vivo no Visual Studio ou modificar o código do aplicativo para registrar mais informações e rastreamentos. Você pode escolher as opções com base na quantidade de acesso que você tem ao aplicativo e o que você observou das ferramentas de monitoramento.

##### <a name="use-application-insights-profiler"></a>Usar Application Insights Profiler
Você pode habilitar a Application Insights Profiler para iniciar a captura de rastreamentos de desempenho detalhados. Você pode acessar rastreamentos capturados até cinco dias atrás quando você precisa investigar problemas ocorridos no passado. Você pode escolher essa opção contanto que tenha acesso ao recurso de Application Insights do aplicativo em portal do Azure.

Application Insights Profiler fornece estatísticas sobre o tempo de resposta para cada chamada da Web e rastreamentos que indicam qual linha de código causou as respostas lentas. Às vezes, o aplicativo do serviço de aplicativo está lento porque determinado código não é escrito de forma de alto desempenho. Os exemplos incluem código sequencial que pode ser executado em contenções de bloqueio de banco de dados paralelas e indesejadas. A remoção desses afunilamentos no código aumenta o desempenho do aplicativo, mas é difícil detectar sem configurar rastreamentos e logs elaborados. Os rastreamentos coletados pelo Application Insights Profiler ajudam a identificar as linhas de código que tornam o aplicativo lento e superar esse desafio para aplicativos do serviço de aplicativo.

 Para obter mais informações, consulte [criação de perfil de aplicativos em tempo real no serviço de Azure app com Application insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Usar criação de perfil remota
No Azure App Service, aplicativos Web, aplicativos de API, back-ends móveis e webjobs podem ser Profiles remotamente. Escolha esta opção se você tiver acesso ao recurso de aplicativo e souber como reproduzir o problema, ou se souber o intervalo de tempo exato que o problema de desempenho ocorre.

A criação de perfil remota é útil se o uso da CPU do processo for alto e o processo estiver sendo executado mais devagar do que o esperado ou se a latência de solicitações HTTP for maior que o normal, você poderá criar o perfil do processo remotamente e obter as pilhas de chamadas de amostragem da CPU para analisar o processo caminhos ativos de atividade e código.

Para obter mais informações, consulte [suporte de criação de perfil remota no serviço de Azure app](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Configurar rastreamentos de diagnóstico manualmente
Se você tiver acesso ao código-fonte do aplicativo Web, o Application Diagnostics permitirá que você capture informações produzidas por um aplicativo Web. Os aplicativos ASP.net podem usar `System.Diagnostics.Trace` a classe para registrar informações no log do Application Diagnostics. No entanto, você precisa alterar o código e reimplantar seu aplicativo. Esse método é recomendado se seu aplicativo estiver em execução em um ambiente de teste.

Para obter instruções detalhadas sobre como configurar seu aplicativo para registro em log, consulte [habilitar o log de diagnóstico para aplicativos no serviço Azure app](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Usar a ferramenta de diagnóstico
O serviço de aplicativo fornece uma experiência inteligente e interativa para ajudá-lo a solucionar problemas de seu aplicativo sem nenhuma configuração necessária. Quando você tiver problemas com seu aplicativo, a ferramenta de diagnóstico indicará o que há de errado para orientá-lo sobre as informações certas para solucionar problemas e resolver o problema com mais facilidade e rapidez.

Para acessar o diagnóstico do serviço de aplicativo, navegue até o aplicativo do serviço de aplicativo ou Ambiente do Serviço de Aplicativo na [portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **diagnosticar e resolver problemas**.

#### <a name="use-the-kudu-debug-console"></a>Usar o console de depuração do kudu
O serviço de aplicativo vem com um console de depuração que você pode usar para depurar, explorar, carregar arquivos, bem como pontos de extremidade JSON para obter informações sobre o seu ambiente. Esse console é chamado de *console do kudu* ou do *painel do SCM* para seu aplicativo.

Você pode acessar esse painel acessando o link **https://&lt;Your app Name >. SCM. azurewebsites. net/** .

Algumas das coisas que o kudu fornece são:

* configurações de ambiente para seu aplicativo
* fluxo de log
* despejo de diagnóstico
* console de depuração no qual você pode executar os cmdlets do PowerShell e os comandos básicos do DOS.

Outro recurso útil do kudu é que, caso seu aplicativo esteja lançando exceções de primeira chance, você pode usar o Kudu e a ferramenta SysInternals Procdump para criar despejos de memória. Esses despejos de memória são instantâneos do processo e, muitas vezes, podem ajudá-lo a solucionar problemas mais complicados com seu aplicativo.

Para obter mais informações sobre os recursos disponíveis no kudu, consulte [ferramentas de DevOps do Azure que você deve conhecer](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Atenuar o problema
#### <a name="scale-the-app"></a>Dimensionar o aplicativo
No Azure App Service, para aumentar o desempenho e a taxa de transferência, você pode ajustar a escala na qual você está executando seu aplicativo. Escalar verticalmente um aplicativo envolve duas ações relacionadas: alterar o plano do serviço de aplicativo para um tipo de preço mais alto e definir determinadas configurações depois que você tiver alternado para o tipo de preço mais alto.

Para obter mais informações sobre o dimensionamento, consulte [dimensionar um aplicativo no serviço Azure app](manage-scale-up.md).

Além disso, você pode optar por executar seu aplicativo em mais de uma instância. Escalar horizontalmente não só fornece mais capacidade de processamento, mas também oferece uma quantidade de tolerância a falhas. Se o processo falhar em uma instância, as outras instâncias continuarão atendendo às solicitações.

Você pode definir o dimensionamento como manual ou automático.

#### <a name="use-autoheal"></a>Usar a autoreparo
A autoreparo recicla o processo de trabalho para seu aplicativo com base nas configurações escolhidas (como alterações de configuração, solicitações, limites baseados na memória ou no tempo necessário para executar uma solicitação). Na maioria das vezes, reciclar o processo é a maneira mais rápida de se recuperar de um problema. Embora você sempre possa reiniciar o aplicativo diretamente dentro do portal do Azure, a recuperação automática faz isso automaticamente para você. Tudo o que você precisa fazer é adicionar alguns gatilhos no Web. config raiz para seu aplicativo. Essas configurações funcionarão da mesma maneira mesmo que seu aplicativo não seja um aplicativo .NET.

Para obter mais informações, consulte [auto-auto-recuperável Web sites do Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Reiniciar o aplicativo
A reinicialização geralmente é a maneira mais simples de se recuperar de problemas de uma só vez. Na [portal do Azure](https://portal.azure.com/), na folha do aplicativo, você tem as opções para parar ou reiniciar o aplicativo.

 ![reiniciar o aplicativo para resolver problemas de desempenho](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Você também pode gerenciar seu aplicativo usando o Azure PowerShell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../powershell-azure-resource-manager.md).
