---
title: Controlo de acesso a ônibus de serviço Azure com assinaturas de acesso partilhado
description: Visão geral do controlo de acesso de ônibus de serviço usando visão geral de Assinaturas de Acesso Partilhada, detalhes sobre a autorização da SAS com o Ônibus de Serviço Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259477"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Controlo de acesso de ônibus de serviço com assinaturas de acesso partilhado

*As Assinaturas* de Acesso Partilhado (SAS) são o principal mecanismo de segurança para as mensagens de autocarro de serviço. Este artigo discute a SAS, como funcionam e como usá-los de forma agnóstica de plataforma.

A SAS guarda o acesso ao Ônibus de serviço com base nas regras de autorização. Estes são configurados quer num espaço de nome, quer numa entidade de mensagens (retransmissão, fila ou tópico). Uma regra de autorização tem um nome, está associada a direitos específicos, e carrega um par de chaves criptográficas. Você usa o nome e a chave da regra através do Service Bus SDK ou no seu próprio código para gerar um token SAS. Um cliente pode então passar o símbolo ao Service Bus para provar a autorização para a operação solicitada.

> [!NOTE]
> A Azure Service Bus suporta a autorização de acesso a um espaço de nome de autocarro de serviço e às suas entidades utilizando o Azure Ative Directory (Azure AD). Autorizar utilizadores ou aplicações utilizando o token OAuth 2.0 devolvido pela Azure AD proporciona uma segurança e facilidade de utilização superiores sobre assinaturas de acesso partilhado (SAS). Com a AD Azure, não há necessidade de armazenar os tokens no seu código e arriscar potenciais vulnerabilidades de segurança.
>
> A Microsoft recomenda a utilização do Azure AD com as suas aplicações Azure Service Bus, quando possível. Para obter mais informações, veja os artigos seguintes:
> - [Autenticar e autorizar uma aplicação com o Azure Ative Directory para aceder às entidades azure service bus.](authenticate-application.md)
> - [Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos recursos do Azure Service Bus](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Visão geral da SAS

As Assinaturas de Acesso Partilhado são um mecanismo de autorização baseado em sinistros utilizando fichas simples. Utilizando sas, as chaves nunca são passadas no fio. As chaves são usadas para assinar criptograficamente informações que podem ser verificadas posteriormente pelo serviço. O SAS pode ser utilizado de forma semelhante a um esquema de nome de utilizador e palavra-passe quando o cliente se encontra na posse imediata de um nome de regra de autorização e de uma chave correspondente. A SAS também pode ser usada semelhante a um modelo de segurança federado, onde o cliente recebe um sinal de acesso limitado e assinado a partir de um serviço de fichas de segurança sem nunca entrar na posse da chave de assinatura.

A autenticação SAS no Bus de Serviço é configurada com as chamadas Regras de Autorização de [Acesso Partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com direitos de acesso associados, e um par de chaves criptográficas primárias e secundárias. As chaves são valores de 256 bits na representação base64. Pode configurar regras ao nível do espaço de nome, em [retransmissores](../service-bus-relay/relay-what-is-it.md)de ônibus de serviço, [filas](service-bus-messaging-overview.md#queues)e [tópicos.](service-bus-messaging-overview.md#topics)

O token [Signature de Acesso Partilhado](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) contém o nome da regra de autorização escolhida, o URI do recurso a que deve ser acedido, um instante de validade, e uma assinatura criptográfica HMAC-SHA256 computada sobre estes campos utilizando a chave criptográfica primária ou secundária da regra de autorização escolhida.

## <a name="shared-access-authorization-policies"></a>Políticas de autorização de acesso partilhado

Cada espaço de nome de ônibus de serviço e cada entidade de ônibus de serviço tem uma política de autorização de acesso partilhado composta por regras. A política a nível do espaço de nome aplica-se a todas as entidades dentro do espaço de nome, independentemente da sua configuração política individual.

Para cada regra da política de autorização, você decide sobre três peças de informação: **nome,** **âmbito,** e **direitos**. O **nome** é apenas isso; um nome único dentro desse âmbito. O âmbito é fácil o suficiente: é o URI do recurso em questão. Para um espaço de nome do Ônibus de serviço, o âmbito `https://<yournamespace>.servicebus.windows.net/`é o nome de domínio totalmente qualificado (FQDN), como .

Os direitos conferidos pela regra política podem ser uma combinação de:

* 'Enviar' - Confere o direito de enviar mensagens à entidade
* 'Ouvir' - Confere o direito de ouvir (retransmissão) ou receber (fila, subscrições) e todo o manuseamento de mensagens relacionadas
* 'Gerir' - Confere o direito de gerir a topologia do espaço de nome, incluindo a criação e a eliminar entidades

O direito de "Gerir" inclui os direitos de "Enviar" e "Receber".

Um espaço de nome ou política de entidades pode manter até 12 regras de Autorização de Acesso Partilhado, proporcionando espaço para três conjuntos de regras, cada uma abrangendo os direitos básicos e a combinação de Enviar e Ouvir. Este limite sublinha que a loja de política SAS não se destina a ser uma loja de conta de utilizador ou de serviço. Se a sua aplicação necessitar de conceder acesso ao Service Bus com base em identidades de utilizador ou de serviço, deverá implementar um serviço de token de segurança que emita tokens SAS após uma verificação de autenticação e acesso.

É atribuída uma regra de autorização a uma *chave primária* e uma *chave secundária.* Estas são chaves criptograficamente fortes. Não os perca saquendo- estarão sempre disponíveis no [portal Azure.][Azure portal] Podes usar qualquer uma das chaves geradas, e podes regenera-las a qualquer momento. Se regenerar ou alterar uma chave na política, todos os tokens anteriormente emitidos com base nessa tecla tornam-se instantaneamente inválidos. No entanto, as ligações em curso criadas com base nesses tokens continuarão a funcionar até que o token expire.

Quando cria um espaço de nome de autocarro de serviço, uma regra de política chamada **RootManageSharedAccessKey** é criada automaticamente para o espaço de nome. Esta política tem permissões de gestão para todo o espaço de nome. Recomenda-se que trate esta regra como uma conta **de raiz** administrativa e não a use na sua aplicação. Pode criar regras de política adicionais no separador **Configure** para o espaço de nome no portal, via Powershell ou Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuração para autenticação de assinatura de acesso partilhado

Pode configurar a regra [sharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em espaços de nomes de ônibus de serviço, filas ou tópicos. Configurar uma Regra de Autorização de [Acesso Partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) numa subscrição de ônibus de serviço não é suportado atualmente, mas pode usar regras configuradas num espaço de nome ou tópico para garantir o acesso a subscrições. Para obter uma amostra de trabalho que ilustra este procedimento, consulte a autenticação de Assinatura de [Acesso Partilhado (SAS) com](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) amostra de Assinaturas de Autocarros de Serviço.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Neste valor, as regras de *autorização ruleNS*de gestão , *sendRuleNS*e *listenRuleNS* aplicam-se tanto à fila Q1 como ao tópico T1, enquanto *ouveRuleQ* e *sendRuleQ* aplicam-se apenas à fila Q1 e *o envio RuleT* aplica-se apenas ao tópico T1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um símbolo de assinatura de acesso partilhado

Qualquer cliente que tenha acesso ao nome de uma regra de autorização e uma das suas chaves de assinatura pode gerar um token SAS. O símbolo é gerado através da criação de uma corda no seguinte formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`**- Token expira instantaneamente. Inteiro refletindo segundos desde `00:00:00 UTC` a época em 1 de janeiro de 1970 (época UNIX) quando o token expira.
* **`skn`**- Nome da regra da autorização.
* **`sr`**- URI do recurso a ser acedido.
* **`sig`** A assinatura.

O `signature-string` é o hash SHA-256 computado sobre o recurso URI **(âmbito** descrito na secção anterior) e a representação de cordas do instante de validade do símbolo, separado por LF.

A computação de hash parece semelhante ao seguinte código pseudo e devolve um valor hash de 256 bits/32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

O símbolo contém os valores não hashed para que o destinatário possa recomputar o hash com os mesmos parâmetros, verificando que o emitente está na posse de uma chave de assinatura válida.

O recurso URI é o URI completo do recurso Service Bus a que o acesso é reclamado. Por `http://<namespace>.servicebus.windows.net/<entityPath>` exemplo, `sb://<namespace>.servicebus.windows.net/<entityPath>`ou; isto é, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. 

**O URI deve ser codificado por [cento.](https://msdn.microsoft.com/library/4fkewx0t.aspx)**

A regra de autorização de acesso partilhado utilizada para a assinatura deve ser configurada na entidade especificada por este URI, ou por um dos seus pais hierárquicos. Por `http://contoso.servicebus.windows.net/contosoTopics/T1` exemplo, `http://contoso.servicebus.windows.net` ou no exemplo anterior.

Um token SAS é válido para `<resourceURI>` todos os `signature-string`recursos pré-fixados com o utilizado na .

## <a name="regenerating-keys"></a>Chaves regeneradoras

Recomenda-se que regenera periodicamente as teclas utilizadas no objeto [SharedAccessAuthorizationRule.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) As ranhuras-chave primárias e secundárias existem para que possa rodar as teclas gradualmente. Se a sua aplicação utilizar geralmente a chave principal, pode copiar a chave primária para a ranhura da chave secundária e, em seguida, regenerar a chave primária. O novo valor principal principal pode então ser configurado nas aplicações do cliente, que têm acesso continuado usando a antiga chave primária na ranhura secundária. Uma vez atualizados todos os clientes, pode regenerar a chave secundária para finalmente retirar a antiga chave primária.

Se souber ou suspeitar que uma chave está comprometida e tiver de revogar as teclas, pode regenerar tanto a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) como a [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) de uma Regra de Autorização de [Acesso Partilhado,](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)substituindo-as por novas teclas. Este procedimento invalida todas as fichas assinadas com as chaves antigas.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autenticação de assinatura de acesso partilhado com ônibus de serviço

Os cenários descritos como os seguintes incluem a configuração de regras de autorização, geração de tokens SAS e autorização do cliente.

Para obter uma amostra completa de uma aplicação service Bus que ilustra a configuração e utiliza a autorização SAS, consulte [a autenticação De Assinatura](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)de Acesso Partilhado com o Bus de Serviço . Uma amostra relacionada que ilustra o uso de regras de autorização SAS configuradas em espaços de nome ou tópicos para garantir subscrições de ônibus de serviço está disponível aqui: Usando a autenticação de Assinatura de [Acesso Partilhado (SAS) com assinaturas](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)de ônibus de serviço .

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Regras de Autorização de Acesso Partilhado de Acesso a uma entidade

Com o Service Bus .NET Framework libraries, pode aceder a um objeto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurado numa fila de ônibus de serviço ou tópico através da recolha [de AutorizaçõesRegras](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) na correspondente [Descrição](/dotnet/api/microsoft.servicebus.messaging.queuedescription) da Fila ou [TópicoDescrição](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

O código seguinte mostra como adicionar regras de autorização para uma fila.

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

## <a name="use-shared-access-signature-authorization"></a>Utilizar autorização de assinatura de acesso partilhado

As aplicações que utilizam o Azure .NET SDK com o Bus de Serviço .NET bibliotecas podem utilizar a autorização SAS através da classe [SharedAccessSignatureTokenProvider.](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) O código que se segue ilustra a utilização do fornecedor simbólico para enviar mensagens para uma fila de autocarros de serviço. Alternativa ao uso mostrado aqui, também pode passar um token previamente emitido para o método de fábrica do fornecedor de token.

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

Também pode utilizar o fornecedor simbólico diretamente para emitir fichas para passar para outros clientes.

As cordas de ligação podem incluir um nome de regra *(SharedAccessKeyName*) e a chave de regras *(SharedAccessKey*) ou um token anteriormente emitido *(SharedAccessSignature*). Quando estes estão presentes na cadeia de ligação passado a qualquer construtor ou método de fábrica que aceite uma cadeia de ligação, o fornecedor de fichas SAS é automaticamente criado e povoado.

Note que para utilizar a autorização SAS com retransmissores de ônibus de serviço, pode utilizar chaves SAS configuradas no espaço de nome do Ônibus de serviço. Se criar explicitamente um retransmissor no objeto[namespace (NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) com uma [Descrição relé),](/dotnet/api/microsoft.servicebus.messaging.relaydescription)pode definir as regras SAS apenas para esse retransmissor. Para utilizar a autorização SAS com subscrições de Ônibus de serviço, pode utilizar chaves SAS configuradas num espaço de nome de autocarro de serviço ou num tópico.

## <a name="use-the-shared-access-signature-at-http-level"></a>Utilize a Assinatura de Acesso Partilhado (ao nível HTTP)

Agora que sabe como criar Assinaturas de Acesso Partilhado para qualquer entidade em Service Bus, está pronto para realizar um POST HTTP:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Lembre-se, isto funciona para tudo. Pode criar SAS para uma fila, tópico ou subscrição.

Se você dá a um remetente ou cliente um token SAS, eles não têm a chave diretamente, e eles não podem reverter o hash para obtê-lo. Como tal, tens controlo sobre o que eles podem aceder, e por quanto tempo. Uma coisa importante a lembrar é que se mudar a chave primária da política, quaisquer Assinaturas de Acesso Partilhada criadas a partir dela são invalidadas.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Utilize a Assinatura de Acesso Partilhado (ao nível AMQP)

Na secção anterior, viu como utilizar o token SAS com um pedido HTTP POST para o envio de dados para o Ônibus de Serviço. Como sabe, pode aceder ao Service Bus utilizando o Protocolo de Fila de Mensagens Avançadas (AMQP) que é o protocolo preferido para usar por razões de desempenho, em muitos cenários. O uso simbólico da SAS com AMQP é descrito no documento [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está em projeto de trabalho desde 2013, mas bem apoiado pela Azure hoje.

Antes de começar a enviar dados para o Service Bus, a editora deve enviar o token SAS dentro de uma mensagem AMQP para um nó AMQP bem definido chamado **$cbs** (pode vê-lo como uma fila "especial" usada pelo serviço para adquirir e validar todos os tokens SAS). O editor deve especificar o campo **AnswerTo** dentro da mensagem AMQP; este é o nó em que o serviço responde à editora com o resultado da validação simbólica (um simples padrão de pedido/resposta entre o editor e o serviço). Este nó de resposta é criado "on the fly", falando sobre a "criação dinâmica do nó remoto", conforme descrito pela especificação AMQP 1.0. Depois de verificar se o token SAS é válido, a editora pode avançar e começar a enviar dados para o serviço.

Os seguintes passos mostram como enviar o token SAS com o protocolo AMQP usando a biblioteca [AMQP.NET Lite.](https://github.com/Azure/amqpnetlite) Isto é útil se não puder utilizar o SDK oficial do bus de serviço (por exemplo no WinRT,\#.NET Compact Framework, .NET Micro Framework e Mono) desenvolvendo-se em C . É claro que esta biblioteca é útil para ajudar a entender como funciona a segurança baseada em sinistros ao nível da AMQP, como viu como funciona a nível HTTP (com um pedido HTTP POST e o token SAS enviado dentro do cabeçalho "Autorização"). Se não precisar de conhecimentos tão profundos sobre a AMQP, pode utilizar o Service Bus SDK oficial com aplicações .NET Framework, que o farão por si.

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

O `PutCbsToken()` método recebe a *ligação* (instância de classe de ligação AMQP fornecida pela [biblioteca AMQP .NET Lite)](https://github.com/Azure/amqpnetlite)que representa a ligação TCP ao serviço e o parâmetro *sasToken* que é o símbolo SAS a enviar.

> [!NOTE]
> É importante que a ligação seja criada com o mecanismo de **autenticação SASL definido para ANONYMOUS** (e não a PLAIN padrão com nome de utilizador e palavra-passe usado quando não precisa enviar o token SAS).
>
>

Em seguida, a editora cria dois links AMQP para o envio do token SAS e recebendo a resposta (o resultado da validação do token) do serviço.

A mensagem AMQP contém um conjunto de propriedades, e mais informações do que uma simples mensagem. O símbolo SAS é o corpo da mensagem (utilizando o seu construtor). A propriedade **"AnswerTo"** está definida no nome do nó para receber o resultado da validação no link do recetor (pode alterar o seu nome se quiser, e será criada dinamicamente pelo serviço). As três últimas propriedades de aplicação/costume são utilizadas pelo serviço para indicar que tipo de operação tem para executar. Tal como descrito pelo projeto de especificação da CBS, devem ser o nome de **funcionamento** ("put-token"), o **tipo de ficha** (neste caso, a `servicebus.windows.net:sastoken`), e o **"nome" do público** a que o símbolo se aplica (toda a entidade).

Depois de enviar o token SAS no link do remetente, o editor deve ler a resposta no link recetor. A resposta é uma simples mensagem AMQP com uma propriedade de aplicação chamada **"status-code"** que pode conter os mesmos valores que um código de estado HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Direitos necessários para operações de ônibus de serviço

A tabela que se segue mostra os direitos de acesso necessários para várias operações nos recursos da Service Bus.

| Operação | Reclamação necessária | Âmbito de reclamação |
| --- | --- | --- |
| **Espaço de nome** | | |
| Configure regra de autorização em um espaço de nome |Gerir |Qualquer endereço espaço de nome |
| **Registo de Serviços** | | |
| Enumerar políticas privadas |Gerir |Qualquer endereço espaço de nome |
| Comece a ouvir em um espaço de nome |Escutar |Qualquer endereço espaço de nome |
| Envie mensagens a um ouvinte num espaço de nome |Enviar |Qualquer endereço espaço de nome |
| **Filas** | | |
| Criar uma fila |Gerir |Qualquer endereço espaço de nome |
| Eliminar uma fila |Gerir |Qualquer endereço de fila válido |
| Enumerar filas |Gerir |/$Resources/Filas |
| Obtenha a descrição da fila |Gerir |Qualquer endereço de fila válido |
| Configure a regra de autorização para uma fila |Gerir |Qualquer endereço de fila válido |
| Enviar para a fila |Enviar |Qualquer endereço de fila válido |
| Receber mensagens de uma fila |Escutar |Qualquer endereço de fila válido |
| Abandone ou complete mensagens depois de receber a mensagem no modo de bloqueio de peek |Escutar |Qualquer endereço de fila válido |
| Adique uma mensagem para a recuperação posterior |Escutar |Qualquer endereço de fila válido |
| Deadletter uma mensagem |Escutar |Qualquer endereço de fila válido |
| Obter o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| Desmarque o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| Agende uma mensagem para posterior entrega; por exemplo, [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Escutar | Qualquer endereço de fila válido
| **Tópico** | | |
| Criar um tópico |Gerir |Qualquer endereço espaço de nome |
| Apagar um tópico |Gerir |Qualquer endereço de tópico válido |
| Temas enumerados |Gerir |/$Resources/Tópicos |
| Obtenha a descrição do tópico |Gerir |Qualquer endereço de tópico válido |
| Configurar a regra de autorização para um tópico |Gerir |Qualquer endereço de tópico válido |
| Enviar para o tópico |Enviar |Qualquer endereço de tópico válido |
| **Subscrição** | | |
| Criar uma subscrição |Gerir |Qualquer endereço espaço de nome |
| Eliminar subscrição |Gerir |.. /myTopic/Subscrições/mySubscription |
| Enumerar assinaturas |Gerir |.. /myTopic/Subscrições |
| Obtenha descrição da subscrição |Gerir |.. /myTopic/Subscrições/mySubscription |
| Abandone ou complete mensagens depois de receber a mensagem no modo de bloqueio de peek |Escutar |.. /myTopic/Subscrições/mySubscription |
| Adique uma mensagem para a recuperação posterior |Escutar |.. /myTopic/Subscrições/mySubscription |
| Deadletter uma mensagem |Escutar |.. /myTopic/Subscrições/mySubscription |
| Obter o estado associado a uma sessão de tópicos |Escutar |.. /myTopic/Subscrições/mySubscription |
| Definir o estado associado a uma sessão de tópicos |Escutar |.. /myTopic/Subscrições/mySubscription |
| **Regras** | | |
| Criar uma regra |Gerir |.. /myTopic/Subscrições/mySubscription |
| Eliminar uma regra |Gerir |.. /myTopic/Subscrições/mySubscription |
| Enumerar regras |Gerir ou ouvir |.. /myTopic/Subscrições/mySubscription/Rules

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Como utilizar as filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
