---
title: O que é o Reencaminhamento do Azure? | Microsoft Docs
description: Este artigo fornece uma descrição geral do serviço Azure Relay, que permite desenvolver aplicações na cloud que consomem serviços no local em execução na rede empresarial sem abrir uma ligação de firewall ou efetuar alterações intrusivas à infraestrutura de rede.
services: service-bus-relay
author: spelluru
manager: ''
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: 47fbce7ea26bcb7224fe2624d593d85cd178d610
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776313"
---
# <a name="what-is-azure-relay"></a>O que é o Reencaminhamento do Azure?
O serviço Azure Relay permite-lhe expor em segurança serviços que são executados na rede empresarial na cloud pública. Pode fazê-lo sem abrir uma porta no firewall, ou para fazer alterações intrusivas na sua infraestrutura de rede empresarial. 

O serviço de reencaminhamento suporta os seguintes cenários entre serviços no local e aplicações em execução na cloud ou noutro ambiente no local. 

- Comunicação unidirecional tradicional, pedido/resposta e ponto a ponto 
- Distribuição de eventos no âmbito da Internet para permitir cenários de publicação/subscrição 
- Comunicação de sockets bidirecional e não registada nos limites de rede.

O Azure Relay difere das tecnologias de integração ao nível da rede, como a VPN. O Azure Relay pode ser específico de um ponto final de aplicação única num computador. A tecnologia VPN é bastante mais intrusiva, uma vez que depende da alteração do ambiente de rede. 

## <a name="basic-flow"></a>Fluxo básico
No padrão de transferência de dados reencaminhados, os passos básicos envolvidos são:

1. Um serviço no local liga-se ao serviço de reencaminhamento através de uma porta de saída. 
2. Cria um socket bidirecional para comunicação associada a um endereço específico. 
3. Depois, o cliente pode comunicar com o serviço no local ao enviar tráfego para o serviço de reencaminhamento destinado a esse endereço. 
4. Em seguida, o serviço de reencaminhamento *reencaminha* os dados para o serviço no local através de um socket bidirecional dedicado ao cliente. O cliente não precisa de uma ligação direta ao serviço no local. Não precisa de saber a localização do serviço. O serviço no local não precisa de nenhuma porta de entrada aberta na firewall.


## <a name="features"></a>Funcionalidades 
O Reencaminhamento do Azure tem duas funcionalidades:

- [Ligações Híbridas](#hybrid-connections) - utiliza web sockets de padrão aberto para permitir cenários de multiplataformas.
- Reencaminhamentos do WCF - utiliza Windows Communication Foundation (WCF) para ativar chamadas de procedimento remoto. O Reencaminhamento do WCF é a oferta de Reencaminhamento legada e que muitos utilizadores já utilizam nos respetivos modelos de programação do WCF.

## <a name="hybrid-connections"></a>Ligações Híbridas

A funcionalidade Ligações Híbridas do Azure Relay é uma evolução de protocolo aberto seguro das funcionalidades de reencaminhamento que existiam anteriormente. Pode utilizá-la em qualquer plataforma e em qualquer linguagem. A funcionalidade Ligações Híbridas do Azure Relay baseia-se nos protocolos HTTP e WebSockets. Permite enviar pedidos e receber respostas através de sockets Web ou HTTP(S). Esta funcionalidade é compatível com a API de WebSocket em browsers comuns. 

Para obter detalhes sobre o protocolo de Ligações Híbridas, veja [Guia do protocolo de Ligações Híbridas](relay-hybrid-connections-protocol.md). Pode utilizar as Ligações Híbridas com qualquer biblioteca de sockets Web para qualquer runtime/linguagem.

> [!NOTE]
> As Ligações Híbridas do Azure Relay substituem a antiga funcionalidade de Ligações Híbridas dos Serviços BizTalk. A funcionalidade Ligações Híbridas dos Serviços BizTalk foi criada com base no Reencaminhamento do WCF do Azure Service Bus. A capacidade Ligações Híbridas do Azure Relay complementa a funcionalidade de Reencaminhamento do WCF já existente. Estas duas capacidades de serviço (Reencaminhamento do WCF e Ligações Híbridas) existem lado a lado no serviço Azure Relay. Partilham um gateway comum, mas, de resto, são implementações diferentes.

## <a name="wcf-relay"></a>Reencaminhamento do WCF
O Reencaminhamento do WCF funciona com o .NET Framework completo e no WCF. Cria uma ligação entre o serviço no local e o serviço de reencaminhamento através de um conjunto de enlaces de "reencaminhamento" do WCF. Os enlaces de reencaminhamento mapeiam para novos elementos de ligação de transporte concebidos para criar componentes de canal WCF que se integram com o Service Bus na nuvem. Para obter mais informações, veja [introdução ao WCF Relay](relay-wcf-dotnet-get-started.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>Ligações Híbridas vs. Reencaminhamento do WCF
Tanto as Ligações Híbridas como o Reencaminhamento do WCF permitem ligações seguras a recursos que existem dentro de uma rede empresarial. A utilização de uma funcionalidade em detrimento da outra depende das suas necessidades específicas, conforme descrito na tabela seguinte:

|  | Reencaminhamento do WCF | Ligações Híbridas |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **Javascript/Node.JS** | |x |
| **Standards-Based Open Protocol (Protocolo Aberto Baseado em Normas)** | |x |
| **Modelos de programação de RPC** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Arquitetura: Processamento de pedidos de reencaminhamento recebidos
O diagrama seguinte mostra como os pedidos de reencaminhamento recebidos são processados pelo serviço Azure Relay:

![Processamento de Pedidos de Reencaminhamento de WCF Recebidos](./media/relay-what-is-it/ic690645.png)

1. O cliente de escuta envia um pedido de escuta para o serviço Azure Relay. O balanceador de carga do Azure encaminha o pedido para um dos nós de gateway. 
2. O serviço Azure Relay cria um reencaminhamento no arquivo de gateway. 
3. O cliente de envio envia um pedido para ligar ao serviço de escuta. 
4. O gateway que recebe o pedido procura o reencaminhamento no arquivo de gateway. 
5. O gateway encaminha o pedido de ligação para o gateway correto mencionado no arquivo de gateway. 
6. O gateway envia um pedido para o cliente de escuta para este criar um canal temporário para o nó de gateway que está mais próximo do cliente de envio. 
7. O cliente de escuta cria um canal temporário para o gateway que está mais próximo do cliente de envio. Agora que a ligação é estabelecida entre os clientes através de um gateway, os clientes podem trocar mensagens entre si. 
8. O gateway encaminha todas as mensagens do cliente escuta para o cliente de envio. 
9. O gateway encaminha qualquer outra forma de mensagens do cliente de envio para o cliente de escuta.  

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução aos Pedidos de HTTP de .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Introdução ao Node WebSockets](relay-hybrid-connections-node-get-started.md)
* [Introdução aos Pedidos de HTTP de Node](relay-hybrid-connections-http-requests-node-get-started.md)
* [FAQ de Reencaminhamento](relay-faq.md)

