---
title: Degradação do desempenho de resolução de problemas
description: Descubra como resolver problemas de desempenho de aplicações lentas no Azure App Service, incluindo monitorizar o comportamento das aplicações, recolher dados e atenuar o problema.
tags: top-support-issue
keywords: desempenho da aplicação web, app lenta, app lenta
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: 98c11a72b5aea0fac15d943977402289dc33a970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688303"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Problemas de desempenho lento de aplicações no Serviço de Aplicações Azure
Este artigo ajuda-o a resolver problemas de desempenho lento de aplicações no [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode arquivar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter **Suporte**.

## <a name="symptom"></a>Sintoma
Quando navega na aplicação, as páginas carregam lentamente e, por vezes, o tempo de paragem.

## <a name="cause"></a>Causa
Este problema é muitas vezes causado por questões de nível de aplicação, tais como:

* pedidos de rede demorando muito tempo
* código de aplicação ou consultas de base de dados sendo ineficientes
* aplicação utilizando alta memória/CPU
* falha de aplicação devido a uma exceção

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A resolução de problemas pode ser dividida em três tarefas distintas, por ordem sequencial:

1. [Observar e monitorizar o comportamento da aplicação](#observe)
2. [Recolher dados](#collect)
3. [Atenuar a questão](#mitigate)

[O Serviço de Aplicações](overview.md) oferece-lhe várias opções em cada passo.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorizar o comportamento da aplicação
#### <a name="track-service-health"></a>Saúde do Serviço de Rastreio
O Microsoft Azure divulga cada vez que há uma interrupção de serviço ou degradação do desempenho. Pode acompanhar a saúde do serviço no [portal Azure.](https://portal.azure.com/) Para mais informações, consulte [track service health](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorize a sua aplicação
Esta opção permite-lhe descobrir se a sua aplicação está a ter algum problema. Na lâmina da sua aplicação, clique nos **pedidos e erros.** A lâmina **Métrica** mostra-lhe todas as métricas que pode adicionar.

Algumas das métricas que você pode querer monitorizar para a sua app são

* Conjunto de trabalho médio da memória
* Tempo médio de resposta
* Tempo cpu
* Conjunto de trabalho de memória
* Pedidos

![monitorizar o desempenho da aplicação](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitorize aplicações no Serviço de Aplicações Azure](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorizar o estado do ponto final da web
Se estiver a executar a sua aplicação no nível de preços **Standard,** o App Service permite-lhe monitorizar dois pontos finais de três localizações geográficas.

A monitorização do endpoint configura os testes web de locais geodistribuídos que testam o tempo de resposta e o tempo de uptime dos URLs web. O teste realiza uma operação HTTP GET no URL web para determinar o tempo de resposta e o tempo de aditado de cada local. Cada local configurado executa um teste a cada cinco minutos.

O tempo de uptime é monitorizado usando códigos de resposta HTTP, e o tempo de resposta é medido em milissegundos. Um teste de monitorização falha se o código de resposta HTTP for maior ou igual a 400 ou se a resposta demorar mais de 30 segundos. Considera-se disponível um ponto final se os seus testes de monitorização forem bem sucedidos em todos os locais especificados.

Para configurar, consulte [aplicações Monitor no Serviço de Aplicações Azure](web-sites-monitor.md).

Além disso, consulte [Keep Azure Web Sites up plus Endpoint Monitoring - com Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) para um vídeo na monitorização do ponto final.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorização do desempenho da aplicação utilizando extensões
Também pode monitorizar o desempenho da sua aplicação utilizando uma *extensão*do site .

Cada aplicação do App Service fornece um ponto final de gestão extensível que permite utilizar um poderoso conjunto de ferramentas implementadas como extensões do site. As extensões incluem: 

- Editores de código seleções como [o Azure DevOps.](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) 
- Ferramentas de gestão para recursos conectados, como uma base de dados MySQL ligada a uma aplicação.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) é uma extensão do site de monitorização de desempenho que também está disponível. Para utilizar os Insights de Aplicação, reconstrói o seu código com um SDK. Também pode instalar uma extensão que fornece acesso a dados adicionais. O SDK permite-lhe escrever código para monitorizar o uso e desempenho da sua aplicação com mais detalhes. Para mais informações, consulte o [desempenho do Monitor nas aplicações web](../azure-monitor/app/web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Recolher dados
O Serviço de Aplicações fornece funcionalidadede diagnóstico para registar informações tanto do servidor web como da aplicação web. A informação é separada em diagnósticos de servidores web e diagnósticos de aplicações.

#### <a name="enable-web-server-diagnostics"></a>Ativar diagnósticos de servidorweb
Pode ativar ou desativar os seguintes tipos de registos:

* **Registo de erros detalhados** - Informações detalhadas de erro para códigos de estado HTTP que indicam uma falha (código de estado 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que o servidor devolveu o código de erro.
* **Rastreio de Pedido Falhado** - Informações detalhadas sobre pedidos falhados, incluindo um vestígio dos componentes IIS utilizados para processar o pedido e o tempo decorrido em cada componente. Isto pode ser útil se estiver a tentar melhorar o desempenho da aplicação ou isolar o que está a causar um erro http específico.
* **Registo do servidor web** - Informações sobre transações HTTP utilizando o formato de ficheiro de registo alargado W3C. Isto é útil para determinar as métricas globais da aplicação, como o número de pedidos tratados ou quantos pedidos são de um endereço IP específico.

#### <a name="enable-application-diagnostics"></a>Ativar diagnósticos de aplicações
Existem várias opções para recolher dados de desempenho da aplicação do App Service, perfilar a sua aplicação em direto do Visual Studio ou modificar o seu código de aplicação para registar mais informações e vestígios. Pode escolher as opções com base na quantidade de acesso que tem à aplicação e no que observou a partir das ferramentas de monitorização.

##### <a name="use-application-insights-profiler"></a>Utilizar perfilador de insights de aplicação
Pode ativar o Perfil de Insights de Aplicação para começar a capturar vestígios de desempenho detalhados. Podes aceder a vestígios capturados até cinco dias atrás quando precisas de investigar os problemas que aconteceram no passado. Pode escolher esta opção desde que tenha acesso ao recurso Insights de Aplicação da aplicação no portal Azure.

O Profiler de Insights de Aplicação fornece estatísticas sobre o tempo de resposta para cada chamada web e vestígios que indicam que linha de código causou as respostas lentas. Por vezes, a aplicação App Service é lenta porque determinado código não é escrito de forma performativa. Exemplos incluem código sequencial que pode ser executado em argumentos de bloqueio de bases de dados paralelos e indesejados. A remoção destes estrangulamentos no código aumenta o desempenho da aplicação, mas são difíceis de detetar sem configurar vestígios e registos elaborados. Os vestígios recolhidos pelo Application Insights Profiler ajudam a identificar as linhas de código que atrasam a aplicação e superam este desafio para aplicações do App Service.

 Para mais informações, consulte Profiling aplicações ao vivo no Serviço de [Aplicações Azure com Insights de Aplicação](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Utilizar perfis remotos
No Azure App Service, aplicações web, aplicações API, back ends móveis e WebJobs podem ser perfilados remotamente. Escolha esta opção se tiver acesso ao recurso da aplicação e souber reproduzir o problema, ou se sabe o intervalo de tempo exato que o problema de desempenho acontece.

O Perfil Remoto é útil se o uso do processo do CPU for elevado e o seu processo estiver a decorrer mais devagar do que o esperado, ou se a latência dos pedidos http for superior ao normal, pode remotamente perfilar o seu processo e obter as pilhas de chamadas de amostragem cpU para analisar o processo atividade e código caminhos quentes.

Para mais informações, consulte o suporte de Perfis Remotos no Serviço de [Aplicações Azure](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Configurar os vestígios de diagnóstico manualmente
Se tiver acesso ao código fonte da aplicação web, o diagnóstico da aplicação permite-lhe capturar informações produzidas por uma aplicação web. ASP.NET aplicações podem `System.Diagnostics.Trace` usar a classe para registar informações no registo de diagnósticos da aplicação. No entanto, precisa de alterar o código e recolocar a sua aplicação. Este método é recomendado se a sua aplicação estiver em execução num ambiente de teste.

Para obter instruções detalhadas sobre como configurar a sua aplicação para o registo, consulte Ativar o registo de [diagnósticos para aplicações no Serviço de Aplicações Azure](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Utilize a ferramenta de diagnóstico
O Serviço de Aplicações proporciona uma experiência inteligente e interativa para ajudá-lo a resolver problemas com a sua aplicação sem necessidade de configuração. Quando tiver problemas com a sua aplicação, a ferramenta de diagnóstico irá apontar o que está errado em guiá-lo para a informação certa para mais facilmente e rapidamente resolver o problema.

Para aceder aos diagnósticos do Serviço de Aplicações, navegue para a sua app de serviço de aplicações ou ambiente de serviço de aplicações no [portal Azure](https://portal.azure.com). Na navegação à esquerda, clique em **Diagnosticar e resolver problemas**.

#### <a name="use-the-kudu-debug-console"></a>Use a consola Kudu Debug
O Serviço de Aplicações vem com uma consola de depuração que podes usar para depurar, explorar, carregar ficheiros, bem como pontos finais da JSON para obter informações sobre o seu ambiente. Esta consola chama-se *Consola Kudu* ou *Painel SCM* para a sua aplicação.

Pode aceder a este dashboard indo ao link **https://&lt;O nome da aplicação>.scm.azurewebsites.net/**.

Algumas das coisas que Kudu fornece são:

* configurações ambientais para a sua aplicação
* fluxo de log
* despejo de diagnóstico
* consola de depuração na qual pode executar cmdlets Powershell e comandos DOS básicos.

Outra característica útil da Kudu é que, caso a sua aplicação esteja a lançar exceções de primeira oportunidade, pode usar kudu e a ferramenta SysInternals Procdump para criar depósitos de memória. Estes depósitos de memória são instantâneos do processo e podem muitas vezes ajudá-lo a resolver problemas mais complicados com a sua aplicação.

Para obter mais informações sobre as funcionalidades disponíveis em Kudu, consulte [as ferramentas Azure DevOps que deve conhecer](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Atenuar a questão
#### <a name="scale-the-app"></a>Escala a app
No Serviço de Aplicações Azure, para um maior desempenho e entrada, pode ajustar a escala em que está a executar a sua aplicação. A escalação de uma aplicação envolve duas ações relacionadas: alterar o seu plano de Serviço de Aplicações para um nível de preços mais elevado, e configurar certas definições depois de ter mudado para o nível de preços mais elevado.

Para obter mais informações sobre escala, consulte [Scale uma aplicação no Azure App Service](manage-scale-up.md).

Além disso, pode optar por executar a sua candidatura em mais de um caso. A escalação não só lhe proporciona mais capacidade de processamento, como também lhe dá alguma tolerância à falha. Se o processo se der por uma instância, as outras instâncias continuam a servir pedidos.

Pode definir a escala para ser Manual ou Automática.

#### <a name="use-autoheal"></a>Use AutoHeal
A AutoHeal recicla o processo do trabalhador para a sua aplicação com base em configurações que escolher (como alterações de configuração, pedidos, limites baseados na memória ou o tempo necessário para executar um pedido). Na maior parte das vezes, reciclar o processo é a forma mais rápida de recuperar de um problema. Embora possa sempre reiniciar a aplicação diretamente dentro do portal Azure, o AutoHeal fá-lo automaticamente para si. Tudo o que precisa fazer é adicionar alguns gatilhos na root web.config para a sua aplicação. Estas definições funcionariam da mesma forma mesmo que a sua aplicação não seja uma aplicação .NET.

Para mais informações, consulte Web [Sites Azure de cura automática](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Reiniciar a aplicação
Reiniciar é muitas vezes a forma mais simples de recuperar de problemas únicos. No [portal Azure,](https://portal.azure.com/)na lâmina da sua aplicação, tem as opções para parar ou reiniciar a sua aplicação.

 ![reiniciar app para resolver problemas de desempenho](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Também pode gerir a sua aplicação utilizando o Azure Powershell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../powershell-azure-resource-manager.md).
