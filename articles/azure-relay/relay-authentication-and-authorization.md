---
title: Autenticação e autorização do Azure Relay Microsoft Docs
description: Este artigo fornece uma visão geral da autenticação da Assinatura de Acesso Partilhado (SAS) com o serviço Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033382"
---
# <a name="azure-relay-authentication-and-authorization"></a>Autenticação e autorização do Relé Azure

As aplicações podem autenticar para O Relé Azure utilizando a autenticação assinatura de acesso partilhado (SAS). A autenticação SAS permite que as aplicações autentem o serviço Azure Relay utilizando uma chave de acesso configurada no espaço de nomes Relé. Em seguida, pode utilizar esta chave para gerar um token Assinatura de Acesso Partilhado que os clientes podem utilizar para autenticar no serviço de retransmissão.

## <a name="shared-access-signature-authentication"></a>Autenticação de assinatura de acesso partilhado

[A autenticação SAS](../service-bus-messaging/service-bus-sas.md) permite-lhe conceder a um utilizador acesso aos recursos do Azure Relay com direitos específicos. A autenticação SAS envolve a configuração de uma chave criptográfica com direitos associados num recurso. Os clientes podem então ter acesso a esse recurso apresentando um token SAS, que consiste no recurso a que o URI está a ser acedido e um prazo de validade assinado com a chave configurada.

Pode configurar as teclas para SAS num espaço de nomes de Retransmissão. Ao contrário das mensagens de Service Bus, [a Relay Hybrid Connections](relay-hybrid-connections-protocol.md) suporta remetentes não autorizados ou anónimos. Pode ativar o acesso anónimo à entidade quando a criar, como mostra a seguinte imagem do portal:

![Uma caixa de diálogo intitulada "Create Hybrid Connection" tem uma caixa de texto "Nome" e uma caixa de verificação com a etiqueta "Requer autenticação do cliente", que é verificada.][0]

Para utilizar SAS, pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) num espaço de nome Relay que consiste no seguinte:

* *Nome chave* que identifica a regra.
* *PrimaryKey* é uma chave criptográfica usada para assinar/validar fichas SAS.
* *SecondaryKey* é uma chave criptográfica usada para assinar/validar fichas SAS.
* *Direitos* que representam a recolha de direitos de escuta, envio ou gestão concedidos.

As regras de autorização configuradas ao nível do espaço de nome podem conceder acesso a todas as ligações de retransmissão num espaço de nome para clientes com fichas assinadas utilizando a chave correspondente. Até 12 dessas regras de autorização podem ser configuradas num espaço de nome Relay. Por padrão, uma [Regra de Polidissauto](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada espaço de nome quando é for provisionada pela primeira vez.

Para aceder a uma entidade, o cliente requer um token SAS gerado usando uma [regra de politização partilhada.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de recursos que consiste no recurso URI a que o acesso é reclamado, e uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte de autenticação SAS para Azure Relay está incluído nas versões Azure .NET SDK 2.0 e posterior. O SAS inclui suporte para uma [Regra de Poliétula Desauthorization.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) Todas as APIs que aceitam uma cadeia de ligação como parâmetro incluem suporte para cordas de conexão SAS.

## <a name="next-steps"></a>Passos seguintes

- Continuar a ler [Autenticação de autocarro de serviço com assinaturas de acesso partilhado](../service-bus-messaging/service-bus-sas.md) para mais detalhes sobre o SAS.
- Consulte o guia de [protocolo de conexões híbridas Azure Relay](relay-hybrid-connections-protocol.md) para obter informações detalhadas sobre a capacidade de conexões híbridas.
- Para obter informações correspondentes sobre a autenticação e autorização de mensagens de serviço, consulte [a autenticação e autorização do Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png