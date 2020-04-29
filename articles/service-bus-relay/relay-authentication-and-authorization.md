---
title: Autenticação e autorização da Estafeta Azure Microsoft Docs
description: Este artigo fornece uma visão geral da autenticação da Assinatura de Acesso Partilhado (SAS) com o serviço Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: aac5c973a99b13d5918a0162feb7f1ede443463b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76514583"
---
# <a name="azure-relay-authentication-and-authorization"></a>Autenticação e autorização do Relé Azure

As aplicações podem autenticar o Relé Azure utilizando a autenticação da Assinatura de Acesso Partilhado (SAS). A autenticação SAS permite que as aplicações se autentiem ao serviço Azure Relay utilizando uma chave de acesso configurada no espaço de nome retransmissor. Em seguida, pode utilizar esta chave para gerar um símbolo de Assinatura de Acesso Partilhado que os clientes podem usar para autenticar o serviço de retransmissão.

## <a name="shared-access-signature-authentication"></a>Autenticação de assinatura de acesso partilhado

[A autenticação SAS](../service-bus-messaging/service-bus-sas.md) permite-lhe conceder ao utilizador acesso aos recursos da Azure Relay com direitos específicos. A autenticação SAS envolve a configuração de uma chave criptográfica com direitos associados num recurso. Os clientes podem então ter acesso a esse recurso apresentando um token SAS, que consiste no recurso URI a ser acedido e uma caducidade assinada com a chave configurada.

Pode configurar as teclas para SAS num espaço de nome retransmissor. Ao contrário das mensagens service Bus, a [Relay Hybrid Connections](relay-hybrid-connections-protocol.md) suporta remetentes não autorizados ou anónimos. Pode ativar o acesso anónimo à entidade quando a criar, como mostra a seguinte imagem do portal:

![][0]

Para utilizar o SAS, pode configurar um objeto [sharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) num espaço de nome relé que consiste no seguinte:

* *Nome chave* que identifica a regra.
* *PrimaryKey* é uma chave criptográfica usada para assinar/validar tokens SAS.
* *SecondaryKey* é uma chave criptográfica usada para assinar/validar tokens SAS.
* *Direitos* que representam a recolha de direitos de escuta, envio ou gestão concedidos.

As regras de autorização configuradas ao nível do espaço de nome podem conceder acesso a todas as ligações de retransmissão num espaço de nome para clientes com fichas assinadas utilizando a chave correspondente. Até 12 dessas regras de autorização podem ser configuradas num espaço de nome retransmissor. Por predefinição, uma Regra de Autorização de [Acesso Partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos está configurada para cada espaço de nome quando é aprovisionado pela primeira vez.

Para aceder a uma entidade, o cliente requer um token SAS gerado através de uma regra específica de Autorização de [Acesso Partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de recursos que consiste no recurso URI a que o acesso é reivindicado, e uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte de autenticação SAS para O Relé Azure está incluído nas versões Azure.NET SDK 2.0 e posteriormente. O SAS inclui suporte para uma Regra de Autorização de [Acesso Partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de ligação como parâmetro incluem suporte para cordas de ligação SAS.

## <a name="next-steps"></a>Passos seguintes

- Continuar a ler [Autenticação de ônibus de serviço com Assinaturas de Acesso Partilhado](../service-bus-messaging/service-bus-sas.md) para mais detalhes sobre sas.
- Consulte o guia de [protocolo azure Relay Hybrid Connections](relay-hybrid-connections-protocol.md) para obter informações detalhadas sobre a capacidade de Conexões Híbridas.
- Para obter informações correspondentes sobre autenticação e autorização de mensagens de autocarro de serviço, consulte a autenticação e autorização do [Autocarro de Serviço.](../service-bus-messaging/service-bus-authentication-and-authorization.md) 

[0]: ./media/relay-authentication-and-authorization/hcanon.png