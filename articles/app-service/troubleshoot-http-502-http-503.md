---
title: Corrija 502 gateway inválidos, 503 erros de serviço indisponível-Azure App serviço | Microsoft Docs
description: Solucione os erros 502 de gateway inadequado e 503 do serviço indisponível em seu aplicativo hospedado no serviço de Azure App.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 gateway inadequado, 503 Serviço indisponível, erro 503, erro 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fdbd77db349eed62af2eb8cf539ef749217a187a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066690"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Solucionar problemas de erros de HTTP de "502 gateway inadequado" e "serviço 503 não disponível" no serviço Azure App
"502 gateway inadequado" e "serviço 503 indisponível" são erros comuns em seu aplicativo hospedado no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714). Este artigo ajuda a solucionar esses erros.

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure no [msdn do Azure e nos fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**.

## <a name="symptom"></a>Sintoma
Quando você navega para o aplicativo, ele retorna um erro HTTP "502 de gateway inadequado" ou um erro HTTP "503 Serviço indisponível".

## <a name="cause"></a>Causa
Esse problema geralmente é causado por problemas no nível do aplicativo, como:

* solicitações demorando um longo tempo
* aplicativo usando memória/CPU alta
* falha no aplicativo devido a uma exceção.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Etapas de solução de problemas para resolver erros de "gateway insatisfatório 502" e "serviço 503 indisponível"
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
Essa opção permite que você descubra se o aplicativo está tendo problemas. Na folha do aplicativo, clique no bloco **solicitações e erros** . A folha **métrica** mostrará todas as métricas que você pode adicionar.

Algumas das métricas que você pode querer monitorar para seu aplicativo são

* Média do conjunto de trabalho de memória
* Tempo médio de resposta
* Tempo de CPU
* Conjunto de trabalho de memória
* Pedidos

![monitorar o aplicativo para resolver erros HTTP de 502 gateway inadequado e serviço 503 indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitorar aplicativos no serviço Azure App](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Recolher dados
#### <a name="use-the-diagnostics-tool"></a>Usar a ferramenta de diagnóstico
O serviço de aplicativo fornece uma experiência inteligente e interativa para ajudá-lo a solucionar problemas de seu aplicativo sem nenhuma configuração necessária. Quando você tiver problemas com seu aplicativo, a ferramenta de diagnóstico indicará o que há de errado para orientá-lo sobre as informações certas para solucionar problemas e resolver o problema com mais facilidade e rapidez.

Para acessar o diagnóstico do serviço de aplicativo, navegue até o aplicativo do serviço de aplicativo ou Ambiente do Serviço de Aplicativo na [portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **diagnosticar e resolver problemas**.

#### <a name="use-the-kudu-debug-console"></a>Usar o console de depuração do kudu
O serviço de aplicativo vem com um console de depuração que você pode usar para depurar, explorar, carregar arquivos, bem como pontos de extremidade JSON para obter informações sobre o seu ambiente. Isso é chamado de *console do kudu* ou do *painel do SCM* para seu aplicativo.

Você pode acessar esse painel acessando o link **https://&lt;Your app Name >. SCM. azurewebsites. net/** .

Algumas das coisas que o kudu fornece são:

* configurações de ambiente para seu aplicativo
* fluxo de log
* despejo de diagnóstico
* console de depuração no qual você pode executar os cmdlets do PowerShell e os comandos básicos do DOS.

Outro recurso útil do kudu é que, caso seu aplicativo esteja lançando exceções de primeira chance, você pode usar o Kudu e a ferramenta SysInternals Procdump para criar despejos de memória. Esses despejos de memória são instantâneos do processo e, muitas vezes, podem ajudá-lo a solucionar problemas mais complicados com seu aplicativo.

Para obter mais informações sobre os recursos disponíveis no kudu, consulte [ferramentas online de sites do Azure que você deve conhecer](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Atenuar o problema
#### <a name="scale-the-app"></a>Dimensionar o aplicativo
No Azure App Service, para aumentar o desempenho e a taxa de transferência, você pode ajustar a escala na qual você está executando seu aplicativo. Escalar verticalmente um aplicativo envolve duas ações relacionadas: alterar o plano do serviço de aplicativo para um tipo de preço mais alto e definir determinadas configurações depois que você tiver alternado para o tipo de preço mais alto.

Para obter mais informações sobre o dimensionamento, consulte [dimensionar um aplicativo no serviço Azure app](manage-scale-up.md).

Além disso, você pode optar por executar seu aplicativo em mais de uma instância. Isso não apenas fornece mais capacidade de processamento, mas também oferece uma quantidade de tolerância a falhas. Se o processo falhar em uma instância, a outra instância ainda continuará atendendo às solicitações.

Você pode definir o dimensionamento como manual ou automático.

#### <a name="use-autoheal"></a>Usar a autoreparo
A autoreparo recicla o processo de trabalho para seu aplicativo com base nas configurações escolhidas (como alterações de configuração, solicitações, limites baseados na memória ou no tempo necessário para executar uma solicitação). Na maioria das vezes, reciclar o processo é a maneira mais rápida de se recuperar de um problema. Embora você sempre possa reiniciar o aplicativo diretamente no portal do Azure, a AutoRecuperação o fará automaticamente para você. Tudo o que você precisa fazer é adicionar alguns gatilhos no Web. config raiz para seu aplicativo. Observe que essas configurações funcionarão da mesma forma, mesmo que seu aplicativo não seja um .NET.

Para obter mais informações, consulte [auto-auto-recuperável Web sites do Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Reiniciar o aplicativo
Geralmente, essa é a maneira mais simples de se recuperar de problemas de uma só vez. No [portal do Azure](https://portal.azure.com/), na folha do aplicativo, você tem as opções para parar ou reiniciar o aplicativo.

 ![Reinicie o aplicativo para resolver erros HTTP de 502 gateway inadequado e serviço 503 indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Você também pode gerenciar seu aplicativo usando o Azure PowerShell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../powershell-azure-resource-manager.md).

