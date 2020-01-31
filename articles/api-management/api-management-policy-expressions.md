---
title: Expressões políticas de gestão da API azure  Microsoft Docs
description: Conheça as expressões políticas na Azure API Management.
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
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845287"
---
# <a name="api-management-policy-expressions"></a>Expressões políticas de gestão da API
Este artigo discute a sintaxe de expressões políticas em C# 7. Cada expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) implicitamente fornecida e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos de .NET- Quadro.

Para mais informações:

- Veja como fornecer informações de contexto ao seu serviço de backend. Utilize o parâmetro de corda de [consulta definido](api-management-transformation-policies.md#SetQueryStringParameter) e defina as políticas de [cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) para fornecer esta informação.
- Consulte como utilizar a política [De validação jWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar o acesso a operações com base em alegações simbólicas.
- Veja como utilizar um rastreio do [Inspetor DaPI](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) para ver como as políticas são avaliadas e os resultados dessas avaliações.
- Veja como usar expressões com o [Get de cache](api-management-caching-policies.md#GetFromCache) e loja para políticas de [cache](api-management-caching-policies.md#StoreToCache) para configurar o cache de resposta de gestão de API. Estabeleça uma duração que corresponda ao cache de resposta do serviço de backend, conforme especificado pela diretiva `Cache-Control` do serviço apoiado.
- Veja como efetuar a filtragem de conteúdo. Remova os elementos de dados da resposta recebida da parte de trás utilizando as políticas de fluxo de [controlo](api-management-advanced-policies.md#choose) e [conjunto do corpo.](api-management-transformation-policies.md#SetBody)
- Para descarregar as declarações políticas, consulte o repo gitHub [api-management/policies.](https://github.com/Azure/api-management-samples/tree/master/policies)


## <a name="Syntax"></a>Sintaxe
As expressões de declaração única são encerradas em C# `@(expression)`, onde `expression` é uma declaração de expressão bem formada.

As expressões multi-afirmações são encerradas em `@{expression}`. Todos os caminhos de código dentro de expressões multi-afirmações devem terminar com uma declaração `return`.

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

## <a name="PolicyExpressionsUsage"></a>Utilização
As expressões podem ser usadas como valores de atributoou valores de texto em quaisquer [políticas](api-management-policies.md) de Gestão da API (a menos que a referência política especifique o contrário).

> [!IMPORTANT]
> Quando se utilizam expressões políticas, só há uma verificação limitada das expressões políticas quando a política é definida. As expressões são executadas pelo portal em tempo de execução, quaisquer exceções geradas por expressões políticas resultam num erro de tempo de execução.

## <a name="CLRTypes"></a>.NET Tipos-quadro permitidos em expressões políticas
O quadro seguinte enumera os tipos de quadro .NET e os seus membros que são permitidos nas expressões políticas.

|Tipo|Membros apoiados|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Tudo|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Tudo|
|Newtonsoft.Json.Linq.JArray|Tudo|
|Newtonsoft.Json.Linq.JConstructor|Tudo|
|Newtonsoft.Json.Linq.JContainer|Tudo|
|Newtonsoft.Json.Linq.JObject|Tudo|
|Newtonsoft.Json.Linq.JProperty|Tudo|
|Newtonsoft.Json.Linq.JRaw|Tudo|
|Newtonsoft.Json.Linq.JToken|Tudo|
|Newtonsoft.Json.Linq.JTokenType|Tudo|
|Newtonsoft.Json.Linq.JValue|Tudo|
|Sistema.Matriz|Tudo|
|System.BitConverter|Tudo|
|System.Boolean|Tudo|
|System.Byte|Tudo|
|System.Char|Tudo|
|System.Collections.Generic.Dictionary<TKey, TValue>|Tudo|
|System.Collections.Generic.HashSet\<T>|Tudo|
|System.Collections.Generic.ICollection\<T>|Tudo|
|System.Collections.Generic.IDictionary<TKey, TValue>|Tudo|
|System.Collections.Generic.IEnumerable\<T>|Tudo|
|System.Collections.Generic.IEnumerator\<T>|Tudo|
|System.Collections.Generic.IList\<T>|Tudo|
|System.Collections.Generic.IReadOnlyCollection\<T>|Tudo|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Tudo|
|System.Collections.Generic.ISet\<T>|Tudo|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Tudo|
|System.Collections.Generic.List\<T>|Tudo|
|System.Collections.Generic.Queue\<T>|Tudo|
|System.Collections.Generic.Stack\<T>|Tudo|
|System.Convert|Tudo|
|System.DateTime|(Construtor), AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now , Parse, Second, Subtract, Ticks, TimeOfDay, Today, ToString, UtcNow, Year|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Tudo|
|System.Decimal|Tudo|
|System.Double|Tudo|
|System.Exception|Tudo|
|System.Guid|Tudo|
|System.Int16|Tudo|
|System.Int32|Tudo|
|System.Int64|Tudo|
|System.IO.StringReader|Tudo|
|System.IO.StringWriter|Tudo|
|System.Linq.Enumerable|Tudo|
|System.Math|Tudo|
|System.MidpointRounding|Tudo|
|System.Net.WebUtility|Tudo|
|System.Nullable|Tudo|
|System.Random|Tudo|
|System.SByte|Tudo|
|System.Security.Cryptography.AsymmetricAlgorithm|Tudo|
|System.Security.Cryptography.CipherMode|Tudo|
|System.Security.Cryptography.HashAlgorithm|Tudo|
|System.Security.Cryptography.HashAlgorithmName|Tudo|
|System.Security.Cryptography.HMAC|Tudo|
|System.Security.Cryptography.HMACMD5|Tudo|
|System.Security.Cryptography.HMACSHA1|Tudo|
|System.Security.Cryptography.HMACSHA256|Tudo|
|System.Security.Cryptography.HMACSHA384|Tudo|
|System.Security.Cryptography.HMACSHA512|Tudo|
|System.Security.Cryptography.KeyedHashAlgorithm|Tudo|
|System.Security.Cryptography.MD5|Tudo|
|System.Security.Cryptography.Oid|Tudo|
|System.Security.Cryptography.PaddingMode|Tudo|
|System.Security.Cryptography.RNGCryptoServiceProvider|Tudo|
|System.Security.Cryptography.RSA|Tudo|
|System.Security.Cryptography.RSAEncryptionPadding|Tudo|
|System.Security.Cryptography.RSASignaturePadding|Tudo|
|System.Security.Cryptography.SHA1|Tudo|
|System.Security.Cryptography.SHA1Managed|Tudo|
|System.Security.Cryptography.SHA256|Tudo|
|System.Security.Cryptography.SHA256Managed|Tudo|
|System.Security.Cryptography.SHA384|Tudo|
|System.Security.Cryptography.SHA384Managed|Tudo|
|System.Security.Cryptography.SHA512|Tudo|
|System.Security.Cryptography.SHA512Managed|Tudo|
|System.Security.Cryptography.SymmetricAlgorithm|Tudo|
|System.Security.Cryptography.X509Certificates.PublicKey|Tudo|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Tudo|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Nome|
|System.Security.Cryptography.X509Certificates.X509Certificate|Tudo|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Tudo|
|System.Security.Cryptography.X509Certificates.X509ContentType|Tudo|
|System.Security.Cryptography.X509Certificates.X509NameType|Tudo|
|System.Single|Tudo|
|System.String|Tudo|
|System.StringComparer|Tudo|
|System.StringComparison|Tudo|
|System.StringSplitOptions|Tudo|
|System.Text.Encoding|Tudo|
|System.Text.RegularExpressions.Capture|Índice, Comprimento, Valor|
|System.Text.RegularExpressions.CaptureCollection|Contagem, Item|
|System.Text.RegularExpressions.Group|Capturas, Sucesso|
|System.Text.RegularExpressions.GroupCollection|Contagem, Item|
|System.Text.RegularExpressions.Match|Vazio, Grupos, Resultado|
|System.Text.RegularExpressions.Regex|(Construtor), IsMatch, Match, Match, Match, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Tudo|
|System.Text.StringBuilder|Tudo|
|System.TimeSpan|Tudo|
|System.TimeZone|Tudo|
|System.TimeZoneInfo.AdjustmentRule|Tudo|
|System.TimeZoneInfo.TransitionTime|Tudo|
|System.TimeZoneInfo|Tudo|
|System.Tuple|Tudo|
|System.UInt16|Tudo|
|System.UInt32|Tudo|
|System.UInt64|Tudo|
|System.Uri|Tudo|
|System.UriPartial|Tudo|
|System.Xml.Linq.Extensions|Tudo|
|System.Xml.Linq.XAttribute|Tudo|
|System.Xml.Linq.XCData|Tudo|
|System.Xml.Linq.XComment|Tudo|
|System.Xml.Linq.XContainer|Tudo|
|System.Xml.Linq.XDeclaration|Tudo|
|System.Xml.Linq.XDocument|Todos, exceto de: Carga|
|System.Xml.Linq.XDocumentType|Tudo|
|System.Xml.Linq.XElement|Tudo|
|System.Xml.Linq.XName|Tudo|
|System.Xml.Linq.XNamespace|Tudo|
|System.Xml.Linq.XNode|Tudo|
|System.Xml.Linq.XNodeDocumentOrderComparer|Tudo|
|System.Xml.Linq.XNodeEqualityComparer|Tudo|
|System.Xml.Linq.XObject|Tudo|
|System.Xml.Linq.XProcessingInstruction|Tudo|
|System.Xml.Linq.XText|Tudo|
|System.Xml.XmlNodeType|Tudo|

## <a name="ContextVariables"></a>Variável de contexto
Uma variável chamada `context` está implicitamente disponível em todas as [expressões](api-management-policy-expressions.md#Syntax)políticas. Os seus membros fornecem informações pertinentes ao `\request`. Todos os membros `context` são apenas leituras.

|Variável de contexto|Métodos, propriedades e valores de parâmetros permitidos|
|----------------------|-------------------------------------------------------|
|noticioso|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementação](#ref-context-deployment)<br /><br /> Decorrido: TimeSpan - intervalo de tempo entre o valor da marca temporal e o tempo atual<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operação](#ref-context-operation)<br /><br /> [Produto](#ref-context-product)<br /><br /> [Pedido](#ref-context-request)<br /><br /> RequestId: Guid - identificador de pedido único<br /><br /> [Resposta](#ref-context-response)<br /><br /> [Subscrição](#ref-context-subscription)<br /><br /> Carimbo de tempo: DataTime - hora em que o pedido foi recebido<br /><br /> Rastreio: bool - indica se o rastreio está ligado ou desligado <br /><br /> [Utilizador](#ref-context-user)<br /><br /> [Variáveis](#ref-context-variables): IReadOnlyDictionary<string, object><br /><br /> vestígio sem efeito (mensagem: corda)|
|<a id="ref-context-api"></a>context.Api|Id: corda<br /><br /> IsCurrentRevision: bool<br /><br />  Nome: cadeia<br /><br /> Caminho: corda<br /><br /> Revisão: cadeia<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versão: string |
|<a id="ref-context-deployment"></a>context.Deployment|Região: corda<br /><br /> ServiceName: string<br /><br /> Certificados: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Fonte: corda<br /><br /> Razão: corda<br /><br /> Mensagem: corda<br /><br /> Âmbito: corda<br /><br /> Secção: corda<br /><br /> Caminho: corda<br /><br /> PolicyId: string<br /><br /> Para mais informações sobre o contexto. LastError, consulte [o manuseamento de erros](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>contexto. Operação|Id: corda<br /><br /> Método: cadeia<br /><br /> Nome: cadeia<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> AprovaçãoNecessária: bool<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: corda<br /><br /> Nome: cadeia<br /><br /> Estado: enum ProductState {NotPublished, Published}<br /><br /> SubscriçãoLimite: int?<br /><br /> AssinaturaNecessária: bool|
|<a id="ref-context-request"></a>contexto. Pedido|Corpo: [IMessageBody](#ref-imessagebody) ou `null` se o pedido não tiver um corpo.<br /><br /> Certificado: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Cabeçalhos](#ref-context-request-headers): IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> Parâmetros combinados: IReadOnlyDictionary<string, string><br /><br /> Método: cadeia<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Devoluções valores de cabeçalho de pedido separados pela vírem ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-response"></a>contexto. Resposta|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Cabeçalhos](#ref-context-response-headers): IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Devolve valores de cabeçalho de resposta separados pela vírposta ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-subscription"></a>contexto. Assinatura|Hora Criada: DataTime<br /><br /> Data limite: Data?<br /><br /> Id: corda<br /><br /> Chave: corda<br /><br /> Nome: cadeia<br /><br /> PrimaryKey: corda<br /><br /> SegundaChave: corda<br /><br /> Data de início: Data de data?|
|<a id="ref-context-user"></a>contexto. Utilizador|E-mail: string<br /><br /> FirstName: string<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: corda<br /><br /> Identidades: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> Apelido: corda<br /><br /> Nota: corda<br /><br /> Data de Inscrição: Data de Data|
|<a id="ref-iapi"></a>IApi|Id: corda<br /><br /> Nome: cadeia<br /><br /> Caminho: corda<br /><br /> Protocolos: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Nomes de parâmetros de subscriçãoKeyParameter: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGrupo|Id: corda<br /><br /> Nome: cadeia|
|<a id="ref-imessagebody"></a>IMessageBody|As<T\>(preservarConteúdo: bool = falso): Onde T: string, byte[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os métodos `context.Request.Body.As<T>` e `context.Response.Body.As<T>` são utilizados para ler um organismo de mensagem de pedido e resposta numa `T`tipo especificada. Por defeito, o método utiliza o fluxo corporal original da mensagem e torna-o indisponível após a sua devolução. Para evitar isso, mantendo o método funcionar numa cópia da corrente corporal, coloque o parâmetro `preserveContent` para `true`. Vá [aqui](api-management-transformation-policies.md#SetBody) ver um exemplo.|
|<a id="ref-iurl"></a>IUrl|Anfitrião: corda<br /><br /> Caminho: corda<br /><br /> Porto: int<br /><br /> [Consulta](#ref-iurl-query): IReadOnlyDictionary<string, string[]><br /><br /> ConsultaString: corda<br /><br /> Esquema: cadeia|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: corda<br /><br /> Fornecedor: cadeia|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Cabeçalho: corda<br /><br /> Consulta: corda|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Devolve valores de parâmetros de consulta separados pela vírdica ou `defaultValue` se o parâmetro não for encontrado.|
|<a id="ref-context-variables"></a>Contexto T. Variáveis.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Devolve o valor variável fundido para escrever `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Este método abre uma exceção se o tipo especificado não corresponder ao tipo real da variável devolvida.|
|BasicAuthCredentials AsBasic (entrada: esta cadeia)|entrada: corda<br /><br /> Se o parâmetro de entrada contiver um valor de pedido de autorização de autenticação básica HTTP válido, o método devolve um objeto de tipo `BasicAuthCredentials`; caso contrário, o método devolve nulo.|
|bool TryParseBasic (entrada: esta cadeia, resultado: out BasicAuthCredentials)|entrada: corda<br /><br /> resultado: fora BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de autenticação básica HTTP válido no cabeçalho de pedido, o método devolve `true` e o parâmetro de resultados contém um valor de tipo `BasicAuthCredentials`; caso contrário, o método retorna `false`.|
|BasicAuthCredentials|Palavra-passe: corda<br /><br /> UserId: string|
|Jwt AsJwt (entrada: esta corda)|entrada: corda<br /><br /> Se o parâmetro de entrada contiver um valor simbólico jWT válido, o método devolve um objeto de tipo `Jwt`; caso contrário, o método retorna `null`.|
|bool TryParseJwt (entrada: esta corda, resultado: fora Jwt)|entrada: corda<br /><br /> resultado: fora Jwt<br /><br /> Se o parâmetro de entrada contiver um valor simbólico jWT válido, o método retorna `true` e o parâmetro de resultados contém um valor de tipo `Jwt`; caso contrário, o método retorna `false`.|
|Jwt|Algoritmo: corda<br /><br /> Público: IEnumerable<string\><br /><br /> Reivindicações: IReadOnlyDictionary<string, string[]><br /><br /> Tempo de validade: Data?<br /><br /> Id: corda<br /><br /> Emitente: corda<br /><br /> EmitidoAt: DataTime?<br /><br /> Não Antes: Hora do Encontro?<br /><br /> Assunto: corda<br /><br /> Tipo: corda|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Devoluções valores de reclamação separados pela vírem ou `defaultValue` se o cabeçalho não for encontrado.|
|byte[] Encrypt[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[]|entrada - texto simples a ser encriptado<br /><br />alg - nome de um algoritmo de encriptação simétrica<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devoluções texto simples encriptado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - texto simples a ser encriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devoluções texto simples encriptado.|
|byte[] Encrypt[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|entrada - texto simples a ser encriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devoluções texto simples encriptado.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|entrada - texto cifrado a ser desencriptado<br /><br />alg - nome de um algoritmo de encriptação simétrica<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve texto simples.|
|byte[] Decrypt (entrada: este byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - texto cifrado a ser desencriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devolve texto simples.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[]|entrada - texto cifrado a ser desencriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve texto simples.|
|bool CheckNoRevocation (entrada: este Sistema.Security.Cryptography.X509Certificados.X509Certificados2)|Executa uma validação em cadeia X.509 sem verificar o estado de revogação do certificado.<br /><br />entrada - objeto de certificado<br /><br />Devoluções `true` se a validação for bem sucedida; `false` se a validação falhar.|


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no gerenciamento de API](api-management-howto-policies.md)
+ [APIs de transformação](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
