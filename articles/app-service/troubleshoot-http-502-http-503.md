---
title: Corrigir http 502 e HTTP 503 erros
description: Problemas 502 bad gateway e 503 erros indisponíveis de serviço na sua aplicação hospedada no Azure App Service.
tags: top-support-issue
keywords: 502 bad gateway, 503 serviço indisponível, erro 503, erro 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9345b6fb28aa282e85f1167f6f2531e5f990e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688334"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Problemas http erros de "502 bad gateway" e "serviço 503 indisponível" no Serviço de Aplicações Azure
"502 bad gateway" e "503 service inavailable" são erros comuns na sua aplicação hospedada no [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Este artigo ajuda-o a resolver estes erros.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode arquivar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter **Suporte**.

## <a name="symptom"></a>Sintoma
Quando navega na aplicação, devolve um erro HTTP "502 Bad Gateway" ou um erro HTTP "503 Service Inavailable".

## <a name="cause"></a>Causa
Este problema é muitas vezes causado por questões de nível de aplicação, tais como:

* pedidos demorando muito tempo
* aplicação utilizando alta memória/CPU
* falha de aplicação devido a uma exceção.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Medidas de resolução de problemas para resolver erros de "502 bad gateway" e "503 erros de serviço indisponíveis"
A resolução de problemas pode ser dividida em três tarefas distintas, por ordem sequencial:

1. [Observar e monitorizar o comportamento da aplicação](#observe)
2. [Recolher dados](#collect)
3. [Atenuar a questão](#mitigate)

[O Serviço de Aplicações](overview.md) oferece-lhe várias opções em cada passo.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorizar o comportamento da aplicação
#### <a name="track-service-health"></a>Saúde do Serviço de Rastreio
O Microsoft Azure divulga cada vez que há uma interrupção de serviço ou degradação do desempenho. Pode acompanhar a saúde do serviço no [Portal Azure.](https://portal.azure.com/) Para mais informações, consulte [track service health](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorize a sua aplicação
Esta opção permite-lhe descobrir se a sua aplicação está a ter algum problema. Na lâmina da sua aplicação, clique nos **pedidos e erros.** A lâmina **Métrica** mostrar-lhe-á todas as métricas que pode adicionar.

Algumas das métricas que você pode querer monitorizar para a sua app são

* Conjunto de trabalho médio da memória
* Tempo médio de resposta
* Tempo cpu
* Conjunto de trabalho de memória
* Pedidos

![monitorizar app para resolver erros HTTP de 502 bad gateway e 503 serviço indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitorize aplicações no Serviço de Aplicações Azure](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Recolher dados
#### <a name="use-the-diagnostics-tool"></a>Utilize a ferramenta de diagnóstico
O Serviço de Aplicações proporciona uma experiência inteligente e interativa para ajudá-lo a resolver problemas com a sua aplicação sem necessidade de configuração. Quando tiver problemas com a sua aplicação, a ferramenta de diagnóstico irá apontar o que está errado em guiá-lo para a informação certa para mais facilmente e rapidamente resolver o problema.

Para aceder aos diagnósticos do Serviço de Aplicações, navegue para a sua app de serviço de aplicações ou ambiente de serviço de aplicações no [portal Azure](https://portal.azure.com). Na navegação à esquerda, clique em **Diagnosticar e resolver problemas**.

#### <a name="use-the-kudu-debug-console"></a>Use a consola Kudu Debug
O Serviço de Aplicações vem com uma consola de depuração que podes usar para depurar, explorar, carregar ficheiros, bem como pontos finais da JSON para obter informações sobre o seu ambiente. Isto *chama-se Consola Kudu* ou *Painel SCM* para a sua aplicação.

Pode aceder a este dashboard indo ao link **https://&lt;O nome da aplicação>.scm.azurewebsites.net/**.

Algumas das coisas que Kudu fornece são:

* configurações ambientais para a sua aplicação
* fluxo de log
* despejo de diagnóstico
* consola de depuração na qual pode executar cmdlets Powershell e comandos DOS básicos.

Outra característica útil da Kudu é que, caso a sua aplicação esteja a lançar exceções de primeira oportunidade, pode usar kudu e a ferramenta SysInternals Procdump para criar depósitos de memória. Estes depósitos de memória são instantâneos do processo e podem muitas vezes ajudá-lo a resolver problemas mais complicados com a sua aplicação.

Para obter mais informações sobre as funcionalidades disponíveis em Kudu, consulte as ferramentas online do [Azure Websites que deve conhecer.](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Atenuar a questão
#### <a name="scale-the-app"></a>Escala a app
No Serviço de Aplicações Azure, para um maior desempenho e entrada, pode ajustar a escala em que está a executar a sua aplicação. A escalação de uma aplicação envolve duas ações relacionadas: alterar o seu plano de Serviço de Aplicações para um nível de preços mais elevado, e configurar certas definições depois de ter mudado para o nível de preços mais elevado.

Para obter mais informações sobre escala, consulte [Scale uma aplicação no Azure App Service](manage-scale-up.md).

Além disso, pode optar por executar a sua aplicação em mais de uma instância . Isto não só lhe fornece mais capacidade de processamento, como também lhe dá alguma tolerância à falha. Se o processo se der por uma instância, a outra instância continuará a ser adostada.

Pode definir a escala para ser Manual ou Automática.

#### <a name="use-autoheal"></a>Use AutoHeal
A AutoHeal recicla o processo do trabalhador para a sua aplicação com base em configurações que escolher (como alterações de configuração, pedidos, limites baseados na memória ou o tempo necessário para executar um pedido). Na maior parte das vezes, reciclar o processo é a forma mais rápida de recuperar de um problema. Embora possa sempre reiniciar a aplicação diretamente dentro do Portal Azure, o AutoHeal fá-lo-á automaticamente para si. Tudo o que precisa fazer é adicionar alguns gatilhos na root web.config para a sua aplicação. Note que estas definições funcionariam da mesma forma mesmo que a sua aplicação não seja uma .NET.

Para mais informações, consulte Web [Sites Azure de cura automática](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Reiniciar a aplicação
Esta é muitas vezes a forma mais simples de recuperar de questões únicas. No [Portal Azure,](https://portal.azure.com/)na lâmina da sua aplicação, tem as opções para parar ou reiniciar a sua aplicação.

 ![reiniciar app para resolver erros http de 502 bad gateway e 503 serviço indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Também pode gerir a sua aplicação utilizando o Azure Powershell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../powershell-azure-resource-manager.md).

