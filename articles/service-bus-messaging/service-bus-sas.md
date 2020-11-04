---
title: Controlo de acesso a autocarros da Azure Service com assinaturas de acesso partilhado
description: Visão geral do controlo de acesso do Service Bus utilizando a visão geral do Shared Access Signatures, detalhes sobre a autorização da SAS com a Azure Service Bus.
ms.topic: article
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f71320613682f7d4b9f3b706845e68f581b3dc10
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339415"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Controlo de acesso de ônibus de serviço com assinaturas de acesso compartilhado

*As Assinaturas de Acesso Partilhado* (SAS) são o principal mecanismo de segurança para as mensagens de Service Bus. Este artigo discute SAS, como funcionam e como usá-los de forma agnóstica plataforma.

A SAS guarda acesso ao Service Bus com base nas regras de autorização. Estes são configurados num espaço de nome, ou numa entidade de mensagens (retransmissão, fila ou tópico). Uma regra de autorização tem um nome, está associada a direitos específicos, e tem um par de chaves criptográficas. Utilize o nome e a chave da regra através do Service Bus SDK ou no seu próprio código para gerar um token SAS. Um cliente pode então passar o token para a Service Bus para provar a autorização para a operação solicitada.

> [!NOTE]
> A Azure Service Bus suporta autorizar o acesso a um espaço de nomes de Service Bus e suas entidades usando o Azure Ative Directory (Azure AD). Autorizar utilizadores ou aplicações utilizando o símbolo OAuth 2.0 devolvido pela Azure AD proporciona uma segurança superior e facilidade de utilização sobre assinaturas de acesso partilhada (SAS). Com o Azure AD, não há necessidade de armazenar os tokens no seu código e arriscar potenciais vulnerabilidades de segurança.
>
> A Microsoft recomenda a utilização do Azure AD com as suas aplicações Azure Service Bus, quando possível. Para obter mais informações, veja os seguintes artigos:
> - [Autenticar e autorizar uma aplicação com o Azure Ative Directory para aceder às entidades do Azure Service Bus.](authenticate-application.md)
> - [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos recursos do Azure Service Bus](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Visão geral do SAS

As Assinaturas de Acesso Partilhado são um mecanismo de autorização baseado em sinistros que utiliza fichas simples. Utilizando SAS, as chaves nunca são transmitidas no fio. As chaves são usadas para assinar criptograficamente informações que podem ser verificadas posteriormente pelo serviço. O SAS pode ser usado semelhante a um esquema de nome de utilizador e senha onde o cliente está na posse imediata de um nome de regra de autorização e uma chave correspondente. O SAS também pode ser usado semelhante a um modelo de segurança federado, onde o cliente recebe um token de acesso limitado e assinado a partir de um serviço de ficha de segurança sem nunca ter entrado na posse da chave de assinatura.

A autenticação SAS em Service Bus está configurada com [as regras](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) de autorização de acesso partilhado com direitos de acesso associados e um par de chaves criptográficas primárias e secundárias. As chaves são valores de 256 bits na representação da Base64. Pode configurar regras ao nível do espaço de nome, nos [retransmissores](../azure-relay/relay-what-is-it.md)de Service Bus, [filas](service-bus-messaging-overview.md#queues)e [tópicos.](service-bus-messaging-overview.md#topics)

O token [assinatura de acesso partilhado](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) contém o nome da regra de autorização escolhida, o URI do recurso a que deve ser acedido, um instante de expiração, e uma assinatura criptográfica HMAC-SHA256 calculada sobre estes campos utilizando a chave criptográfica primária ou secundária da regra de autorização escolhida.

## <a name="shared-access-authorization-policies"></a>Políticas de Autorização de Acesso Partilhado

Cada espaço de nome de serviço de autocarro e cada entidade de Service Bus tem uma política de autorização de acesso compartilhado composta por regras. A política ao nível do espaço de nome aplica-se a todas as entidades dentro do espaço de nome, independentemente da sua configuração de política individual.

Para cada regra de política de autorização, você decide sobre três peças de informação: **nome,** **âmbito** e **direitos.** O **nome** é só isso; um nome único dentro desse âmbito. O âmbito é bastante fácil: é o URI do recurso em questão. Para um espaço de nomes de ônibus de serviço, o âmbito é o nome de domínio totalmente qualificado (FQDN), tais como `https://<yournamespace>.servicebus.windows.net/` .

Os direitos conferidos pela regra da política podem ser uma combinação de:

* 'Enviar' - Confere o direito de enviar mensagens à entidade
* 'Ouvir' - Confere o direito de ouvir (retransmissão) ou receber (fila, subscrições) e todo o tratamento de mensagens relacionadas
* 'Gerir' - Confere o direito de gerir a topologia do espaço de nomes, incluindo a criação e eliminação de entidades

O direito 'Gerir' inclui os direitos de 'Enviar' e 'Receber'.

Uma política de espaço de nome ou de entidade pode manter até 12 regras de Autorização de Acesso Partilhado, proporcionando espaço para três conjuntos de regras, cada uma cobrindo os direitos básicos e a combinação de Enviar e Ouvir. Este limite sublinha que a loja de política SAS não se destina a ser uma loja de conta de utilizador ou de serviço. Se a sua aplicação precisar de conceder acesso ao Service Bus com base nas identidades do utilizador ou do serviço, deverá implementar um serviço de fichas de segurança que emite fichas SAS após uma verificação de autenticação e acesso.

Uma regra de autorização é atribuída a uma *chave primária* e uma *chave secundária.* Estas são chaves criptograficamente fortes. Não os perca nem os divulgue - estarão sempre disponíveis no [portal Azure][Azure portal]. Podes usar qualquer uma das chaves geradas, e podes regenera-las a qualquer momento. Se regenerar ou alterar uma chave na política, todos os tokens previamente emitidos com base nessa chave tornam-se instantaneamente inválidos. No entanto, as ligações em curso criadas com base em tais fichas continuarão a funcionar até que o token expire.

Quando cria um espaço de nomes de Service Bus, uma regra de política chamada **RootManageSharedAccessKey** é criada automaticamente para o espaço de nomes. Esta política tem permissões de Gestão para todo o espaço de nome. Recomenda-se que trate esta regra como uma conta **de raiz** administrativa e não a utilize na sua aplicação. Pode criar regras de política adicionais no **separador Configurar** para o espaço de nomes no portal, via PowerShell ou Azure CLI.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS
Quando utiliza assinaturas de acesso partilhado nas suas aplicações, tem de estar ciente de dois riscos potenciais:

- Se um SAS for vazado, pode ser usado por qualquer pessoa que o obtenha, o que pode potencialmente comprometer os seus recursos de Event Hubs.
- Se um SAS fornecido a uma aplicação de cliente expirar e a aplicação não conseguir recuperar um novo SAS do seu serviço, então a funcionalidade da aplicação pode ser prejudicada.

As seguintes recomendações para a utilização de assinaturas de acesso partilhado podem ajudar a mitigar estes riscos:

- **Ter os clientes a renovar automaticamente o SAS se necessário** : Os clientes devem renovar o SAS bem antes de expirar, para dar tempo para as retrações se o serviço de fornecimento do SAS estiver indisponível. Se o seu SAS for destinado a ser utilizado para um pequeno número de operações imediatas e de curta duração que se espera que sejam concluídas dentro do período de validade, então pode ser desnecessário, uma vez que não se espera que o SAS seja renovado. No entanto, se tiver cliente que está a fazer pedidos rotineiramente através do SAS, então a possibilidade de expiração entra em jogo. A principal consideração é equilibrar a necessidade de o SAS ser de curta duração (como anteriormente declarado) com a necessidade de garantir que o cliente está a solicitar a renovação suficientemente cedo (para evitar perturbações devido à expiração do SAS antes de uma renovação bem sucedida).
- **Tenha cuidado com a hora de início do SAS** : Se definir a hora de início para o SAS **até agora** , então devido a distorção do relógio (diferenças no tempo atual de acordo com diferentes máquinas), as avarias podem ser observadas intermitentemente durante os primeiros minutos. Em geral, desema esta hora de início para pelo menos 15 minutos no passado. Ou não o desemoquila, o que o tornará válido imediatamente em todos os casos. O mesmo se aplica, em geral, também ao prazo de validade. Lembre-se de que pode observadorar até 15 minutos de relógio em qualquer direção em qualquer pedido. 
- **Seja específico com o recurso a ser acedido:** Uma boa prática de segurança é proporcionar ao utilizador os privilégios mínimos exigidos. Se um utilizador apenas precisar de ter acesso lido a uma única entidade, então conceda-lhes que leia o acesso a essa entidade única e não leia/escreva/apague o acesso a todas as entidades. Também ajuda a diminuir os danos se um SAS estiver comprometido porque o SAS tem menos poder nas mãos de um intruso.
- **Nem sempre utilize SAS** : Por vezes, os riscos associados a uma determinada operação contra os seus Centros de Eventos superam os benefícios da SAS. Para tais operações, crie um serviço de nível médio que escreva para os seus Centros de Eventos após validação, autenticação e auditoria às regras de negócio.
- **Utilize sempre HTTPs** : Utilize sempre https para criar ou distribuir um SAS. Se um SAS for ultrapassado http e intercetado, um intruso que executa uma ligação homem-no-meio é capaz de ler o SAS e, em seguida, usá-lo como o utilizador pretendido poderia ter, potencialmente comprometendo dados sensíveis ou permitindo a corrupção de dados pelo utilizador malicioso.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuração para autenticação de assinatura de acesso partilhado

Pode configurar a regra [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em espaços de nomes, filas ou tópicos do Service Bus. Configurar uma [regra SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) numa subscrição de Service Bus não é suportado atualmente, mas você pode usar regras configuradas em um espaço de nome ou tópico para garantir o acesso a subscrições. Para obter uma amostra de trabalho que ilustra este procedimento, consulte a [autenticação de Assinatura de Acesso Partilhado (SAS) com a amostra de Assinaturas de Autocarros](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) de Serviço.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Neste número, as regras de autorização *gerenciais,* *sendRuleNS* e *listenRuleNS* aplicam-se tanto à fila Q1 como ao tópico T1, enquanto *o listenRuleQ* e *o sendRuleQ* aplicam-se apenas à fila Q1 e *ao sendRuleT* aplica-se apenas ao tópico T1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um token de assinatura de acesso partilhado

Qualquer cliente que tenha acesso ao nome de uma regra de autorização e uma das suas chaves de assinatura pode gerar um token SAS. O token é gerado através da criação de uma cadeia no seguinte formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** - Token expiração instantânea. Inteiro refletindo segundos desde a época `00:00:00 UTC` em 1 de janeiro de 1970 (época UNIX) quando o token expira.
* **`skn`** - Nome da regra de autorização.
* **`sr`** - URI do recurso a ser acedido.
* **`sig`** A assinatura.

O `signature-string` hash SHA-256 é calculado sobre o recurso URI **(âmbito** descrito na secção anterior) e a representação de cadeia do instantâneo de expiração do símbolo, separado por LF.

O cálculo de haxixe é semelhante ao seguinte código pseudo e devolve um valor de haxixe de 256 bits/32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

O token contém os valores não hashed para que o destinatário possa recompensar o haxixe com os mesmos parâmetros, verificando se o emitente está na posse de uma chave de assinatura válida.

O recurso URI é o URI completo do recurso Service Bus a que o acesso é reclamado. Por exemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>` , isto é, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3` . 

**O URI deve estar [codificado por cento.](/dotnet/api/system.web.httputility.urlencode)**

A regra de autorização de acesso partilhado utilizada para a assinatura deve ser configurada na entidade especificada por este URI, ou por um dos seus pais hierárquicos. Por exemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos pré-fixados com o `<resourceURI>` usado no `signature-string` .

> [!NOTE]
> Por exemplo, gerar um token SAS utilizando diferentes linguagens de programação, consulte [Generate SAS token](/rest/api/eventhub/generate-sas-token). 

## <a name="regenerating-keys"></a>Chaves regeneradoras

Recomenda-se que regenerar periodicamente as teclas utilizadas no objeto [SharedAccessAuthorizationRule.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) Existem as ranhuras primárias e secundárias para que possa rodar as teclas gradualmente. Se a sua aplicação utilizar geralmente a chave primária, pode copiar a chave primária para a ranhura da chave secundária e, só depois, regenerar a chave primária. O novo valor principal da chave pode então ser configurado nas aplicações do cliente, que têm acesso continuado usando a antiga chave primária na ranhura secundária. Uma vez atualizados todos os clientes, pode regenerar a chave secundária para finalmente retirar a velha chave primária.

Se souber ou suspeitar que uma chave está comprometida e tiver de revogar as chaves, pode regenerar tanto a [Chave Primária](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) como a [SegundaKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) de um [SharedAccessAuthorizationRule,](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)substituindo-as por novas teclas. Este procedimento invalida todos os tokens assinados com as chaves antigas.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autenticação de assinatura de acesso compartilhado com ônibus de serviço

Os cenários descritos da seguinte forma incluem configuração de regras de autorização, geração de fichas SAS e autorização do cliente.

Para obter uma amostra completa de uma aplicação Service Bus que ilustra a configuração e utiliza a autorização SAS, consulte [a autenticação assinatura de acesso partilhado com o Service Bus.](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8) Uma amostra relacionada que ilustra a utilização de regras de autorização SAS configuradas em espaços de nome ou tópicos para garantir subscrições de Service Bus está disponível aqui: [Usando a autenticação de Assinatura de Acesso Partilhado (SAS) com assinaturas de autocarros de serviço.](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Aceder às regras de Autorização de Acesso Partilhado a uma entidade

Com bibliotecas de quadros de Service Bus .NET, pode aceder a um objeto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurado numa fila de autocarros de serviço ou tópico através da coleção [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) na correspondente [Descrição](/dotnet/api/microsoft.servicebus.messaging.queuedescription) de Filas ou [Descrição de Tópicos.](/dotnet/api/microsoft.servicebus.messaging.topicdescription)

O código que se segue mostra como adicionar regras de autorização para uma fila.

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

## <a name="use-shared-access-signature-authorization"></a>Utilizar a autorização de assinatura de acesso partilhado

As aplicações que utilizam o Azure .NET SDK com as bibliotecas Service Bus .NET podem utilizar a autorização SAS através da classe [SharedAccessSignatureTokenProvider.](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) O código que se segue ilustra a utilização do fornecedor de fichas para enviar mensagens para uma fila de autocarros de serviço. Alternativa ao uso mostrado aqui, você também pode passar um token previamente emitido para o método de fábrica do fornecedor de fichas.

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

Também pode utilizar o fornecedor de fichas diretamente para emitir fichas para passar para outros clientes.

As cordas de conexão podem incluir um nome de regra *(SharedAccessKeyName* ) e a chave de *regras (SharedAccessKey* ) ou um token emitido anteriormente *(SharedAccessSignature).* Quando estes estão presentes na cadeia de ligação passada a qualquer construtor ou método de fábrica que aceite uma cadeia de ligação, o fornecedor de fichas SAS é automaticamente criado e povoado.

Tenha em atenção que para utilizar a autorização SAS com relés Service Bus, pode utilizar as teclas SAS configuradas no espaço de nomes do Service Bus. Se criar explicitamente um relé no espaço de nomes[(NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) with a [RelayDescription),](/dotnet/api/microsoft.servicebus.messaging.relaydescription)pode definir as regras SAS apenas para esse relé. Para utilizar a autorização SAS com subscrições de Service Bus, pode utilizar as teclas SAS configuradas num espaço de nomes de Service Bus ou num tópico.

## <a name="use-the-shared-access-signature-at-http-level"></a>Utilize a Assinatura de Acesso Partilhado (a nível HTTP)

Agora que sabe como criar Assinaturas de Acesso Partilhado para qualquer entidade no Service Bus, está pronto para executar um HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Lembre-se, isto funciona para tudo. Pode criar SAS para uma fila, tópico ou subscrição.

Se você der a um remetente ou cliente um sinal SAS, eles não têm a chave diretamente, e eles não podem reverter o haxixe para obtê-lo. Como tal, tens controlo sobre o que eles podem aceder, e por quanto tempo. Uma coisa importante a lembrar é que se alterar a chave principal da política, quaisquer Assinaturas de Acesso Partilhada criadas a partir dela são invalidadas.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Utilize a Assinatura de Acesso Partilhado (ao nível amQP)

Na secção anterior, viu como utilizar o token SAS com um pedido HTTP POST para envio de dados para o Service Bus. Como sabe, pode aceder ao Service Bus utilizando o Protocolo de Fila de Mensagens Avançadas (AMQP) que é o protocolo preferido a utilizar por razões de desempenho, em muitos cenários. O uso de ficha sas com AMQP é descrito no documento [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está em funcionamento desde 2013 mas é apoiado pelo Azure hoje.

Antes de começar a enviar dados para a Service Bus, a editora deve enviar o token SAS dentro de uma mensagem AMQP para um nó AMQP bem definido chamado **$cbs** (pode vê-lo como uma fila "especial" usada pelo serviço para adquirir e validar todos os tokens SAS). O editor deve especificar o campo **AnswerTo** dentro da mensagem AMQP; este é o nó em que o serviço responde ao editor com o resultado da validação simbólica (um simples padrão de pedido/resposta entre editor e serviço). Este nó de resposta é criado "on the fly", falando sobre a "criação dinâmica do nó remoto" como descrito pela especificação AMQP 1.0. Depois de verificar se o token SAS é válido, o editor pode avançar e começar a enviar dados para o serviço.

Os passos a seguir mostram como enviar o token SAS com o protocolo AMQP utilizando a biblioteca [AMQP.NET Lite.](https://github.com/Azure/amqpnetlite) Isto é útil se não puder utilizar o serviço oficial SDK (por exemplo, no WinRT, .NET Compact Framework, .NET Micro Framework e Mono) que se desenvolve em C \# . É claro que esta biblioteca é útil para ajudar a entender como a segurança baseada em sinistros funciona ao nível amQP, como você viu como funciona ao nível HTTP (com um pedido HTTP POST e o token SAS enviado dentro do cabeçalho "Autorização"). Se não precisar de um conhecimento tão profundo sobre amQP, pode utilizar o Serviço Oficial de AutocarroS SDK com aplicações .NET Framework, que o farão por si.

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

O `PutCbsToken()` método recebe a *ligação* (instância de classe de ligação AMQP conforme fornecido pela [biblioteca AMQP .NET Lite)](https://github.com/Azure/amqpnetlite)que representa a ligação TCP ao serviço e o parâmetro *sasToken* que é o símbolo SAS a enviar.

> [!NOTE]
> É importante que a ligação seja criada com o mecanismo de **autenticação SASL definido para ANONYMOUS** (e não o PLAIN padrão com nome de utilizador e palavra-passe utilizados quando não precisa de enviar o token SAS).
>
>

Em seguida, a editora cria dois links AMQP para o envio do token SAS e receber a resposta (o resultado da validação simbólica) do serviço.

A mensagem AMQP contém um conjunto de propriedades, e mais informações do que uma mensagem simples. O símbolo SAS é o corpo da mensagem (utilizando o seu construtor). A propriedade **"AnswerTo"** é definida como o nome do nó para receber o resultado de validação no link recetor (pode alterar o seu nome se quiser, e será criado dinamicamente pelo serviço). As últimas três propriedades de aplicação/personalizados são utilizadas pelo serviço para indicar que tipo de operação tem de executar. Conforme descrito pelo projeto de especificação da CBS, devem ser o nome de **operação** ("put-token"), o **tipo de símbolo** (neste caso, `servicebus.windows.net:sastoken` a), e o **"nome" da audiência a** que se aplica o símbolo (toda a entidade).

Depois de enviar o token SAS no link remetente, o editor deve ler a resposta no link do recetor. A resposta é uma simples mensagem AMQP com uma propriedade de aplicação chamada **"código de estado"** que pode conter os mesmos valores que um código de estado HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Direitos necessários para as operações de Service Bus

A tabela a seguir mostra os direitos de acesso necessários para várias operações nos recursos da Service Bus.

| Operação | Reclamação Necessária | Âmbito de reclamação |
| --- | --- | --- |
| **Espaço de Nomes** | | |
| Regra de autorização de configuração num espaço de nome |Gerir |Qualquer endereço de espaço de nome |
| **Registo de Serviços** | | |
| Enumerar Políticas Privadas |Gerir |Qualquer endereço de espaço de nome |
| Comece a ouvir em um espaço de nome |Escutar |Qualquer endereço de espaço de nome |
| Enviar mensagens a um ouvinte num espaço de nome |Enviar |Qualquer endereço de espaço de nome |
| **Fila** | | |
| Criar uma fila |Gerir |Qualquer endereço de espaço de nome |
| Eliminar uma fila |Gerir |Qualquer endereço de fila válido |
| Enumerar filas |Gerir |/$Resources/Filas |
| Obtenha a descrição da fila |Gerir |Qualquer endereço de fila válido |
| Regra de autorização de configuração para uma fila |Gerir |Qualquer endereço de fila válido |
| Enviar para a fila |Enviar |Qualquer endereço de fila válido |
| Receber mensagens de uma fila |Escutar |Qualquer endereço de fila válido |
| Abandone ou complete mensagens depois de receber a mensagem em modo de bloqueio de espreitar |Escutar |Qualquer endereço de fila válido |
| Adiar uma mensagem para posterior recuperação |Escutar |Qualquer endereço de fila válido |
| Deadletter uma mensagem |Escutar |Qualquer endereço de fila válido |
| Associem o estado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| Definir o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| Agende uma mensagem para posterior entrega; por exemplo, [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Escutar | Qualquer endereço de fila válido
| **Tópico** | | |
| Criar um tópico |Gerir |Qualquer endereço de espaço de nome |
| Excluir um tópico |Gerir |Qualquer endereço de tópico válido |
| Enumerar tópicos |Gerir |/$Resources/Tópicos |
| Obtenha a descrição do tópico |Gerir |Qualquer endereço de tópico válido |
| Regra de autorização de configuração para um tópico |Gerir |Qualquer endereço de tópico válido |
| Enviar para o tópico |Enviar |Qualquer endereço de tópico válido |
| **Subscrição** | | |
| Criar uma subscrição |Gerir |Qualquer endereço de espaço de nome |
| Eliminar subscrição |Gerir |.. /myTopic/Subscrições/mySubscription |
| Assinaturas enumeras |Gerir |.. /myTopic/Subscrições |
| Obtenha a descrição da subscrição |Gerir |.. /myTopic/Subscrições/mySubscription |
| Abandone ou complete mensagens depois de receber a mensagem em modo de bloqueio de espreitar |Escutar |.. /myTopic/Subscrições/mySubscription |
| Adiar uma mensagem para posterior recuperação |Escutar |.. /myTopic/Subscrições/mySubscription |
| Deadletter uma mensagem |Escutar |.. /myTopic/Subscrições/mySubscription |
| Associem o estado a uma sessão de tópicos |Escutar |.. /myTopic/Subscrições/mySubscription |
| Definir o estado associado a uma sessão de tópico |Escutar |.. /myTopic/Subscrições/mySubscription |
| **Regras** | | |
| Criar uma regra |Gerir |.. /myTopic/Subscrições/mySubscription |
| Eliminar uma regra |Gerir |.. /myTopic/Subscrições/mySubscription |
| Enumerar regras |Gerir ou Ouvir |.. /myTopic/Subscrições/mySubscription/Regras

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Como utilizar as filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com