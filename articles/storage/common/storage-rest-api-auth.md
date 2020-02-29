---
title: Ligue para as operações da API REST com autorização de chave partilhada
titleSuffix: Azure Storage
description: Utilize a API de REPOUSO DE ARMAZENAMENTO Azure para fazer um pedido ao armazenamento blob utilizando a autorização de chave partilhada.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5c6125b850062450516e7fc0b19c2e0d5d6f577
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916069"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Ligue para as operações da API REST com autorização de chave partilhada

Este artigo mostra-lhe como chamar as APIs de REPOUSO DE ARMAZENAMENTO Azure, incluindo como formar o cabeçalho de Autorização. Está escrito do ponto de vista de um desenvolvedor que não sabe nada sobre o REST e não faz ideia de como fazer uma chamada de REPOUSO. Depois de aprender a chamar uma operação REST, pode aproveitar este conhecimento para utilizar quaisquer outras operações de Rest de Armazenamento Azure.

## <a name="prerequisites"></a>Pré-requisitos

A aplicação da amostra lista os recipientes blob para uma conta de armazenamento. Para experimentar o código neste artigo, precisa dos seguintes itens:

- Instale o [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) com a carga de trabalho de **desenvolvimento do Azure.**

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento de uso geral. Se ainda não tiver uma conta de armazenamento, consulte [Criar uma conta](storage-account-create.md)de armazenamento .

- O exemplo deste artigo mostra como listar os contentores numa conta de armazenamento. Para ver a saída, adicione alguns recipientes ao armazenamento de bolhas na conta de armazenamento antes de começar.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A aplicação da amostra é C#uma aplicação de consola escrita em .

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Este comando clona o repositório para a sua pasta local do git. Para abrir a solução Visual Studio, procure a pasta storage-dotnet-rest-rest-api-with-auth, abra-a e clique duas vezes no StorageRestApiAuth.sln.

## <a name="about-rest"></a>Sobre o REST

REST significa *transferência representativa do Estado.* Para uma definição específica, confira a [Wikipédia.](https://en.wikipedia.org/wiki/Representational_state_transfer)

Rest é uma arquitetura que lhe permite interagir com um serviço através de um protocolo de internet, como HTTP/HTTPS. O REST é independente do software que funciona no servidor ou no cliente. A Rest API pode ser chamada a partir de qualquer plataforma que suporte HTTP/HTTPS. Pode escrever uma aplicação que funciona num Mac, Windows, Linux, um telemóvel ou tablet Android, iPhone, iPod ou web site, e usar a mesma API REST para todas essas plataformas.

Uma chamada para a REST API consiste num pedido, que é feito pelo cliente, e uma resposta, que é devolvida pelo serviço. No pedido, envia um URL com informações sobre qual a operação que pretende ligar, o recurso a agir, quaisquer parâmetros de consulta e cabeçalhos, e dependendo da operação que foi chamada, uma carga útil de dados. A resposta do serviço inclui um código de estado, um conjunto de cabeçalhos de resposta, e dependendo da operação que foi chamada, uma carga útil de dados.

## <a name="about-the-sample-application"></a>Sobre a aplicação da amostra

A aplicação da amostra lista os recipientes numa conta de armazenamento. Uma vez que você entende como a informação na documentação REST API correlaciona com o seu código real, outras chamadas REST são mais fáceis de descobrir.

Se olhar para a API do [Serviço Blob REST,](/rest/api/storageservices/Blob-Service-REST-API)vê todas as operações que pode realizar no armazenamento de blob. As bibliotecas de clientes de armazenamento são invólucros em torno das APIs REST – facilitam o acesso ao armazenamento sem utilizar diretamente as APIs rest. Mas, como referido acima, às vezes quer usar a API REST em vez de uma biblioteca de clientes de armazenamento.

## <a name="list-containers-operation"></a>Operação de contentores de lista

Reveja a referência para a operação [ListContainers.](/rest/api/storageservices/List-Containers2) Esta informação irá ajudá-lo a entender de onde vêm alguns dos campos no pedido e resposta.

**Método de Pedido**: GET. Este verbo é o método HTTP que especifica como propriedade do objeto de pedido. Outros valores para este verbo incluem HEAD, PUT e DELETE, dependendo da API que está a chamar.

**Pedido URI**: `https://myaccount.blob.core.windows.net/?comp=list`.  O pedido URI é criado a partir do ponto final da conta de armazenamento blob `http://myaccount.blob.core.windows.net` e da cadeia de recursos `/?comp=list`.

[Parâmetros URI](/rest/api/storageservices/List-Containers2#uri-parameters): Existem parâmetros de consulta adicionais que pode utilizar ao ligar para listContainers. Alguns destes parâmetros são *tempo de paragem* para a chamada (em segundos) e *prefixo*, que é usado para filtrar.

Outro parâmetro útil são *os resultados máximos:* se houver mais contentores disponíveis do que este valor, o corpo de resposta conterá um elemento *NextMarker* que indica que o próximo recipiente deve regressar no próximo pedido. Para utilizar esta funcionalidade, fornece o valor *NextMarker* como parâmetro de *marcador* no URI quando fizer o próximo pedido. Ao utilizar esta funcionalidade, é análoga a paging através dos resultados.

Para utilizar parâmetros adicionais, acomode-os à cadeia de recursos com o valor, como este exemplo:

```
/?comp=list&timeout=60&maxresults=100
```

[Cabeçalhos de pedido](/rest/api/storageservices/List-Containers2#request-headers) **:** Esta secção lista os cabeçalhos de pedido necessários e opcionais. São necessários três cabeçalhos: um cabeçalho de *autorização,* *x-ms-data* (contém o tempo UTC para o pedido) e *a versão x-ms* (especifica a versão da API REST a utilizar). Incluir *x-ms-cliente-request-id* nos cabeçalhos é opcional – você pode definir o valor para este campo para qualquer coisa; está escrito nos registos de análise de armazenamento quando o registo está ativado.

[Órgão de Pedido](/rest/api/storageservices/List-Containers2#request-body) **:** Não existe nenhum órgão de pedido para ListContainers. Request Body é usado em todas as operações PUT ao carregar bolhas, bem como SetContainerAccessPolicy, que lhe permite enviar uma lista XML de políticas de acesso armazenadas a aplicar. As políticas de acesso armazenadas são discutidas no artigo Utilizando Assinaturas de [Acesso Partilhada (SAS)](storage-sas-overview.md).

[Código do Estado](/rest/api/storageservices/List-Containers2#status-code)de Resposta **:** Indica quaisquer códigos de estado que precisa de saber. Neste exemplo, um código de estado HTTP de 200 está ok. Para obter uma lista completa dos códigos de estado http, consulte as [definições](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)do Código de Estado . Para ver códigos de erro específicos das APIs de repouso de armazenamento, consulte códigos de [erro DaPI Rest Comuns](/rest/api/storageservices/common-rest-api-error-codes)

[Cabeçalhos de resposta](/rest/api/storageservices/List-Containers2#response-headers) **:** Estes incluem tipo de *conteúdo;* *x-ms-request-id*, que é o ID de pedido que passou; *x-ms-versão*, que indica a versão do serviço Blob utilizado; e a *Data*, que está na UTC e diz a que horas o pedido foi feito.

[Corpo de Resposta](/rest/api/storageservices/List-Containers2#response-body): Este campo é uma estrutura XML que fornece os dados solicitados. Neste exemplo, a resposta é uma lista de contentores e suas propriedades.

## <a name="creating-the-rest-request"></a>Criação do pedido REST

Para obter segurança ao executar a produção, utilize sempre HTTPS em vez de HTTP. Para efeitos deste exercício, deverá utilizar http para poder ver os dados de pedido e resposta. Para visualizar as informações de pedido e resposta nas chamadas DE REST reais, pode baixar [o Fiddler](https://www.telerik.com/fiddler) ou uma aplicação similar. Na solução Visual Studio, o nome da conta de armazenamento e a chave estão codificados na classe. O método ListContainersAsyncREST passa a chave da conta de armazenamento e da conta de armazenamento para os métodos utilizados para criar os vários componentes do pedido REST. Numa aplicação do mundo real, o nome e a chave da conta de armazenamento residem num ficheiro de configuração, variáveis ambientais ou seriam recuperados de um Cofre chave Azure.

No nosso projeto de amostragem, o código para criar o cabeçalho de Autorização está numa classe separada. A ideia é que possas pegar em toda a aula e adicioná-la à tua própria solução e usá-la "como está". O código de cabeçalho de autorização funciona para a maioria das chamadas REST API para o Armazenamento Azure.

Para construir o pedido, que é um objeto HttpRequestMessage, vá ao ListContainersAsyncREST em Program.cs. Os passos para a construção do pedido são:

- Crie o URI para ser usado para chamar o serviço.
- Crie o objeto HttpRequestMessage e detete a carga útil. A carga útil é nula para listContainersAsyncREST porque não estamos a passar nada.
- Adicione os cabeçalhos de pedido para x-ms-date e x-ms-versão.
- Pegue o cabeçalho de autorização e adicione.

Algumas informações básicas que precisa:

- Para os ListContainers, o **método** é `GET`. Este valor é definido ao instantaneamente o pedido.
- O **recurso** é a parte de consulta do URI que indica a chamada API, pelo que o valor é `/?comp=list`. Como notado anteriormente, o recurso está na página de documentação de referência que mostra as informações sobre a [API listContainers](/rest/api/storageservices/List-Containers2).
- O URI é construído através da criação do ponto final do serviço Blob para essa conta de armazenamento e concatenação do recurso. O valor para **pedido URI** acaba por ser `http://contosorest.blob.core.windows.net/?comp=list`.
- Para ListContainers, **requestBody** é nulo e não há **cabeçalhos**extra .

APIs diferentes podem ter outros parâmetros para passar, tais como *se Match*. Um exemplo de onde pode usar se o Match for quando ligar para PutBlob. Nesse caso, define seMatch para um eTag, e só atualiza a bolha se o eTag que fornece corresponder ao eTag atual na bolha. Se alguém tiver atualizado a bolha desde que recuperou o eTag, a mudança não será ultrapassada.

Primeiro, desloque a `uri` e a `payload`.

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Em seguida, instantaneamente o pedido, definindo o método para `GET` e fornecendo o URI.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adicione os cabeçalhos de pedido para `x-ms-date` e `x-ms-version`. Este lugar no código também é onde você adiciona quaisquer cabeçalhos de pedido adicionais necessários para a chamada. Neste exemplo, não existem cabeçalhos adicionais. Um exemplo de uma API que passa em cabeçalhos extraé a operação set Container ACL. Esta chamada da API adiciona um cabeçalho chamado "x-ms-blob-public-access" e o valor para o nível de acesso.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Ligue para o método que cria o cabeçalho de autorização e adicione-o aos cabeçalhos de pedido. Verá como criar o cabeçalho de autorização mais tarde no artigo. O nome do método é GetAuthorizationHeader, que pode ver neste código:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Neste ponto, `httpRequestMessage` contém o pedido REST completo com os cabeçalhos de autorização.

## <a name="send-the-request"></a>Enviar o pedido

Agora que construiu o pedido, pode ligar para o método SendAsync para enviá-lo para o Armazenamento Azure. Verifique se o valor do código de estado de resposta é de 200, o que significa que a operação foi bem sucedida. Em seguida, analise a resposta. Neste caso, obtém-se uma lista de recipientes XML. Vamos olhar para o código para chamar o método GetRESTRequest para criar o pedido, executar o pedido e, em seguida, examinar a resposta para a lista de recipientes.

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

Se executar um sniffer de rede como [o Fiddler](https://www.telerik.com/fiddler) ao fazer a chamada para SendAsync, pode ver as informações de pedido e resposta. Vamos dar uma olhada. O nome da conta de armazenamento é *contosorest.*

**Pedido:**

```
GET /?comp=list HTTP/1.1
```

**Cabeçalhos de pedido:**

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

**Corpo de resposta (XML):** Para a operação List Containers, isto mostra a lista de contentores e suas propriedades.

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

Agora que entende como criar o pedido, ligue para o serviço e analise os resultados, vamos ver como criar o cabeçalho de autorização. Criar esse cabeçalho é complicado, mas a boa notícia é que uma vez que tenha o código a funcionar, funciona para todas as APIs REST do Serviço de Armazenamento.

## <a name="creating-the-authorization-header"></a>Criando o cabeçalho de autorização

> [!TIP]
> O Azure Storage suporta agora a integração do Azure Ative Directory (Azure AD) para bolhas e filas. A Azure AD oferece uma experiência muito mais simples para autorizar um pedido ao Azure Storage. Para obter mais informações sobre a utilização da AD Azure para autorizar operações REST, consulte [Autorizar com o Diretório Ativo Azure](/rest/api/storageservices/authorize-with-azure-active-directory). Para uma visão geral da integração da Azure AD com o Armazenamento Azure, consulte o [acesso authenticado ao Armazenamento Azure utilizando o Diretório Ativo Azure.](storage-auth-aad.md)

Existe um artigo que explica conceptualmente (sem código) como [autorizar pedidos de armazenamento azure](/rest/api/storageservices/authorize-requests-to-azure-storage).

Vamos destilar o artigo para baixo exatamente é necessário e mostrar o código.

Primeiro, use a autorização da Chave Partilhada. O formato cabeçalho de autorização é assim:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

O campo de assinatura é um Código de Autenticação de Mensagens (HMAC) baseado em Hash criado a partir do pedido e calculado usando o algoritmo SHA256, e depois codificado usando a codificação Base64. Tem aquilo? (Aguenta aí, ainda nem ouviste a palavra *canonicalizada.)*

Este snippet de código mostra o formato da linha de assinatura Chave Partilhada:

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

A maioria destes campos raramente são usados. Para armazenamento blob, especifice VERB, md5, comprimento de conteúdo, Cabeçalhos Canonicalizados e Recurso Canonicalizado. Pode deixar os outros em branco (mas colocar no `\n` para que saiba que estão em branco).

O que são Cabeçalhos Canonicalizados e Recursos Canonicalizados? Boa pergunta. Na verdade, o que significa canonicalizado? O Microsoft Word nem sequer o reconhece como uma palavra. Eis o que a [Wikipédia diz sobre a canonicalização](https://en.wikipedia.org/wiki/Canonicalization): *Na ciência da computação, a canonicalização (por vezes normalização ou normalização) é um processo de conversão de dados que tem mais do que uma possível representação numa forma "normal", "normal" ou canónica.* Em termos normais, isto significa pegar na lista de itens (como cabeçalhos no caso dos Cabeçalhos Canonicalizados) e padronizá-los num formato exigido. Basicamente, a Microsoft decidiu um formato e é preciso compará-lo.

Vamos começar com esses dois campos canonicalizados, porque eles são obrigados a criar o cabeçalho de Autorização.

### <a name="canonicalized-headers"></a>Cabeçalhos canonicalizados

Para criar este valor, recupere os cabeçalhos que começam com "x-ms-" e os separe, em seguida, forte-os numa série de `[key:value\n]` instâncias, concatenadas numa corda. Para este exemplo, os cabeçalhos canonicalizados são assim:

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

### <a name="canonicalized-resource"></a>Recurso canonicalizado

Esta parte da cadeia de assinatura representa a conta de armazenamento visada pelo pedido. Lembre-se que o Pedido URI é `<http://contosorest.blob.core.windows.net/?comp=list>`, com o nome real da conta (`contosorest` neste caso). Neste exemplo, este é devolvido:

```
/contosorest/\ncomp:list
```

Se tiver parâmetros de consulta, este exemplo também inclui esses parâmetros. Aqui está o código, que também lida com parâmetros de consulta adicionais e parâmetros de consulta com vários valores. Lembre-se que está construindo este código para trabalhar para todas as APIs restantes. Você quer incluir todas as possibilidades, mesmo que o método ListContainers não precise de todas.

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

Agora que as cordas canonicalizadas estão definidas, vamos ver como criar o próprio cabeçalho de autorização. Começa por criar uma série de assinaturas de mensagem no formato do StringToSign anteriormente apresentado neste artigo. Este conceito é mais fácil de explicar usando comentários no código, por isso aqui está, o método final que devolve o Cabeçalho de Autorização:

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

Quando executa este código, o MessageSignature resultante parece este exemplo:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Aqui está o valor final para AutorizaçõesHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

O Cabeçalho de Autorização é o último cabeçalho colocado nos cabeçalhos de pedido antes de publicar a resposta.

Isso cobre tudo o que precisa de saber para montar uma aula com a qual pode criar um pedido para chamar os Serviços de Armazenamento DE APIs.

## <a name="example-list-blobs"></a>Exemplo: Lista de bolhas

Vamos ver como alterar o código para ligar para a operação List Blobs para *contentor-1*. Este código é quase idêntico ao código de listagem de contentores, sendo que as únicas diferenças são o URI e a forma como analisa a resposta.

Se olhar para a documentação de referência para [ListBlobs,](/rest/api/storageservices/List-Blobs)você descobre que o método é *GET* e o RequestURI é:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

No ListContainersAsyncREST, altere o código que define o URI para a API para ListBlobs. O nome do recipiente é **recipiente-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Em seguida, onde lida com a resposta, altere o código para procurar bolhas em vez de recipientes.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Quando executa esta amostra, obtém resultados como os seguintes:

**Cabeçalhos canonicalizados:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Recurso canonicalizado:**

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

**Cabeçalhos de pedido:**

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

Neste artigo, aprendeu a fazer um pedido à API de armazenamento de blob REST. Com o pedido, pode recuperar uma lista de contentores ou uma lista de bolhas num recipiente. Aprendeu a criar a assinatura de autorização para a chamada REST API e como usá-la no pedido REST. Finalmente, aprendeu a examinar a resposta.

## <a name="next-steps"></a>Passos seguintes

- [Blob Serviço REST API](/rest/api/storageservices/blob-service-rest-api)
- [Serviço de Arquivo REST API](/rest/api/storageservices/file-service-rest-api)
- [Serviço de Fila REST API](/rest/api/storageservices/queue-service-rest-api)
- [Serviço de Mesa REST API](/rest/api/storageservices/table-service-rest-api)
