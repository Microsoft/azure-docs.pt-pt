---
title: Ligue para as operações da API com autorização de Chave Partilhada
titleSuffix: Discover how to call Azure Storage REST API operations with Shared Key authorization. Get detailed information about each step of the sample operation.
description: Utilize a API A API do Azure Storage REST para fazer um pedido de armazenamento blob utilizando a autorização da Chave Partilhada.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: ozge
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f569fdac19c4f765828d24f4d6615fdd7bafef8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010907"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Ligue para as operações da API com autorização de Chave Partilhada

Este artigo mostra-lhe como chamar apis de repouso de armazenamento Azure, incluindo como formar o cabeçalho de Autorização. Está escrito do ponto de vista de um desenvolvedor que não sabe nada sobre REST e não faz ideia de como fazer uma chamada REST. Depois de aprender a chamar uma operação REST, pode aproveitar este conhecimento para utilizar quaisquer outras operações de REST de armazenamento Azure.

## <a name="prerequisites"></a>Pré-requisitos

O pedido de amostra lista os recipientes blob para uma conta de armazenamento. Para experimentar o código neste artigo, precisa dos seguintes itens:

- Instale [o Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) com a carga de trabalho **de desenvolvimento do Azure.**

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento para fins gerais. Se ainda não tiver uma conta de armazenamento, consulte [criar uma conta de armazenamento.](storage-account-create.md)

- O exemplo deste artigo mostra como listar os contentores numa conta de armazenamento. Para ver a saída, adicione alguns recipientes para o armazenamento de bolhas na conta de armazenamento antes de começar.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A aplicação da amostra é uma aplicação de consola escrita em C#.

Use [git](https://git-scm.com/) para descarregar uma cópia da aplicação para o seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Este comando clona o repositório para a sua pasta local do git. Para abrir a solução Visual Studio, procure a pasta storage-dotnet-rest-api-com-auth, abra-a e clique duas vezes no StorageRestApiAuth.sln.

## <a name="about-rest"></a>Sobre o REST

REST significa *transferência de estado de representação*. Para obter uma definição específica, confira a [Wikipédia.](https://en.wikipedia.org/wiki/Representational_state_transfer)

REST é uma arquitetura que lhe permite interagir com um serviço através de um protocolo de internet, como HTTP/HTTPS. Rest é independente do software em execução no servidor ou no cliente. A API REST pode ser chamada a partir de qualquer plataforma que suporte HTTP/HTTPS. Pode escrever uma aplicação que seja executado num Mac, Windows, Linux, um telemóvel ou tablet Android, iPhone, iPod ou web site, e utilizar a mesma API REST para todas essas plataformas.

Uma chamada para a API REST consiste num pedido, que é feito pelo cliente, e uma resposta, que é devolvida pelo serviço. No pedido, envia um URL com informações sobre qual a operação que pretende ligar, o recurso a atuar, quaisquer parâmetros de consulta e cabeçalhos, e dependendo da operação que foi chamada, uma carga útil de dados. A resposta do serviço inclui um código de estado, um conjunto de cabeçalhos de resposta, e dependendo da operação que foi chamada, uma carga útil de dados.

## <a name="about-the-sample-application"></a>Sobre a aplicação da amostra

O pedido de amostra lista os contentores numa conta de armazenamento. Uma vez que você entenda como a informação na documentação da API REST se relaciona com o seu código real, outras chamadas REST são mais fáceis de descobrir.

Se olhar para a [API do Blob Service REST,](/rest/api/storageservices/Blob-Service-REST-API)veja todas as operações que pode realizar no armazenamento de bolhas. As bibliotecas de clientes de armazenamento são invólucros em torno das APIs REST – facilitam o acesso ao armazenamento sem utilizar diretamente as APIs REST. Mas, como referido acima, às vezes você quer usar a API REST em vez de uma biblioteca de clientes de armazenamento.

## <a name="list-containers-operation"></a>Operação de contentores de lista

Reveja a referência para a operação [ListContainers.](/rest/api/storageservices/List-Containers2) Esta informação irá ajudá-lo a entender de onde vêm alguns dos campos no pedido e resposta.

**Método de Pedido**: GET. Este verbo é o método HTTP que especifica como propriedade do objeto pedido. Outros valores para este verbo incluem HEAD, PUT e DELETE, dependendo da API que está a chamar.

**Pedido URI**: `https://myaccount.blob.core.windows.net/?comp=list` .  O pedido URI é criado a partir do ponto final da conta de armazenamento blob `https://myaccount.blob.core.windows.net` e da cadeia de recursos `/?comp=list` .

[Parâmetros URI](/rest/api/storageservices/List-Containers2#uri-parameters): Existem parâmetros de consulta adicionais que pode utilizar ao chamar ListContainers. Alguns destes parâmetros são *tempo limite* para a chamada (em segundos) e *prefixo*, que é usado para filtragem.

Outro parâmetro útil são *os maxresults:* se houver mais contentores disponíveis do que este valor, o corpo de resposta conterá um elemento *NextMarker* que indica que o próximo recipiente deve regressar no próximo pedido. Para utilizar esta função, fornece o valor *NextMarker* como *parâmetro* marcador no URI quando então fizer o próximo pedido. Ao utilizar esta função, é análogo ao chamamento através dos resultados.

Para utilizar parâmetros adicionais, apeci-los à cadeia de recursos com o valor, como este exemplo:

```
/?comp=list&timeout=60&maxresults=100
```

[Pedido Cabeçalhos](/rest/api/storageservices/List-Containers2#request-headers)**:** Esta secção lista os cabeçalhos de pedido necessários e opcionais. São necessários três dos cabeçalhos: um cabeçalho *de autorização,* *x-ms-date* (contém o tempo UTC para o pedido) e *x-ms-versão* (especifica a versão da API REST para utilizar). Incluindo *x-ms-cliente-pedido-id* nos cabeçalhos é opcional – você pode definir o valor deste campo para qualquer coisa; é escrito para os registos de análise de armazenamento quando o registo está ativado.

[Órgão de Pedido](/rest/api/storageservices/List-Containers2#request-body)**:** Não existe qualquer órgão de pedido para ListContainers. O Corpo de Pedidos é utilizado em todas as operações PUT ao carregar bolhas, bem como SetContainerAccessPolicy, que lhe permite enviar uma lista de XML de políticas de acesso armazenadas para aplicar. As políticas de acesso armazenadas são discutidas no artigo [Usando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).

[Código de Estado](/rest/api/storageservices/List-Containers2#status-code)de Resposta **:** Informa de quaisquer códigos de estado que precisa de saber. Neste exemplo, um código de estado HTTP de 200 está ok. Para obter uma lista completa dos códigos de estado HTTP, consulte [definições do código de estado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Para ver códigos de erro específicos das APIs de repouso de armazenamento, consulte [códigos de erro de API de REST Comuns](/rest/api/storageservices/common-rest-api-error-codes)

[Cabeçalhos de resposta](/rest/api/storageservices/List-Containers2#response-headers)**:** Estes incluem o Tipo *de Conteúdo;* *x-ms-request-id*, que é o ID pedido que você passou; *versão x-ms,* que indica a versão do serviço Blob utilizado; e a *Data*, que está na UTC e diz a que horas o pedido foi feito.

[Órgão de Resposta](/rest/api/storageservices/List-Containers2#response-body): Este campo é uma estrutura XML que fornece os dados solicitados. Neste exemplo, a resposta é uma lista de contentores e suas propriedades.

## <a name="creating-the-rest-request"></a>Criação do pedido REST

Para segurança ao executar em produção, utilize sempre HTTPS em vez de HTTP. Para efeitos deste exercício, deverá utilizar HTTP para que possa visualizar os dados de pedido e resposta. Para ver as informações de pedido e resposta nas chamadas REST reais, pode descarregar [o Fiddler](https://www.telerik.com/fiddler) ou uma aplicação semelhante. Na solução Visual Studio, o nome e a chave da conta de armazenamento são codificados na classe. O método ListContainersAsyncREST passa a chave do nome da conta de armazenamento e da conta de armazenamento para os métodos utilizados para criar os vários componentes do pedido REST. Numa aplicação do mundo real, o nome e a chave da conta de armazenamento residiriam num ficheiro de configuração, variáveis ambientais ou seriam recuperados de um Cofre de Chaves Azure.

No nosso projeto de amostra, o código para criar o cabeçalho de autorização está numa classe separada. A ideia é que possas pegar em toda a turma e adicioná-la à tua própria solução e usá-la "como está". O código do cabeçalho de autorização funciona para a maioria das chamadas de API rest para o armazenamento Azure.

Para construir o pedido, que é um objeto httpRequestMessage, vá ao ListContainersAsyncREST no Programa.cs. As etapas para a construção do pedido são:

- Crie o URI para ser utilizado para chamar o serviço.
- Crie o objeto HttpRequestMessage e desfaça a carga útil. A carga é nula para listContainersAsyncREST porque não estamos a passar nada.
- Adicione os cabeçalhos de pedido para x-ms-date e versão x-ms-ms.
- Pegue o cabeçalho de autorização e adicione- o.

Algumas informações básicas que precisa:

- Para listContainers, o **método** é `GET` . Este valor é definido ao instantanear o pedido.
- O **recurso** é a parte de consulta do URI que indica qual a API que está a ser chamada, pelo que o valor é `/?comp=list` . Como já foi notado, o recurso está na página de documentação de referência que mostra as informações sobre a [API dos ListContainers](/rest/api/storageservices/List-Containers2).
- O URI é construído criando o ponto final de serviço Blob para essa conta de armazenamento e concatenando o recurso. O valor para **pedido URI** acaba por ser `http://contosorest.blob.core.windows.net/?comp=list` .
- Para ListContainers, **o requestBody** é nulo e não há **cabeçalhos extras**.

ApIs diferentes podem ter outros parâmetros para passar, tais como *se OAtch*. Um exemplo de onde pode usar se OMatch é quando chama PutBlob. Nesse caso, define seSatch para um eTag, e só atualiza a bolha se o eTag que fornece corresponder ao eTag atual na bolha. Se alguém tiver atualizado a bolha desde que recuperou o eTag, a sua mudança não será ultrapassada.

Primeiro, definir o `uri` e o `payload` .

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Em seguida, instantânea o pedido, definindo o método `GET` e fornecendo o URI.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adicione os cabeçalhos de pedido para `x-ms-date` e `x-ms-version` . Este lugar no código é também onde você adiciona quaisquer cabeçalhos de pedido adicionais necessários para a chamada. Neste exemplo, não há cabeçalhos adicionais. Um exemplo de uma API que passa em cabeçalhos extras é a operação set Container ACL. Esta chamada API adiciona um cabeçalho chamado "x-ms-blob-acesso público" e o valor para o nível de acesso.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Ligue para o método que cria o cabeçalho de autorização e adicione-o aos cabeçalhos de pedido. Verá como criar o cabeçalho de autorização mais tarde no artigo. O nome do método é GetAuthorizationHeader, que pode ver neste corte de código:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Neste ponto, `httpRequestMessage` contém o pedido REST completo com os cabeçalhos de autorização.

## <a name="send-the-request"></a>Enviar o pedido

Agora que construiu o pedido, pode ligar para o método SendAsync para enviá-lo para o Azure Storage. Verifique se o valor do código de estado de resposta é de 200, o que significa que a operação foi bem sucedida. Em seguida, analise a resposta. Neste caso, obtém-se uma lista de contentores XML. Vamos olhar para o código para ligar para o método GetRESTRequest para criar o pedido, executar o pedido e, em seguida, examinar a resposta para a lista de contentores.

```csharp
    // Send the request.
    using (HttpResponseMessage httpResponseMessage =
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200),
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Se executar um farejador de rede, como [o Fiddler,](https://www.telerik.com/fiddler) ao escoar a chamada para SendAsync, poderá ver as informações de pedido e resposta. Vamos dar uma vista de olhos. O nome da conta de armazenamento é *contosorest.*

**Pedido:**

```
GET /?comp=list HTTP/1.1
```

**Pedido cabeçalhos:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Código de estado e cabeçalhos de resposta devolvidos após a execução:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Corpo de resposta (XML):** Para a operação dos Contentores listas, isto mostra a lista de contentores e suas propriedades.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Agora que entende como criar o pedido, ligar para o serviço e analisar os resultados, vamos ver como criar o cabeçalho de autorização. Criar esse cabeçalho é complicado, mas a boa notícia é que uma vez que você tem o código funcionando, ele funciona para todas as APIs de Serviço de Armazenamento REST.

## <a name="creating-the-authorization-header"></a>Criação do cabeçalho de autorização

> [!TIP]
> O Azure Storage suporta agora a integração do Azure Ative Directory (Azure AD) para bolhas e filas. A Azure AD oferece uma experiência muito mais simples para autorizar um pedido ao Azure Storage. Para obter mais informações sobre a utilização da Azure AD para autorizar operações REST, consulte [Autorizar com o Azure Ative Directory](/rest/api/storageservices/authorize-with-azure-active-directory). Para obter uma visão geral da integração da AD Azure com o Azure Storage, consulte [o Acesso autenticado ao Azure Storage utilizando o Azure Ative Directory](storage-auth-aad.md).

Existe um artigo que explica conceptualmente (sem código) como [autorizar pedidos ao Azure Storage](/rest/api/storageservices/authorize-requests-to-azure-storage).

Vamos destilar o artigo para baixo para exatamente necessário e mostrar o código.

Primeiro, utilize a autorização da Chave Partilhada. O formato do cabeçalho de autorização é assim:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

O campo de assinatura é um Código de Autenticação de Mensagens (HMAC) baseado em Hash, criado a partir do pedido e calculado usando o algoritmo SHA256, codificado depois através da codificação Base64. Tem aquilo? (Aguenta aí, ainda nem ouviste a palavra *canonicalizada.)*

Este corte de código mostra o formato da cadeia de assinaturas Da Chave Partilhada:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

A maioria destes campos raramente são usados. Para o armazenamento blob, especifique VERB, md5, comprimento de conteúdo, Cabeçalhos Canonizados e Recursos Canonizados. Pode deixar os outros em branco (mas coloque-os para `\n` saber que estão em branco).

O que são CanonicalizedHeaders e CanonicalizedResource? Boa pergunta. Na verdade, o que significa canonizar? O Microsoft Word nem sequer o reconhece como uma palavra. Eis o que [a Wikipédia diz sobre a canonização](https://en.wikipedia.org/wiki/Canonicalization): Na ciência da *computação, a canonização (por vezes a normalização ou a normalização) é um processo de conversão de dados que tem mais do que uma representação possível numa forma "normal", "normal", ou canónica.* Em termos normais, isto significa pegar na lista de itens (como cabeçalhos no caso dos Cabeçalhos Canonizados) e normalizá-los num formato necessário. Basicamente, a Microsoft decidiu um formato e precisa de o igualar.

Vamos começar com estes dois campos canónicos, porque eles são obrigados a criar o cabeçalho de autorização.

### <a name="canonicalized-headers"></a>Cabeçalhos canonizados

Para criar este valor, recupere os cabeçalhos que começam com "x-ms-ms-" e os classificam, em seguida, formatiem-nos numa série de `[key:value\n]` instâncias, concatenadas numa corda. Para este exemplo, os cabeçalhos canónicos são assim:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Aqui está o código usado para criar esta saída:

```csharp
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

### <a name="canonicalized-resource"></a>Recurso canónico

Esta parte da cadeia de assinatura representa a conta de armazenamento visada pelo pedido. Lembre-se que o Pedido URI `<http://contosorest.blob.core.windows.net/?comp=list>` é, com o nome da conta real `contosorest` (neste caso). Neste exemplo, este é devolvido:

```
/contosorest/\ncomp:list
```

Se tiver parâmetros de consulta, este exemplo também inclui esses parâmetros. Aqui está o código, que também lida com parâmetros de consulta adicionais e parâmetros de consulta com vários valores. Lembre-se que está a construir este código para trabalhar para todas as APIs do REST. Você quer incluir todas as possibilidades, mesmo que o método ListContainers não precise de todas elas.

```csharp
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Agora que as cordas canónicas estão definidas, vamos ver como criar o próprio cabeçalho de autorização. Começa por criar uma série de assinaturas de mensagens no formato stringToSign anteriormente exibido neste artigo. Este conceito é mais fácil de explicar usando comentários no código, por isso aqui está, o método final que devolve o Cabeçalho de Autorização:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + signature);
    return authHV;
}
```

Quando executam este código, a assinatura da mensagem resultante parece este exemplo:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Aqui está o valor final para o AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

O Cabeça de Autorização é o último cabeçalho colocado nos cabeçalhos de pedido antes de publicar a resposta.

Isso cobre tudo o que precisa de saber para montar uma aula com a qual pode criar um pedido para chamar as APIs de DESCANSO dos Serviços de Armazenamento.

## <a name="example-list-blobs"></a>Exemplo: Lista de bolhas

Vamos ver como alterar o código para chamar a operação List Blobs para *contentor de contentores 1*. Este código é quase idêntico ao código para a listagem de contentores, sendo as únicas diferenças o URI e como analisa a resposta.

Se olhar para a documentação de referência para [ListBlobs,](/rest/api/storageservices/List-Blobs)descobre que o método é *GET* e o RequestURI é:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

Em ListContainersAsyncREST, altere o código que define o URI para a API para ListBlobs. O nome do recipiente é **contentor-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Em seguida, onde você lida com a resposta, altere o código para procurar bolhas em vez de recipientes.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Quando executam esta amostra, obtêm resultados como os seguintes:

**Cabeçalhos canonizados:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Recurso canónico:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Assinatura de mensagem:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Cabeçalho de autorização:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Os seguintes valores são do [Violinista:](https://www.telerik.com/fiddler)

**Pedido:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Pedido cabeçalhos:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Código de estado e cabeçalhos de resposta devolvidos após a execução:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Corpo de resposta (XML):** Esta resposta XML mostra a lista de bolhas e suas propriedades.

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Resumo

Neste artigo, aprendeu a fazer um pedido ao blob storage REST API. Com o pedido, pode obter uma lista de contentores ou uma lista de bolhas num recipiente. Aprendeu a criar a assinatura de autorização para a chamada REST API e como usá-la no pedido REST. Finalmente, aprendeu a examinar a resposta.

## <a name="next-steps"></a>Passos seguintes

- [API REST de Serviço Blob](/rest/api/storageservices/blob-service-rest-api)
- [API REST de Serviço de Ficheiros](/rest/api/storageservices/file-service-rest-api)
- [API REST de Serviço de Filas](/rest/api/storageservices/queue-service-rest-api)
- [API REST de Serviço de Tabelas](/rest/api/storageservices/table-service-rest-api)
