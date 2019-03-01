---
title: Modelo de autenticação e segurança - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo descreve o modelo de autenticação e segurança dos Hubs de eventos do Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 19b347423c28b4c615f90f325ead462b9d3e8e9e
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990039"
---
# <a name="azure-event-hubs---authentication-and-security-model"></a>Hubs de eventos do Azure - autenticação e o modelo de segurança

O modelo de segurança de Event Hubs do Azure cumpre os seguintes requisitos:

* Apenas clientes apresentam credenciais válidas podem enviar dados para um hub de eventos.
* Um cliente não é possível representar outro cliente.
* Um cliente não autorizado pode ser impedido de enviar dados para um hub de eventos.

## <a name="client-authentication"></a>Autenticação de cliente

O modelo de segurança de Hubs de eventos é baseado numa combinação de [assinatura de acesso partilhado (SAS)](../service-bus-messaging/service-bus-sas.md) tokens e *os publicadores de eventos*. Um publicador de eventos define um ponto final virtual para um hub de eventos. O publicador só pode ser utilizado para enviar mensagens para um hub de eventos. Não é possível receber mensagens de um publicador.

Normalmente, um hub de eventos emprega um publicador por cliente. Todas as mensagens enviadas a qualquer um dos editores de um hub de eventos são colocados em fila nesse hub de eventos. Os publicadores ativar o controlo de acesso detalhado e limitação.

Cada cliente dos Hubs de eventos é atribuído um token exclusivo, que é carregado para o cliente. Os tokens são produzidos, de modo a que cada token exclusivo concede acesso a um outro editor exclusivo. Um cliente que tiver um token só pode enviar para um publicador, mas nenhum outro editor. Se vários clientes partilharem o mesmo token, em seguida, cada um deles compartilha um publicador.

Embora não seja recomendado, é possível equiparem dispositivos com tokens que concedem acesso direto a um hub de eventos. Todos os dispositivos que contém este token podem enviar mensagens diretamente para esse hub de eventos. Tal um dispositivo não estará sujeito a limitação. Além disso, o dispositivo não pode ser bloqueado de enviar para esse hub de eventos.

Todos os tokens são assinados com uma chave SAS. Normalmente, todos os tokens são assinados com a mesma chave. Os clientes não têm conhecimento da chave; Isto impede que outros clientes tokens de fabrico.

### <a name="create-the-sas-key"></a>Criar a chave SAS

Ao criar um espaço de nomes de Hubs de eventos, o serviço gera automaticamente uma chave SAS de 256 bits denominada **RootManageSharedAccessKey**. Esta regra tem um associado par de chaves primárias e secundárias que concedem send, escutar e a gestão de direitos para o espaço de nomes. Também pode criar chaves adicionais. Recomenda-se que produza uma chave que concede envia permissões para o hub de eventos específicos. Para o resto deste tópico, presume-se que com o nome desta chave **EventHubSendKey**.

O exemplo seguinte cria uma chave apenas de envio, ao criar o hub de eventos:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Gerar tokens

Pode gerar tokens utilizando a chave SAS. Tem de produzir o token de apenas um por cliente. Tokens, em seguida, podem ser gerados usando o seguinte método. Todos os tokens são gerados com o **EventHubSendKey** chave. Cada token é atribuído um URI exclusivo. O parâmetro de "resource" corresponde ao ponto final do URI do serviço (hub de eventos, neste caso).

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Ao chamar esse método, o URI deve ser especificado como `https://<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Para todos os tokens, o URI é idêntico, com exceção do `PUBLISHER_NAME`, que deve ser diferente para cada token. Idealmente, `PUBLISHER_NAME` representa o ID do cliente que recebe esse token.

Esse método gera um token com a seguinte estrutura:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

O tempo de expiração do token é especificado em segundos a partir de 1 de Janeiro de 1970. Segue-se um exemplo de um token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Normalmente, os tokens têm um tempo de vida que se assemelha ou exceda o tempo de vida do cliente. Se o cliente tem a capacidade de obter um novo token, tokens com um menor tempo de vida podem ser utilizados.

### <a name="sending-data"></a>Envio de dados

Depois dos tokens foram criados, cada cliente é aprovisionada com seu próprio token exclusivo.

Quando o cliente envia dados para um hub de eventos, as etiquetas seu pedido de envio com o token. Para impedir que um invasor a interceptação e roubar o token, a comunicação entre o cliente e o hub de eventos tem de ocorrer por um canal criptografado.

### <a name="blacklisting-clients"></a>Clientes blacklisting

Se um token for roubado por um atacante, o invasor pode representar o cliente cujo token tenha sido roubado. Blacklisting um cliente renderiza esse cliente inutilizável até receber um novo token que utiliza um outro editor.

## <a name="authentication-of-back-end-applications"></a>Autenticação de aplicações de back-end

Para autenticar as aplicações de back-end que consomem os dados gerados pelos clientes de Hubs de eventos, os Hubs de eventos emprega um modelo de segurança que é semelhante ao modelo que é utilizado para tópicos do Service Bus. Um grupo de consumidores de Hubs de eventos é equivalente a uma subscrição para um tópico do Service Bus. Um cliente pode criar um grupo de consumidores, se o pedido para criar o grupo de consumidores é acompanhado por um token que concede gerir privilégios para o hub de eventos ou para o espaço de nomes ao qual pertence o hub de eventos. Um cliente tem permissão para consumir dados a partir de um grupo de consumidores, se o pedido de receção é acompanhado por um token que conceda direitos de recebimento nesse grupo de consumidores, o hub de eventos ou o espaço de nomes ao qual pertence o hub de eventos.

A versão atual do Service Bus não suporta regras SAS para assinaturas individuais. O mesmo se aplica a grupos de consumidores de Hubs de eventos. Suporte SAS será adicionado para os dois recursos no futuro.

Na ausência de autenticação de SAS para grupos de consumidores individuais, pode utilizar chaves SAS para proteger todos os grupos de consumidores com uma chave comum. Esta abordagem permite que um aplicativo consumir dados de qualquer um dos grupos de consumidores de um hub de eventos.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os Hubs de eventos, visite os seguintes tópicos:

* [Descrição geral dos Hubs de Eventos]
* [Descrição geral das assinaturas de acesso partilhado]
* [Aplicações de exemplo que utilizam Hubs de Eventos]

[Descrição geral dos Hubs de Eventos]: event-hubs-what-is-event-hubs.md
[Aplicações de exemplo que utilizam Hubs de Eventos]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Descrição geral das assinaturas de acesso partilhado]: ../service-bus-messaging/service-bus-sas.md

