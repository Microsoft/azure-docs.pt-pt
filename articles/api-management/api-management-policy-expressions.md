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
ms.openlocfilehash: fa5e84ba62896969458b84cf014e2b35ee869df7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072178"
---
# <a name="api-management-policy-expressions"></a>Expressões de política de gerenciamento de API
Este artigo discute a sintaxe de expressões de C# política é 7. Cada expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) fornecida implicitamente e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos de .NET Framework.

Para obter mais informações:

- Consulte como fornecer informações de contexto para seu serviço de back-end. Use o [parâmetro definir cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) e defina as políticas de [cabeçalho http](api-management-transformation-policies.md#SetHTTPheader) para fornecer essas informações.
- Consulte como usar a política [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para autorizar o acesso a operações com base em declarações de token.
- Consulte como usar um rastreamento do [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) para ver como as políticas são avaliadas e os resultados dessas avaliações.
- Consulte como usar expressões com as políticas [obter do cache](api-management-caching-policies.md#GetFromCache) e [armazenar em cache](api-management-caching-policies.md#StoreToCache) para configurar o cache de resposta de gerenciamento de API. Defina uma duração que corresponda ao cache de resposta do serviço de back-end conforme especificado pela diretiva do `Cache-Control` serviço com suporte.
- Consulte como executar a filtragem de conteúdo. Remova os elementos de dados da resposta recebida do back-end usando o [fluxo de controle](api-management-advanced-policies.md#choose) e defina as políticas de [corpo](api-management-transformation-policies.md#SetBody) .
- Para baixar as instruções de política, consulte o repositório GitHub [API-Management-Samples/Policies](https://github.com/Azure/api-management-samples/tree/master/policies) .


## <a name="Syntax"></a>Sintaxe
Expressões de instrução única são incluídas `@(expression)`em, `expression` em que é uma instrução C# de expressão bem formada.

Expressões de várias instruções são colocadas em `@{expression}`. Todos os caminhos de código dentro de expressões de várias instruções devem `return` terminar com uma instrução.

## <a name="PolicyExpressionsExamples"></a>Disso

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

## <a name="PolicyExpressionsUsage"></a>Usos
As expressões podem ser usadas como valores de atributo ou valores de texto em qualquer [política](api-management-policies.md) de gerenciamento de API (a menos que a referência de política especifique o contrário).

> [!IMPORTANT]
> Quando você usa expressões de política, há apenas uma verificação limitada das expressões de política quando a política é definida. As expressões são executadas pelo gateway em tempo de execução, todas as exceções geradas por expressões de política resultam em um erro de tempo de execução.

## <a name="CLRTypes"></a>Tipos de .NET Framework permitidos em expressões de política
A tabela a seguir lista os tipos de .NET Framework e seus membros que são permitidos em expressões de política.

|Type|Membros com suporte|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Todos|
|Newtonsoft.Json.JsonConvert|SerializeObject, desserializaobject|
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
|System. array|Todos|
|System.BitConverter|Todos|
|System.Boolean|Todos|
|System.Byte|Todos|
|System.Char|Todos|
|System. Collections. Generic. Dictionary < TKey, TValue >|Todos|
|System.Collections.Generic.HashSet\<T>|Todos|
|System.Collections.Generic.ICollection\<T>|Todos|
|System. Collections. Generic. IDictionary < TKey, TValue >|Todos|
|System.Collections.Generic.IEnumerable\<T>|Todos|
|System.Collections.Generic.IEnumerator\<T>|Todos|
|System.Collections.Generic.IList\<T>|Todos|
|System.Collections.Generic.IReadOnlyCollection\<T>|Todos|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Todos|
|System.Collections.Generic.ISet\<T>|Todos|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Todos|
|System.Collections.Generic.List\<T>|Todos|
|System.Collections.Generic.Queue\<T>|Todos|
|System.Collections.Generic.Stack\<T>|Todos|
|System.Convert|Todos|
|System.DateTime|(Construtor), adicionar, subdias, addhora, addmilésimos de segundo, addminutos, addmeses, adicionar, submarcações, AddYears, data, Day, DayOfWeek, DayOfYear, DaysInMonth, hora, IsDaylightSavingTime, IsLeapYear, MaxValue, milissegundo, minuto, MinValue, mês, agora , Análise, segundo, subtrair, tiques, TimeOfDay, hoje, ToString, UtcNow, year|
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
|System.Text.RegularExpressions.Capture|Índice, comprimento, valor|
|System.Text.RegularExpressions.CaptureCollection|Contagem, item|
|System.Text.RegularExpressions.Group|Capturas, êxito|
|System.Text.RegularExpressions.GroupCollection|Contagem, item|
|System.Text.RegularExpressions.Match|Vazio, grupos, resultado|
|System.Text.RegularExpressions.Regex|(Construtor), IsMatch, corresponder, corresponder, substituir, cancelar escape, dividir|
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
|System.Xml.Linq.XDocument|Todos, exceto de: Carregamento|
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

## <a name="ContextVariables"></a>Variável de contexto
Uma variável chamada `context` está implicitamente disponível em cada [expressão](api-management-policy-expressions.md#Syntax)de política. Seus membros fornecem informações pertinentes ao `\request`. Todos os membros são somente leitura. `context`

|Variável de contexto|Métodos, propriedades e valores de parâmetro permitidos|
|----------------------|-------------------------------------------------------|
|noticioso|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementação](#ref-context-deployment)<br /><br /> Decorrido TimeSpan-intervalo de tempo entre o valor de timestamp e a hora atual<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operação](#ref-context-operation)<br /><br /> [Remessa](#ref-context-product)<br /><br /> [Pedido](#ref-context-request)<br /><br /> RequestId GUID – identificador de solicitação exclusivo<br /><br /> [Resposta](#ref-context-response)<br /><br /> [Subscrição](#ref-context-subscription)<br /><br /> Estampa DateTime – ponto no tempo em que a solicitação foi recebida<br /><br /> Rastreamento: bool – indica se o rastreamento está ativado ou desativado <br /><br /> [Usuário](#ref-context-user)<br /><br /> [Variáveis](#ref-context-variables): Cadeia de < IReadOnlyDictionary, objeto ><br /><br /> Rastreamento void (mensagem: cadeia de caracteres)|
|<a id="ref-context-api"></a>context.Api|ID: cadeia de caracteres<br /><br /> IsCurrentRevision: bool<br /><br />  Nome: cadeia de caracteres<br /><br /> Caminho: cadeia de caracteres<br /><br /> Revisão: cadeia de caracteres<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versão: cadeia de caracteres |
|<a id="ref-context-deployment"></a>context.Deployment|Região: cadeia de caracteres<br /><br /> ServiceName: string<br /><br /> Certificado IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Fonte: cadeia de caracteres<br /><br /> Motivo: cadeia de caracteres<br /><br /> Mensagem: cadeia de caracteres<br /><br /> Escopo: cadeia de caracteres<br /><br /> Seção: cadeia de caracteres<br /><br /> Caminho: cadeia de caracteres<br /><br /> PolicyId: cadeia de caracteres<br /><br /> Para obter mais informações sobre o contexto. LastError, consulte [tratamento de erros](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>noticioso. Operacional|ID: cadeia de caracteres<br /><br /> Método: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|API IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Agrupa IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> Estado: enum Productstate {canpublished, published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>noticioso. Quest|Conteúdo [IMessageBody](#ref-imessagebody) ou `null` se a solicitação não tiver um corpo.<br /><br /> Certificate System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Cabeçalhos](#ref-context-request-headers): IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> Rematchparameters: IReadOnlyDictionary<string, string><br /><br /> Método: cadeia de caracteres<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: cadeia de caracteres<br /><br /> Retorna valores de cabeçalho de solicitação separados por `defaultValue` vírgula ou se o cabeçalho não for encontrado.|
|<a id="ref-context-response"></a>noticioso. Responde|Conteúdo [IMessageBody](#ref-imessagebody)<br /><br /> [Cabeçalhos](#ref-context-response-headers): IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: cadeia de caracteres<br /><br /> Retorna valores de cabeçalho de resposta separados por `defaultValue` vírgula ou se o cabeçalho não for encontrado.|
|<a id="ref-context-subscription"></a>noticioso. Scriçõe|CreatedTime DateTime<br /><br /> Término Horário?<br /><br /> ID: cadeia de caracteres<br /><br /> Chave: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> PrimaryKey: cadeia de caracteres<br /><br /> SecondaryKey: cadeia de caracteres<br /><br /> Início Horário?|
|<a id="ref-context-user"></a>noticioso. Usuário|Email: cadeia de caracteres<br /><br /> FirstName: string<br /><br /> Agrupa IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: cadeia de caracteres<br /><br /> Identities IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> Sobrenome: cadeia de caracteres<br /><br /> Observação: cadeia de caracteres<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres<br /><br /> Caminho: cadeia de caracteres<br /><br /> Protocolos IEnumerable < cadeia de caracteres\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: cadeia de caracteres<br /><br /> Nome: cadeia de caracteres|
|<a id="ref-imessagebody"></a>IMessageBody|Como < T\>(preserveContent: bool = false): Em que T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os `context.Request.Body.As<T>` métodos `context.Response.Body.As<T>` e são usados para ler um corpo de mensagem de solicitação e resposta em um `T`tipo especificado. Por padrão, o método usa o fluxo do corpo da mensagem original e o torna indisponível depois de retornar. Para evitar isso fazendo com que o método opere em uma cópia do fluxo do corpo, defina `preserveContent` o parâmetro `true`como. Acesse [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|
|<a id="ref-iurl"></a>IUrl|Host: cadeia de caracteres<br /><br /> Caminho: cadeia de caracteres<br /><br /> Porta: int<br /><br /> [Consulta](#ref-iurl-query): IReadOnlyDictionary<string, string[]><br /><br /> QueryString: cadeia de caracteres<br /><br /> Esquema: cadeia de caracteres|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: cadeia de caracteres<br /><br /> Provedor: cadeia de caracteres|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Cabeçalho: cadeia de caracteres<br /><br /> Consulta: cadeia de caracteres|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: cadeia de caracteres<br /><br /> Retorna valores de parâmetros de consulta separados por `defaultValue` vírgula ou se o parâmetro não for encontrado.|
|<a id="ref-context-variables"></a>T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> ValorPadrão T<br /><br /> Retorna o valor da variável Cast `T` para `defaultValue` o tipo ou se a variável não for encontrada.<br /><br /> Esse método gera uma exceção se o tipo especificado não corresponde ao tipo real da variável retornada.|
|BasicAuthCredentials asbasic (entrada: esta cadeia de caracteres)|entrada: cadeia de caracteres<br /><br /> Se o parâmetro de entrada contiver um valor válido de cabeçalho de solicitação de autorização de autenticação básica http, o `BasicAuthCredentials`método retornará um objeto do tipo; caso contrário, o método retornará NULL.|
|bool TryParseBasic (entrada: esta cadeia de caracteres, resultado: out BasicAuthCredentials)|entrada: cadeia de caracteres<br /><br /> resultado: out BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de autenticação básica http válido no cabeçalho de `true` solicitação, o método retornará e o parâmetro de `BasicAuthCredentials`resultado conterá um valor `false`do tipo; caso contrário, o método retornará.|
|BasicAuthCredentials|Senha: cadeia de caracteres<br /><br /> UserId: string|
|AsJwt JWT (entrada: esta cadeia de caracteres)|entrada: cadeia de caracteres<br /><br /> Se o parâmetro de entrada contiver um valor de token JWT válido, o método retornará um `Jwt`objeto do tipo; caso `null`contrário, o método retornará.|
|bool TryParseJwt (entrada: esta cadeia de caracteres, resultado: out JWT)|entrada: cadeia de caracteres<br /><br /> resultado: out JWT<br /><br /> Se o parâmetro de entrada contiver um valor de token JWT válido, `true` o método retornará e o parâmetro de resultado conterá um valor do tipo `false` `Jwt`; caso contrário, o método retornará.|
|Jwt|Algoritmo: cadeia de caracteres<br /><br /> Platéia IEnumerable < cadeia de caracteres\><br /><br /> Declarações IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime Horário?<br /><br /> ID: cadeia de caracteres<br /><br /> Emissor: cadeia de caracteres<br /><br /> IssuedAt Horário?<br /><br /> NotBefore Horário?<br /><br /> Assunto: cadeia de caracteres<br /><br /> Tipo: cadeia de caracteres|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: cadeia de caracteres<br /><br /> Retorna valores de declaração separados por vírgula `defaultValue` ou se o cabeçalho não for encontrado.|
|Byte [] criptografar (entrada: este byte [], alg: String, Key: byte [], IV: byte [])|entrada-texto sem formatação a ser criptografado<br /><br />ALG-nome de um algoritmo de criptografia simétrica<br /><br />chave de criptografia de chave<br /><br />IV-vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|Byte [] criptografar (entrada: este byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada-texto sem formatação a ser criptografado<br /><br />ALG-algoritmo de criptografia<br /><br />Retorna texto sem formatação criptografado.|
|Byte [] criptografar (entrada: este byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, chave: byte [], IV: byte [])|entrada-texto sem formatação a ser criptografado<br /><br />ALG-algoritmo de criptografia<br /><br />chave de criptografia de chave<br /><br />IV-vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|entrada-texto criptografia a ser descriptografado<br /><br />ALG-nome de um algoritmo de criptografia simétrica<br /><br />chave de criptografia de chave<br /><br />IV-vetor de inicialização<br /><br />Retorna texto não criptografado.|
|Byte [] descriptografar (entrada: este byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada-texto criptografia a ser descriptografado<br /><br />ALG-algoritmo de criptografia<br /><br />Retorna texto não criptografado.|
|Byte [] descriptografar (entrada: este byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, chave: byte [], IV: byte [])|entrada-texto criptografia a ser descriptografado<br /><br />ALG-algoritmo de criptografia<br /><br />chave de criptografia de chave<br /><br />IV-vetor de inicialização<br /><br />Retorna texto não criptografado.|
|bool VerifyNoRevocation (entrada: este System. Security. Cryptography. X509Certificates. X509Certificate2)|Executa uma validação de cadeia X. 509 sem verificar o status de revogação do certificado.<br /><br />objeto de entrada-certificado<br /><br />Retorna `true` se a validação for realizada com sucesso; `false` se a validação falhar.|


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no gerenciamento de API](api-management-howto-policies.md)
+ [APIs de transformação](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
