---
title: Controle de acesso do barramento de serviço do Azure com assinaturas de acesso compartilhado
description: Visão geral do controle de acesso do barramento de serviço usando as assinaturas de acesso compartilhado visão geral, detalhes sobre a autorização SAS com o barramento de serviço do Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: c381d9413c4003bc2ab9a9357ff2769e84d14c3e
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121748"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Controle de acesso do barramento de serviço com assinaturas de acesso compartilhado

As SAS ( *assinaturas de acesso compartilhado* ) são o mecanismo de segurança principal para mensagens do barramento de serviço. Este artigo discute a SAS, como elas funcionam e como usá-las de forma independente da plataforma.

A SAS protege o acesso ao barramento de serviço com base nas regras de autorização. Eles são configurados em um namespace ou em uma entidade de mensagens (retransmissão, fila ou tópico). Uma regra de autorização tem um nome, está associada a direitos específicos e transporta um par de chaves de criptografia. Você usa o nome e a chave da regra por meio do SDK do barramento de serviço ou em seu próprio código para gerar um token SAS. Um cliente pode passar o token para o barramento de serviço para provar a autorização para a operação solicitada.

> [!NOTE]
> O barramento de serviço do Azure dá suporte à autorização de acesso a um namespace do barramento de serviço e suas entidades usando o Azure Active Directory (AD do Azure). A autorização de usuários ou aplicativos usando o token 2,0 do OAuth retornado pelo Azure AD fornece segurança superior e facilidade de uso sobre SAS (assinaturas de acesso compartilhado). Com o Azure AD, não é necessário armazenar os tokens no código e arriscar as vulnerabilidades de segurança potenciais.
>
> A Microsoft recomenda usar o Azure AD com seus aplicativos do barramento de serviço do Azure quando possível. Para obter mais informações, veja os artigos seguintes:
> - [Autentique e autorize um aplicativo com Azure Active Directory para acessar entidades do barramento de serviço do Azure](authenticate-application.md).
> - [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos do barramento de serviço do Azure](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Visão geral da SAS

As assinaturas de acesso compartilhado são um mecanismo de autorização baseado em declarações usando tokens simples. Usando a SAS, as chaves nunca são passadas na conexão. As chaves são usadas para assinar criptograficamente informações que podem ser verificadas posteriormente pelo serviço. A SAS pode ser usada de forma semelhante a um esquema de nome de usuário e senha, em que o cliente está em posse imediata de um nome de regra de autorização e uma chave correspondente. A SAS também pode ser usada de forma semelhante a um modelo de segurança federado, em que o cliente recebe um token de acesso assinado e limitado de um serviço de token de segurança sem nunca receber a posse da chave de assinatura.

A autenticação SAS no barramento de serviço é configurada com [regras de autorização de acesso compartilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nomeadas que têm direitos de acesso associados e um par de chaves de criptografia primárias e secundárias. As chaves são valores de 256 bits na representação base64. Você pode configurar regras no nível de namespace, nas [retransmissões](../service-bus-relay/relay-what-is-it.md), [filas](service-bus-messaging-overview.md#queues)e [Tópicos](service-bus-messaging-overview.md#topics)do barramento de serviço.

O token de [assinatura de acesso compartilhado](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) contém o nome da regra de autorização escolhida, o URI do recurso que deve ser acessado, um instante de expiração e uma assinatura de criptografia HMAC-SHA256 computada nesses campos usando a chave de criptografia primária ou secundária da regra de autorização escolhida.

## <a name="shared-access-authorization-policies"></a>Políticas de autorização de acesso compartilhado

Cada namespace do barramento de serviço e cada entidade do barramento de serviço tem uma política de autorização de acesso compartilhado composta por regras. A política no nível de namespace se aplica a todas as entidades dentro do namespace, independentemente de sua configuração de política individual.

Para cada regra de política de autorização, você decide três partes de informações: **nome**, **escopo**e **direitos**. O **nome** é exatamente isso; um nome exclusivo dentro desse escopo. O escopo é fácil o suficiente: é o URI do recurso em questão. Para um namespace do barramento de serviço, o escopo é o FQDN (nome de domínio totalmente qualificado), como `https://<yournamespace>.servicebus.windows.net/`.

Os direitos conferidos pela regra de política podem ser uma combinação de:

* ' Send '-confere o direito de enviar mensagens para a entidade
* ' Escutar '-confere o direito de escutar (retransmissão) ou recebimento (fila, assinaturas) e todos os tratamentos de mensagens relacionados
* ' Gerenciar '-confere o direito de gerenciar a topologia do namespace, incluindo a criação e a exclusão de entidades

O direito ' Gerenciar ' inclui os direitos ' enviar ' e ' receber '.

Uma política de namespace ou de entidade pode conter até 12 regras de autorização de acesso compartilhado, fornecendo espaço para três conjuntos de regras, cada um cobrindo os direitos básicos e a combinação de enviar e escutar. Esse limite sublinha que o repositório de políticas SAS não pretende ser um armazenamento de conta de serviço ou de usuário. Se seu aplicativo precisar conceder acesso ao barramento de serviço com base em identidades de usuário ou de serviço, ele deverá implementar um serviço de token de segurança que emite tokens SAS após uma verificação de autenticação e acesso.

Uma regra de autorização recebe uma *chave primária* e uma *chave secundária*. Essas são chaves criptograficamente fortes. Não perca-os ou vazando-os, eles estarão sempre disponíveis no [portal do Azure][Azure portal]. Você pode usar qualquer uma das chaves geradas e pode gerá-las novamente a qualquer momento. Se você regenerar ou alterar uma chave na política, todos os tokens emitidos anteriormente com base nessa chave se tornarão inválidos instantaneamente. No entanto, as conexões em andamento criadas com base nesses tokens continuarão a funcionar até que o token expire.

Quando você cria um namespace do barramento de serviço, uma regra de política chamada **RootManageSharedAccessKey** é criada automaticamente para o namespace. Esta política tem permissões de gerenciamento para o namespace inteiro. É recomendável que você trate essa regra como uma conta **raiz** administrativa e não a use em seu aplicativo. Você pode criar regras de política adicionais na guia **Configurar** para o namespace no portal, por meio do Powershell ou CLI do Azure.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuração da autenticação de assinatura de acesso compartilhado

Você pode configurar a regra [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em namespaces, filas ou tópicos do barramento de serviço. Não há suporte para a configuração de um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em uma assinatura do barramento de serviço no momento, mas você pode usar regras configuradas em um namespace ou tópico para proteger o acesso a assinaturas. Para obter um exemplo funcional que ilustre esse procedimento, consulte o exemplo [usando a autenticação SAS (assinatura de acesso compartilhado) com assinaturas do barramento de serviço](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Nesta figura, as regras de autorização *manageRuleNS*, *sendRuleNS*e *listenRuleNS* se aplicam à fila Q1 e ao tópico T1, enquanto *listenRuleQ* e *sendRuleQ* aplicam-se somente à fila Q1 e *sendRuleT* aplica-se somente ao tópico T1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um token de assinatura de acesso compartilhado

Qualquer cliente que tenha acesso ao nome de um nome de regra de autorização e uma de suas chaves de assinatura pode gerar um token SAS. O token é gerado pela criação de uma cadeia de caracteres no seguinte formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* instantâneo de expiração de **`se`** tokens. Inteiro refletindo segundos desde a época `00:00:00 UTC` em 1 de janeiro de 1970 (época do UNIX) quando o token expira.
* **`skn`** -nome da regra de autorização.
* **`sr`** -URI do recurso que está sendo acessado.
* assinatura **`sig`** .

O `signature-string` é o hash SHA-256 calculado sobre o URI de recurso (**escopo** , conforme descrito na seção anterior) e a representação de cadeia de caracteres do instantâneo de expiração do token, separados por LF.

A computação de hash é semelhante ao seguinte pseudocódigo e retorna um valor de hash de 256 bits/32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

O token contém os valores sem hash para que o destinatário possa recalcular o hash com os mesmos parâmetros, verificando se o emissor está em posse de uma chave de assinatura válida.

O URI de recurso é o URI completo do recurso do barramento de serviço ao qual o acesso é reivindicado. Por exemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>`; ou seja, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. 

**O URI deve ser [codificado por percentual](https://msdn.microsoft.com/library/4fkewx0t.aspx).**

A regra de autorização de acesso compartilhado usada para assinatura deve ser configurada na entidade especificada por esse URI ou por um de seus pais hierárquicos. Por exemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos prefixados com o `<resourceURI>` usado na `signature-string`.

## <a name="regenerating-keys"></a>Regenerando chaves

É recomendável que você regenere periodicamente as chaves usadas no objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . Os slots de chave primária e secundária existem para que você possa girar as chaves gradualmente. Se seu aplicativo geralmente usa a chave primária, você pode copiar a chave primária para o slot de chave secundária e, em seguida, regenerar a chave primária. O novo valor de chave primária pode então ser configurado nos aplicativos cliente, que têm acesso contínuo usando a antiga chave primária no slot secundário. Depois que todos os clientes forem atualizados, você poderá regenerar a chave secundária para finalmente desativar a chave primária antiga.

Se você souber ou suspeitar que uma chave está comprometida e precisar revogar as chaves, poderá regenerar o [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) e o [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) de um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), substituindo-os por novas chaves. Este procedimento invalida todos os tokens assinados com as chaves antigas.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autenticação de assinatura de acesso compartilhado com o barramento de serviço

Os cenários descritos a seguir incluem a configuração de regras de autorização, a geração de tokens SAS e a autorização do cliente.

Para obter um exemplo funcional completo de um aplicativo do barramento de serviço que ilustra a configuração e usa a autorização de SAS, consulte [autenticação de assinatura de acesso compartilhado com o barramento de serviço](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Um exemplo relacionado que ilustra o uso de regras de autorização SAS configuradas em namespaces ou tópicos para proteger assinaturas do barramento de serviço está disponível aqui: [usando a autenticação SAS (assinatura de acesso compartilhado) com assinaturas do barramento de serviço](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Acessar regras de autorização de acesso compartilhado em uma entidade

Com as bibliotecas de .NET Framework do barramento de serviço, você pode acessar um objeto [Microsoft. ServiceBus. Messaging. SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurado em uma fila ou um tópico do barramento de serviço por meio da coleção [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) no [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)correspondente.

O código a seguir mostra como adicionar regras de autorização para uma fila.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Usar autorização de assinatura de acesso compartilhado

Os aplicativos que usam o SDK do .NET do Azure com as bibliotecas .NET do barramento de serviço podem usar a autorização SAS por meio da classe [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . O código a seguir ilustra o uso do provedor de token para enviar mensagens para uma fila do barramento de serviço. Alternativa ao uso mostrado aqui, você também pode passar um token emitido anteriormente para o método de fábrica do provedor de token.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Você também pode usar o provedor de token diretamente para emitir tokens para passar para outros clientes.

As cadeias de conexão podem incluir um nome de regra (*SharedAccessKeyName*) e uma chave de regra (*SharedAccessKey*) ou um token emitido anteriormente (*SharedAccessSignature*). Quando estão presentes na cadeia de conexão passada para qualquer Construtor ou método de fábrica que aceita uma cadeia de conexão, o provedor de token SAS é automaticamente criado e populado.

Observe que para usar a autorização SAS com retransmissões do barramento de serviço, você pode usar chaves SAS configuradas no namespace do barramento de serviço. Se você criar explicitamente uma retransmissão no namespace ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) com um objeto [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)), poderá definir as regras de SAS apenas para essa retransmissão. Para usar a autorização SAS com assinaturas do barramento de serviço, você pode usar chaves SAS configuradas em um namespace do barramento de serviço ou em um tópico.

## <a name="use-the-shared-access-signature-at-http-level"></a>Usar a assinatura de acesso compartilhado (no nível de HTTP)

Agora que você sabe como criar assinaturas de acesso compartilhado para todas as entidades no barramento de serviço, você está pronto para executar um HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Lembre-se de que isso funciona para tudo. Você pode criar SAS para uma fila, tópico ou assinatura.

Se você fornecer a um remetente ou cliente um token SAS, eles não terão a chave diretamente e não poderão reverter o hash para obtê-lo. Dessa forma, você tem controle sobre o que eles podem acessar e por quanto tempo. Uma coisa importante a ser lembrada é que, se você alterar a chave primária na política, qualquer assinatura de acesso compartilhado criada a partir dela será invalidada.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Usar a assinatura de acesso compartilhado (no nível de AMQP)

Na seção anterior, você viu como usar o token SAS com uma solicitação HTTP POST para enviar dados para o barramento de serviço. Como você sabe, é possível acessar o barramento de serviço usando o Advanced Message Queuing Protocol (AMQP), que é o protocolo preferencial a ser usado por motivos de desempenho, em muitos cenários. O uso do token SAS com AMQP é descrito no documento [AMQP de segurança baseada em declarações versão 1,0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está em rascunho funcional desde 2013, mas bem suportada pelo Azure hoje.

Antes de começar a enviar dados para o barramento de serviço, o Publicador deve enviar o token SAS dentro de uma mensagem AMQP para um nó AMQP bem definido chamado **$CBS** (você pode vê-lo como uma fila "especial" usada pelo serviço para adquirir e validar todos os tokens SAS). O Publicador deve especificar o campo **ReplyTo** dentro da mensagem AMQP; Esse é o nó no qual o serviço responde ao Publicador com o resultado da validação do token (um padrão simples de solicitação/resposta entre o Publicador e o serviço). Esse nó de resposta é criado imediatamente, "falando sobre a criação dinâmica do nó remoto", conforme descrito pela especificação AMQP 1,0. Depois de verificar se o token SAS é válido, o Publicador pode avançar e começar a enviar dados para o serviço.

As etapas a seguir mostram como enviar o token SAS com o protocolo AMQP usando a biblioteca [AMQP.net Lite](https://github.com/Azure/amqpnetlite) . Isso é útil se você não pode usar o SDK oficial do barramento de serviço (por exemplo, em WinRT, .NET Compact Framework, .NET micro Framework e mono) desenvolvendo em C\#. É claro que essa biblioteca é útil para ajudar a entender como a segurança baseada em declarações funciona no nível de AMQP, como você viu como ela funciona no nível de HTTP (com uma solicitação HTTP POST e o token SAS enviado dentro do cabeçalho "Authorization"). Se você não precisar desse conhecimento profundo sobre o AMQP, poderá usar o SDK oficial do barramento de serviço com .NET Framework aplicativos, o que fará isso para você.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

O método `PutCbsToken()` recebe a *conexão* (instância de classe de conexão AMQP, conforme fornecido pela [biblioteca AMQP do .net Lite](https://github.com/Azure/amqpnetlite)) que representa a conexão TCP com o serviço e o parâmetro *SASTOKEN* que é o token SAS a ser enviado.

> [!NOTE]
> É importante que a conexão seja criada com o **mecanismo de autenticação SASL definido como anônimo** (e não o padrão sem formatação com nome de usuário e senha usados quando você não precisa enviar o token SAS).
>
>

Em seguida, o Publicador cria dois links AMQP para enviar o token SAS e receber a resposta (o resultado de validação do token) do serviço.

A mensagem AMQP contém um conjunto de propriedades e mais informações do que uma mensagem simples. O token SAS é o corpo da mensagem (usando seu construtor). A propriedade **"ReplyTo"** é definida como o nome do nó para receber o resultado da validação no link do destinatário (você pode alterar seu nome, se desejar, e ele será criado dinamicamente pelo serviço). As três últimas Propriedades de aplicativo/personalizadas são usadas pelo serviço para indicar que tipo de operação ele deve executar. Conforme descrito pela especificação de rascunho do CBS, ele deve ser o **nome da operação** ("Put-token"), o **tipo de token** (nesse caso, um `servicebus.windows.net:sastoken`) e o **"nome" do público** ao qual o token se aplica (a entidade inteira).

Depois de enviar o token SAS no link do remetente, o Publicador deve ler a resposta no link do destinatário. A resposta é uma mensagem AMQP simples com uma propriedade de aplicativo chamada **"status-código"** que pode conter os mesmos valores de um código de status http.

## <a name="rights-required-for-service-bus-operations"></a>Direitos necessários para operações do barramento de serviço

A tabela a seguir mostra os direitos de acesso necessários para várias operações nos recursos do barramento de serviço.

| Operação | Declaração necessária | Escopo da declaração |
| --- | --- | --- |
| **Namespace** | | |
| Configurar a regra de autorização em um namespace |Gerir |Qualquer endereço de namespace |
| **Registro de serviço** | | |
| Enumerar políticas privadas |Gerir |Qualquer endereço de namespace |
| Começar a escutar em um namespace |Escutar |Qualquer endereço de namespace |
| Enviar mensagens para um ouvinte em um namespace |Enviar |Qualquer endereço de namespace |
| **Fila** | | |
| Criar uma fila |Gerir |Qualquer endereço de namespace |
| Eliminar uma fila |Gerir |Qualquer endereço de fila válido |
| Enumerar filas |Gerir |/$Resources/Queues |
| Obter a descrição da fila |Gerir |Qualquer endereço de fila válido |
| Configurar a regra de autorização para uma fila |Gerir |Qualquer endereço de fila válido |
| Enviar para a fila |Enviar |Qualquer endereço de fila válido |
| Receber mensagens de uma fila |Escutar |Qualquer endereço de fila válido |
| Abandonar ou concluir as mensagens depois de receber a mensagem no modo de bloqueio de pico |Escutar |Qualquer endereço de fila válido |
| Adiar uma mensagem para recuperação posterior |Escutar |Qualquer endereço de fila válido |
| Mensagem de mensagens mortas |Escutar |Qualquer endereço de fila válido |
| Obter o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| Definir o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| Agendar uma mensagem para entrega posterior; por exemplo, [ScheduleMessageAsync ()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Escutar | Qualquer endereço de fila válido
| **Tópico** | | |
| Criar um tópico |Gerir |Qualquer endereço de namespace |
| Excluir um tópico |Gerir |Qualquer endereço de tópico válido |
| Enumerar tópicos |Gerir |/$Resources/topics |
| Obter a descrição do tópico |Gerir |Qualquer endereço de tópico válido |
| Configurar a regra de autorização para um tópico |Gerir |Qualquer endereço de tópico válido |
| Enviar para o tópico |Enviar |Qualquer endereço de tópico válido |
| **Subscrição** | | |
| Criar uma subscrição |Gerir |Qualquer endereço de namespace |
| Eliminar subscrição |Gerir |../myTopic/subscriptions/mySubscription |
| Enumerar assinaturas |Gerir |../myTopic/subscrições |
| Obter descrição da assinatura |Gerir |../myTopic/subscriptions/mySubscription |
| Abandonar ou concluir as mensagens depois de receber a mensagem no modo de bloqueio de pico |Escutar |../myTopic/subscriptions/mySubscription |
| Adiar uma mensagem para recuperação posterior |Escutar |../myTopic/subscriptions/mySubscription |
| Mensagem de mensagens mortas |Escutar |../myTopic/subscriptions/mySubscription |
| Obter o estado associado a uma sessão de tópico |Escutar |../myTopic/subscriptions/mySubscription |
| Definir o estado associado a uma sessão de tópico |Escutar |../myTopic/subscriptions/mySubscription |
| **Regras** | | |
| Criar uma regra |Gerir |../myTopic/subscriptions/mySubscription |
| Eliminar uma regra |Gerir |../myTopic/subscriptions/mySubscription |
| Enumerar regras |Gerenciar ou escutar |../myTopic/subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Como utilizar as filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
