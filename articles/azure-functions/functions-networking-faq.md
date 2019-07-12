---
title: Perguntas mais frequentes sobre o funcionamento em rede nas funções do Azure
description: Respostas a algumas das perguntas mais comuns e cenários para funcionamento em rede com as funções do Azure.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0d352d61a971e289a6286c2d948c3f0869ddd8d2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706388"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Perguntas mais frequentes sobre o funcionamento em rede nas funções do Azure

Este artigo apresenta uma lista de perguntas mais frequentes sobre o funcionamento em rede nas funções do Azure. Para obter uma descrição mais completa, consulte [opções de redes de funções](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Como posso definir um IP estático em funções?

Implementar uma função num ambiente de serviço de aplicações está atualmente a única forma de ter um IP estático de entrada e saído para a função. Para obter detalhes sobre como utilizar um ambiente de serviço de aplicações, começar com o artigo [criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Como posso restringir o acesso à internet para minha função?

Pode restringir o acesso à internet de duas formas:

* [Restrições de IP](../app-service/app-service-ip-restrictions.md): Restringir o tráfego de entrada para a sua aplicação de função, intervalo de IP.
    * Em restrições de IP, também é possível configurar [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md), que restringir sua função para aceitar apenas o tráfego de entrada de uma rede virtual específico.
* Remoção de todos os acionadores HTTP. Para alguns aplicativos, é suficiente para simplesmente evitar acionadores HTTP e usar qualquer outra origem de evento para acionar a função.

Tenha em atenção que o editor do portal do Azure requer acesso direto à sua função em execução. Quaisquer alterações de código através do portal do Azure irão exigir que o dispositivo que estiver a utilizar para navegar até ao portal para têm a sua lista branca IP. Mas ainda pode usar qualquer coisa na guia de funcionalidades de plataforma com restrições de rede no local.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Como posso restringir a minha aplicação de função a uma rede virtual?

É possível restringir **entrada** tráfego para uma aplicação de funções para uma rede virtual com [pontos finais de serviço](./functions-networking-options.md#private-site-access). Esta configuração permite, ainda, a aplicação de funções fazer chamadas de saída à internet.

A única forma de restringir totalmente uma função de forma que todo o tráfego flui através de uma rede virtual é usar um ambiente de serviço de aplicações internamente com balanceamento de carga. Esta opção implementa o seu site numa infraestrutura dedicada numa rede virtual e envia todos os acionadores e tráfego através da rede virtual. 

Para obter detalhes sobre como utilizar um ambiente de serviço de aplicações, começar com o artigo [criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Como posso aceder a recursos numa rede virtual a partir de uma aplicação de funções?

Pode acessar os recursos numa rede virtual de uma função em execução ao utilizar a integração da rede virtual. Para obter mais informações, consulte [integração da rede Virtual](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Como posso aceder a recursos protegidos por pontos finais de serviço?

Ao utilizar a integração da rede virtual (atualmente em pré-visualização), pode aceder a recursos protegidos de ponto final de serviço de uma função em execução. Para obter mais informações, consulte [pré-visualizar a integração de rede virtual](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Como posso acionar uma função de um recurso numa rede virtual?

Pode permitir que os acionadores HTTP a ser chamado a partir de uma rede virtual com [pontos finais de serviço](./functions-networking-options.md#private-site-access). 

Também pode acionar uma função de um recurso numa rede virtual ao implementar a aplicação de funções para um ambiente de serviço de aplicações. Para obter detalhes sobre como utilizar um ambiente de serviço de aplicações, consulte [criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações](../app-service/environment/create-ilb-ase.md).

Os Premium e o serviço de aplicações plano suporte acionadores HTTP de uma rede virtual, mas apenas um ambiente de serviço de aplicações suportam todos os tipos de Acionador outra função através de uma rede virtual.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Como posso implementar meu aplicativo de função numa rede virtual?

Implantar num ambiente de serviço de aplicações é a única forma de criar uma aplicação de função que é totalmente dentro de uma rede virtual. Para obter detalhes sobre como utilizar o Balanceador de carga interno com um ambiente de serviço de aplicações, começar com o artigo [criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Para cenários em que precisa Apenas unidirecional acesso aos recursos de rede virtual, ou menos isolamento de rede abrangente, consulte a [descrição geral de funcionamento em rede das funções](functions-networking-options.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre redes e as funções: 

* [Siga o tutorial sobre como começar a integração da rede virtual](./functions-create-vnet.md)
* [Saiba mais sobre as opções de funcionamento em rede nas funções do Azure](./functions-networking-options.md)
* [Saiba mais sobre a integração de rede virtual com o serviço de aplicações e funções](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre as redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais recursos de rede e controlo com ambientes de serviço de aplicações](../app-service/environment/intro.md)
