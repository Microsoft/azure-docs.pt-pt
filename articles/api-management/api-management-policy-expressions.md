---
title: Expressões de política de gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre expressões de política na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 90b2dfdbec0d6dc81a05b845832fda92fe36d98c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403096"
---
# <a name="api-management-policy-expressions"></a>Expressões de política de gestão de API
Este artigo aborda as expressões de diretriz sintaxe é C# 7. Cada expressão tem acesso ao implicitamente fornecido [contexto](api-management-policy-expressions.md#ContextVariables) variável e um permitidos [subconjunto](api-management-policy-expressions.md#CLRTypes) de tipos do .NET Framework.

Para obter mais informações:

- Veja como fornecer informações de contexto ao seu serviço de back-end. Utilize o [defina o parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) e [cabeçalho de HTTP definir](api-management-transformation-policies.md#SetHTTPheader) políticas para fornecer essas informações.
- Veja como usar o [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) política para autorizar previamente o acesso às operações com base nas afirmações de token.
- Veja como usar um [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) rastreio para ver como são avaliadas as políticas e os resultados de avaliações desses.
- Veja como usar expressões com o [obter a partir do cache](api-management-caching-policies.md#GetFromCache) e [Store à cache](api-management-caching-policies.md#StoreToCache) políticas para configurar a cache de resposta de gestão de API. Definir um período de tempo que corresponde à cache de resposta do serviço de back-end conforme especificado pelo serviço de segurança `Cache-Control` diretiva.
- Veja como realizar a filtragem de conteúdo. Remover elementos de dados da resposta recebida do back-end com o [controlar o fluxo](api-management-advanced-policies.md#choose) e [definir corpo](api-management-transformation-policies.md#SetBody) políticas.
- Para transferir as instruções de políticas, consulte a [amostras/políticas de api-gestão](https://github.com/Azure/api-management-samples/tree/master/policies) repositório do GitHub.


## <a name="Syntax"></a> Sintaxe
Expressões de instrução única encontram-se entre `@(expression)`, onde `expression` é bem formado C# declaração da expressão.

Expressões com múltiplas instruções estão incluídas no `@{expression}`. Todos os caminhos de código nas expressões de várias declarações tem de terminar com um `return` instrução.

## <a name="PolicyExpressionsExamples"></a> Exemplos

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));
  }
  else
  {
    return null;
  }
}
```

## <a name="PolicyExpressionsUsage"></a>Utilização
Expressões podem ser utilizadas como valores de atributo ou valores de texto em qualquer gestão de API [políticas](api-management-policies.md) (a menos que a referência de política especifique o contrário).

> [!IMPORTANT]
> Quando utilizar expressões de política, há apenas limitada verificação das expressões de política quando a política estiver definida. As expressões são executadas pelo gateway em tempo de execução, todas as exceções geradas pelo resultado de expressões de política num erro de tempo de execução.

## <a name="CLRTypes"></a> Tipos do .NET framework permitidos em expressões de política
A tabela seguinte lista os tipos do .NET Framework e seus membros que são permitidos em expressões de política.

|Type|Membros suportados|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Todos|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Todos|
|Newtonsoft.Json.Linq.JArray|Todos|
|Newtonsoft.Json.Linq.JConstructor|Todos|
|Newtonsoft.Json.Linq.JContainer|Todos|
|Newtonsoft.Json.Linq.JObject|Todos|
|Newtonsoft.Json.Linq.JProperty|Todos|
|Newtonsoft.Json.Linq.JRaw|Todos|
|Newtonsoft.Json.Linq.JToken|Todos|
|Newtonsoft.Json.Linq.JTokenType|Todos|
|Newtonsoft.Json.Linq.JValue|Todos|
|System.Array|Todos|
|System.BitConverter|Todos|
|System.Boolean|Todos|
|System.Byte|Todos|
|System.Char|Todos|
|System.Collections.Generic.Dictionary<TKey, TValue>|Todos|
|System.Collections.Generic.HashSet<T>|Todos|
|System.Collections.Generic.ICollection<T>|Todos|
|System.Collections.Generic.IDictionary<TKey, TValue>|Todos|
|System.Collections.Generic.IEnumerable<T>|Todos|
|System.Collections.Generic.IEnumerator<T>|Todos|
|System.Collections.Generic.IList<T>|Todos|
|System.Collections.Generic.IReadOnlyCollection<T>|Todos|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Todos|
|System.Collections.Generic.ISet<T>|Todos|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Todos|
|System.Collections.Generic.List<T>|Todos|
|System.Collections.Generic.Queue<T>|Todos|
|System.Collections.Generic.Stack<T>|Todos|
|System.Convert|Todos|
|System.DateTime|(Construtor), adicionar, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, data, dia, DayOfWeek, DayOfYear, DaysInMonth, hora, IsDaylightSavingTime, IsLeapYear, MaxValue, milissegundo, minuto, MinValue, mês, agora , Análise, em segundo lugar, subtrair, Ticks, TimeOfDay, hoje, ToString, UtcNow, ano|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Todos|
|System.Decimal|Todos|
|System.Double|Todos|
|System.Exception|Todos|
|System.Guid|Todos|
|System.Int16|Todos|
|System.Int32|Todos|
|System.Int64|Todos|
|System.IO.StringReader|Todos|
|System.IO.StringWriter|Todos|
|System.Linq.Enumerable|Todos|
|System.Math|Todos|
|System.MidpointRounding|Todos|
|System.Net.WebUtility|Todos|
|System.Nullable|Todos|
|System.Random|Todos|
|System.SByte|Todos|
|System.Security.Cryptography.AsymmetricAlgorithm|Todos|
|System.Security.Cryptography.CipherMode|Todos|
|System.Security.Cryptography.HashAlgorithm|Todos|
|System.Security.Cryptography.HashAlgorithmName|Todos|
|System.Security.Cryptography.HMAC|Todos|
|System.Security.Cryptography.HMACMD5|Todos|
|System.Security.Cryptography.HMACSHA1|Todos|
|System.Security.Cryptography.HMACSHA256|Todos|
|System.Security.Cryptography.HMACSHA384|Todos|
|System.Security.Cryptography.HMACSHA512|Todos|
|System.Security.Cryptography.KeyedHashAlgorithm|Todos|
|System.Security.Cryptography.MD5|Todos|
|System.Security.Cryptography.Oid|Todos|
|System.Security.Cryptography.PaddingMode|Todos|
|System.Security.Cryptography.RNGCryptoServiceProvider|Todos|
|System.Security.Cryptography.RSA|Todos|
|System.Security.Cryptography.RSAEncryptionPadding|Todos|
|System.Security.Cryptography.RSASignaturePadding|Todos|
|System.Security.Cryptography.SHA1|Todos|
|System.Security.Cryptography.SHA1Managed|Todos|
|System.Security.Cryptography.SHA256|Todos|
|System.Security.Cryptography.SHA256Managed|Todos|
|System.Security.Cryptography.SHA384|Todos|
|System.Security.Cryptography.SHA384Managed|Todos|
|System.Security.Cryptography.SHA512|Todos|
|System.Security.Cryptography.SHA512Managed|Todos|
|System.Security.Cryptography.SymmetricAlgorithm|Todos|
|System.Security.Cryptography.X509Certificates.PublicKey|Todos|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Todos|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name|
|System.Security.Cryptography.X509Certificates.X509Certificate|Todos|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Todos|
|System.Security.Cryptography.X509Certificates.X509ContentType|Todos|
|System.Security.Cryptography.X509Certificates.X509NameType|Todos|
|System.Single|Todos|
|System.String|Todos|
|System.StringComparer|Todos|
|System.StringComparison|Todos|
|System.StringSplitOptions|Todos|
|System.Text.Encoding|Todos|
|System.Text.RegularExpressions.Capture|Valor de índice, comprimento,|
|System.Text.RegularExpressions.CaptureCollection|Contagem de Item|
|System.Text.RegularExpressions.Group|Capturas de sucesso|
|System.Text.RegularExpressions.GroupCollection|Contagem de Item|
|System.Text.RegularExpressions.Match|Resultado vazio, grupos,|
|System.Text.RegularExpressions.Regex|(Construtor), IsMatch, correspondência, correspondências, substituir, Unescape, dividida|
|System.Text.RegularExpressions.RegexOptions|Todos|
|System.Text.StringBuilder|Todos|
|System.TimeSpan|Todos|
|System.TimeZone|Todos|
|System.TimeZoneInfo.AdjustmentRule|Todos|
|System.TimeZoneInfo.TransitionTime|Todos|
|System.TimeZoneInfo|Todos|
|System.Tuple|Todos|
|System.UInt16|Todos|
|System.UInt32|Todos|
|System.UInt64|Todos|
|System.Uri|Todos|
|System.UriPartial|Todos|
|System.Xml.Linq.Extensions|Todos|
|System.Xml.Linq.XAttribute|Todos|
|System.Xml.Linq.XCData|Todos|
|System.Xml.Linq.XComment|Todos|
|System.Xml.Linq.XContainer|Todos|
|System.Xml.Linq.XDeclaration|Todos|
|System.Xml.Linq.XDocument|Todos, exceto de: Carregar|
|System.Xml.Linq.XDocumentType|Todos|
|System.Xml.Linq.XElement|Todos|
|System.Xml.Linq.XName|Todos|
|System.Xml.Linq.XNamespace|Todos|
|System.Xml.Linq.XNode|Todos|
|System.Xml.Linq.XNodeDocumentOrderComparer|Todos|
|System.Xml.Linq.XNodeEqualityComparer|Todos|
|System.Xml.Linq.XObject|Todos|
|System.Xml.Linq.XProcessingInstruction|Todos|
|System.Xml.Linq.XText|Todos|
|System.Xml.XmlNodeType|Todos|

## <a name="ContextVariables"></a> Variável de contexto
Uma variável chamada `context` implicitamente disponível em cada política [expressão](api-management-policy-expressions.md#Syntax). Seus membros fornecem informações pertinentes para o `\request`. Todos os `context` membros são só de leitura.

|Variável de contexto|Permissão de métodos, propriedades e valores de parâmetros|
|----------------------|-------------------------------------------------------|
|context|Api: IApi<br /><br /> Implementação<br /><br /> Decorrido: Período de tempo - intervalo de tempo entre o valor de Timestamp e a hora atual<br /><br /> LastError<br /><br /> Operação<br /><br /> Produto<br /><br /> Pedir<br /><br /> RequestId: GUID - identificador exclusivo do pedido<br /><br /> Resposta<br /><br /> Subscrição<br /><br /> Carimbo de data/hora: DateTime - ponto anterior no tempo quando o pedido foi recebido<br /><br /> Rastreio: bool - indica se o rastreio está ativada ou desativada <br /><br /> Utilizador<br /><br /> Variáveis: IReadOnlyDictionary < string, object ><br /><br /> void Trace(message: string)|
|context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revisão: cadeia<br /><br /> ServiceUrl: IUrl<br /><br /> Versão: cadeia |
|context.Deployment|Região: cadeia<br /><br /> ServiceName: string<br /><br /> Certificados: IReadOnlyDictionary<string, X509Certificate2>|
|context.LastError|Origem: cadeia<br /><br /> Reason: string<br /><br /> Mensagem: cadeia<br /><br /> Âmbito: cadeia<br /><br /> Secção: cadeia<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Para obter mais informações sobre o contexto. LastError, consulte [tratamento de erros](api-management-error-handling-policies.md).|
|context.Operation|Id: string<br /><br /> Método: cadeia<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|context.Product|Apis: IEnumerable < IApi\><br /><br /> ApprovalRequired: booleano<br /><br /> Grupos: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> Estado: enumeração ProductState {NotPublished, publicado}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: booleano|
|context.Request|Corpo: IMessageBody<br /><br /> Certificado: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Cabeçalhos: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Método: cadeia<br /><br /> OriginalUrl:IUrl<br /><br /> URL: IUrl|
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Devolve os valores de cabeçalho de pedido separada por vírgulas ou `defaultValue` se não for encontrado no cabeçalho.|
|context.Response|Corpo: IMessageBody<br /><br /> Cabeçalhos: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Devolve os valores de cabeçalho de resposta separados por vírgulas ou `defaultValue` se não for encontrado no cabeçalho.|
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Chave: cadeia<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Grupos: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identidades: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Nota: cadeia<br /><br /> RegistrationDate: DateTime|
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocolos: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|
|IGroup|Id: string<br /><br /> Name: string|
|IMessageBody|As<T\>(preserveContent: bool = false): Em que t: cadeia de caracteres, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> O `context.Request.Body.As<T>` e `context.Response.Body.As<T>` métodos são utilizados para ler uma solicitação e resposta corpos das mensagens num tipo especificado `T`. Por predefinição, o método usa o fluxo de corpo de mensagem original e processa indisponível após ela retornar. Para evitar que fazendo com que o método operam numa cópia do fluxo de corpo, defina o `preserveContent` parâmetro `true`. Vá [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Porta: int<br /><br /> Consulta: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|
|IUserIdentity|Id: string<br /><br /> Fornecedor: cadeia|
|ISubscriptionKeyParameterNames|Cabeçalho: cadeia<br /><br /> Query: string|
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Devolve os valores de parâmetros de consulta separados por vírgulas ou `defaultValue` se o parâmetro não for encontrado.|
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Devolve o valor da variável convertido para o tipo `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Esse método lança uma exceção se o tipo especificado não coincide com o tipo real da variável retornado.|
|BasicAuthCredentials AsBasic(input: this string)|entrada: cadeia<br /><br /> Se o parâmetro de entrada contém um valor de cabeçalho do pedido de autorização de autenticação básica HTTP válido, o método retorna um objeto do tipo `BasicAuthCredentials`; caso contrário, o método devolverá um nulo.|
|bool TryParseBasic (entrada: Esta cadeia de caracteres, o resultado: horizontalmente BasicAuthCredentials)|entrada: cadeia<br /><br /> resultado: horizontalmente BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contém um valor válido para a autorização de autenticação básica HTTP no cabeçalho do pedido o método retorna `true` e o parâmetro de resultado contém um valor do tipo `BasicAuthCredentials`; caso contrário, o método retorna `false`.|
|BasicAuthCredentials|Palavra-passe: cadeia<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|entrada: cadeia<br /><br /> Se o parâmetro de entrada contém um valor de token JWT válido, o método retorna um objeto do tipo `Jwt`; caso contrário, o método retorna `null`.|
|bool TryParseJwt (entrada: Esta cadeia de caracteres, o resultado: horizontalmente Jwt)|entrada: cadeia<br /><br /> resultado: horizontalmente Jwt<br /><br /> Se o parâmetro de entrada contém um valor de token JWT válido, o método retorna `true` e o parâmetro de resultado contém um valor do tipo `Jwt`; caso contrário, o método retorna `false`.|
|Jwt|Algorithm: string<br /><br /> Público-alvo: IEnumerable<string\><br /><br /> Afirmações: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Emissor: cadeia<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Assunto: cadeia<br /><br /> Type: string|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Devolve separados por vírgulas de valores de afirmação ou `defaultValue` se não for encontrado no cabeçalho.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|introdução - texto sem formatação seja encriptado<br /><br />alg - nome de um algoritmo de criptografia simétrica<br /><br />chave - chave de encriptação<br /><br />IV - vetor de inicialização<br /><br />Devolve texto não encriptado.|
|byte [] Encrypt (entrada: este byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|introdução - texto sem formatação seja encriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devolve texto não encriptado.|
|byte [] Encrypt (entrada: este byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|introdução - texto sem formatação seja encriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />IV - vetor de inicialização<br /><br />Devolve texto não encriptado.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|introdução - texto de cifras para ser desencriptada<br /><br />alg - nome de um algoritmo de criptografia simétrica<br /><br />chave - chave de encriptação<br /><br />IV - vetor de inicialização<br /><br />Devolve texto sem formatação.|
|byte [] desencriptação (entrada: este byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|introdução - texto de cifras para ser desencriptada<br /><br />alg - algoritmo de encriptação<br /><br />Devolve texto sem formatação.|
|byte [] desencriptação (entrada: este byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|texto de entrada cifras - entrada - para ser desencriptada<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />IV - vetor de inicialização<br /><br />Devolve texto sem formatação.|


## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações para trabalhar com políticas, consulte:

+ [Políticas de gestão de API](api-management-howto-policies.md)
+ [Transforme as APIs](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
+ [Exemplos de política](policy-samples.md)
