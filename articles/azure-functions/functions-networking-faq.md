---
title: Perguntas mais frequentes sobre o funcionamento em rede no funções do Azure
description: Respostas a algumas das perguntas mais comuns e cenários para funcionamento em rede com as funções do Azure.
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 2/26/2019
ms.author: alkarche
ms.openlocfilehash: 7946b7f45ff3df9225a27b70ccfbdb895bfd03c4
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896493"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Perguntas mais frequentes sobre o funcionamento em rede no funções do Azure

Segue-se uma lista de perguntas mais frequentes sobre a rede. Para obter uma descrição mais abrangente, leia o [as funções de sistema de rede documento de opções](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Como posso definir um IP estático em funções?

Implementar uma função num ambiente de serviço de aplicações (ASE) está atualmente a única forma de ter um IP estático de entrada e saído para a função. Para obter detalhes sobre como utilizar um ASE, começar com o artigo aqui: [Criar e utilizar um ASE de ILB](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Como posso restringir o acesso à Internet para minha função?

Pode restringir o acesso à internet de diversas formas, listadas abaixo.

* [Restrições de IP](../app-service/app-service-ip-restrictions.md): restringir o tráfego de entrada para a sua aplicação de função por intervalo de IP.
* Remova todos os acionadores HTTP. Para alguns aplicativos, é suficiente para simplesmente evitar acionadores HTTP e usar qualquer outra origem de evento para acionar a função.

A consideração mais importante ao fazer isso é Lembre-se de que o editor do portal do Azure requer acesso direto à sua função em execução para utilizar. Quaisquer alterações de código através do portal do Azure irão exigir que o dispositivo que estiver a utilizar para navegar até ao portal para têm a sua lista branca IP. Ainda pode, no entanto, usar qualquer coisa na guia de funcionalidades de plataforma com restrições de rede no local.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>Como posso restringir a minha aplicação de função a uma VNET?

A única forma de restringir totalmente uma função de forma que todo o tráfego flui através de uma VNET é usar um internamente com balanceamento de carga (ILB) ambiente do serviço de aplicações (ASE). Esta opção implementa o seu site numa infraestrutura dedicada dentro de uma VNET e envia todos os acionadores e tráfego através da VNET. Para obter detalhes sobre como utilizar um ASE, começar com o artigo aqui: [Criar e utilizar um ASE de ILB](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>Como posso aceder a recursos numa VNET a partir de uma aplicação de funções?

Pode acessar os recursos numa VNET de uma função em execução a utilizar a integração de VNET. Para obter mais informações, consulte [integração de VNET](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Como posso aceder a recursos protegidos por pontos finais de serviço?

Utilizar a nova integração de VNET (atualmente em pré-visualização), pode aceder a recursos de uma função em execução protegidos pelo ponto final de serviço. Para obter mais informações, consulte [pré-visualizar a integração de VNET](functions-networking-options.md#preview-vnet-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>Como posso acionar uma função de um recurso numa VNET?

Só pode acionar uma função de um recurso numa VNET ao implementar a aplicação de funções para um ambiente de serviço de aplicações. Para obter detalhes sobre como utilizar um ASE, veja [criando e usando um ASE de ILB](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>Como posso implementar a minha aplicação de função numa VNET?

Implantar num ambiente de serviço de aplicações é a única forma de criar uma aplicação de função que é totalmente dentro de uma VNET para obter detalhes sobre como utilizar um ASE de ILB, começar com o artigo aqui: [Criar e utilizar um ASE de ILB](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Para cenários em que só precisa unidirecional acesso aos recursos da VNET, ou menos isolamento de rede abrangente, consulte a [descrição geral de funcionamento em rede das funções](functions-networking-options.md).
