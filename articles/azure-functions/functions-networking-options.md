---
title: Opções de funcionamento em rede das funções do Azure
description: Uma visão geral de todas as opções de redes disponíveis nas funções do Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: conceptual
ms.date: 1/14/2019
ms.author: alkarche
ms.openlocfilehash: 8bbc44e7af68f005f30fff143741bc4bfe0adcf2
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896641"
---
# <a name="azure-functions-networking-options"></a>Opções de funcionamento em rede das funções do Azure

Este documento descreve o conjunto de funcionalidades de rede disponíveis entre as funções do Azure, as opções de hospedagem. Todas as seguintes opções de funcionamento em rede fornecem alguns capacidade de aceder aos recursos sem utilizar endereços encaminháveis da internet ou restringir o acesso à internet para uma aplicação de funções. Os modelos de hospedagem todos os tem diferentes níveis de isolamento de rede disponível e escolher a correta permite-lhe cumprir os requisitos de isolamento da rede.

Aplicações de função podem ser hospedadas de várias maneiras diferentes.

* Há um conjunto de opções do plano que são executadas na infraestrutura de multi-inquilino, com vários níveis da conectividade VNET e opções de expansão.
    1. O plano de consumo, que dimensiona dinamicamente em resposta a carregar e oferece opções de isolamento de rede mínima.
    1. O plano Premium, que também dimensiona dinamicamente, oferecendo isolamento de rede mais abrangente.
    1. O plano do serviço de aplicações, que opera numa escala fixa e oferecem isolamento de rede semelhante para o plano Premium.
* As funções também podem ser executadas num serviço de ambiente aplicações (ASE) que implementa a sua função na sua VNet e oferece controle de rede completo e isolamento.

## <a name="networking-feature-matrix"></a>Matriz de recursos de rede

|                |[Plano de Consumo](functions-scale.md#consumption-plan)|⚠ [Premium Plan](functions-scale.md##premium-plan-public-preview)|[Plano do Serviço de Aplicações](functions-scale.md#app-service-plan)|[Ambiente do Serviço de Aplicações](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**Restrições de IP de entrada**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**Integração de VNET**](#vnet-integration)|❌No|⚠ Sim|✅Yes|✅Yes|
|[**Integração de VNET de pré-visualização (Express Route e pontos finais de serviço)**](#preview-vnet-integration)|❌No|⚠ Sim|⚠ Sim|✅Yes|
|[**Ligações Híbridas**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**Acesso a sites privada**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Funcionalidade de pré-visualização, não para uso em produção

## <a name="inbound-ip-restrictions"></a>Restrições de IP de entrada

Restrições de IP permitem-lhe definir uma prioridade ordenadas permitir/recusar a lista de endereços IP que têm permissão para aceder à sua aplicação. Lista de permissões pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, existe, em seguida, um implícita Negar tudo o que existe no final da lista. A capacidade de restrições de IP funciona com a função de todas as opções de hospedagem.

> ! [IMPORTANTE] Para poder utilizar o editor do portal do Azure, o portal tem de ser capaz de acessar diretamente a aplicação de funções em execução e o dispositivo que estiver a utilizar para aceder ao portal tem de ter sua lista de permissões IP. Com restrições de rede no local, ainda pode aceder a qualquer recurso no **funcionalidades de plataforma** separador.

[Saiba mais aqui](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>Integração de VNET

Integração VNET permite à aplicação de função aceder aos recursos dentro de uma VNET. Integração VNET está disponível no plano Premium e no plano do serviço de aplicações. Se a sua aplicação está num ambiente de serviço de aplicações, em seguida, ele já se encontra numa VNet e não requer a utilização da funcionalidade de integração de VNet para aceder aos recursos na mesma VNet.

Integração VNet oferece o acesso de aplicação de função aos recursos na sua rede virtual, mas não conceder [acesso a sites privada](#private-site-access) à sua aplicação de função a partir da rede virtual.

Integração de VNet, muitas vezes, é utilizada para ativar o acesso a partir de aplicações para uma base de dados e web services em execução na sua VNet. Com a integração de VNet, não terá de expor um ponto final público para aplicações na sua VM, mas pode utilizam os endereços de encaminháveis privados não internet em vez disso.

A versão em disponibilidade geral de integração de VNET depende de um gateway de VPN para ligar aplicações de funções a uma rede virtual. Está disponível nas funções hospedadas num plano do serviço de aplicações. Para saber como configurar esta funcionalidade, consulte a [documento de serviço de aplicações para a mesma funcionalidade](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-vnet-integration"></a>Integração de VNET de pré-visualização

Há uma nova versão da funcionalidade de integração de VNet que está em pré-visualização. Ele não depende de VPN ponto a site e também suporta recursos ao aceder ao ExpressRoute ou pontos finais de serviço. Esta funcionalidade está disponível no plano Premium e nos planos de serviço de aplicações dimensionados para PremiumV2.

A nova versão da integração de VNet, o que está atualmente em pré-visualização, fornece as seguintes vantagens:

* Nenhum gateway é necessário para utilizar a nova funcionalidade de integração de VNet
* Pode aceder a recursos através de ligações do ExpressRoute sem qualquer configuração adicional para além da integração com o ExpressRoute ligado VNet.
* Pode consumir recursos executem funções protegidos pelo ponto final de serviço. Para tal, ative pontos finais de serviço na sub-rede utilizada para a integração de VNet.
* Não é possível configurar acionadores para utilizar com a nova capacidade de integração de VNet de recursos protegidos pelo ponto final de serviço. 
* A aplicação de funções e a VNet tem de ser na mesma região.
* O novo recurso exige uma sub-rede não utilizada na sua VNet do Resource Manager.
* Cargas de trabalho de produção não são suportadas na nova versão de integração de VNet enquanto estiverem em pré-visualização.
* Tabelas de rotas e o global peering ainda não estão disponíveis com a nova integração de VNet.
* Um endereço é utilizado para cada instância de aplicação de função potenciais. Porque o tamanho de sub-rede não pode ser alterado após a atribuição, utilize uma sub-rede que pode dar suporte ao tamanho de dimensionamento máximo. Por exemplo, para oferecer suporte a um plano Premium que pode ser dimensionado até 80 instâncias, recomendamos um `/25` sub-rede que fornece 126 endereços do anfitrião.

Para saber mais sobre como utilizar a integração de VNET de pré-visualização, veja [integrar uma aplicação de função com uma rede Virtual do Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Ligações Híbridas

[Ligações híbridas](../service-bus-relay/relay-hybrid-connections-protocol.md) é uma funcionalidade do reencaminhamento do Azure que podem ser utilizadas para aceder aos recursos de aplicação nas outras redes. Ele fornece acesso a partir da sua aplicação para um ponto final da aplicação. Não pode ser utilizado para aceder à sua aplicação. As ligações híbridas são disponíveis para funções em execução num [plano do App Service](functions-scale.md#app-service-plan) e uma [ambiente de serviço de aplicações](../app-service/environment/intro.md).

Como o utilizado nas funções, cada ligação híbrida está correlacionada com uma combinação única do anfitrião e a porta TCP. Isso significa que o ponto de final de ligação híbrida pode ser em qualquer sistema operativo e qualquer aplicativo, fornecido estão a aceder a uma porta de escuta de TCP. A funcionalidade ligações híbridas não sabe nem se importa o que é o protocolo de aplicação ou o que está a aceder. Ele simplesmente está fornecendo acesso de rede.

Para obter mais informações, consulte a [documentação de serviço de aplicações para as ligações híbridas](../app-service/app-service-hybrid-connections.md), que suporta as funções e aplicações Web.

## <a name="private-site-access"></a>Acesso a sites privada

Acesso a sites privada refere-se para tornar a sua aplicação apenas acessível a partir de uma rede privada, tais como a partir de dentro de uma rede virtual do Azure. Acesso de sites privados só está disponível com um ASE configurado com uma carga balanceador interno (ILB). Para obter detalhes sobre como utilizar um ASE de ILB, veja [criando e usando um ASE de ILB](../app-service/environment/create-ilb-ase.md).

Existem várias formas de aceder a recursos da VNET em outras opções de hospedagem, mas um ASE é a única forma de permitir que os acionadores para uma função para ocorrem através de uma VNET.
