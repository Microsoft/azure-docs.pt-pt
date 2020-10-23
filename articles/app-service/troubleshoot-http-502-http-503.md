---
title: Corrigir erros HTTP 502 e HTTP 503
description: Resolução de problemas 502 bad gateway e 503 erros indisponíveis do serviço na sua aplicação hospedada no Azure App Service.
tags: top-support-issue
keywords: 502 bad gateway, serviço 503 indisponível, erro 503, erro 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 6dd67ff4f7346daf7a590ab4fb45a32d227f1c3e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147541"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Resolução de problemas ERROS HTTP de "502 bad gateway" e "serviço 503 indisponível" no Azure App Service
"502 bad gateway" e "503 service inavailable" são erros comuns na sua aplicação hospedada no [Azure App Service](./overview.md). Este artigo ajuda-o a resolver estes erros.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn azure e nos fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter **Suporte.**

## <a name="symptom"></a>Sintoma
Quando navega na aplicação, devolve um erro HTTP "502 Bad Gateway" ou um erro HTTP "Serviço Indisponível" HTTP.

## <a name="cause"></a>Causa
Este problema é muitas vezes causado por problemas de nível de aplicação, tais como:

* pedidos que demoram muito tempo
* aplicação usando alta memória/CPU
* aplicação crashing devido a uma exceção.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Medidas de resolução de problemas para resolver erros de "502 bad gateway" e "503 serviços indisponíveis"
A resolução de problemas pode ser dividida em três tarefas distintas, por ordem sequencial:

1. [Observar e monitorizar o comportamento da aplicação](#observe)
2. [Recolher dados](#collect)
3. [Atenuar a questão](#mitigate)

[O Serviço de Aplicações](overview.md) oferece-lhe várias opções em cada passo.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorizar o comportamento da aplicação
#### <a name="track-service-health"></a>Saúde do Serviço de Rastreio
O Microsoft Azure divulga cada vez que há uma interrupção de serviço ou degradação de desempenho. Pode acompanhar a saúde do serviço no [Portal Azure.](https://portal.azure.com/) Para mais informações, consulte [a saúde do serviço track.](../service-health/service-notifications.md)

#### <a name="monitor-your-app"></a>Monitorize a sua aplicação
Esta opção permite-lhe descobrir se a sua aplicação está a ter algum problema. Na lâmina da sua aplicação, clique nos **pedidos e erros** em azulejo. A lâmina **métrica** irá mostrar-lhe todas as métricas que pode adicionar.

Algumas das métricas que pode querer monitorizar para a sua aplicação são

* Conjunto de trabalho de memória média
* Tempo médio de resposta
* Tempo de CPU
* Conjunto de trabalho de memória
* Pedidos

![monitor app para resolver ERROS HTTP de 502 bad gateway e 503 serviço indisponíveis](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitorize aplicativos no Azure App Service](web-sites-monitor.md)
* [Receber notificações de alerta](../azure-monitor/platform/alerts-overview.md)

<a name="collect"></a>

### <a name="2-collect-data"></a>2. Recolher dados
#### <a name="use-the-diagnostics-tool"></a>Utilize a ferramenta de diagnóstico
O App Service proporciona uma experiência inteligente e interativa para o ajudar a resolver problemas na sua aplicação sem necessidade de configuração. Quando tiver problemas com a sua aplicação, a ferramenta de diagnóstico irá indicar o que é errado guiá-lo para as informações certas para resolver e resolver o problema de forma mais fácil e rápida.

Para aceder aos diagnósticos do Serviço de Aplicações, navegue para a sua app app App Service ou App Service Environment no [portal Azure.](https://portal.azure.com) Na navegação à esquerda, clique no **Diagnóstico e resolva problemas.**

#### <a name="use-the-kudu-debug-console"></a>Use a consola Kudu Debug
O App Service vem com uma consola de depuração que podes usar para depurar, explorar, carregar ficheiros, bem como pontos finais JSON para obter informações sobre o teu ambiente. Isto chama-se *Consola Kudu* ou *O Painel de Instrumentos SCM* para a sua aplicação.

Pode aceder a este dashboard indo ao link **https:// O nome da sua &lt; aplicação>.scm.azurewebsites.net/**.

Algumas das coisas que Kudu fornece são:

* configurações de ambiente para a sua aplicação
* fluxo de log
* despejo de diagnóstico
* depurar consola na qual pode executar cmdlets Powershell e comandos básicos DOS.

Outra característica útil da Kudu é que, caso a sua aplicação esteja a lançar exceções de primeira oportunidade, pode usar a Kudu e a ferramenta SysInternals Procdump para criar despejos de memória. Estes despejos de memória são instantâneos do processo e podem muitas vezes ajudá-lo a resolver problemas mais complicados com a sua app.

Para obter mais informações sobre as funcionalidades disponíveis em Kudu, consulte [as ferramentas online da Azure Websites que deve conhecer.](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. Atenuar a questão
#### <a name="scale-the-app"></a>Dimensione a aplicação
No Azure App Service, para maior desempenho e produção, pode ajustar a escala em que está a executar a sua aplicação. O escalonamento de uma aplicação envolve duas ações relacionadas: alterar o seu plano de Serviço de Aplicações para um nível de preços mais elevado e configurar determinadas definições depois de ter mudado para o nível de preços mais elevado.

Para obter mais informações sobre o dimensionamento, consulte [Scale uma aplicação no Azure App Service](manage-scale-up.md).

Além disso, pode optar por executar a sua aplicação em mais de um caso. Isto não só lhe proporciona mais capacidade de processamento, como também lhe dá alguma tolerância à falha. Se o processo se der em curso numa instância, a outra instância continuará a servir os pedidos.

Pode configurar a escala para ser manual ou automática.

#### <a name="use-autoheal"></a>Use AutoHeal
O AutoHeal recicla o processo de trabalhador para a sua aplicação com base nas definições que escolhe (como alterações de configuração, pedidos, limites baseados na memória ou o tempo necessário para executar um pedido). Na maior parte do tempo, reciclar o processo é a forma mais rápida de recuperar de um problema. Embora possa sempre reiniciar a aplicação diretamente dentro do Portal Azure, o AutoHeal fá-lo-á automaticamente para si. Tudo o que precisa fazer é adicionar alguns gatilhos na raiz web.config para a sua aplicação. Note que estas definições funcionariam da mesma forma, mesmo que a sua aplicação não seja uma .NET.

Para obter mais informações, consulte [os Web Sites Azure de auto-cura.](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)

#### <a name="restart-the-app"></a>Reiniciar a aplicação
Esta é muitas vezes a forma mais simples de recuperar de problemas pontuais. No [Portal Azure](https://portal.azure.com/), na lâmina da sua aplicação, tem as opções para parar ou reiniciar a sua aplicação.

 ![reiniciar app para resolver erros HTTP de 502 bad gateway e 503 serviço indisponíveis](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Também pode gerir a sua aplicação usando a Azure Powershell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../azure-resource-manager/management/manage-resources-powershell.md).