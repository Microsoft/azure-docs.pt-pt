---
title: Controlo de acesso do Service Bus do Azure com assinaturas de acesso partilhado | Documentos da Microsoft
description: Descrição geral do controlo de acesso de Service Bus através de descrição geral de assinaturas de acesso partilhado, detalhes sobre a autorização de SAS com o Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 8f5c1755462d2bbd28dd7f8db427cda141817588
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308861"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Controlo de acesso do Service Bus com assinaturas de acesso partilhado

*Assinaturas de acesso partilhado* (SAS) são o mecanismo de segurança principal para mensagens do Service Bus. Este artigo aborda o SAS, como elas funcionam e como utilizá-los de forma independente de plataforma.

SAS protege o acesso ao Service Bus com base nas regras de autorização. Esses são configurados no espaço de nomes ou uma entidade de mensagens (reencaminhamento, fila ou tópico). Uma regra de autorização tem um nome, está associada com direitos específicos e carrega um par de chaves criptográficas. Utilize o nome da regra e a chave através do SDK do Service Bus ou no seu próprio código para gerar um token SAS. Um cliente, em seguida, pode passar o token para o Service Bus para provar a autorização para a operação pedida.

## <a name="overview-of-sas"></a>Descrição geral de SAS

Assinaturas de acesso partilhado são um mecanismo de autorização baseada em declarações usando tokens simples. Através da SAS, as chaves são nunca transmitidas na conexão. Chaves são utilizadas para assinar criptograficamente informações que mais tarde podem ser verificadas pelo serviço. SAS pode ser utilizada semelhante a um esquema de nome de utilizador e palavra-passe em que o cliente está na posse imediata de um nome de regra de autorização e uma chave correspondente. SAS também pode ser utilizada semelhante a um modelo de segurança federada, onde o cliente recebe um token de acesso de tempo limitado e assinado de um serviço de token de segurança sem nunca chegam a posse da chave de assinatura.

Se a autenticação SAS no Service Bus com o nome [regras de autorização de acesso partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) ter direitos de acesso e um par de chaves criptográficas primários e secundários associados. As chaves são valores de 256 bits na representação Base64. Pode configurar regras ao nível do espaço de nomes, no Service Bus [retransmite](../service-bus-relay/relay-what-is-it.md), [filas](service-bus-messaging-overview.md#queues), e [tópicos](service-bus-messaging-overview.md#topics).

O [assinatura de acesso partilhado](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token contém o nome da regra de autorização escolhida, o URI do recurso que deve ser acessado, um instantâneo, de expiração e uma assinatura criptográfica do HMAC-SHA256 calculada com esses campos com o principal ou a chave criptográfica secundária da regra de autorização escolhida.

## <a name="shared-access-authorization-policies"></a>Políticas de autorização de acesso partilhado

Cada espaço de nomes do Service Bus e cada entidade do Service Bus tem uma política de autorização de acesso partilhado constituída por regras. A política ao nível do espaço de nomes se aplica a todas as entidades dentro do espaço de nomes, independentemente de suas configurações de política individuais.

Para cada regra de política de autorização, decidir em três partes de informações: **name**, **âmbito**, e **direitos**. O **nome** é exatamente isso; um nome exclusivo dentro dele. O âmbito é bastante simples: é o URI do recurso em questão. Para um espaço de nomes do Service Bus, o escopo é o nome de domínio completamente qualificado (FQDN), tal como `https://<yournamespace>.servicebus.windows.net/`.

Os direitos conferidos pela regra de política podem ser uma combinação de:

* "Envio" - confere o direito para enviar mensagens para a entidade
* "Escutar" - confere o direito de escutar (reencaminhamento) ou receber (fila, subscrições) e todos os relacionados que o processamento de mensagens
* "Gerir" - confere o direito de gerir a topologia do espaço de nomes, incluindo criar e eliminar entidades

O direito de "Gerir" inclui o direito de "Send" e "Receive".

Uma política de espaço de nomes ou entidade pode conter até 12 regras de autorização de acesso partilhado, fornecendo espaço para três conjuntos de regras, cada um que abrangem os direitos de básicos e a combinação de envio e a escutar. Sublinhar este limite que armazenam a política SAS não se destina a ser um utilizador ou o armazenamento de conta de serviço. Se a sua aplicação tem de conceder acesso ao Service Bus com base no utilizador ou de identidades de serviço, ele deve implementar um serviço de token de segurança que emite tokens SAS após uma verificação de autenticação e acesso.

Uma regra de autorização é atribuída um *chave primária* e uma *chave secundária*. São chaves criptograficamente fortes. Não perca-los ou de fuga-los - estarão sempre disponíveis na [portal do Azure][Azure portal]. Pode usar qualquer uma das chaves geradas e pode voltar a gerá-los em qualquer altura. Se voltar a gerar ou alterar uma chave na política, todos emitido anteriormente tokens com base nessa chave tornar-se imediatamente inválida. No entanto, as ligações em curso, criadas com base nesses tokens irão continuar a funcionar até que o token expira.

Quando cria um espaço de nomes do Service Bus, uma regra de política com o nome **RootManageSharedAccessKey** é criado automaticamente para o espaço de nomes. Esta política não tem permissões de gestão para todo o espaço de nomes. Recomenda-se que trate esta regra, como um administrativas **raiz** conta e não usá-lo em seu aplicativo. Pode criar regras de política adicionais no **configurar** separador para o espaço de nomes no portal, através do Powershell ou da CLI do Azure.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuração para a autenticação de assinatura de acesso partilhado

Pode configurar o [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regra em espaços de nomes do Service Bus, filas ou tópicos. Configurar uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) num barramento de serviço de subscrição não é atualmente suportada, mas pode utilizar as regras configuradas num espaço de nomes ou tópico para proteger o acesso a subscrições. Para obter um exemplo de trabalho que ilustra este procedimento, consulte a [autenticação de utilizar o acesso à assinatura partilhado (SAS) com subscrições do Service Bus](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) exemplo.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Nessa figura, o *manageRuleNS*, *sendRuleNS*, e *listenRuleNS* regras de autorização são aplicadas a fila de Q1 e tópico T1, enquanto *listenRuleQ*  e *sendRuleQ* aplicam-se apenas a fila de Q1 e *sendRuleT* aplica-se apenas ao tópico T1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um token de assinatura de acesso partilhado

Qualquer cliente que tenha acesso ao nome de um nome de regra de autorização e uma das suas chaves de assinatura podem gerar um token SAS. O token é gerado por uma cadeia de caracteres no seguinte formato de composição:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Expiração do token instantânea. Número inteiro que reflete segundos desde a época `00:00:00 UTC` em de 1970 1 de Janeiro (época do UNIX) quando o token expira.
* **`skn`** -Nome da regra de autorização.
* **`sr`** -URI do recurso que está sendo acessado.
* **`sig`** -Assinatura.

O `signature-string` é o hash SHA-256 é calculado com o URI do recurso (**âmbito** conforme descrito na secção anterior) e a representação de cadeia de caracteres de instantânea, a expiração do token separados por CRLF.

O cálculo de hash terá um aspeto semelhante ao seguinte pseudocódigo e retorna um valor de hash de 256 bits/32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

O token contém os valores não protegido por hash, para que o destinatário pode Reprocessar o hash com os mesmos parâmetros, verificar se o emissor está na posse de uma chave de assinatura válida.

O URI do recurso é o URI completo do recurso do Service Bus para o qual o acesso seja solicitado. Por exemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>`, ou seja, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. O URI tem de ser [codificada de percentagem](https://msdn.microsoft.com/library/4fkewx0t.aspx).

A regra de autorização de acesso partilhado utilizada na assinatura deve ser configurada na entidade especificada por este URI ou por uma das respetivas classes principais hierárquicas. Por exemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos com o prefixo a `<resourceURI>` utilizada no `signature-string`.

## <a name="regenerating-keys"></a>Regenerar chaves

Recomenda-se que periodicamente regenerar as chaves usadas na [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto. As ranhuras de chave primárias e secundárias existem para que pode girar chaves gradualmente. Se seu aplicativo, geralmente, utiliza a chave primária, pode copiar a chave primária para a ranhura de chave secundária e, apenas, em seguida, voltar a gerar a chave primária. O novo valor da chave primário, em seguida, pode ser configurado nos aplicativos do cliente, continuaram acesso utilizando a chave primária antiga na ranhura de secundária. Depois de todos os clientes são atualizados, pode voltar a gerar a chave secundária para finalmente extinguir a chave primária antiga.

Se conhecer ou se suspeitar que uma chave fica comprometida e tem de revogar as chaves, pode voltar a gerar ambas as [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) e o [secundária de regeneração](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) de uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), substituí-los com chaves novas. Este procedimento invalida todos os tokens assinados com as chaves antigas.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autenticação da assinatura de acesso partilhada com o Service Bus

Os cenários descritos da seguinte forma incluem a configuração de regras de autorização, geração de tokens SAS e autorização de cliente.

Para um completo exemplo funcional de um aplicativo de Service Bus que ilustra a autorização de SAS de configuração e utilizações, consulte [autenticação de assinatura de acesso partilhado com o Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Um exemplo relacionado que ilustra o uso de regras de autorização de SAS configuradas em espaços de nomes ou tópicos para proteger as subscrições do Service Bus está disponível aqui: [Utilizar a autenticação de assinatura de acesso partilhado (SAS) com as subscrições do Service Bus](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Regras de autorização de acesso partilhado de acesso numa entidade

Com bibliotecas do Service Bus .NET Framework, pode acessar um [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurado numa fila do Service Bus ou tópico por meio do objeto a [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) coleção nas correspondentes [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

O código seguinte mostra como adicionar regras de autorização de uma fila.

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

## <a name="use-shared-access-signature-authorization"></a>Utilizam a autorização de assinatura de acesso partilhado

Aplicações com o SDK de .NET do Azure com as bibliotecas .NET de barramento de serviço podem utilizar a autorização de SAS através de [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) classe. O código a seguir ilustra o uso do fornecedor do token para enviar mensagens para uma fila do Service Bus. Alternativa à utilização de mostrado aqui, que também é possível passar um token emitido anteriormente para o método de fábrica do fornecedor do token.

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

Também pode utilizar o fornecedor do token diretamente para a emissão de tokens para passar para outros clientes.

Cadeias de ligação podem incluir um nome de regra (*SharedAccessKeyName*) e a chave de regra (*SharedAccessKey*) ou um token emitido anteriormente (*SharedAccessSignature*). Quando os estão presentes na cadeia de ligação transmitida a qualquer construtor ou método de fábrica abertos ao recebimento de uma cadeia de ligação, o fornecedor do token SAS é automaticamente criado e preenchido.

Tenha em atenção que o uso da autorização de SAS com reencaminhamentos do Service Bus, pode utilizar chaves SAS configuradas no espaço de nomes do Service Bus. Se criar explicitamente um reencaminhamento no espaço de nomes ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) com um [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) de objeto, pode definir as regras SAS apenas para desse reencaminhamento. Para utilizar a autorização de SAS com subscrições do Service Bus, pode utilizar chaves SAS configuradas num espaço de nomes do Service Bus ou num tópico.

## <a name="use-the-shared-access-signature-at-http-level"></a>Utilize a assinatura de acesso partilhado (no nível HTTP)

Agora que sabe como criar assinaturas de acesso partilhado para todas as entidades no Service Bus, está pronto para executar um HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Lembre-se de que isso funciona para tudo. Pode criar a SAS para uma fila, tópico ou subscrição.

Se conceder um remetente ou o cliente um token SAS, eles não têm a chave diretamente e não é possível reverter o hash para obtê-lo. Como tal, tem controlo sobre o que eles podem acessar e como há muito tempo. Uma coisa importante a lembrar é que se alterar a chave primária na política, criados a partir deste qualquer assinaturas de acesso partilhado são também invalidadas.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Utilize a assinatura de acesso partilhado (no nível AMQP)

Na secção anterior, vimos como utilizar o token SAS com um pedido HTTP POST para enviar dados para o Service Bus. Como sabe, pode acessar o Service Bus com o Advanced Message colocação Protocol (AMQP) que é o protocolo preferencial a utilizar por motivos de desempenho, em muitos cenários. A utilização de token de SAS com o AMQP é descrita no documento [AMQP Claim-Based segurança versão 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) ou seja em rascunho funcional desde 2013, mas bem suportada pelo Azure hoje mesmo.

Antes de começar a enviar dados para o Service Bus, o publicador tem de enviar o token SAS dentro de uma mensagem AMQP para um nó AMQP bem definido, denominado **$cbs** (pode vê-lo como uma fila "especial" utilizada pelo serviço para adquirir e validar todas as a SAS tokens). O publicador tem de especificar o **ReplyTo** campo dentro da mensagem AMQP; este é o nó no qual o serviço responde ao publicador com o resultado da validação do token (uma solicitação/resposta simples padrão entre o publicador e o serviço ). Este nó de resposta é criada "em tempo real," falando sobre "criação dinâmica de nó remoto", conforme descrito pela especificação do AMQP 1.0. Depois de verificar que o token SAS é válido, o publicador pode ir adiante e começar a enviar dados para o serviço.

Os passos seguintes mostram como enviar o token SAS com a utilização do protocolo AMQP a [AMQP.NET Lite](https://github.com/Azure/amqpnetlite) biblioteca. Isto é útil se não é possível utilizar o SDK do barramento de serviço (por exemplo no WinRT, .NET Compact Framework, .NET Micro Framework e Mono) oficial desenvolver em C\#. É claro, esta biblioteca é útil para ajudar a compreender como baseado em declarações funciona ao nível do AMQP, como viu como ele funciona no nível HTTP (com um pedido POST de HTTP e o token SAS enviado dentro o cabeçalho "Authorization"). Se não precisar de tal conhecimento profundo sobre AMQP, pode utilizar o SDK de barramento de serviço oficial com aplicativos do .NET Framework, que farão isso para.

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

O `PutCbsToken()` método recebe o *ligação* (instância de classe de ligação AMQP fornecido pela [biblioteca .NET de AMQP Lite](https://github.com/Azure/amqpnetlite)) que representa a ligação de TCP para o serviço e a *sasToken* token de parâmetro que é a SAS para enviar.

> [!NOTE]
> É importante que a ligação é criada com **mecanismo de autenticação do SASL definido como anónimo** (e não o predefinido simples com o nome de utilizador e palavra-passe utilizada quando não precisar de enviar o token SAS).
>
>

Em seguida, o publicador cria duas ligações AMQP para enviar o token SAS e receber a resposta (o resultado de validação do token) do serviço.

A mensagem AMQP contém um conjunto de propriedades e mais informações do que uma simple mensagem. O token SAS é o corpo da mensagem (com seu construtor). O **"ReplyTo"** propriedade está definida como o nome do nó para receber o resultado de validação no link de destinatário (pode alterar o nome se desejar, e ele será criado dinamicamente pelo serviço). As últimas três propriedades de aplicação/personalizado são utilizadas pelo serviço para indicar que tipo de operação precisa ser executado. Conforme descrito pela especificação de rascunho CBS, têm de ser o **nome da operação** ("put-token"), o **tipo de token** (neste caso, uma `servicebus.windows.net:sastoken`) e o **"nome" o público-alvo** ao qual o token se aplica (a entidade inteira).

Depois de enviar o token SAS na ligação de remetente, o publicador tem de ler a resposta no link de recetor. A resposta é uma mensagem AMQP simple com uma propriedade de aplicativo chamada **"código de estado"** que pode conter os mesmos valores que um código de estado HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Direitos necessários para operações de Service Bus

A tabela seguinte mostra os direitos de acesso necessários para várias operações em recursos do Service Bus.

| Operação | Afirmação necessária | Âmbito de afirmação |
| --- | --- | --- |
| **Namespace** | | |
| Configurar a regra de autorização num espaço de nomes |Gerir |Qualquer endereço de espaço de nomes |
| **Registo de serviço** | | |
| Enumerar políticas privada |Gerir |Qualquer endereço de espaço de nomes |
| Começar a escutar num espaço de nomes |Vigiar |Qualquer endereço de espaço de nomes |
| Enviar mensagens para um serviço de escuta num espaço de nomes |Enviar |Qualquer endereço de espaço de nomes |
| **fila** | | |
| Criar uma fila |Gerir |Qualquer endereço de espaço de nomes |
| Eliminar uma fila |Gerir |Qualquer endereço de fila válido |
| Enumerar as filas |Gerir |/$Resources/Queues |
| Obter a descrição da fila |Gerir |Qualquer endereço de fila válido |
| Configurar regra de autorização de uma fila |Gerir |Qualquer endereço de fila válido |
| Enviar para o para a fila |Enviar |Qualquer endereço de fila válido |
| Receber mensagens de uma fila |Vigiar |Qualquer endereço de fila válido |
| Abandonar ou concluir mensagens depois de receber a mensagem no modo de bloqueio de pré-visualização |Vigiar |Qualquer endereço de fila válido |
| Diferir a uma mensagem para uma recuperação posterior |Vigiar |Qualquer endereço de fila válido |
| Uma mensagem de mensagens não entregues |Vigiar |Qualquer endereço de fila válido |
| Obter o Estado associado a uma sessão de fila de mensagens |Vigiar |Qualquer endereço de fila válido |
| Definir o Estado associado a uma sessão de fila de mensagens |Vigiar |Qualquer endereço de fila válido |
| Agendar uma mensagem para entrega posterior; Por exemplo, [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Vigiar | Qualquer endereço de fila válido
| **Tópico** | | |
| Criar um tópico |Gerir |Qualquer endereço de espaço de nomes |
| Eliminar um tópico |Gerir |Qualquer endereço de tópico válido |
| Enumerar tópicos |Gerir |Recursos de $/ tópicos |
| Obter a descrição de tópico |Gerir |Qualquer endereço de tópico válido |
| Configurar a regra de autorização para um tópico |Gerir |Qualquer endereço de tópico válido |
| Enviar para o tópico |Enviar |Qualquer endereço de tópico válido |
| **Subscrição** | | |
| Criar uma subscrição |Gerir |Qualquer endereço de espaço de nomes |
| Eliminar subscrição |Gerir |../myTopic/subscriptions/mySubscription |
| Enumerar subscrições |Gerir |../myTopic/subscrições |
| Obter a descrição da subscrição |Gerir |../myTopic/subscriptions/mySubscription |
| Abandonar ou concluir mensagens depois de receber a mensagem no modo de bloqueio de pré-visualização |Vigiar |../myTopic/subscriptions/mySubscription |
| Diferir a uma mensagem para uma recuperação posterior |Vigiar |../myTopic/subscriptions/mySubscription |
| Uma mensagem de mensagens não entregues |Vigiar |../myTopic/subscriptions/mySubscription |
| Obter o Estado associado a uma sessão de tópico |Vigiar |../myTopic/subscriptions/mySubscription |
| Definir o Estado associado a uma sessão de tópico |Vigiar |../myTopic/subscriptions/mySubscription |
| **Regras** | | |
| Criar uma regra |Gerir |../myTopic/subscriptions/mySubscription |
| Eliminar uma regra |Gerir |../myTopic/subscriptions/mySubscription |
| Enumerar regras |Gerir ou ouvir |../myTopic/subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Como utilizar as filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
