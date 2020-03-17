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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244007"
---
# <a name="api-management-policy-expressions"></a>Expressões políticas de gestão da API
Este artigo discute a sintaxe de expressões políticas em C# 7. Cada expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) implicitamente fornecida e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos de .NET- Quadro.

Para obter mais informações:

- Veja como fornecer informações de contexto ao seu serviço de backend. Utilize o parâmetro de corda de [consulta definido](api-management-transformation-policies.md#SetQueryStringParameter) e defina as políticas de [cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) para fornecer esta informação.
- Consulte como utilizar a política [De validação jWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar o acesso a operações com base em alegações simbólicas.
- Veja como utilizar um rastreio do [Inspetor DaPI](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) para ver como as políticas são avaliadas e os resultados dessas avaliações.
- Veja como usar expressões com o [Get de cache](api-management-caching-policies.md#GetFromCache) e loja para políticas de [cache](api-management-caching-policies.md#StoreToCache) para configurar o cache de resposta de gestão de API. Estabeleça uma duração que corresponda ao cache de resposta do serviço de backend, conforme especificado pela diretiva `Cache-Control` do serviço apoiado.
- Veja como efetuar a filtragem de conteúdo. Remova os elementos de dados da resposta recebida da parte de trás utilizando as políticas de fluxo de [controlo](api-management-advanced-policies.md#choose) e [conjunto do corpo.](api-management-transformation-policies.md#SetBody)
- Para descarregar as declarações políticas, consulte o repo gitHub [api-management/policies.](https://github.com/Azure/api-management-samples/tree/master/policies)


## <a name="Syntax"></a>Sintaxe
As expressões de declaração única são encerradas em C# `@(expression)`, onde `expression` é uma declaração de expressão bem formada.

As expressões multi-afirmações são encerradas em `@{expression}`. Todos os caminhos de código dentro de expressões multi-afirmações devem terminar com uma declaração `return`.

## <a name="PolicyExpressionsExamples"></a>Exemplos

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
|Newtonsoft.Json.Formatting|Todas|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Todas|
|Newtonsoft.Json.Linq.JArray|Todas|
|Newtonsoft.Json.Linq.JConstructor|Todas|
|Newtonsoft.Json.Linq.JContainer|Todas|
|Newtonsoft.Json.Linq.JObject|Todas|
|Newtonsoft.Json.Linq.JProperty|Todas|
|Newtonsoft.Json.Linq.JRaw|Todas|
|Newtonsoft.Json.Linq.JToken|Todas|
|Newtonsoft.Json.Linq.JTokenType|Todas|
|Newtonsoft.Json.Linq.JValue|Todas|
|Sistema.Matriz|Todas|
|System.BitConverter|Todas|
|System.Boolean|Todas|
|System.Byte|Todas|
|System.Char|Todas|
|System.Collections.Generic.Dictionary<TKey, TValue>|Todas|
|System.Collections.Generic.HashSet\<T>|Todas|
|System.Collections.Generic.ICollection\<T>|Todas|
|System.Collections.Generic.IDictionary<TKey, TValue>|Todas|
|System.Collections.Generic.IEnumerable\<T>|Todas|
|System.Collections.Generic.IEnumerator\<T>|Todas|
|System.Collections.Generic.IList\<T>|Todas|
|System.Collections.Generic.IReadOnlyCollection\<T>|Todas|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Todas|
|System.Collections.Generic.ISet\<T>|Todas|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Todas|
|System.Collections.Generic.List\<T>|Todas|
|System.Collections.Generic.Fila\<T>|Todas|
|System.Collections.Generic.Stack\<T>|Todas|
|System.Convert|Todas|
|System.DateTime|(Construtor), AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now , Parse, Second, Subtract, Ticks, TimeOfDay, Today, ToString, UtcNow, Year|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Todas|
|System.Decimal|Todas|
|System.Double|Todas|
|System.Exception|Todas|
|System.Guid|Todas|
|System.Int16|Todas|
|System.Int32|Todas|
|System.Int64|Todas|
|System.IO.StringReader|Todas|
|System.IO.StringWriter|Todas|
|System.Linq.Enumerable|Todas|
|System.Math|Todas|
|System.MidpointRounding|Todas|
|System.Net.WebUtility|Todas|
|System.Nullable|Todas|
|System.Random|Todas|
|System.SByte|Todas|
|System.Security.Cryptography.AsymmetricAlgorithm|Todas|
|System.Security.Cryptography.CipherMode|Todas|
|System.Security.Cryptography.HashAlgorithm|Todas|
|System.Security.Cryptography.HashAlgorithmName|Todas|
|System.Security.Cryptography.HMAC|Todas|
|System.Security.Cryptography.HMACMD5|Todas|
|System.Security.Cryptography.HMACSHA1|Todas|
|System.Security.Cryptography.HMACSHA256|Todas|
|System.Security.Cryptography.HMACSHA384|Todas|
|System.Security.Cryptography.HMACSHA512|Todas|
|System.Security.Cryptography.KeyedHashAlgorithm|Todas|
|System.Security.Cryptography.MD5|Todas|
|System.Security.Cryptography.Oid|Todas|
|System.Security.Cryptography.PaddingMode|Todas|
|System.Security.Cryptography.RNGCryptoServiceProvider|Todas|
|System.Security.Cryptography.RSA|Todas|
|System.Security.Cryptography.RSAEncryptionPadding|Todas|
|System.Security.Cryptography.RSASignaturePadding|Todas|
|System.Security.Cryptography.SHA1|Todas|
|System.Security.Cryptography.SHA1Managed|Todas|
|System.Security.Cryptography.SHA256|Todas|
|System.Security.Cryptography.SHA256Managed|Todas|
|System.Security.Cryptography.SHA384|Todas|
|System.Security.Cryptography.SHA384Managed|Todas|
|System.Security.Cryptography.SHA512|Todas|
|System.Security.Cryptography.SHA512Managed|Todas|
|System.Security.Cryptography.SymmetricAlgorithm|Todas|
|System.Security.Cryptography.X509Certificates.PublicKey|Todas|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Todas|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Nome|
|System.Security.Cryptography.X509Certificates.X509Certificate|Todas|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Todas|
|System.Security.Cryptography.X509Certificates.X509ContentType|Todas|
|System.Security.Cryptography.X509Certificates.X509NameType|Todas|
|System.Single|Todas|
|System.String|Todas|
|System.StringComparer|Todas|
|System.StringComparison|Todas|
|System.StringSplitOptions|Todas|
|System.Text.Encoding|Todas|
|System.Text.RegularExpressions.Capture|Índice, Comprimento, Valor|
|System.Text.RegularExpressions.CaptureCollection|Contagem, Item|
|System.Text.RegularExpressions.Group|Capturas, Sucesso|
|System.Text.RegularExpressions.GroupCollection|Contagem, Item|
|System.Text.RegularExpressions.Match|Vazio, Grupos, Resultado|
|System.Text.RegularExpressions.Regex|(Construtor), IsMatch, Match, Match, Match, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Todas|
|System.Text.StringBuilder|Todas|
|System.TimeSpan|Todas|
|System.TimeZone|Todas|
|System.TimeZoneInfo.AdjustmentRule|Todas|
|System.TimeZoneInfo.TransitionTime|Todas|
|System.TimeZoneInfo|Todas|
|System.Tuple|Todas|
|System.UInt16|Todas|
|System.UInt32|Todas|
|System.UInt64|Todas|
|System.Uri|Todas|
|System.UriPartial|Todas|
|System.Xml.Linq.Extensions|Todas|
|System.Xml.Linq.XAttribute|Todas|
|System.Xml.Linq.XCData|Todas|
|System.Xml.Linq.XComment|Todas|
|System.Xml.Linq.XContainer|Todas|
|System.Xml.Linq.XDeclaration|Todas|
|System.Xml.Linq.XDocument|Todos, exceto de: Carga|
|System.Xml.Linq.XDocumentType|Todas|
|System.Xml.Linq.XElement|Todas|
|System.Xml.Linq.XName|Todas|
|System.Xml.Linq.XNamespace|Todas|
|System.Xml.Linq.XNode|Todas|
|System.Xml.Linq.XNodeDocumentOrderComparer|Todas|
|System.Xml.Linq.XNodeEqualityComparer|Todas|
|System.Xml.Linq.XObject|Todas|
|System.Xml.Linq.XProcessingInstruction|Todas|
|System.Xml.Linq.XText|Todas|
|System.Xml.XmlNodeType|Todas|

## <a name="ContextVariables"></a>Variável de contexto
Uma variável chamada `context` está implicitamente disponível em todas as [expressões](api-management-policy-expressions.md#Syntax)políticas. Os seus membros fornecem informações pertinentes ao `\request`. Todos os membros `context` são apenas leituras.

|Variável de contexto|Métodos, propriedades e valores de parâmetros permitidos|
|----------------------|-------------------------------------------------------|
|contexto|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementação](#ref-context-deployment)<br /><br /> Decorrido: TimeSpan - intervalo de tempo entre o valor da marca temporal e o tempo atual<br /><br /> [Erro Último](#ref-context-lasterror)<br /><br /> [Operação](#ref-context-operation)<br /><br /> [Produto](#ref-context-product)<br /><br /> [Pedido](#ref-context-request)<br /><br /> RequestId: Guid - identificador de pedido único<br /><br /> [Resposta](#ref-context-response)<br /><br /> [Subscrição](#ref-context-subscription)<br /><br /> Carimbo de tempo: DataTime - hora em que o pedido foi recebido<br /><br /> Rastreio: bool - indica se o rastreio está ligado ou desligado <br /><br /> [Utilizador](#ref-context-user)<br /><br /> [Variáveis](#ref-context-variables): IReadOnlyDictionary<string, object><br /><br /> vestígio sem efeito (mensagem: corda)|
|<a id="ref-context-api"></a>contexto. Rio Api|Id: corda<br /><br /> IsCurrentRevision: bool<br /><br />  Nome: cadeia<br /><br /> Caminho: corda<br /><br /> Revisão: cadeia<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versão: string |
|<a id="ref-context-deployment"></a>contexto. Implantação|Região: corda<br /><br /> ServiceName: string<br /><br /> Certificados: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>contexto. Erro Último|Fonte: corda<br /><br /> Razão: corda<br /><br /> Mensagem: corda<br /><br /> Âmbito: corda<br /><br /> Secção: corda<br /><br /> Caminho: corda<br /><br /> PolicyId: string<br /><br /> Para mais informações sobre o contexto. LastError, consulte [o manuseamento de erros](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>contexto. Operação|Id: corda<br /><br /> Método: cadeia<br /><br /> Nome: cadeia<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>contexto. Produto|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> AprovaçãoNecessária: bool<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: corda<br /><br /> Nome: cadeia<br /><br /> Estado: enum ProductState {NotPublished, Published}<br /><br /> SubscriçãoLimite: int?<br /><br /> AssinaturaNecessária: bool|
|<a id="ref-context-request"></a>contexto. Pedido|Corpo: [IMessageBody](#ref-imessagebody) ou `null` se o pedido não tiver um corpo.<br /><br /> Certificado: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Cabeçalhos](#ref-context-request-headers): IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> Parâmetros combinados: IReadOnlyDictionary<string, string><br /><br /> Método: cadeia<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>contexto de cordas. Request.Headers.GetValueOrDefault (nome do cabeçalho: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Devoluções valores de cabeçalho de pedido separados pela vírem ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-response"></a>contexto. Resposta|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Cabeçalhos](#ref-context-response-headers): IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>contexto de cordas. Response.Headers.GetValueOrDefault (nome do cabeçalho: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Devolve valores de cabeçalho de resposta separados pela vírposta ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-subscription"></a>contexto. Assinatura|Hora Criada: DataTime<br /><br /> Data limite: Data?<br /><br /> Id: corda<br /><br /> Chave: corda<br /><br /> Nome: cadeia<br /><br /> PrimaryKey: corda<br /><br /> SegundaChave: corda<br /><br /> Data de início: Data de data?|
|<a id="ref-context-user"></a>contexto. Utilizador|E-mail: string<br /><br /> FirstName: string<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: corda<br /><br /> Identidades: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> Apelido: corda<br /><br /> Nota: corda<br /><br /> Data de Inscrição: Data de Data|
|<a id="ref-iapi"></a>IApi|Id: corda<br /><br /> Nome: cadeia<br /><br /> Caminho: corda<br /><br /> Protocolos: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Nomes de parâmetros de subscriçãoKeyParameter: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGrupo|Id: corda<br /><br /> Nome: cadeia|
|<a id="ref-imessagebody"></a>Corpo de Mensagem|As<T\>(preservarConteúdo: bool = falso): Onde T: string, byte[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os métodos `context.Request.Body.As<T>` e `context.Response.Body.As<T>` são utilizados para ler um organismo de mensagem de pedido e resposta numa `T`tipo especificada. Por defeito, o método utiliza o fluxo corporal original da mensagem e torna-o indisponível após a sua devolução. Para evitar isso, mantendo o método funcionar numa cópia da corrente corporal, coloque o parâmetro `preserveContent` para `true`. Vá [aqui](api-management-transformation-policies.md#SetBody) ver um exemplo.|
|<a id="ref-iurl"></a>IUrl|Anfitrião: corda<br /><br /> Caminho: corda<br /><br /> Porto: int<br /><br /> [Consulta](#ref-iurl-query): IReadOnlyDictionary<string, string[]><br /><br /> ConsultaString: corda<br /><br /> Esquema: cadeia|
|<a id="ref-iuseridentity"></a>Identidade iUser|Id: corda<br /><br /> Fornecedor: cadeia|
|<a id="ref-isubscriptionkeyparameternames"></a>Nomes iSubscriptionKeyParameterNames|Cabeçalho: corda<br /><br /> Consulta: corda|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault (queryParâmetrometerName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Devolve valores de parâmetros de consulta separados pela vírdica ou `defaultValue` se o parâmetro não for encontrado.|
|<a id="ref-context-variables"></a>Contexto T. Variáveis.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Devolve o valor variável fundido para escrever `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Este método abre uma exceção se o tipo especificado não corresponder ao tipo real da variável devolvida.|
|BasicAuthCredentials AsBasic (entrada: esta cadeia)|entrada: corda<br /><br /> Se o parâmetro de entrada contiver um valor de pedido de autorização de autenticação básica HTTP válido, o método devolve um objeto de tipo `BasicAuthCredentials`; caso contrário, o método devolve nulo.|
|bool TryParseBasic (entrada: esta cadeia, resultado: out BasicAuthCredentials)|entrada: corda<br /><br /> resultado: fora BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de autenticação básica HTTP válido no cabeçalho de pedido, o método devolve `true` e o parâmetro de resultados contém um valor de tipo `BasicAuthCredentials`; caso contrário, o método retorna `false`.|
|BasicAuthCredentials|Palavra-passe: corda<br /><br /> UserId: string|
|Jwt AsJwt (entrada: esta corda)|entrada: corda<br /><br /> Se o parâmetro de entrada contiver um valor simbólico jWT válido, o método devolve um objeto de tipo `Jwt`; caso contrário, o método retorna `null`.|
|bool TryParseJwt (entrada: esta corda, resultado: fora Jwt)|entrada: corda<br /><br /> resultado: fora Jwt<br /><br /> Se o parâmetro de entrada contiver um valor simbólico jWT válido, o método retorna `true` e o parâmetro de resultados contém um valor de tipo `Jwt`; caso contrário, o método retorna `false`.|
|Jwt|Algoritmo: corda<br /><br /> Público: IEnumerable<string\><br /><br /> Reivindicações: IReadOnlyDictionary<string, string[]><br /><br /> Tempo de validade: Data?<br /><br /> Id: corda<br /><br /> Emitente: corda<br /><br /> EmitidoAt: DataTime?<br /><br /> Não Antes: Hora do Encontro?<br /><br /> Assunto: corda<br /><br /> Tipo: corda|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Devoluções valores de reclamação separados pela vírem ou `defaultValue` se o cabeçalho não for encontrado.|
|byte[] Encrypt[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|entrada - texto simples a ser encriptado<br /><br />alg - nome de um algoritmo de encriptação simétrica<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devoluções texto simples encriptado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - texto simples a ser encriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devoluções texto simples encriptado.|
|byte[] Encrypt[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|entrada - texto simples a ser encriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devoluções texto simples encriptado.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|entrada - texto cifrado a ser desencriptado<br /><br />alg - nome de um algoritmo de encriptação simétrica<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve texto simples.|
|byte[] Decrypt (entrada: este byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - texto cifrado a ser desencriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devolve texto simples.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[]|entrada - texto cifrado a ser desencriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve texto simples.|
|bool CheckNoRevocation (entrada: este Sistema.Security.Cryptography.X509Certificados.X509Certificados2)|Executa uma validação em cadeia X.509 sem verificar o estado de revogação do certificado.<br /><br />entrada - objeto de certificado<br /><br />Devoluções `true` se a validação for bem sucedida; `false` se a validação falhar.|


## <a name="next-steps"></a>Passos Seguintes

Para mais informações que trabalhem com políticas, consulte:

+ [Políticas em Gestão aPi](api-management-howto-policies.md)
+ [Transforme APIs](transform-api.md)
+ [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
+ [Amostras políticas](policy-samples.md)
