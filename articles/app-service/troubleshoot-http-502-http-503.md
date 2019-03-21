---
title: Corrigir 502 gateway incorreto, 503 Serviço indisponíveis erros - serviço de aplicações do Azure | Documentos da Microsoft
description: Resolva 502 gateway incorreto e 503 Serviço indisponível na sua aplicação alojada no App Service do Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 gateway incorreto, 503 Serviço indisponível, o erro 503, Erro 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5edd3e51e83b5ab324d1e110a1882b20d935a9b5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899133"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Resolver erros HTTP "502 gateway inválido" e "503 Serviço indisponível" no serviço de aplicações do Azure
"502 gateway inválido" e "503 Serviço indisponível" são erros comuns na sua aplicação alojada num [App Service do Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Este artigo ajuda-o a resolver esses erros.

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [do Azure do MSDN e os fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**.

## <a name="symptom"></a>Sintoma
Quando navegar para a aplicação, ele retorna um HTTP ou um pedido HTTP de erro de "502 Gateway incorreto" erro "503 Serviço indisponível".

## <a name="cause"></a>Causa
Esse problema normalmente é causado por problemas de nível de aplicativos, tais como:

* pedidos a demorar muito tempo
* aplicação com a memória/CPU elevada
* aplicativo falha devido a uma exceção.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Passos de resolução de problemas para resolver "502 gateway inválido" e erros de "503 Serviço indisponível"
Resolução de problemas pode ser dividida em três tarefas distintas, em ordem sequencial:

1. [Observar e monitorar o comportamento do aplicativo](#observe)
2. [Recolher dados](#collect)
3. [Mitigar o problema](#mitigate)

[Serviço de aplicações](overview.md) dá-lhe várias opções em cada passo.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorar o comportamento do aplicativo
#### <a name="track-service-health"></a>Controlar o estado de funcionamento do serviço
Microsoft Azure publicizes sempre que há uma degradação de desempenho ou interrupções de serviço. Pode acompanhar o estado de funcionamento do serviço no [Portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [controlar o estado de funcionamento do serviço](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorizar a sua aplicação
Esta opção permite-lhe saber se a aplicação está a ter problemas. No painel da sua aplicação, clique a **pedidos e erros** mosaico. O **métrica** painel mostrará a todas as métricas, pode adicionar.

Algumas das métricas que possa querer monitorizar para a sua aplicação são

* Média do conjunto de trabalho de memória
* Tempo médio de resposta
* Tempo de CPU
* Conjunto de trabalho de memória
* Pedidos

![monitorizar a aplicação na solução de erros HTTP 502 gateway incorreto e 503 Serviço indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitorizar aplicações no serviço de aplicações do Azure](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Recolher dados
#### <a name="use-the-diagnostics-tool"></a>Utilize a ferramenta de diagnóstico
Serviço de aplicações fornece uma experiência interativa e inteligente para o ajudar a resolver problemas da aplicação sem qualquer configuração necessária. Quando se deparar com problemas com a sua aplicação, vai apontar a ferramenta de diagnóstico o que há de errado para orientá-lo para as informações certas para a forma mais fácil e rapidamente resolver problemas e resolver o problema.

Para aceder ao diagnóstico do serviço de aplicações, navegue até à sua aplicação de serviço de aplicações ou o ambiente de serviço de aplicações no [portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **diagnosticar e resolver problemas**.

#### <a name="use-the-kudu-debug-console"></a>Utilizar a consola de depuração do Kudu
Serviço de aplicações é fornecido com uma consola de depuração que pode usar para depuração, explorar, carregar ficheiros, bem como pontos finais de JSON para obter informações sobre o seu ambiente. Isso é chamado do *consola Kudu* ou o *SCM Dashboard* para a sua aplicação.

Pode aceder a este dashboard ao aceder à ligação **https://&lt;o nome da aplicação >.scm.azurewebsites.net/**.

Algumas das coisas que fornece o Kudu são:

* definições de ambiente para a sua aplicação
* fluxo de registo
* informação de diagnóstico
* consola na qual pode executar cmdlets do Powershell e comandos básicos de depuração.

Outro recurso útil do Kudu é que, no caso da aplicação que está a gerar exceções de primeira chance, pode utilizar o Kudu e despeja a ferramenta SysInternals Procdump para criar a memória. Esses despejos de memória são instantâneos do processo e, muitas vezes, podem ajudar a resolver problemas mais complicados, com a sua aplicação.

Para obter mais informações sobre os recursos disponíveis no Kudu, consulte [Web sites do Azure ferramentas online, deve saber sobre](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Mitigar o problema
#### <a name="scale-the-app"></a>Dimensionar a aplicação
No serviço de aplicações do Azure, para aumentar o desempenho e o débito, pode ajustar a escala a que está a executar a aplicação. Aumentar verticalmente uma aplicação envolve duas ações relacionadas: alterar o seu plano do serviço de aplicações para um escalão de preço superior e a configuração de determinadas definições depois que tiver mudado para o escalão de preço superior.

Para obter mais informações sobre o dimensionamento, veja [dimensionar uma aplicação no App Service do Azure](web-sites-scale.md).

Além disso, pode optar por executar a sua aplicação em mais de uma instância. Isso não apenas fornece a mais capacidade de processamento, mas também fornece algum período de tolerância a falhas. Se o processo de ficar inativo numa instância, a outra instância irá continuar a servir pedidos.

Pode definir o dimensionamento Manual ou automático.

#### <a name="use-autoheal"></a>Utilizam o AutoHeal
AutoHeal recicla o processo de trabalho para a sua aplicação com base nas definições que escolher (como alterações de configuração, pedidos, limites baseados em memória ou o tempo necessário para executar um pedido). Na maioria das vezes, a forma mais rápida para recuperar a partir de um problema é a Reciclagem do processo. Embora sempre pode reiniciar a aplicação a partir diretamente no Portal do Azure, AutoHeal fará automaticamente para. Tudo o que precisa fazer é adicionar alguns acionadores ao Web. config de raiz para a sua aplicação. Tenha em atenção que estas definições funciona da mesma forma, mesmo que seu aplicativo não é um .NET um.

Para obter mais informações, consulte [Autorrecuperação dos Web Sites Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Reinicie a aplicação
Geralmente essa é a forma mais simples para recuperar rapidamente de problemas de uso individual. Sobre o [Portal do Azure](https://portal.azure.com/), no painel da sua aplicação, terá as opções para parar ou reiniciar a aplicação.

 ![Reinicie a aplicação para resolver erros HTTP 502 gateway incorreto e 503 Serviço indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Também pode gerir a sua aplicação com o Azure Powershell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../powershell-azure-resource-manager.md).

