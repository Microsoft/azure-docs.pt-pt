---
title: Chamar operações de API de REST de serviços de armazenamento do Azure, incluindo autenticação | Documentos da Microsoft
description: Chamar operações de API de REST de serviços de armazenamento do Azure, incluindo autenticação
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 771f910fce44724250ff79e770e0d1ca56e8765c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768421"
---
# <a name="using-the-azure-storage-rest-api"></a>Utilizar a API REST do Armazenamento do Azure

Este artigo mostra-lhe como utilizar as APIs de REST do serviço de armazenamento de BLOBs e como autenticar a chamada para o serviço. Ele foi escrito do ponto de vista de alguém que não sabe nada sobre REST e não sabe como fazer uma chamada REST, mas é um desenvolvedor. Vamos examinar a documentação de referência para uma chamada REST e veja como convertê-lo numa chamada REST real – os campos que ir para qual lugar? Após saber como configurar uma chamada REST, pode aproveitar esse conhecimento para utilizar qualquer uma das outras APIs armazenamento serviço REST.

## <a name="prerequisites"></a>Pré-requisitos 

A aplicação apresenta uma lista de contentores no armazenamento de BLOBs para uma conta de armazenamento. Para experimentar o código neste artigo, precisa do seguinte: 

* Instale [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) com a carga de trabalho seguinte:
    - Desenvolvimento do Azure

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Uma conta de armazenamento para fins gerais. Se ainda não tiver uma conta de armazenamento, veja [criar uma conta de armazenamento](storage-quickstart-create-account.md).

* O exemplo neste artigo mostra como listar os contentores numa conta de armazenamento. Para ver um resultado, adicione alguns contentores no armazenamento de BLOBs na conta de armazenamento antes de começar.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

O aplicativo de exemplo é uma aplicação de consola escrita em c#.

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Este comando clona o repositório para a sua pasta local do git. Para abrir a solução do Visual Studio, procure a pasta storage-dotnet-rest-api-with-auth, abra-o e clique duas vezes em StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>O que é o REST?

REST significa *transferência de estado representational*. Para obter uma definição específica, confira [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

Basicamente, o REST é uma arquitetura que pode utilizar quando chamar APIs ou APIs disponíveis para ser chamado de fazer. É independente do que está acontecendo em ambos os lados, e outro software que está a ser utilizado ao enviar ou receber o resto chama. Pode escrever um aplicativo que é executado num Mac, Windows, Linux, um telemóvel Android ou tablet, iPhone, iPod ou web site e utilizar a mesma API de REST para todas essas plataformas. Dados podem ser passados na e/ou horizontalmente quando a API REST é chamada. A API REST não importa a partir do qual plataforma é chamado – o que é importante as informações transmitidas no pedido e os dados fornecidos na resposta.

Saber como utilizar o REST é uma habilidade útil. A equipe de produto do Azure com frequência lança novos recursos. Muitas vezes, os novos recursos estão acessíveis através da interface REST, mas ainda não foram anexados através de **todos os** das bibliotecas de cliente do armazenamento ou a interface do Usuário (por exemplo, o portal do Azure). Se sempre queira usar o mais recente e melhor, REST é um requisito de aprendizado. Além disso, se quiser escrever sua própria biblioteca para interagir com o armazenamento do Azure, ou desejar acessar o armazenamento do Azure com uma linguagem de programação que não tenha uma biblioteca de cliente SDK ou armazenamento, pode utilizar a API REST.

## <a name="about-the-sample-application"></a>Acerca da aplicação de exemplo

O aplicativo de exemplo lista os contentores numa conta de armazenamento. Assim que compreender como as informações na documentação da API de REST está relacionada ao seu código real, outras chamadas REST são mais fáceis de descobrir. 

Se examinar a [API de REST do serviço Blob](/rest/api/storageservices/Blob-Service-REST-API), verá todas as operações que pode efetuar no armazenamento de Blobs. As bibliotecas de cliente de armazenamento são invólucros em torno as APIs REST – eles tornam mais fácil para si para o armazenamento de acesso sem utilizar as APIs REST diretamente. Mas, conforme indicado acima, algumas vezes deseja usar a API REST em vez de uma biblioteca de cliente de armazenamento.

## <a name="rest-api-reference-list-containers-api"></a>Referência da API REST: API de contentores de lista

Vamos ver a página na referência de API do REST para o [ListContainers](/rest/api/storageservices/List-Containers2) operação para que entender onde alguns dos campos vêm no pedido e resposta, na secção seguinte com o código.

**Método de pedido**: OBTER. Esse verbo é o método HTTP que especificar como uma propriedade do objeto request. Outros valores para esse verbo incluem HEAD, PUT e DELETE, consoante a API está a chamar.

**URI do pedido**: https://myaccount.blob.core.windows.net/?comp=list  Isso é criado a partir do ponto de final de conta de armazenamento de BLOBs `http://myaccount.blob.core.windows.net` e a cadeia de caracteres de recurso `/?comp=list`.

[Parâmetros do URI](/rest/api/storageservices/List-Containers2#uri-parameters): Existem parâmetros de consulta adicionais, que pode usar ao chamar ListContainers. Alguns desses parâmetros são *tempo limite* para a chamada (em segundos) e *prefixo*, que é utilizada para filtragem.

É o outro parâmetro útil *maxresults:* se mais contentores disponíveis que este valor, o corpo da resposta irá conter uma *NextMarker* elemento que indica o contentor seguinte para devolver na próxima pedido. Para utilizar esta funcionalidade, é fornecer a *NextMarker* valor como o *marcador* parâmetro no URI quando fizer a próxima solicitação. Ao utilizar esta funcionalidade, é semelhante a paginação pelos resultados. 

Para utilizar parâmetros adicionais, acrescentá-los para a cadeia de caracteres de recurso com o valor, semelhante a este exemplo:

```
/?comp=list&timeout=60&maxresults=100
```

[Cabeçalhos de pedido](/rest/api/storageservices/List-Containers2#request-headers)**:** Esta secção lista os cabeçalhos de pedido obrigatórios e opcionais. Três dos cabeçalhos são necessários: uma *autorização* cabeçalho, *x-ms-data* (contém a hora UTC para o pedido), e *x-ms-version* (Especifica a versão do RESTANTE API para utilizar). Incluindo *x-ms-client-request-id* nos cabeçalhos é opcional – pode definir o valor para este campo para qualquer coisa; ele é escrito nos registos de análise de armazenamento quando o registo está ativado.

[Corpo do pedido](/rest/api/storageservices/List-Containers2#request-body)**:** Não existe nenhum corpo de pedido para ListContainers. O corpo do pedido é utilizado em todas as operações PUT ao carregar blobs, bem como SetContainerAccessPolicy, que permite que envie numa lista XML de políticas de acesso armazenadas para aplicar. Políticas de acesso armazenadas são abordadas no artigo [usando partilhado assinaturas de acesso (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Código de estado de resposta](/rest/api/storageservices/List-Containers2#status-code)**:** Informa de qualquer códigos de estado que precisa saber. Neste exemplo, um código de estado HTTP de 200 está ok. Para obter uma lista completa dos códigos de estado HTTP, confira [definições de código de estado](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Para ver os códigos de erro específicos para as APIs de REST de armazenamento, consulte [códigos de erro comuns API de REST](/rest/api/storageservices/common-rest-api-error-codes)

[Cabeçalhos de resposta](/rest/api/storageservices/List-Containers2#response-headers)**:** Estes incluem *tipo de conteúdo*; *x-ms-request-id* (o id do pedido é passado no, se aplicável); *x-ms-version* (indica a versão do serviço Blob utilizado) e o *data* (UTC, informa que altura em que o pedido foi efectuado).

[Corpo da resposta](/rest/api/storageservices/List-Containers2#response-body): Este campo é uma estrutura XML, fornecendo os dados solicitados. Neste exemplo, a resposta é uma lista de contentores e as respetivas propriedades.

## <a name="creating-the-rest-request"></a>Criar o pedido REST

Algumas observações antes de iniciar – para segurança quando em execução na produção, utilize sempre HTTPS em vez de HTTP. Para os fins deste exercício, deve usar HTTP para que pode ver os dados de solicitação e resposta. Para ver as informações de solicitação e resposta nas chamadas REST reais, pode baixar [Fiddler](http://www.telerik.com/fiddler) ou um aplicativo semelhante. Na solução do Visual Studio, o nome da conta de armazenamento e a chave são codificadas na classe e o método ListContainersAsyncREST transmite o nome da conta de armazenamento e a chave da conta de armazenamento para os métodos que são utilizados para criar os vários componentes da solicitação REST . Num aplicativo do mundo real, o nome da conta de armazenamento e a chave residem num arquivo de configuração, variáveis de ambiente, ou ser obtidos a partir de um Azure Key Vault.

Em nosso projeto de exemplo, o código para criar o cabeçalho de autorização está numa classe separada, com a idéia que poderia levar a classe inteira e adicioná-lo à sua própria solução e usá-lo "como"está. O código de cabeçalho de autorização trabalha para a maioria das chamadas de REST API para o armazenamento do Azure.

Para criar a solicitação, o que é um objeto HttpRequestMessage, vá para ListContainersAsyncREST em Program.cs. Os passos para criar o pedido são: 

* Crie o URI a ser utilizada para chamar o serviço. 
* Crie o objeto HttpRequestMessage e defina o payload. A carga é nula para ListContainersAsyncREST porque que não estamos passando qualquer coisa.
* Adicione os cabeçalhos de pedido x-ms-data e x-ms-version.
* Obtenha o cabeçalho de autorização e adicioná-lo.

Algumas informações básicas que precisa: 

*  Para ListContainers, o **método** é `GET`. Este valor é definido quando o pedido de criação de instâncias. 
*  O **resource** é a parte da consulta do URI que indica que está a ser chamada de API, pelo que é o valor `/?comp=list`. Conforme observado anteriormente, o recurso está na página de documentação de referência, que mostra as informações sobre o [ListContainers API](/rest/api/storageservices/List-Containers2).
*  O URI é construído ao criar o ponto de final de serviço de BLOBs para essa conta de armazenamento e concatenando o recurso. O valor para **URI do pedido** acaba sendo `http://contosorest.blob.core.windows.net/?comp=list`.
*  Para ListContainers, **requestBody** é nulo e não há nenhum extra **cabeçalhos**.

APIs diferentes podem ter outros parâmetros, tais como passar *ifMatch*. Um exemplo de onde é possível usar ifMatch é ao chamar PutBlob. Nesse caso, definir ifMatch como uma eTag e apenas atualiza o blob se a eTag que é fornecer corresponder à eTag atual no blob. Se alguém tiver atualizado o blob desde a obter a eTag, suas alterações não ser substituída. 

Em primeiro lugar, defina o `uri` e o `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Em seguida, criar uma instância de solicitação, o método na definição `GET` e fornecer o URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adicione os cabeçalhos de pedido x-ms-data e x-ms-version. Este local no código também é onde adiciona qualquer cabeçalho de pedido adicionais necessário para a chamada. Neste exemplo, não há nenhum cabeçalhos adicionais. Um exemplo de uma API que passa nos cabeçalhos extras é SetContainerACL. Para armazenamento de BLOBs, ele adiciona um cabeçalho denominado "x-ms-BLOBs-acesso público" e o valor para o nível de acesso.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Chamar o método que cria o cabeçalho de autorização e adicioná-lo para os cabeçalhos de pedido. Verá como criar o cabeçalho de autorização posteriormente neste artigo. O nome do método é GetAuthorizationHeader, o que pode ver neste trecho de código:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Neste momento, `httpRequestMessage` contém o pedido REST completo com os cabeçalhos de autorização. 

## <a name="call-the-rest-api-with-the-request"></a>Chamar a API de REST com o pedido

Agora que tem o pedido, pode chamar SendAsync para enviar o pedido REST. SendAsync chama a API e recebe a resposta de volta. Examinar a resposta, StatusCode (200 está OK), em seguida, analisar a resposta. Neste caso, obtém uma lista XML de contentores. Vamos examinar o código para chamar o método de GetRESTRequest para criar o pedido, execute o pedido e, em seguida, examinar a resposta para a lista de contentores.

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

Se executar um sniffer de rede, tal como [Fiddler](https://www.telerik.com/fiddler) ao fazer a chamada a SendAsync, pode ver as informações de solicitação e resposta. Vamos dar uma olhada. É o nome da conta do storage *contosorest*.

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

**Cabeçalhos de resposta e de código de estado devolvido após a execução:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Corpo da resposta (XML):** Para ListContainers, isso mostra a lista de contentores e as respetivas propriedades.

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

Agora que sabe como criar a solicitação, chamar o serviço e analisar os resultados, vamos ver como criar o cabeçalho de autorização. Criar esse cabeçalho é complicado, mas a boa notícia é que assim que tiver o código a funcionar, ele funciona para todas as APIs de REST do serviço de armazenamento.

## <a name="creating-the-authorization-header"></a>Criar o cabeçalho de autorização

> [!TIP]
> O armazenamento do Azure agora suporta a integração do Azure Active Directory (Azure AD) para os serviços de BLOBs e filas (pré-visualização). O Azure AD oferece uma experiência muito mais simples para autorizar um pedido ao armazenamento do Azure. Para obter mais informações sobre a utilização do Azure AD para autorizar as operações REST, consulte [autenticar com o Azure Active Directory (pré-visualização)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Para uma descrição geral da integração do Azure AD com o armazenamento do Azure, consulte [autenticar o acesso ao armazenamento do Azure com o Azure Active Directory (pré-visualização)](storage-auth-aad.md).

Há um artigo que explica conceitualmente (não código) como realizar [autenticação para os serviços de armazenamento do Azure](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services).
Vamos converta esse artigo para exatamente é necessária e mostrar o código.

Primeiro, use uma autenticação de chave partilhada. O formato do cabeçalho de autorização tem esta aparência:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

O campo de assinatura é uma base em Hash Message Authentication Code (HMAC) criados a partir do pedido e calculado utilizando o algoritmo SHA256, em seguida, codificados usando a codificação Base64. Entendeu? (Aguente firme, até mesmo nunca ouviu a palavra *canonizado* ainda.)

Este fragmento de código mostra o formato da cadeia de caracteres de assinatura de chave partilhada:

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

A maioria desses campos raramente é utilizada. Para armazenamento de BLOBs, especifique VERBO, md5, o comprimento do conteúdo, Canonizado cabeçalhos e recursos Canonizado. Pode deixar as outras em branco (mas put no `\n` para que ele sabe que estão em branco).

Quais são CanonicalizedHeaders e CanonicalizedResource? Boa pergunta. Na verdade, o que faz canonizado significam? Microsoft Word ainda não reconhecê-lo como uma palavra. Eis o que [Wikipedia não diz respeito a canonização](http://en.wikipedia.org/wiki/Canonicalization): *Em ciência da computação, a canonização (às vezes, padronização ou normalização) é um processo para a conversão de dados que tenha mais do que uma representação possível num formato canónico, "standard" ou "normal".* No jargão de normal, isso significa que a lista de itens (por exemplo, cabeçalhos no caso de cabeçalhos Canonizado) e padronizá-los num formato necessário. Basicamente, a Microsoft decidiu num formato e tem de corresponder ao mesmo.

Vamos começar com esses dois campos canonicalized, pois são necessários para criar o cabeçalho de autorização.

**Canonizado cabeçalhos**

Para criar este valor, obter os cabeçalhos que começam com "x - ms-" e classificação-los, em seguida, formatá-los numa cadeia de caracteres de `[key:value\n]` instâncias, concatenadas numa cadeia. Neste exemplo, os cabeçalhos canonicalized o seguinte aspeto: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Eis o código usado para criar essa saída:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Recurso canonizado**

Esta parte da cadeia de assinatura representa a conta de armazenamento visada pelo pedido. Lembre-se de que o URI do pedido é `<http://contosorest.blob.core.windows.net/?comp=list>`, com o nome da conta real (`contosorest` neste caso). Neste exemplo, este é devolvido:

```
/contosorest/\ncomp:list
```

Se tiver de parâmetros de consulta, isso inclui as também. Aqui está o código, que também lida com parâmetros de consulta adicionais e os parâmetros de consulta com vários valores. Lembre-se de que está criando esse código funcionar para todas as APIs REST, para que deseja incluir todas as possibilidades, mesmo que o método ListContainers não precisa de todos eles.

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
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Agora que as cadeias de caracteres canonicalized forem definidas, Vejamos como criar o cabeçalho de autorização em si. Comece por criar uma cadeia de caracteres da assinatura da mensagem no formato de StringToSign apresentada anteriormente neste artigo. Esse conceito é mais fácil explicar com comentários no código, então aqui está, o método final que devolve o cabeçalho Authorization:

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
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Quando executar esse código, o MessageSignature resultante é semelhante a isso:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Este é o valor final para AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

O AuthorizationHeader é o último cabeçalho colocado nos cabeçalhos do pedido antes de lançar a resposta.

Que aborda tudo o que precisa saber, juntamente com o código, juntar-se de uma classe que pode usar para criar um pedido para ser utilizada para chamar as APIs de REST de serviços de armazenamento.

## <a name="how-about-another-example"></a>E quanto a outro exemplo? 

Vamos dar uma olhada em como alterar o código para chamar ListBlobs para o contentor *contentor-1*. Isso é quase idêntico ao código para listar os contentores, as únicas diferenças a ser o URI e a forma como analisar a resposta. 

Se examinar a documentação de referência [ListBlobs](/rest/api/storageservices/List-Blobs), descobrir que é o método *obter* e o RequestURI é:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

No ListContainersAsyncREST, altere o código que define o URI para a API para ListBlobs. É o nome do contentor **contentor-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Em seguida, em que manipular a resposta, altere o código para procurar blobs em vez de contentores.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Quando executa esse exemplo, obtém resultados semelhante ao seguinte:

**Cabeçalhos canonizados:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Recurso canonizado:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Os seguintes valores são de [Fiddler](http://www.telerik.com/fiddler):

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

**Cabeçalhos de resposta e de código de estado devolvido após a execução:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Corpo da resposta (XML):** Essa resposta XML mostra a lista de blobs e as respetivas propriedades. 

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

Neste artigo, aprendeu a fazer um pedido para o API REST para obter uma lista de contentores ou uma lista de blobs num contentor de armazenamento de Blobs. Também aprendeu como criar a assinatura de autorização para a chamada de REST API, como usá-lo no pedido de REST e examinar a resposta.

## <a name="next-steps"></a>Passos Seguintes

* [REST API do serviço de BLOBs](/rest/api/storageservices/blob-service-rest-api)
* [API de REST do serviço de ficheiros](/rest/api/storageservices/file-service-rest-api)
* [API de REST do serviço de fila](/rest/api/storageservices/queue-service-rest-api)
