---
title: Perguntas frequentes sobre networking em Funções Azure
description: Respostas a algumas das questões e cenários mais comuns para o networking com as Funções Azure.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75409534"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Perguntas frequentes sobre networking em Funções Azure

Este artigo lista frequentemente perguntas sobre networking em Funções Azure. Para uma visão geral mais abrangente, consulte [as opções de networking functions](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Como posso definir um IP estático em Funções?

Implementar uma função num Ambiente de Serviço de Aplicações é atualmente a única forma de ter um IP estático de entrada e saída para a sua função. Para mais detalhes sobre a utilização de um Ambiente de Serviço de Aplicações, comece com o artigo [Criar e utilizar um equilibrador de carga interno com um Ambiente de Serviço de Aplicações.](../app-service/environment/create-ilb-ase.md)

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Como posso restringir o acesso à Internet à minha função?

Pode restringir o acesso à Internet de várias formas:

* [Restrições IP](../app-service/app-service-ip-restrictions.md): Restringir o tráfego de entrada na sua aplicação de funções por gama IP.
    * Sob restrições IP, também é possível configurar [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)de serviço, que restringem a sua Função apenas para aceitar o tráfego de entrada de uma determinada rede virtual.
* Remoção de todos os gatilhos HTTP. Para algumas aplicações, basta simplesmente evitar os gatilhos http e usar qualquer outra fonte de evento para desencadear a sua função.

Tenha em mente que o editor do portal Azure necessita de acesso direto à sua função de funcionamento. Quaisquer alterações de código através do portal Azure exigirão o dispositivo que está a usar para navegar no portal para ter o seu IP whitelist. Mas ainda pode utilizar qualquer coisa no âmbito da plataforma com restrições de rede no lugar.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Como restringir a minha aplicação de funções a uma rede virtual?

É capaz de restringir o tráfego **de entrada** para uma aplicação de função a uma rede virtual utilizando [pontos finais](./functions-networking-options.md#private-site-access)de serviço . Esta configuração ainda permite que a aplicação de funções eprepreprefunte para a internet.

A única maneira de restringir totalmente uma função de tal forma que todos os fluxos de tráfego através de uma rede virtual é usar um Ambiente de Serviço de Aplicações equilibrado internamente. Esta opção implementa o seu site numa infraestrutura dedicada dentro de uma rede virtual e envia todos os gatilhos e tráfego através da rede virtual. 

Para mais detalhes sobre a utilização de um Ambiente de Serviço de Aplicações, comece com o artigo [Criar e utilizar um equilibrador de carga interno com um Ambiente de Serviço de Aplicações.](../app-service/environment/create-ilb-ase.md)

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Como posso aceder a recursos numa rede virtual a partir de uma aplicação de função?

Você pode aceder a recursos numa rede virtual a partir de uma função de funcionamento usando a integração de rede virtual. Para mais informações, consulte a [integração da rede Virtual.](functions-networking-options.md#virtual-network-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Como posso aceder aos recursos protegidos por pontos finais de serviço?

Ao utilizar a integração virtual da rede, pode aceder a recursos garantidos por pontos de serviço a partir de uma função de funcionamento. Para mais informações, consulte a [integração da rede virtual.](functions-networking-options.md#virtual-network-integration)

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Como posso desencadear uma função a partir de um recurso numa rede virtual?

É possível permitir que os gatilhos HTTP sejam chamados de uma rede virtual utilizando [pontos finais](./functions-networking-options.md#private-site-access)de serviço . 

Também pode desencadear uma função de todos os outros recursos numa rede virtual, implementando a sua aplicação de função para um plano Premium, plano de serviço de aplicações ou Ambiente de Serviço de Aplicações. Consulte [os gatilhos](./functions-networking-options.md#virtual-network-triggers-non-http) da rede virtual não HTTP para obter mais informações

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Como posso implantar a minha aplicação de funções numa rede virtual?

A implantação para um App Service Environment é a única forma de criar uma aplicação de função que esteja totalmente dentro de uma rede virtual. Para mais detalhes sobre a utilização de um equilibrador de carga interna com um Ambiente de Serviço de Aplicações, comece com o artigo [Criar e utilizar um equilibrador de carga interno com um Ambiente de Serviço](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)de Aplicações .

Para cenários em que só precisa de acesso único aos recursos de rede virtuais, ou isolamento de rede menos abrangente, consulte a visão geral da [rede Functions](functions-networking-options.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre networking e funções: 

* [Siga o tutorial sobre começar com a integração de rede virtual](./functions-create-vnet.md)
* [Saiba mais sobre as opções de networking em Funções Azure](./functions-networking-options.md)
* [Saiba mais sobre integração de rede virtual com Serviço de Aplicações e Funções](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais em Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais funcionalidades de networking e controlo com ambientes de serviço de aplicação](../app-service/environment/intro.md)
