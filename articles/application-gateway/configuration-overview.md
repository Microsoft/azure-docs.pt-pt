---
title: Visão geral da configuração do Gateway de aplicação Azure
description: Este artigo descreve como configurar os componentes do Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89652009"
---
# <a name="application-gateway-configuration-overview"></a>Visão geral da configuração do Gateway de Aplicação

O Azure Application Gateway é composto por vários componentes que pode configurar de várias formas para diferentes cenários. Este artigo mostra-lhe como configurar cada componente.

![Gráfico de fluxo de componentes do Gateway de aplicação](./media/configuration-overview/configuration-overview1.png)

Esta imagem ilustra uma aplicação que tem três ouvintes. Os dois primeiros são ouvintes multi-sites para `http://acme.com/*` `http://fabrikam.com/*` e, respectivamente. Ambos ouvem no porto 80. O terceiro é um ouvinte básico que tem a rescisão de segurança da camada de transporte (TLS) de ponta a ponta, anteriormente conhecida como terminação da Camada de Tomadas Seguras (SSL).

## <a name="infrastructure"></a>Infraestrutura

A infraestrutura Application Gateway inclui a rede virtual, sub-redes, grupos de segurança de rede e rotas definidas pelo utilizador.

Para obter mais informações, consulte a [configuração da infraestrutura do Gateway de Aplicação](configuration-infrastructure.md).



## <a name="front-end-ip-address"></a>Endereço IP de front-end

Pode configurar a porta de aplicação para ter um endereço IP público, um endereço IP privado ou ambos. É necessário um IP público quando acolhe uma parte traseira que os clientes devem aceder através da Internet através de um IP virtual (VIP) virado para a Internet.

Para obter mais informações, consulte a [configuração do endereço IP frontal do Application Gateway.](configuration-front-end-ip.md)

## <a name="listeners"></a>Serviços de escuta

Um ouvinte é uma entidade lógica que verifica os pedidos de ligação recebidas utilizando o endereço porta, protocolo, anfitrião e IP. Ao configurar o ouvinte, deve introduzir valores para estes que correspondam aos valores correspondentes no pedido de entrada no gateway.

Para obter mais informações, consulte [a configuração do ouvinte do Application Gateway](configuration-listeners.md).

## <a name="request-routing-rules"></a>Pedir regras de encaminhamento

Quando cria um gateway de aplicação utilizando o portal Azure, cria uma regra padrão *(regra 1).* Esta regra liga o ouvinte predefinido *(appGatewayHttpListener*) com o pool back-end padrão *(appGatewayBackendPool*) e as definições HTTP de back-end predefinidos *(appGatewayBackendHttpSettings).* Depois de criar o gateway, pode editar as definições da regra padrão ou criar novas regras.

Para obter mais informações, consulte [as regras de encaminhamento do Application Gateway](configuration-request-routing-rules.md).

## <a name="http-settings"></a>Definições de HTTP

O gateway de aplicação encaminha o tráfego para os servidores de back-end usando a configuração que especifica aqui. Depois de criar uma definição HTTP, deve associá-la a uma ou mais regras de encaminhamento de pedidos.

Para obter mais informações, consulte [a configuração das definições do Gateway HTTP da aplicação.](configuration-http-settings.md)

## <a name="back-end-pool"></a>Conjunto back-end

Pode apontar uma piscina traseira para quatro tipos de membros backend: uma máquina virtual específica, um conjunto de escala de máquina virtual, um endereço IP/FQDN ou um serviço de aplicações. 

Depois de criar uma piscina traseira, deve associá-la a uma ou mais regras de encaminhamento de pedidos. Também deve configurar sondas de saúde para cada piscina traseira no seu gateway de aplicação. Quando uma condição de regra de encaminhamento de pedido é satisfeita, o gateway de aplicação encaminha o tráfego para os servidores saudáveis (conforme determinado pelas sondas de saúde) na piscina traseira correspondente.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Uma porta de aplicação monitoriza a saúde de todos os recursos no seu final por defeito. Mas recomendamos vivamente que crie uma sonda personalizada para cada definição HTTP de back-end para obter um maior controlo sobre a monitorização da saúde. Para aprender a configurar uma sonda personalizada, consulte as [definições de sonda de saúde personalizada](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Depois de criar uma sonda de saúde personalizada, tem de a associar a uma definição HTTP de fundo. Uma sonda personalizada não monitorizará a saúde da piscina traseira a menos que a definição HTTP correspondente esteja explicitamente associada a um ouvinte usando uma regra.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe sobre os componentes do Application Gateway, pode:

- [Criar uma porta de entrada de aplicações no portal Azure](quick-create-portal.md)
- [Criar um gateway de aplicações usando o PowerShell](quick-create-powershell.md)
- [Criar um gateway de aplicações utilizando o Azure CLI](quick-create-cli.md)
