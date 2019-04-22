---
title: Funções do Azure, as opções de redes
description: Uma visão geral de todas as opções de redes disponíveis nas funções do Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: b7af0149a690e3cc3a357a5cb769751e3674d374
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698206"
---
# <a name="azure-functions-networking-options"></a>Funções do Azure, as opções de redes

Este artigo descreve os recursos de rede disponíveis entre as opções de hospedagem para as funções do Azure. Todas as seguintes opções de funcionamento em rede fornecem alguns capacidade de aceder aos recursos sem utilizar endereços encaminháveis da internet ou restringir o acesso à internet para uma aplicação de funções. 

Os modelos de alojamento tem diferentes níveis de isolamento de rede disponível. Escolher correta irá ajudá-lo a satisfazer os seus requisitos de isolamento de rede.

Pode alojar as aplicações de função de duas formas:

* Existe um conjunto de opções do plano que são executados numa infraestrutura multi-inquilino, com vários níveis de conectividade de rede virtual e opções de dimensionamento:
    * O [plano de consumo](functions-scale.md#consumption-plan), que dimensiona dinamicamente em resposta a carregar e oferece opções de isolamento de rede mínima.
    * O [plano Premium](functions-scale.md#premium-plan-public-preview), que também pode ser dimensionada de forma dinâmica, oferecendo isolamento de rede mais abrangente.
    * O Azure [plano do App Service](functions-scale.md#app-service-plan), que opera numa escala fixa e oferece o isolamento de rede semelhante para o plano Premium.
* Pode executar as funções [ambiente do serviço de aplicações](../app-service/environment/intro.md). Esse método implementa sua função na sua rede virtual e oferece controle de rede completo e isolamento.

## <a name="matrix-of-networking-features"></a>Matriz de funcionalidades de rede

|                |[Plano de consumo](functions-scale.md#consumption-plan)|⚠ [Plano premium](functions-scale.md#premium-plan-public-preview)|[Plano do Serviço de Aplicações](functions-scale.md#app-service-plan)|[Ambiente do Serviço de Aplicações](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrições de IP de entrada](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integração da rede virtual](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[Pré-visualização de integração da rede virtual (ExpressRoute do Azure e pontos finais de serviço)](#preview-version-of-virtual-network-integration)|❌No|⚠Sim|⚠Sim|✅Yes|
|[Ligações Híbridas](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[acesso de sites privados](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Esta funcionalidade de pré-visualização não é para utilização em produção.

## <a name="inbound-ip-restrictions"></a>Restrições de IP de entrada

Pode utilizar as restrições de IP para definir uma lista de ordem de prioridade de endereços IP que seja permitido/negado o acesso à sua aplicação. A lista pode incluir endereços IPv4 e IPv6. Quando existe uma ou mais entradas, uma implícita "Negar tudo" existe no final da lista. Restrições de IP trabalham com todas as opções de hospedagem de função.

> [!NOTE]
> Para utilizar o editor do portal do Azure, o portal tem de ser capaz de acessar diretamente a aplicação de funções em execução. Além disso, o dispositivo que está a utilizar para aceder ao portal tem de ter sua lista de permissões IP. Com restrições de rede no local, ainda pode aceder a quaisquer funcionalidades sobre a **funcionalidades de plataforma** separador.

Para obter mais informações, consulte [restrições de acesso estático do serviço de aplicações do Azure](../app-service/app-service-ip-restrictions.md).

## <a name="virtual-network-integration"></a>Integração da rede virtual

Integração da rede virtual permite que a aplicação de funções aceder aos recursos numa rede virtual. Esta funcionalidade está disponível no plano Premium e o plano do serviço de aplicações. Se a sua aplicação está num ambiente de serviço de aplicações, ele já se encontra numa rede virtual e não requer a utilização de integração da rede virtual para aceder aos recursos na mesma rede virtual.

Integração da rede virtual fornece o acesso de aplicação de função aos recursos na sua rede virtual, mas não conceder [acesso a sites privada](#private-site-access) à sua aplicação de função a partir da rede virtual.

Pode utilizar a integração da rede virtual para ativar o acesso a partir de aplicações para bases de dados e serviços web em execução na sua rede virtual. Com integração da rede virtual, não precisa expor um ponto final público para as aplicações na sua VM. Em vez disso, pode usar os endereços encaminháveis privados e não da internet.

A versão em disponibilidade geral de integração da rede virtual se baseia num gateway de VPN para ligar aplicações de funções a uma rede virtual. Está disponível nas funções alojadas num plano do serviço de aplicações. Para saber como configurar esta funcionalidade, veja [integrar a sua aplicação com uma Azure virtual network](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-version-of-virtual-network-integration"></a>Versão de pré-visualização de integração da rede virtual

Uma nova versão da funcionalidade de integração de rede virtual está em pré-visualização. Ele não depende de VPN ponto a site. Ele oferece suporte a acessar recursos de ExpressRoute ou pontos finais de serviço. Está disponível no plano Premium e nos planos de serviço de aplicações dimensionados para PremiumV2.

Aqui estão algumas características desta versão:

* Não é necessário um gateway para utilizá-lo.
* Pode aceder aos recursos nas ligações do ExpressRoute sem qualquer configuração adicional além da integração com a rede virtual ligada ao ExpressRoute.
* Pode consumir recursos de segurança de ponto final de serviço de mensagens em fila a execução de funções. Para tal, ative pontos finais de serviço na sub-rede utilizada para a integração da rede virtual.
* Não é possível configurar acionadores para utilizar recursos de segurança de ponto final de serviço. 
* A aplicação de funções e a rede virtual tem de ser na mesma região.
* O novo recurso exige uma sub-rede não utilizada na rede virtual que tenha implementado através do Gestor de recursos do Azure.
* Cargas de trabalho de produção não são suportadas, enquanto a funcionalidade estiver em pré-visualização.
* Tabelas de rotas e o global peering ainda não estão disponíveis com a funcionalidade.
* Um endereço é utilizado para cada instância de potencial de uma aplicação de funções. Uma vez que não é possível alterar o tamanho da sub-rede após a atribuição, utilize uma sub-rede que pode dar suporte ao tamanho de dimensionamento máximo. Por exemplo, para oferecer suporte a um plano Premium que pode ser dimensionado até 80 instâncias, recomendamos um `/25` sub-rede que fornece 126 endereços do anfitrião.

Para saber mais sobre como utilizar a versão de pré-visualização de integração da rede virtual, veja [integrar uma aplicação de função com uma Azure virtual network](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Ligações Híbridas

[Ligações híbridas](../service-bus-relay/relay-hybrid-connections-protocol.md) é uma funcionalidade do reencaminhamento do Azure que pode utilizar para aceder aos recursos de aplicação nas outras redes. Ele fornece acesso a partir da sua aplicação para um ponto final da aplicação. Não é possível utilizá-lo para aceder à sua aplicação. As ligações híbridas são disponíveis para funções em execução num [plano do App Service](functions-scale.md#app-service-plan) e uma [ambiente de serviço de aplicações](../app-service/environment/intro.md).

Como o utilizado nas funções do Azure, cada ligação híbrida está correlacionada com uma combinação única do anfitrião e a porta TCP. Isso significa que o ponto final da ligação híbrida pode estar em qualquer sistema operativo e qualquer aplicativo, desde que está acessando uma porta de escuta de TCP. A funcionalidade ligações híbridas não sabe nem se importa o que é o protocolo de aplicação, ou que está a aceder. Ele simplesmente fornece acesso à rede.

Para obter mais informações, consulte a [documentação de serviço de aplicações para as ligações híbridas](../app-service/app-service-hybrid-connections.md), que suporta as funções num plano do serviço de aplicações.

## <a name="private-site-access"></a>O acesso a sites privados

Acesso a sites privada refere-se para tornar a sua aplicação acessível apenas a partir de uma rede privada, tais como a partir de dentro de uma rede virtual do Azure. Acesso de sites privados está disponível apenas com um ambiente de serviço de aplicações configuradas com um balanceador de carga interno (ILB). Para obter mais informações, consulte [criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações](../app-service/environment/create-ilb-ase.md).

Existem várias formas de aceder aos recursos de rede virtual nas outras opções de hospedagem. Mas um ambiente de serviço de aplicações é a única forma de permitir que os acionadores para uma função para ocorrem através de uma rede virtual.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as funções de sistema de rede e do Azure: 

* [Siga o tutorial sobre como começar a integração da rede virtual](./functions-create-vnet.md)
* [Ler as funções de FAQ de rede](./functions-networking-faq.md)
* [Saiba mais sobre a integração de rede virtual com o serviço/funções de aplicação](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre as redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais recursos de rede e controlo com ambientes de serviço de aplicações](../app-service/environment/intro.md)
* [Ligar a recursos locais individuais sem alterações de firewall com ligações híbridas](../app-service/app-service-hybrid-connections.md)
