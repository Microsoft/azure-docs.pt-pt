---
title: Degradação do desempenho da resolução de problemas
description: Descubra como resolver problemas de desempenho de aplicações lentas no Azure App Service, incluindo monitorizar o comportamento da aplicação, recolher dados e mitigar o problema.
tags: top-support-issue
keywords: desempenho de aplicativo web, app lenta, app lenta
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: cf8b0e1fda03a74d30ec77c911d705bf12cf0126
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763813"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Resolução de problemas problemas de desempenho de aplicações lentas no Azure App Service
Este artigo ajuda-o a resolver problemas de desempenho de aplicações lentas no [Azure App Service](./overview.md).

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn azure e nos fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter **Suporte.**

## <a name="symptom"></a>Sintoma
Quando navega na aplicação, as páginas carregam lentamente e às vezes o tempo limite.

## <a name="cause"></a>Causa
Este problema é muitas vezes causado por problemas de nível de aplicação, tais como:

* pedidos de rede demorando muito tempo
* código de aplicação ou consultas de base de dados sendo ineficientes
* aplicação usando alta memória/CPU
* aplicação crashing devido a uma exceção

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A resolução de problemas pode ser dividida em três tarefas distintas, por ordem sequencial:

1. [Observar e monitorizar o comportamento da aplicação](#observe)
2. [Recolher dados](#collect)
3. [Atenuar a questão](#mitigate)

[O Serviço de Aplicações](overview.md) oferece-lhe várias opções em cada passo.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorizar o comportamento da aplicação
#### <a name="track-service-health"></a>Saúde do Serviço de Rastreio
O Microsoft Azure divulga cada vez que há uma interrupção de serviço ou degradação de desempenho. Pode acompanhar a saúde do serviço no [portal Azure.](https://portal.azure.com/) Para mais informações, consulte [a saúde do serviço track.](../service-health/service-notifications.md)

#### <a name="monitor-your-app"></a>Monitorize a sua aplicação
Esta opção permite-lhe descobrir se a sua aplicação está a ter algum problema. Na lâmina da sua aplicação, clique nos **pedidos e erros** em azulejo. A lâmina **métrica** mostra-lhe todas as métricas que pode adicionar.

Algumas das métricas que pode querer monitorizar para a sua aplicação são

* Conjunto de trabalho de memória média
* Tempo médio de resposta
* Tempo de CPU
* Conjunto de trabalho de memória
* Pedidos

![monitorizar o desempenho da aplicação](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para obter mais informações, veja:

* [Monitorize aplicativos no Azure App Service](web-sites-monitor.md)
* [Receber notificações de alerta](../azure-monitor/platform/alerts-overview.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorar o estado do ponto final da web
Se estiver a executar a sua aplicação no nível de preços **Standard,** o App Service permite-lhe monitorizar dois pontos finais de três localizações geográficas.

A monitorização do ponto final configura testes web a partir de locais geo-distribuídos que testam o tempo de resposta e o tempo de paragem dos URLs web. O teste executa uma operação HTTP GET no URL web para determinar o tempo de resposta e o tempo de funcionamento de cada local. Cada local configurado realiza um teste a cada cinco minutos.

O tempo de utilização é monitorizado utilizando códigos de resposta HTTP e o tempo de resposta é medido em milissegundos. Um teste de monitorização falha se o código de resposta HTTP for superior ou igual a 400 ou se a resposta demorar mais de 30 segundos. Considera-se disponível um ponto final se os seus testes de monitorização forem bem sucedidos em todos os locais especificados.

Para o configurar, consulte [as aplicações do Monitor no Azure App Service](web-sites-monitor.md).

Além disso, consulte [Keep Azure Web Sites up plus Endpoint Monitoring - com Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) para um vídeo na monitorização do ponto final.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorização do desempenho da aplicação utilizando extensões
Também pode monitorizar o desempenho da sua aplicação utilizando uma *extensão do site.*

Cada aplicação do Serviço de Aplicações fornece um ponto final de gestão extensível que lhe permite usar um poderoso conjunto de ferramentas implementadas como extensões do site. As extensões incluem: 

- Editores de código fonte como [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Ferramentas de gestão para recursos conectados, como uma base de dados MySQL ligada a uma aplicação.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) é uma extensão do site de monitorização de desempenho que também está disponível. Para utilizar o Application Insights, reconstrói o seu código com um SDK. Também pode instalar uma extensão que dá acesso a dados adicionais. O SDK permite-lhe escrever código para monitorizar o uso e desempenho da sua aplicação com mais detalhes. Para obter mais informações, consulte [o desempenho do Monitor em aplicações web](../azure-monitor/app/app-insights-overview.md).

<a name="collect"></a>

### <a name="2-collect-data"></a>2. Recolher dados
O Serviço de Aplicações fornece funcionalidade de diagnóstico para registar informações tanto do servidor web como da aplicação web. A informação é separada em diagnósticos de servidores web e diagnósticos de aplicações.

#### <a name="enable-web-server-diagnostics"></a>Ativar diagnósticos de servidor web
Pode ativar ou desativar os seguintes tipos de registos:

* **Registo de erros detalhados** - Informações detalhadas de erro para códigos de estado HTTP que indicam uma falha (código de estado 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que o servidor devolveu o código de erro.
* **Rastreio de pedidos falhados** - Informações detalhadas sobre pedidos falhados, incluindo um traço dos componentes IIS utilizados para processar o pedido e o tempo de cada componente. Isto pode ser útil se estiver a tentar melhorar o desempenho da aplicação ou isolar o que está a causar um erro http específico.
* **Início de Sessão do Servidor Web** - Informações sobre transações HTTP utilizando o formato de ficheiro de registo estendido W3C. Isto é útil para determinar as métricas globais das aplicações, como o número de pedidos tratados ou quantos pedidos são de um endereço IP específico.

#### <a name="enable-application-diagnostics"></a>Ativar diagnósticos de aplicações
Existem várias opções para recolher dados de desempenho da aplicação a partir do App Service, perfilar a sua aplicação ao vivo a partir do Visual Studio ou modificar o seu código de aplicação para registar mais informações e vestígios. Pode escolher as opções com base no acesso que tem à aplicação e no que observou a partir das ferramentas de monitorização.

##### <a name="use-application-insights-profiler"></a>Use o perfil de insights de aplicações
Pode ativar o Profiler Application Insights para começar a capturar vestígios de desempenho detalhados. Podes aceder a vestígios capturados até há cinco dias, quando precisas de investigar que os problemas aconteceram no passado. Pode escolher esta opção desde que tenha acesso ao recurso Application Insights da aplicação no portal Azure.

O Application Insights Profiler fornece estatísticas sobre o tempo de resposta para cada chamada web e vestígios que indicam qual a linha de código que causou as respostas lentas. Por vezes, a aplicação Do Serviço de Aplicações é lenta porque determinado código não está escrito de forma performante. Exemplos incluem código sequencial que pode ser executado em contenções de bloqueio de base de dados paralelas e não-existentes. A remoção destes estrangulamentos no código aumenta o desempenho da aplicação, mas são difíceis de detetar sem configurar vestígios e registos elaborados. Os vestígios recolhidos pelo Application Insights Profiler ajudam a identificar as linhas de código que abrandam a aplicação e superam este desafio para aplicações do App Service.

 Para obter mais informações, consulte [Profiling aplicações ao vivo no Azure App Service com Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Use perfis remotos
No Azure App Service, aplicações web, aplicações API, back ends móveis e WebJobs podem ser perfilado remotamente. Escolha esta opção se tiver acesso ao recurso da aplicação e souber reproduzir o problema, ou se souber o intervalo de tempo exato que o problema de desempenho acontece.

O Perfil Remoto é útil se o uso do CPU do processo for elevado e o seu processo estiver a funcionar mais devagar do que o esperado, ou se a latência dos pedidos HTTP for superior ao normal, pode perfilar remotamente o seu processo e obter as pilhas de chamadas de amostragem do CPU para analisar a atividade do processo e codificar caminhos quentes.

Para obter mais informações, consulte [o suporte de perfis remotos no Serviço de Aplicações Azure.](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service)

##### <a name="set-up-diagnostic-traces-manually"></a>Configurar os vestígios de diagnóstico manualmente
Se tiver acesso ao código fonte da aplicação web, o diagnóstico de aplicação permite-lhe capturar informações produzidas por uma aplicação web. ASP.NET aplicações podem usar a `System.Diagnostics.Trace` classe para registar informações no registo de diagnósticos de aplicação. No entanto, é necessário alterar o código e recolocar a sua aplicação. Este método é recomendado se a sua aplicação estiver a funcionar num ambiente de teste.

Para obter instruções detalhadas sobre como configurar a sua aplicação para registo de registo, consulte [Ativar o registo de diagnósticos de aplicações no Azure App Service](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Utilize a ferramenta de diagnóstico
O App Service proporciona uma experiência inteligente e interativa para o ajudar a resolver problemas na sua aplicação sem necessidade de configuração. Quando tiver problemas com a sua aplicação, a ferramenta de diagnóstico irá indicar o que é errado guiá-lo para as informações certas para resolver e resolver o problema de forma mais fácil e rápida.

Para aceder aos diagnósticos do Serviço de Aplicações, navegue para a sua app app App Service ou App Service Environment no [portal Azure.](https://portal.azure.com) Na navegação à esquerda, clique no **Diagnóstico e resolva problemas.**

#### <a name="use-the-kudu-debug-console"></a>Use a consola Kudu Debug
O App Service vem com uma consola de depuração que podes usar para depurar, explorar, carregar ficheiros, bem como pontos finais JSON para obter informações sobre o teu ambiente. Esta consola chama-se *Consola Kudu* ou *o Painel de Instrumentos SCM* para a sua aplicação.

Pode aceder a este dashboard indo ao link **https:// O nome da sua &lt; aplicação>.scm.azurewebsites.net/**.

Algumas das coisas que Kudu fornece são:

* configurações de ambiente para a sua aplicação
* fluxo de log
* despejo de diagnóstico
* depurar consola na qual pode executar cmdlets PowerShell e comandos básicos DOS.

Outra característica útil da Kudu é que, caso a sua aplicação esteja a lançar exceções de primeira oportunidade, pode usar a Kudu e a ferramenta SysInternals Procdump para criar despejos de memória. Estes despejos de memória são instantâneos do processo e podem muitas vezes ajudá-lo a resolver problemas mais complicados com a sua app.

Para obter mais informações sobre as funcionalidades disponíveis em Kudu, consulte [as ferramentas Azure DevOps que deve conhecer.](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. Atenuar a questão
#### <a name="scale-the-app"></a>Dimensione a aplicação
No Azure App Service, para maior desempenho e produção, pode ajustar a escala em que está a executar a sua aplicação. O escalonamento de uma aplicação envolve duas ações relacionadas: alterar o seu plano de Serviço de Aplicações para um nível de preços mais elevado e configurar determinadas definições depois de ter mudado para o nível de preços mais elevado.

Para obter mais informações sobre o dimensionamento, consulte [Scale uma aplicação no Azure App Service](manage-scale-up.md).

Além disso, pode optar por executar a sua aplicação em mais de um caso. A escala não só lhe proporciona mais capacidade de processamento, como também lhe dá alguma tolerância à falha. Se o processo se der em curso numa instância, as outras instâncias continuam a servir pedidos.

Pode configurar a escala para ser manual ou automática.

#### <a name="use-autoheal"></a>Use AutoHeal
O AutoHeal recicla o processo de trabalhador para a sua aplicação com base nas definições que escolhe (como alterações de configuração, pedidos, limites baseados na memória ou o tempo necessário para executar um pedido). Na maior parte do tempo, reciclar o processo é a forma mais rápida de recuperar de um problema. Embora possa sempre reiniciar a aplicação diretamente dentro do portal Azure, o AutoHeal fá-la automaticamente para si. Tudo o que precisa fazer é adicionar alguns gatilhos na raiz web.config para a sua aplicação. Estas definições funcionariam da mesma forma, mesmo que a sua aplicação não seja uma aplicação .NET.

Para obter mais informações, consulte [os Web Sites Azure de auto-cura.](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)

#### <a name="restart-the-app"></a>Reiniciar a aplicação
Reiniciar é muitas vezes a forma mais simples de recuperar de problemas únicos. No [portal Azure](https://portal.azure.com/), na lâmina da sua aplicação, tem as opções para parar ou reiniciar a sua aplicação.

 ![reiniciar app para resolver problemas de desempenho](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Também pode gerir a sua aplicação utilizando o Azure PowerShell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../azure-resource-manager/management/manage-resources-powershell.md).