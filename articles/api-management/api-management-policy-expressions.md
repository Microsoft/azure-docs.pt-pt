---
title: Azure API Expressões políticas de gestão | Microsoft Docs
description: Conheça as expressões políticas na Azure API Management. Veja exemplos e veja recursos adicionais disponíveis.
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
ms.openlocfilehash: aec1967f0652e18c4a24ca258c14a103355b22af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219320"
---
# <a name="api-management-policy-expressions"></a>Expressões políticas de gestão da API
Este artigo discute a sintaxe das expressões políticas em C# 7. Cada expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) implicitamente fornecida e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos de quadros .NET.

Para obter mais informações:

- Veja como fornecer informações de contexto ao seu serviço de backend. Utilize o parâmetro de [cadeia de consulta de conjunto](api-management-transformation-policies.md#SetQueryStringParameter) e [desempenhe](api-management-transformation-policies.md#SetHTTPheader) as políticas do cabeçalho HTTP para fornecer estas informações.
- Veja como utilizar a política [validada do JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar o acesso a operações com base em reclamações simbólicas.
- Veja como utilizar um [rastreador de inspetor da API](./api-management-howto-api-inspector.md) para ver como as políticas são avaliadas e os resultados dessas avaliações.
- Veja como usar expressões com o [Get from cache](api-management-caching-policies.md#GetFromCache) e Store para [cache](api-management-caching-policies.md#StoreToCache) políticas para configurar a resposta da API Management. Desace uma duração que corresponda ao caching de resposta do serviço de backend, conforme especificado pela diretiva do serviço `Cache-Control` apoiado.
- Veja como executar a filtragem de conteúdos. Remova os elementos de dados da resposta recebida do backend utilizando as políticas do [corpo do controle](api-management-advanced-policies.md#choose) e [do conjunto.](api-management-transformation-policies.md#SetBody)
- Para descarregar as declarações de política, consulte as [amostras/políticas de gestão da API](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub.


## <a name="syntax"></a><a name="Syntax"></a> Sintaxe
As expressões de declaração única são incluídas, `@(expression)` onde está uma declaração de expressão `expression` C# bem formada.

As expressões multi-afirmações são incluídas em `@{expression}` . Todos os caminhos de código dentro de expressões multi-afirmações devem terminar com uma `return` declaração.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> Exemplos

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

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Utilização
As expressões podem ser usadas como valores de atributos ou valores de texto em quaisquer [políticas](api-management-policies.md) de Gestão de API (a menos que a referência política especifica o contrário).

> [!IMPORTANT]
> Quando se utiliza expressões políticas, só há uma verificação limitada das expressões políticas quando a política é definida. As expressões são executadas pelo gateway no tempo de execução, quaisquer exceções geradas por expressões políticas resultam num erro de tempo de execução.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> .NET Tipos-quadro permitidos em expressões políticas
A tabela que se segue lista os tipos de quadros .NET e os seus membros que são permitidos em expressões políticas.

|Tipo|Membros apoiados|
|--------------|-----------------------|
|Newtonsoft.Js. Formatação|Todos|
|Newtonsoft.Json.JsnoConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Js. Extensões Linq.|Todos|
|Newtonsoft.Js. Linq.JArray|Todos|
|Newtonsoft.Js. Linq.JConstructr|Todos|
|Newtonsoft.Js. Linq.JContainer|Todos|
|Newtonsoft.Js. Linq.JObject|Todos|
|Newtonsoft.Js. Linq.JProperty|Todos|
|Newtonsoft.Js. Linq.JRaw|Todos|
|Newtonsoft.Js. Linq.JToken|Todos|
|Newtonsoft.Js. Linq.JTokenType|Todos|
|Newtonsoft.Js. Linq.JValue|Todos|
|Sistema.Array|Todos|
|Sistema.BitConverter|Todos|
|System.Boolean|Todos|
|Sistema.Byte|Todos|
|Sistema.Char|Todos|
|System.Collections.Generic.Dictionary<TKey, TValue>|Todos|
|System.Collections.Generic.HashSet\<T>|Todos|
|System.Collections.Generic.ICollection\<T>|Todos|
|System.Collections.Generic.IDdictionary<TKey, TValue>|Todos|
|System.Collections.Generic.IEnumerable\<T>|Todos|
|System.Collections.Generic.IEnumerator\<T>|Todos|
|System.Collections.Generic.IList\<T>|Todos|
|System.Collections.Generic.IReadOnlyCollection\<T>|Todos|
|System.collections.generic.IReadOnlyDictionary<TKey, TValue>|Todos|
|System.Collections.Generic.ISet\<T>|Todos|
|System.collections.Generic.KeyValuePair<TKey, TValue>|Todos|
|System.Collections.Generic.List\<T>|Todos|
|System.Collections.Generic.Queue\<T>|Todos|
|System.Collections.Generic.Stack\<T>|Todos|
|Sistema.Converter|Todos|
|Sistema.DataTime|(Construtor), Adicionar, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now, Parse, Second, Subtract, Ticks, TimeOfDay, Today, ToString, Utc, Year,|
|System.DateTimeKind|Utc|
|Sistema.DataTimeOffset|Todos|
|Sistema.Decimal|Todos|
|Sistema.Duplo|Todos|
|Sistema.Exceção|Todos|
|Sistema.Guid|Todos|
|Sistema.Int16|Todos|
|Sistema.Int32|Todos|
|Sistema.Int64|Todos|
|System.io.StringReader|Todos|
|System.io.StringWriter|Todos|
|System.Linq.Enumerable|Todos|
|Sistema.Matemática|Todos|
|Sistema.MidpointRounding|Todos|
|System.net.webutility|Todos|
|Sistema.Anulado|Todos|
|Sistema.Aleatório|Todos|
|System.SByte|Todos|
|System.Security.Cryptography.AssimétricaAlgorithm|Todos|
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
|System.Security.Cryptography.RSAEncrypationPadding|Todos|
|System.Security.Cryptography.RSASignaturePadding|Todos|
|System.Security.Cryptography.SHA1|Todos|
|System.Security.Cryptography.SHA1Managed|Todos|
|System.Security.Cryptography.SHA256|Todos|
|System.Security.Cryptography.SHA256Gered|Todos|
|System.Security.Cryptography.SHA384|Todos|
|System.Security.Cryptography.SHA384Gered|Todos|
|System.Security.Cryptography.SHA512|Todos|
|System.Security.Cryptography.SHA512Gered|Todos|
|System.Security.Cryptography.SymmetricAlgorithm|Todos|
|System.Security.Cryptography.X509Certificates.PublicKey|Todos|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Todos|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name|
|System.Security.Cryptography.X509Certificates.X509Certificate|Todos|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Todos|
|System.Security.Cryptography.X509Certificates.X509ContentType|Todos|
|System.Security.Cryptography.X509Certificates.X509NameType|Todos|
|Sistema.Único|Todos|
|Sistema.String|Todos|
|System.StringComparer|Todos|
|System.StringComparison|Todos|
|System.StringSplitOptions|Todos|
|System.Text.Encoding|Todos|
|System.Text.RegularExpressions.Capture|Índice, Comprimento, Valor|
|System.Text.RegularExpressions.CaptureCollection|Contagem, Item|
|System.Text.RegularExpressions.Group|Capturas, Sucesso|
|System.Text.RegularExpressions.GroupCollection|Contagem, Item|
|System.Text.RegularExpressions.Match|Vazio, Grupos, Resultado|
|System.Text.RegularExpressions.Regex|(construtor), Ismatch, Match, Matches, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Todos|
|System.Text.StringBuilder|Todos|
|System.TimeSpan|Todos|
|Sistema.TimeZone|Todos|
|System.TimeZoneInfo.AdjustmentRule|Todos|
|System.TimeZoneInfo.TransitionTime|Todos|
|Sistema.TimeZoneInfo|Todos|
|Sistema.Tuple|Todos|
|Sistema.UInt16|Todos|
|Sistema.UInt32|Todos|
|Sistema.UInt64|Todos|
|Sistema.Uri|Todos|
|Sistema.UriPartial|Todos|
|System.Xml. Extensões Linq.|Todos|
|System.Xml. Linq.XAttribute|Todos|
|System.Xml. Linq.XCData|Todos|
|System.Xml. Linq.XComment|Todos|
|System.Xml. Linq.XContainer|Todos|
|System.Xml. Linq.XDeclaração|Todos|
|System.Xml. Linq.XDocument|Todos, exceto: Carga|
|System.Xml. Linq.XDocumentType|Todos|
|System.Xml. Linq.XElement|Todos|
|System.Xml. Linq.XName|Todos|
|System.Xml. Linq.XNamespace|Todos|
|System.Xml. Linq.XNode|Todos|
|System.Xml. Linq.XNodeDocumentOrder|Todos|
|System.Xml. Linq.XNodeEqualityComparer|Todos|
|System.Xml. Linq.XObject|Todos|
|System.Xml. Linq.XProcessingInstruction|Todos|
|System.Xml. Linq.XText|Todos|
|System.Xml.XmlNodeType|Todos|

## <a name="context-variable"></a><a name="ContextVariables"></a> Variável de contexto
Uma variável nomeada `context` está implicitamente disponível em todas as [expressões](api-management-policy-expressions.md#Syntax)políticas. Os seus membros fornecem informações pertinentes ao `\request` . Todos os `context` membros são apenas leitura.

|Variável de contexto|Métodos, propriedades e valores de parâmetros permitidos|
|----------------------|-------------------------------------------------------|
|contexto|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementação](#ref-context-deployment)<br /><br /> Decorrido: TimeSpan - intervalo de tempo entre o valor do timestamp e o tempo atual<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operação](#ref-context-operation)<br /><br /> [Produto](#ref-context-product)<br /><br /> [Pedir](#ref-context-request)<br /><br /> RequestId: Guid - identificador de pedido único<br /><br /> [Response](#ref-context-response)<br /><br /> [Subscrição](#ref-context-subscription)<br /><br /> Timetamp: DateTime - ponto no tempo em que o pedido foi recebido<br /><br /> Rastreio: bool - indica se o rastreio está ligado ou desligado <br /><br /> [Utilizador](#ref-context-user)<br /><br /> [Variáveis](#ref-context-variables): IReadOnlyDictionary<string, object><br /><br /> vestígios vazios (mensagem: cadeia)|
|<a id="ref-context-api"></a>contexto. Api|Id: corda<br /><br /> IsCurrentRevision: bool<br /><br />  Nome: cadeia<br /><br /> Caminho: corda<br /><br /> Revisão: cadeia<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versão: corda |
|<a id="ref-context-deployment"></a>contexto. Implantação|Região: corda<br /><br /> Nome de serviço: cadeia<br /><br /> Certificados: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>contexto. LastError|Fonte: cadeia<br /><br /> Razão: corda<br /><br /> Mensagem: cadeia<br /><br /> Âmbito: cadeia<br /><br /> Secção: cadeia<br /><br /> Caminho: corda<br /><br /> PolicyId: string<br /><br /> Para mais informações sobre o contexto. LastError, consulte [o tratamento de erros](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>contexto. Operação|Id: corda<br /><br /> Método: cadeia<br /><br /> Nome: cadeia<br /><br /> UrlTemplate: corda|
|<a id="ref-context-product"></a>contexto. Produto|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: corda<br /><br /> Nome: cadeia<br /><br /> Estado: enum ProductState {NotPublished, Published}<br /><br /> SubscriçãoLimite: int?<br /><br /> SubscriçãoRequired: bool|
|<a id="ref-context-request"></a>contexto. Pedido|Corpo: [IMessageBody](#ref-imessagebody) ou `null` se o pedido não tiver um corpo.<br /><br /> Certificado: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Cabeçalhos](#ref-context-request-headers): IReadOnlyDictionary<string[]><br /><br /> IpAddress: cadeia<br /><br /> CombinaçãoParameters: IReadOnlyDictionary<string, string><br /><br /> Método: cadeia<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>contexto de cordas. Pedido.Headers.GetValueOrDefault (headerName: string, defaultValue: string)|headerName: corda<br /><br /> defaultValue: cadeia<br /><br /> Devolve valores de cabeçalho de pedido separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-response"></a>contexto. Resposta|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Cabeçalhos](#ref-context-response-headers): IReadOnlyDictionary<string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: corda|
|<a id="ref-context-response-headers"></a>contexto de cordas. Resposta.Headers.GetValueOrDefault (headerName: string, defaultValue: string)|headerName: corda<br /><br /> defaultValue: cadeia<br /><br /> Devolve os valores do cabeçalho de resposta separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-subscription"></a>contexto. Assinatura|CreatedDate: DateTime<br /><br /> Fim: DataTime?<br /><br /> Id: corda<br /><br /> Chave: corda<br /><br /> Nome: cadeia<br /><br /> PrimaryKey: corda<br /><br /> SecondaryKey: cadeia<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>contexto. Utilizador|E-mail: corda<br /><br /> Primeiro Nome: corda<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: corda<br /><br /> Identidades: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: string<br /><br /> Nota: corda<br /><br /> RegistoDate: Data Hora|
|<a id="ref-iapi"></a>IAPI|Id: corda<br /><br /> Nome: cadeia<br /><br /> Caminho: corda<br /><br /> Protocolos: IEnumerable<cadeia\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriçãoKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: corda<br /><br /> Nome: cadeia|
|<a id="ref-imessagebody"></a>IMessageBody|Como<T \> (preserveContent: bool = falso): Where T: string, byte[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os `context.Request.Body.As<T>` `context.Response.Body.As<T>` métodos e métodos são utilizados para ler um pedido e órgãos de mensagens de resposta num tipo especificado `T` . Por predefinição, o método utiliza o fluxo original do corpo da mensagem e torna-o indisponível após a sua devolução. Para evitar isso, fazendo com que o método funcione numa cópia do fluxo do corpo, desaperte o `preserveContent` parâmetro para `true` . Vá [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|
|<a id="ref-iurl"></a>IUrl|Anfitrião: corda<br /><br /> Caminho: corda<br /><br /> Porto: int<br /><br /> [Consulta](#ref-iurl-query): IReadOnlyDictionary<string[]><br /><br /> Consulta: corda<br /><br /> Esquema: cadeia|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: corda<br /><br /> Fornecedor: cadeia|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Cabeçalho: corda<br /><br /> Consulta: corda|
|<a id="ref-iurl-query"></a>cadeia IUrl.Query.GetValueOrDefault (consultaParameterName: string, defaultValue: string)|consultaParameterName: cadeia<br /><br /> defaultValue: cadeia<br /><br /> Devolve valores de parâmetros de consulta separados por vírgula ou `defaultValue` se o parâmetro não for encontrado.|
|<a id="ref-context-variables"></a>Contexto T. Variáveis.GetValueOrDefault<T \> (nome variávelName: cadeia, padrãoValue: T)|nome variável: cadeia<br /><br /> defaultValue: T<br /><br /> Devolve o valor variável lançado para o tipo `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Este método abre uma exceção se o tipo especificado não corresponder ao tipo real da variável devolvida.|
|BasicAuthCredentials AsBasic (entrada: esta cadeia)|entrada: cadeia<br /><br /> Se o parâmetro de entrada contiver um valor de cabeçalho de pedido de autenticação básica http válido, o método devolve um objeto do `BasicAuthCredentials` tipo; caso contrário, o método devolve nulo.|
|bool TryParseBasic (entrada: esta corda, resultado: out BasicAuthCredentials)|entrada: cadeia<br /><br /> resultado: out BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de autenticação básica http válido no cabeçalho de pedido, o método devolve `true` e o parâmetro de resultados contém um valor do `BasicAuthCredentials` tipo; caso contrário, o método retorna `false` .|
|Básicos DeAuthCredentials|Palavra-passe: cadeia<br /><br /> UserId: cadeia|
|Jwt AsJwt (entrada: esta cadeia)|entrada: cadeia<br /><br /> Se o parâmetro de entrada contiver um valor simbólico JWT válido, o método devolve um objeto do `Jwt` tipo; caso contrário, o método retorna `null` .|
|bool TryParseJwt (entrada: esta corda, resultado: fora Jwt)|entrada: cadeia<br /><br /> resultado: fora Jwt<br /><br /> Se o parâmetro de entrada contiver um valor simbólico JWT válido, o método regressa `true` e o parâmetro de resultados contém um valor do `Jwt` tipo; caso contrário, o método retorna `false` .|
|Jwt|Algoritmo: corda<br /><br /> Público: IEnumerable<string\><br /><br /> Reivindicações: IReadOnlyDictionary<string[]><br /><br /> Tempo de validade: DataTime?<br /><br /> Id: corda<br /><br /> Emitente: corda<br /><br /> Emitido: DataTime?<br /><br /> Não Antes: DateTime?<br /><br /> Objeto: corda<br /><br /> Tipo: corda|
|string Jwt.Claims.GetValueOrDefault (claimName: string, defaultValue: string)|nome de reivindicação: corda<br /><br /> defaultValue: cadeia<br /><br /> Devolve valores de reclamação separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|byte[] Criptografo (entrada: este byte[], alg: string, key:byte[], iv:byte[])|entrada - texto simples a ser encriptado<br /><br />alg - nome de um algoritmo de encriptação simétrico<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve textos encriptados.|
|byte[] Criptografe(entrada: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - texto simples a ser encriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devolve textos encriptados.|
|byte[] Criptografo (entrada: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|entrada - texto simples a ser encriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve textos encriptados.|
|byte[] Desencript (entrada: this byte[], alg: string, key:byte[], iv:byte[])|input - texto cifrado a ser desencriptado<br /><br />alg - nome de um algoritmo de encriptação simétrico<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve o texto simples.|
|byte[] Desencript (entrada: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - texto cifrado a ser desencriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devolve o texto simples.|
|byte[] Desencript (input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - texto cifrado a ser desencriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve o texto simples.|
|bool CheckNoRevocation (entrada: este sistema.Security.Cryptography.X509Certificates.X509Certificate2)|Executa uma validação em cadeia X.509 sem verificar o estado de revogação do certificado.<br /><br />entrada - objeto de certificado<br /><br />Devoluções `true` se a validação for bem sucedida; `false` se a validação falhar.|


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas, consulte:

+ [Políticas em Gestão de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
+ [Exemplos de Políticas](./policy-reference.md)
