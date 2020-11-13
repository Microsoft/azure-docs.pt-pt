---
title: Perguntas frequentes sobre networking em Funções Azure
description: Respostas a algumas das perguntas e cenários mais comuns para networking com Funções Azure.
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.reviewer: glenga
ms.openlocfilehash: 3e8a992aac95b6c2688cb45aa980bf0b01883a53
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578234"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Perguntas frequentes sobre networking em Funções Azure

Este artigo lista perguntas frequentes sobre networking em Funções Azure. Para uma visão geral mais abrangente, consulte [as opções de networking de funções](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Como posso definir um IP estático em Funções?

Implementar uma função num Ambiente de Serviço de Aplicações é atualmente a única forma de ter um IP de entrada e saída estático para a sua função. Para obter mais informações sobre a utilização de um Ambiente de Serviço de Aplicações, comece com o artigo [Crie e utilize um equilibrador de carga interno com um Ambiente de Serviço de Aplicações](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Como posso restringir o acesso à Internet à minha função?

Pode restringir o acesso à Internet de várias formas:

* [Restrições IP](../app-service/app-service-ip-restrictions.md): Restringir o tráfego de entrada à sua aplicação de função por gama IP.
    * Sob as restrições IP, também é possível configurar [os Pontos finais de serviço,](../virtual-network/virtual-network-service-endpoints-overview.md)que limitam a sua Função a apenas aceitar o tráfego de entrada a partir de uma determinada rede virtual.
* Remoção de todos os gatilhos HTTP. Para algumas aplicações, basta simplesmente evitar os gatilhos HTTP e utilizar qualquer outra fonte de evento para ativar a sua função.

Tenha em mente que o editor do portal Azure requer acesso direto à sua função de funcionamento. Quaisquer alterações de código através do portal Azure exigirão que o dispositivo que está a utilizar navegue no portal tenha o seu IP adicionado à lista aprovada. Mas ainda pode utilizar qualquer coisa no separador de funcionalidades da plataforma com restrições de rede em vigor.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Como posso restringir a minha aplicação de função a uma rede virtual?

É capaz de restringir o tráfego **de entrada** de uma aplicação de função a uma rede virtual utilizando [os Pontos Finais de Serviço.](./functions-networking-options.md#use-service-endpoints) Esta configuração ainda permite que a aplicação de função faça chamadas de saída para a internet.

Para restringir completamente uma função de tal forma que todo o tráfego flui através de uma rede virtual, você pode usar um [ponto final privado](./functions-networking-options.md#private-endpoint-connections) com integração de rede virtual de saída ou um Ambiente de Serviço de Aplicações.

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Como posso aceder a recursos numa rede virtual a partir de uma aplicação de função?

Pode aceder a recursos numa rede virtual a partir de uma função de execução utilizando a integração de rede virtual. Para obter mais informações, consulte [a integração da rede Virtual.](functions-networking-options.md#virtual-network-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Como acedo a recursos protegidos por pontos finais de serviço?

Ao utilizar a integração de rede virtual, pode aceder a recursos seguros de serviço a partir de uma função de funcionamento. Para mais informações, consulte [a integração da rede virtual.](functions-networking-options.md#virtual-network-integration)

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Como posso desencadear uma função a partir de um recurso numa rede virtual?

É possível permitir que os gatilhos HTTP sejam chamados a partir de uma rede virtual utilizando [endpoints](./functions-networking-options.md#use-service-endpoints) de serviço ou [ligações Private Endpoint](./functions-networking-options.md#private-endpoint-connections). 

Também pode desencadear uma função de todos os outros recursos numa rede virtual, implementando a sua aplicação de função para um plano Premium, plano de Serviço de Aplicações ou Ambiente de Serviço de Aplicações. Consulte [os gatilhos de rede virtual não-HTTP](./functions-networking-options.md#virtual-network-triggers-non-http) para obter mais informações

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Como posso implementar a minha aplicação de função numa rede virtual?

Implementar um Ambiente de Serviço de Aplicações é a única forma de criar uma aplicação de função que esteja totalmente dentro de uma rede virtual. Para obter mais informações sobre a utilização de um equilibrador de carga interno com um Ambiente de Serviço de Aplicações, comece com o artigo [Criar e utilizar um equilibrador de carga interno com um Ambiente de Serviço de Aplicações](../app-service/environment/create-ilb-ase.md).

Para cenários em que só precisa de acesso unidirecional aos recursos de rede virtuais, ou isolamento de rede menos abrangente, consulte a visão geral da [rede de Funções](functions-networking-options.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre networking e funções: 

* [Acompanhe o tutorial sobre o início com integração de rede virtual](./functions-create-vnet.md)
* [Saiba mais sobre as opções de networking em Azure Functions](./functions-networking-options.md)
* [Saiba mais sobre integração de rede virtual com o Serviço de Aplicações e Funções](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais em Azure](../virtual-network/virtual-networks-overview.md)
* [Permitir mais funcionalidades de networking e controlo com Ambientes de Serviço de Aplicações](../app-service/environment/intro.md)
