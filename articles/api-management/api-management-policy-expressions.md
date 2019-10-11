---
title: Expressões de política de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as expressões de política no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: e9e6eff4c527ff2e22be57ebc1eb3dcdb3c4e0ab
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241984"
---
# <a name="api-management-policy-expressions"></a>Expressões de política de gerenciamento de API
Este artigo discute a sintaxe de expressões de C# política é 7. Cada expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) fornecida implicitamente e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos de .NET Framework.

Para mais informações:

- Consulte como fornecer informações de contexto para seu serviço de back-end. Use o [parâmetro definir cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) e defina as políticas de [cabeçalho http](api-management-transformation-policies.md#SetHTTPheader) para fornecer essas informações.
- Consulte como usar a política [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para autorizar o acesso a operações com base em declarações de token.
- Consulte como usar um rastreamento do [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) para ver como as políticas são avaliadas e os resultados dessas avaliações.
- Consulte como usar expressões com as políticas [obter do cache](api-management-caching-policies.md#GetFromCache) e [armazenar em cache](api-management-caching-policies.md#StoreToCache) para configurar o cache de resposta de gerenciamento de API. Defina uma duração que corresponda ao cache de resposta do serviço de back-end conforme especificado pela diretiva `Cache-Control` do serviço com suporte.
- Consulte como executar a filtragem de conteúdo. Remova os elementos de dados da resposta recebida do back-end usando o [fluxo de controle](api-management-advanced-policies.md#choose) e defina as políticas de [corpo](api-management-transformation-policies.md#SetBody) .
- Para baixar as instruções de política, consulte o repositório GitHub [API-Management-Samples/Policies](https://github.com/Azure/api-management-samples/tree/master/policies) .


## <a name="Syntax"></a>Sintaxe
Expressões de instrução única são colocadas em `@(expression)`, em que `expression` é uma instrução C# de expressão bem formada.

As expressões de várias instruções são colocadas em `@{expression}`. Todos os caminhos de código dentro de expressões de várias instruções devem terminar com uma instrução `return`.

## <a name="PolicyExpressionsExamples"></a>Disso

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="PolicyExpressionsUsage"></a>Usos
As expressões podem ser usadas como valores de atributo ou valores de texto em qualquer [política](api-management-policies.md) de gerenciamento de API (a menos que a referência de política especifique o contrário).

> [!IMPORTANT]
> Quando você usa expressões de política, há apenas uma verificação limitada das expressões de política quando a política é definida. As expressões são executadas pelo gateway em tempo de execução, todas as exceções geradas por expressões de política resultam em um erro de tempo de execução.

## <a name="CLRTypes"></a>Tipos de .NET Framework permitidos em expressões de política
A tabela a seguir lista os tipos de .NET Framework e seus membros que são permitidos em expressões de política.

|Tipo|Membros com suporte|
|--------------|-----------------------|
|Newtonsoft. JSON. Formatting|Tudo|
|Newtonsoft. JSON. JsonConvert|SerializeObject, desserializaobject|
|Newtonsoft. JSON. Linq. Extensions|Tudo|
|Newtonsoft. JSON. Linq. JArray|Tudo|
|Newtonsoft. JSON. Linq. JConstructor|Tudo|
|Newtonsoft. JSON. Linq. JContainer|Tudo|
|Newtonsoft. JSON. Linq. JObject|Tudo|
|Newtonsoft. JSON. Linq. JProperty|Tudo|
|Newtonsoft. JSON. Linq. JRaw|Tudo|
|Newtonsoft. JSON. Linq. JToken|Tudo|
|Newtonsoft. JSON. Linq. JTokenType|Tudo|
|Newtonsoft. JSON. Linq. JValue|Tudo|
|System. array|Tudo|
|System. BitConverter|Tudo|
|System. Boolean|Tudo|
|System. byte|Tudo|
|System. Char|Tudo|
|System. Collections. Generic. Dictionary < TKey, TValue >|Tudo|
|System. Collections. Generic. HashSet @ no__t-0T >|Tudo|
|System. Collections. Generic. ICollection @ no__t-0T >|Tudo|
|System. Collections. Generic. IDictionary < TKey, TValue >|Tudo|
|System. Collections. Generic. IEnumerable @ no__t-0T >|Tudo|
|System. Collections. Generic. IEnumerator @ no__t-0T >|Tudo|
|System. Collections. Generic. IList @ no__t-0T >|Tudo|
|System. Collections. Generic. IReadOnlyCollection @ no__t-0T >|Tudo|
|System. Collections. Generic. IReadOnlyDictionary < TKey, TValue >|Tudo|
|System. Collections. Generic. ISet @ no__t-0T >|Tudo|
|System. Collections. Generic. KeyValuePair < TKey, TValue >|Tudo|
|System. Collections. Generic. List @ no__t-0T >|Tudo|
|System. coleções. Generic. Queue @ no__t-0T >|Tudo|
|System. coleções. Generic. Stack @ no__t-0T >|Tudo|
|Sistema. Convert|Tudo|
|System. DateTime|(Construtor), adicionar, subdias, addhora, addmilésimos de segundo, addminutos, addmeses, adicionar, submarcações, AddYears, data, Day, DayOfWeek, DayOfYear, DaysInMonth, hora, IsDaylightSavingTime, IsLeapYear, MaxValue, milissegundo, minuto, MinValue, mês, agora , Análise, segundo, subtrair, tiques, TimeOfDay, hoje, ToString, UtcNow, year|
|System. DateTimeKind|Horário|
|System. DateTimeOffset|Tudo|
|System. decimal|Tudo|
|Sistema. Double|Tudo|
|System. Exception|Tudo|
|System. GUID|Tudo|
|System. Int16|Tudo|
|System. Int32|Tudo|
|System. Int64|Tudo|
|System. IO. StringReader|Tudo|
|System. IO. StringWriter|Tudo|
|System. Linq. Enumerable|Tudo|
|System. Math|Tudo|
|System. MidpointRounding|Tudo|
|Sistema .net. WebUtility|Tudo|
|Sistema. Nullable|Tudo|
|Sistema. aleatório|Tudo|
|System. SByte|Tudo|
|System. Security. Cryptography. AsymmetricAlgorithm|Tudo|
|System. Security. Cryptography. CipherMode|Tudo|
|System. Security. Cryptography. HashAlgorithm|Tudo|
|System. Security. Cryptography. HashAlgorithmname|Tudo|
|System. Security. Cryptography. HMAC|Tudo|
|System. Security. Cryptography. HMACMD5|Tudo|
|System. Security. Cryptography. HMACSHA1|Tudo|
|System. Security. Cryptography. HMACSHA256|Tudo|
|System. Security. Cryptography. HMACSHA384|Tudo|
|System. Security. Cryptography. HMACSHA512|Tudo|
|System. Security. Cryptography. KeyedHashAlgorithm|Tudo|
|System. Security. Cryptography. MD5|Tudo|
|System. Security. Cryptography. Oid|Tudo|
|System. Security. Cryptography. PaddingMode|Tudo|
|System. Security. Cryptography. RNGCryptoServiceProvider|Tudo|
|System. Security. Cryptography. RSA|Tudo|
|System. Security. Cryptography. RSAEncryptionPadding|Tudo|
|System. Security. Cryptography. RSASignaturePadding|Tudo|
|System. Security. Cryptography. SHA1|Tudo|
|System. Security. Cryptography. SHA1Managed|Tudo|
|System. Security. Cryptography. SHA256|Tudo|
|System. Security. Cryptography. SHA256Managed|Tudo|
|System. Security. Cryptography. SHA384|Tudo|
|System. Security. Cryptography. SHA384Managed|Tudo|
|System. Security. Cryptography. SHA512|Tudo|
|System. Security. Cryptography. SHA512Managed|Tudo|
|System. Security. Cryptography. SymmetricAlgorithm|Tudo|
|System. Security. Cryptography. X509Certificates. PublicKey|Tudo|
|System. Security. Cryptography. X509Certificates. RSACertificateExtensions|Tudo|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Nome|
|System. Security. Cryptography. X509Certificates. X509Certificate|Tudo|
|System. Security. Cryptography. X509Certificates. X509Certificate2|Tudo|
|System. Security. Cryptography. X509Certificates. X509ContentType|Tudo|
|System. Security. Cryptography. X509Certificates. X509NameType|Tudo|
|Sistema. único|Tudo|
|System. String|Tudo|
|System. StringComparer|Tudo|
|System. StringComparison|Tudo|
|System. StringSplitOptions|Tudo|
|System. Text. Encoding|Tudo|
|System. Text. RegularExpressions. Capture|Índice, comprimento, valor|
|System. Text. RegularExpressions. CaptureCollection|Contagem, item|
|System. Text. RegularExpressions. Group|Capturas, êxito|
|System. Text. RegularExpressions. GroupCollection|Contagem, item|
|System. Text. RegularExpressions. Match|Vazio, grupos, resultado|
|System. Text. RegularExpressions. Regex|(Construtor), IsMatch, corresponder, corresponder, substituir, cancelar escape, dividir|
|System. Text. RegularExpressions. RegexOptions|Tudo|
|System. Text. StringBuilder|Tudo|
|System. TimeSpan|Tudo|
|System. TimeZone|Tudo|
|System. TimeZoneInfo. AdjustmentRule|Tudo|
|System. TimeZoneInfo. Transicionatime|Tudo|
|System. TimeZoneInfo|Tudo|
|System. tupla|Tudo|
|System. UInt16|Tudo|
|System. UInt32|Tudo|
|System. UInt64|Tudo|
|System. URI|Tudo|
|System. UriPartial|Tudo|
|System. xml. Linq. Extensions|Tudo|
|System. xml. Linq. XAttribute|Tudo|
|System. xml. Linq. XCData|Tudo|
|System. xml. Linq. XComment|Tudo|
|System. xml. Linq. XContainer|Tudo|
|System. xml. Linq. XDeclaration|Tudo|
|System. xml. Linq. XDocument|Todos, exceto de: Load|
|System. xml. Linq. XDocumenttype|Tudo|
|System. xml. Linq. XElement|Tudo|
|System. xml. Linq. XName|Tudo|
|System. xml. Linq. XNamespace|Tudo|
|System. xml. Linq. XNode|Tudo|
|System. xml. Linq. XNodeDocumentOrderComparer|Tudo|
|System. xml. Linq. XNodeEqualityComparer|Tudo|
|System. xml. Linq. XObject|Tudo|
|System. xml. Linq. XProcessingInstruction|Tudo|
|System. xml. Linq. XText|Tudo|
|System. xml. XmlNodeType|Tudo|

## <a name="ContextVariables"></a>Variável de contexto
Uma variável chamada `context` está implicitamente disponível em cada [expressão](api-management-policy-expressions.md#Syntax)de política. Seus membros fornecem informações pertinentes ao `\request`. Todos os membros `context` são somente leitura.

|Variável de contexto|Métodos, propriedades e valores de parâmetro permitidos|
|----------------------|-------------------------------------------------------|
|noticioso|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementação](#ref-context-deployment)<br /><br /> Decorrido: TimeSpan-intervalo de tempo entre o valor de timestamp e a hora atual<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operação](#ref-context-operation)<br /><br /> [Remessa](#ref-context-product)<br /><br /> [Pedido](#ref-context-request)<br /><br /> RequestId: GUID-identificador de solicitação exclusivo<br /><br /> [Resposta](#ref-context-response)<br /><br /> [Subscrição](#ref-context-subscription)<br /><br /> Timestamp: DateTime – ponto no tempo em que a solicitação foi recebida<br /><br /> Rastreamento: bool – indica se o rastreamento está ativado ou desativado <br /><br /> [Usuário](#ref-context-user)<br /><br /> [Variables](#ref-context-variables): IReadOnlyDictionary < string, > de objeto<br /><br /> Rastreamento void (mensagem: cadeia de caracteres)|
|<a id="ref-context-api"></a>noticioso. API|ID: cadeia de caracteres<br /><br /> IsCurrentRevision: bool<br /><br />  Nome: cadeia de caracteres<br /><br /> Caminho: cadeia de caracteres<br /><br /> Revisão: cadeia de caracteres<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versão: cadeia de caracteres |
|<a id="ref-context-deployment"></a>noticioso. Planta|Região: cadeia de caracteres<br /><br /> ServiceName: cadeia de caracteres<br /><br /> Certificados: IReadOnlyDictionary < cadeia de caracteres, X509Certificate2 >|
|<a id="ref-context-lasterror"></a>noticioso. LastError|Fonte: cadeia de caracteres<br /><br /> Motivo: cadeia de caracteres<br /><br /> Mensagem: cadeia de caracteres<br /><br /> Escopo: cadeia de caracteres<br /><br /> Seção: cadeia de caracteres<br /><br /> Caminho: cadeia de caracteres<br /><br /> PolicyId: cadeia de caracteres<br /><br /> Para obter mais informações sobre o contexto. LastError, consulte [tratamento de erros](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>noticioso. Operacional|ID: cadeia de caracteres<br /><br /> Método: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> UrlTemplate: cadeia de caracteres|
|<a id="ref-context-product"></a>noticioso. Remessa|APIs: IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Grupos: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> Estado: enum Productstate {canpublished, published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>noticioso. Quest|Corpo: [IMessageBody](#ref-imessagebody) ou `null` se a solicitação não tiver um corpo.<br /><br /> Certificado: System. Security. Cryptography. X509Certificates. X509Certificate2<br /><br /> [Cabeçalhos](#ref-context-request-headers): IReadOnlyDictionary < cadeia de caracteres, Cadeia de caracteres [] ><br /><br /> IpAddress: cadeia de caracteres<br /><br /> Matchable: IReadOnlyDictionary < String, String ><br /><br /> Método: cadeia de caracteres<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>contexto da cadeia de caracteres. Request. Headers. GetValueOrDefault (HeaderName: String, defaultValue: String)|HeaderName: cadeia de caracteres<br /><br /> defaultValue: cadeia de caracteres<br /><br /> Retorna valores de cabeçalho de solicitação separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-response"></a>noticioso. Responde|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Cabeçalhos](#ref-context-response-headers): IReadOnlyDictionary < cadeia de caracteres, Cadeia de caracteres [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: cadeia de caracteres|
|<a id="ref-context-response-headers"></a>contexto da cadeia de caracteres. Response. Headers. GetValueOrDefault (HeaderName: String, defaultValue: String)|HeaderName: cadeia de caracteres<br /><br /> defaultValue: cadeia de caracteres<br /><br /> Retorna valores de cabeçalho de resposta separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-subscription"></a>noticioso. Scriçõe|Createdtime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: cadeia de caracteres<br /><br /> Chave: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> PrimaryKey: cadeia de caracteres<br /><br /> SecondaryKey: cadeia de caracteres<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>noticioso. Usuário|Email: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> Grupos: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: cadeia de caracteres<br /><br /> Identidades: IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> Sobrenome: cadeia de caracteres<br /><br /> Observação: cadeia de caracteres<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> Caminho: cadeia de caracteres<br /><br /> Protocolos: IEnumerable < cadeia de caracteres @ no__t-0<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres|
|<a id="ref-imessagebody"></a>IMessageBody|Como < T @ no__t-0 (preserveContent: bool = false): em que T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os métodos `context.Request.Body.As<T>` e `context.Response.Body.As<T>` são usados para ler um corpo de mensagem de solicitação e resposta em um tipo especificado `T`. Por padrão, o método usa o fluxo do corpo da mensagem original e o torna indisponível depois de retornar. Para evitar que, ao fazer com que o método opere em uma cópia do fluxo do corpo, defina o parâmetro `preserveContent` como `true`. Acesse [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|
|<a id="ref-iurl"></a>IUrl|Host: cadeia de caracteres<br /><br /> Caminho: cadeia de caracteres<br /><br /> Porta: int<br /><br /> [Consulta](#ref-iurl-query): IReadOnlyDictionary < String, String [] ><br /><br /> QueryString: cadeia de caracteres<br /><br /> Esquema: cadeia de caracteres|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: cadeia de caracteres<br /><br /> Provedor: cadeia de caracteres|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Cabeçalho: cadeia de caracteres<br /><br /> Consulta: cadeia de caracteres|
|<a id="ref-iurl-query"></a>String IUrl. Query. GetValueOrDefault (queryParameterName: String, defaultValue: String)|queryParameterName: cadeia de caracteres<br /><br /> defaultValue: cadeia de caracteres<br /><br /> Retorna valores de parâmetros de consulta separados por vírgula ou `defaultValue` se o parâmetro não for encontrado.|
|<a id="ref-context-variables"></a>Contexto T. Variables. GetValueOrDefault < T @ no__t-1 (VariableName: String, defaultValue: T)|VariableName: cadeia de caracteres<br /><br /> defaultValue: T<br /><br /> Retorna o valor da variável CAST para o tipo `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Esse método gera uma exceção se o tipo especificado não corresponde ao tipo real da variável retornada.|
|BasicAuthCredentials asbasic (entrada: esta cadeia de caracteres)|entrada: cadeia de caracteres<br /><br /> Se o parâmetro de entrada contiver um valor válido de cabeçalho de solicitação de autorização de autenticação básica HTTP, o método retornará um objeto do tipo `BasicAuthCredentials`; caso contrário, o método retornará NULL.|
|bool TryParseBasic (entrada: esta cadeia de caracteres, resultado: out BasicAuthCredentials)|entrada: cadeia de caracteres<br /><br /> resultado: out BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de autenticação básica HTTP válido no cabeçalho de solicitação, o método retornará `true` e o parâmetro de resultado conterá um valor do tipo `BasicAuthCredentials`; caso contrário, o método retornará `false`.|
|BasicAuthCredentials|Senha: cadeia de caracteres<br /><br /> UserId: cadeia de caracteres|
|AsJwt JWT (entrada: esta cadeia de caracteres)|entrada: cadeia de caracteres<br /><br /> Se o parâmetro de entrada contiver um valor de token JWT válido, o método retornará um objeto do tipo `Jwt`; caso contrário, o método retornará `null`.|
|bool TryParseJwt (entrada: esta cadeia de caracteres, resultado: out JWT)|entrada: cadeia de caracteres<br /><br /> resultado: out JWT<br /><br /> Se o parâmetro de entrada contiver um valor de token JWT válido, o método retornará `true` e o parâmetro de resultado conterá um valor do tipo `Jwt`; caso contrário, o método retornará `false`.|
|JWT|Algoritmo: cadeia de caracteres<br /><br /> Audience: IEnumerable < cadeia de caracteres @ no__t-0<br /><br /> Claims: IReadOnlyDictionary < String, String [] ><br /><br /> Vencimento: DateTime?<br /><br /> ID: cadeia de caracteres<br /><br /> Emissor: cadeia de caracteres<br /><br /> IssuedAt: DateTime?<br /><br /> Não antes: DateTime?<br /><br /> Assunto: cadeia de caracteres<br /><br /> Tipo: cadeia de caracteres|
|String JWT. Claims. GetValueOrDefault (claimname: String, defaultValue: String)|claimname: cadeia de caracteres<br /><br /> defaultValue: cadeia de caracteres<br /><br /> Retorna valores de declaração separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|Byte [] criptografar (entrada: este byte [], alg: String, Key: byte [], IV: byte [])|entrada-texto sem formatação a ser criptografado<br /><br />ALG-nome de um algoritmo de criptografia simétrica<br /><br />chave de criptografia de chave<br /><br />IV-vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|Byte [] criptografar (entrada: este byte [], alg: System. Security. Cryptography. SymmetricAlgorithm)|entrada-texto sem formatação a ser criptografado<br /><br />ALG-algoritmo de criptografia<br /><br />Retorna texto sem formatação criptografado.|
|Byte [] criptografar (entrada: este byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, Key: byte [], IV: byte [])|entrada-texto sem formatação a ser criptografado<br /><br />ALG-algoritmo de criptografia<br /><br />chave de criptografia de chave<br /><br />IV-vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|Byte [] descriptografar (entrada: este byte [], alg: String, Key: byte [], IV: byte [])|entrada-texto criptografia a ser descriptografado<br /><br />ALG-nome de um algoritmo de criptografia simétrica<br /><br />chave de criptografia de chave<br /><br />IV-vetor de inicialização<br /><br />Retorna texto não criptografado.|
|Byte [] descriptografar (entrada: este byte [], alg: System. Security. Cryptography. SymmetricAlgorithm)|entrada-texto criptografia a ser descriptografado<br /><br />ALG-algoritmo de criptografia<br /><br />Retorna texto não criptografado.|
|Byte [] descriptografar (entrada: este byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, Key: byte [], IV: byte [])|entrada-texto criptografia a ser descriptografado<br /><br />ALG-algoritmo de criptografia<br /><br />chave de criptografia de chave<br /><br />IV-vetor de inicialização<br /><br />Retorna texto não criptografado.|
|bool VerifyNoRevocation (entrada: este System. Security. Cryptography. X509Certificates. X509Certificate2)|Executa uma validação de cadeia X. 509 sem verificar o status de revogação do certificado.<br /><br />objeto de entrada-certificado<br /><br />Retornará `true` se a validação for realizada com sucesso; `false` se a validação falhar.|


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no gerenciamento de API](api-management-howto-policies.md)
+ [APIs de transformação](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
