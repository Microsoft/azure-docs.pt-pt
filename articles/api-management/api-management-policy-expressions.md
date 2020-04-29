---
title: Expressões políticas de gestão da API azure [ Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244007"
---
# <a name="api-management-policy-expressions"></a>Expressões políticas de gestão da API
Este artigo discute a sintaxe de expressões políticas em C#7. Cada expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) implicitamente fornecida e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos de .NET- Quadro.

Para obter mais informações:

- Veja como fornecer informações de contexto ao seu serviço de backend. Utilize o parâmetro de corda de [consulta definido](api-management-transformation-policies.md#SetQueryStringParameter) e defina as políticas de [cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) para fornecer esta informação.
- Consulte como utilizar a política [De validação jWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar o acesso a operações com base em alegações simbólicas.
- Veja como utilizar um rastreio do [Inspetor DaPI](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) para ver como as políticas são avaliadas e os resultados dessas avaliações.
- Veja como usar expressões com o [Get de cache](api-management-caching-policies.md#GetFromCache) e loja para políticas de [cache](api-management-caching-policies.md#StoreToCache) para configurar o cache de resposta de gestão de API. Estabeleça uma duração que corresponda ao cache de resposta do serviço `Cache-Control` de backend, conforme especificado pela diretiva do serviço apoiado.
- Veja como efetuar a filtragem de conteúdo. Remova os elementos de dados da resposta recebida da parte de trás utilizando as políticas de fluxo de [controlo](api-management-advanced-policies.md#choose) e [conjunto do corpo.](api-management-transformation-policies.md#SetBody)
- Para descarregar as declarações políticas, consulte o repo gitHub [api-management/policies.](https://github.com/Azure/api-management-samples/tree/master/policies)


## <a name="syntax"></a><a name="Syntax"></a>Sintaxe
Expressões de declaração única `@(expression)`são `expression` fechadas em , onde é uma declaração de expressão C# bem formada.

Expressões multi-afirmações são `@{expression}`fechadas em . Todos os caminhos de código dentro de `return` expressões multi-afirmações devem terminar com uma declaração.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>Exemplos

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
As expressões podem ser usadas como valores de atributoou valores de texto em quaisquer [políticas](api-management-policies.md) de Gestão da API (a menos que a referência política especifique o contrário).

> [!IMPORTANT]
> Quando se utilizam expressões políticas, só há uma verificação limitada das expressões políticas quando a política é definida. As expressões são executadas pelo portal em tempo de execução, quaisquer exceções geradas por expressões políticas resultam num erro de tempo de execução.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>.NET Tipos-quadro permitidos em expressões políticas
O quadro seguinte enumera os tipos de quadro .NET e os seus membros que são permitidos nas expressões políticas.

|Tipo|Membros apoiados|
|--------------|-----------------------|
|Newtonsoft.Json.Formatação|Todos|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensões|Todos|
|Newtonsoft.Json.Linq.JArray|Todos|
|Newtonsoft.Json.Linq.JConstructor|Todos|
|Newtonsoft.Json.Linq.JContainer|Todos|
|Newtonsoft.Json.Linq.JObject|Todos|
|Newtonsoft.Json.Linq.JProperty|Todos|
|Newtonsoft.Json.Linq.JRaw|Todos|
|Newtonsoft.Json.Linq.JToken|Todos|
|Newtonsoft.Json.Linq.JTokenType|Todos|
|Newtonsoft.Json.Linq.JValue|Todos|
|Sistema.Matriz|Todos|
|Sistema.BitConvers|Todos|
|Sistema.Boolean|Todos|
|Sistema.Byte|Todos|
|Sistema.Char|Todos|
|System.Collections.Generic.Dictionary<TKey, TValue>|Todos|
|System.Collections.Generic.HashSet\<T>|Todos|
|System.Collections.Generic.ICollection\<T>|Todos|
|System.Collections.Generic.IDictionary<TKey, TValue>|Todos|
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
|Sistema.Converter|Todos|
|Sistema.Data|(Construtor), AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now, Parse, Second, Subtract, Ticks, TimeOfDay, Today, ToString, UtcNow, Year|
|Sistema.DateTimeKind|Utc|
|Sistema.DataTimeOffset|Todos|
|Sistema.Decimal|Todos|
|Sistema.Duplo|Todos|
|Sistema.Exceção|Todos|
|Sistema.Guia|Todos|
|Sistema.Int16|Todos|
|Sistema.Int32|Todos|
|Sistema.Int64|Todos|
|System.Io.StringReader|Todos|
|System.IO.StringWriter|Todos|
|System.Linq.Enumerable|Todos|
|Sistema.Matemática|Todos|
|Sistema.MidpointRounding|Todos|
|System.net.WebUtility|Todos|
|Sistema.Anulado|Todos|
|Sistema.Aleatório|Todos|
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
|System.Security.Cryptography.SimetricAlgorithm|Todos|
|System.Security.Cryptography.X509Certificates.PublicKey|Todos|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Todos|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Nome|
|System.Security.Cryptography.X509Certificates.X509Certificates|Todos|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Todos|
|System.Security.Cryptography.X509Certificates.X509ContentType|Todos|
|System.Security.Cryptography.X509Certificates.X509NameType|Todos|
|Sistema.Single|Todos|
|Sistema.String|Todos|
|System.StringComparer|Todos|
|Sistema.StringCom|Todos|
|System.StringSplitOptions|Todos|
|Sistema.Text.Encoding|Todos|
|System.Text.RegularExpressions.Capture|Índice, Comprimento, Valor|
|System.Text.RegularExpressions.CaptureCollection|Contagem, Item|
|System.Text.RegularExpressions.Group|Capturas, Sucesso|
|System.Text.RegularExpressions.GroupCollection|Contagem, Item|
|System.Text.RegularExpressions.Match|Vazio, Grupos, Resultado|
|System.Text.RegularExpressions.Regex|(Construtor), IsMatch, Match, Match, Match, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Todos|
|System.Text.StringBuilder|Todos|
|Sistema.Timespan|Todos|
|System.TimeZone|Todos|
|System.TimeZoneInfo.AdjustmentRule|Todos|
|System.TimeZoneInfo.TransitionTime|Todos|
|System.TimeZoneInfo|Todos|
|Sistema.Tuple|Todos|
|System.UInt16|Todos|
|System.UInt32|Todos|
|System.UInt64|Todos|
|Sistema.Uri|Todos|
|Sistema.UriPartial|Todos|
|System.Xml.Linq.Extensões|Todos|
|System.Xml.Linq.XAttribute|Todos|
|System.Xml.Linq.XCData|Todos|
|System.Xml.Linq.XComment|Todos|
|System.Xml.Linq.XContainer|Todos|
|System.Xml.Linq.XDeclaration|Todos|
|System.Xml.Linq.XDocument|Todos, exceto de: Carga|
|System.Xml.Linq.XDocumentType|Todos|
|System.Xml.Linq.XElement|Todos|
|System.Xml.Linq.XName|Todos|
|System.Xml.Linq.XNamespace|Todos|
|System.Xml.Linq.XNode|Todos|
|System.Xml.Linq.XNodeDocumentOrderOrderComparer|Todos|
|System.Xml.Linq.XNodeEqualityComparer|Todos|
|System.Xml.Linq.XObject|Todos|
|System.Xml.Linq.XProcessingInstruction|Todos|
|System.Xml.Linq.XText|Todos|
|System.Xml.XmlNodeType|Todos|

## <a name="context-variable"></a><a name="ContextVariables"></a>Variável de contexto
Uma variável nomeada `context` está implicitamente disponível em todas as [expressões](api-management-policy-expressions.md#Syntax)políticas. Os seus membros `\request`fornecem informações pertinentes ao . Todos os `context` membros são apenas para leitura.

|Variável de contexto|Métodos, propriedades e valores de parâmetros permitidos|
|----------------------|-------------------------------------------------------|
|contexto|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementação](#ref-context-deployment)<br /><br /> Decorrido: TimeSpan - intervalo de tempo entre o valor da marca temporal e o tempo atual<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operação](#ref-context-operation)<br /><br /> [Produto](#ref-context-product)<br /><br /> [Pedir](#ref-context-request)<br /><br /> RequestId: Guid - identificador de pedido único<br /><br /> [Resposta](#ref-context-response)<br /><br /> [Subscrição](#ref-context-subscription)<br /><br /> Carimbo de tempo: DataTime - hora em que o pedido foi recebido<br /><br /> Rastreio: bool - indica se o rastreio está ligado ou desligado <br /><br /> [Utilizador](#ref-context-user)<br /><br /> [Variáveis](#ref-context-variables): IReadOnlyDictionary<string, object><br /><br /> vestígio sem efeito (mensagem: corda)|
|<a id="ref-context-api"></a>contexto. Rio Api|Id: corda<br /><br /> IsCurrentRevision: bool<br /><br />  Nome: cadeia<br /><br /> Caminho: corda<br /><br /> Revisão: cadeia<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versão: string |
|<a id="ref-context-deployment"></a>contexto. Implantação|Região: corda<br /><br /> Nome do serviço: corda<br /><br /> Certificados: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>contexto. Erro Último|Fonte: corda<br /><br /> Razão: corda<br /><br /> Mensagem: corda<br /><br /> Âmbito: corda<br /><br /> Secção: corda<br /><br /> Caminho: corda<br /><br /> PolicyId: string<br /><br /> Para mais informações sobre o contexto. LastError, consulte [o manuseamento de erros](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>contexto. Operação|Id: corda<br /><br /> Método: cadeia<br /><br /> Nome: cadeia<br /><br /> UrlTemplate: corda|
|<a id="ref-context-product"></a>contexto. Produto|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> AprovaçãoNecessária: bool<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: corda<br /><br /> Nome: cadeia<br /><br /> Estado: enum ProductState {NotPublished, Published}<br /><br /> SubscriçãoLimite: int?<br /><br /> AssinaturaNecessária: bool|
|<a id="ref-context-request"></a>contexto. Pedido|Corpo: [IMessageBody](#ref-imessagebody) ou `null` se o pedido não tiver um corpo.<br /><br /> Certificado: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Cabeçalhos](#ref-context-request-headers): IReadOnlyDictionary<corda, corda[]><br /><br /> IpAddress: cadeia<br /><br /> Parâmetros combinados: IReadOnlyDictionary<string, string><br /><br /> Método: cadeia<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>contexto de cordas. Request.Headers.GetValueOrDefault (nome do cabeçalho: string, defaultValue: string)|nome do cabeçalho: corda<br /><br /> defaultValue: string<br /><br /> Devoluções valores de cabeçalho `defaultValue` de pedido separados pela vírem ou se o cabeçalho não for encontrado.|
|<a id="ref-context-response"></a>contexto. Resposta|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Cabeçalhos](#ref-context-response-headers): IReadOnlyDictionary<corda, corda[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>contexto de cordas. Response.Headers.GetValueOrDefault (nome do cabeçalho: string, defaultValue: string)|nome do cabeçalho: corda<br /><br /> defaultValue: string<br /><br /> Devolve os valores do cabeçalho `defaultValue` de resposta separados pela vírsia ou se o cabeçalho não for encontrado.|
|<a id="ref-context-subscription"></a>contexto. Assinatura|Hora Criada: DataTime<br /><br /> Data limite: Data?<br /><br /> Id: corda<br /><br /> Chave: corda<br /><br /> Nome: cadeia<br /><br /> PrimaryKey: corda<br /><br /> SegundaChave: corda<br /><br /> Data de início: Data de data?|
|<a id="ref-context-user"></a>contexto. Utilizador|E-mail: string<br /><br /> Primeiro Nome: corda<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: corda<br /><br /> Identidades: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> Apelido: corda<br /><br /> Nota: corda<br /><br /> Data de Inscrição: Data de Data|
|<a id="ref-iapi"></a>IApi|Id: corda<br /><br /> Nome: cadeia<br /><br /> Caminho: corda<br /><br /> Protocolos: Linha<IEnumerable\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Nomes de parâmetros de subscriçãoKeyParameter: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGrupo|Id: corda<br /><br /> Nome: cadeia|
|<a id="ref-imessagebody"></a>Corpo de Mensagem|Como<\>T (preservarContent: bool = falso): Onde T: string, byte[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os `context.Request.Body.As<T>` `context.Response.Body.As<T>` métodos e métodos são utilizados para ler `T`um pedido e corpos de mensagem de resposta num tipo especificado . Por defeito, o método utiliza o fluxo corporal original da mensagem e torna-o indisponível após a sua devolução. Para evitar isso, mantendo o método funcionar numa `preserveContent` cópia da `true`corrente corporal, coloque o parâmetro para . Vá [aqui](api-management-transformation-policies.md#SetBody) ver um exemplo.|
|<a id="ref-iurl"></a>IUrl|Anfitrião: corda<br /><br /> Caminho: corda<br /><br /> Porto: int<br /><br /> [Consulta](#ref-iurl-query): IReadOnlyDictionary<string, string[]><br /><br /> ConsultaString: corda<br /><br /> Esquema: cadeia|
|<a id="ref-iuseridentity"></a>Identidade iUser|Id: corda<br /><br /> Fornecedor: cadeia|
|<a id="ref-isubscriptionkeyparameternames"></a>Nomes iSubscriptionKeyParameterNames|Cabeçalho: corda<br /><br /> Consulta: corda|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault (queryParâmetrometerName: string, defaultValue: string)|consultaParâmetronome: cadeia<br /><br /> defaultValue: string<br /><br /> Devolve valores de parâmetro seletiva `defaultValue` separados da vírdica ou se o parâmetro não for encontrado.|
|<a id="ref-context-variables"></a>Contexto T. Variáveis.GetValueOrDefault<\>T (nome variável: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Devolve o valor `T` variável fundido para o tipo ou `defaultValue` se a variável não for encontrada.<br /><br /> Este método abre uma exceção se o tipo especificado não corresponder ao tipo real da variável devolvida.|
|BasicAuthCredentials AsBasic (entrada: esta cadeia)|entrada: corda<br /><br /> Se o parâmetro de entrada contiver um valor de pedido de pedido `BasicAuthCredentials`de autorização de autenticação básica HTTP válido, o método devolve um objeto de tipo; caso contrário, o método devolve nulo.|
|bool TryParseBasic (entrada: esta cadeia, resultado: out BasicAuthCredentials)|entrada: corda<br /><br /> resultado: fora BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de `true` autenticação básica HTTP válido `BasicAuthCredentials`no cabeçalho de pedido, o método devolve e o parâmetro de resultados contém um valor de tipo; caso contrário, `false`o método retorna.|
|BasicAuthCredentials|Palavra-passe: corda<br /><br /> UserId: cadeia|
|Jwt AsJwt (entrada: esta corda)|entrada: corda<br /><br /> Se o parâmetro de entrada contiver um valor simbólico jWT `Jwt`válido, o método devolve um objeto de tipo; caso contrário, `null`o método retorna.|
|bool TryParseJwt (entrada: esta corda, resultado: fora Jwt)|entrada: corda<br /><br /> resultado: fora Jwt<br /><br /> Se o parâmetro de entrada contiver um valor simbólico `true` jWT válido, o método `Jwt`retorna e o parâmetro de resultados contém um valor de tipo; caso contrário, `false`o método retorna.|
|Rio Jwt|Algoritmo: corda<br /><br /> Público: Linha de<IEnumerable\><br /><br /> Reivindicações: IReadOnlyDictionary<string, string[]><br /><br /> Tempo de validade: Data?<br /><br /> Id: corda<br /><br /> Emitente: corda<br /><br /> EmitidoAt: DataTime?<br /><br /> Não Antes: Hora do Encontro?<br /><br /> Assunto: corda<br /><br /> Tipo: corda|
|string Jwt.Claims.GetValueOrDefault (nome de reclamação: string, defaultValue: string)|reivindicaçãoNome: cadeia<br /><br /> defaultValue: string<br /><br /> Devoluções valores de reclamação `defaultValue` separados pela vírem ou se o cabeçalho não for encontrado.|
|byte[] Encrypt[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[]]|entrada - texto simples a ser encriptado<br /><br />alg - nome de um algoritmo de encriptação simétrica<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devoluções texto simples encriptado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - texto simples a ser encriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devoluções texto simples encriptado.|
|byte[] Encrypt[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|entrada - texto simples a ser encriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devoluções texto simples encriptado.|
|byte[] Decrypt(entrada: este byte[], alg: string, key:byte[], iv:byte[]]|entrada - texto cifrado a ser desencriptado<br /><br />alg - nome de um algoritmo de encriptação simétrica<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve texto simples.|
|byte[] Decrypt (entrada: este byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|entrada - texto cifrado a ser desencriptado<br /><br />alg - algoritmo de encriptação<br /><br />Devolve texto simples.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[]]|entrada - texto cifrado a ser desencriptado<br /><br />alg - algoritmo de encriptação<br /><br />chave - chave de encriptação<br /><br />iv - vetor de inicialização<br /><br />Devolve texto simples.|
|bool CheckNoRevocation (entrada: este Sistema.Security.Cryptography.X509Certificados.X509Certificados2)|Executa uma validação em cadeia X.509 sem verificar o estado de revogação do certificado.<br /><br />entrada - objeto de certificado<br /><br />Devoluções `true` se a validação for bem sucedida; `false` se a validação falhar.|


## <a name="next-steps"></a>Passos seguintes

Para mais informações que trabalhem com políticas, consulte:

+ [Políticas em Gestão aPi](api-management-howto-policies.md)
+ [Transforme APIs](transform-api.md)
+ [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
+ [Amostras políticas](policy-samples.md)
