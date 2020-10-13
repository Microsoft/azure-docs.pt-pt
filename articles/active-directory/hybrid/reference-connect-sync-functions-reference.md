---
title: 'Azure AD Connect Sync: Funções Referência / Microsoft Docs'
description: Referência das expressões declarativas de provisionamento na sincronização Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8ec8eec28d66cf93608393ddca45f78460d831
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279776"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect Sync: Funções Referência
No Azure AD Connect, as funções são usadas para manipular um valor de atributo durante a sincronização.  
A Sintaxe das funções é expressa utilizando o seguinte formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se uma função estiver sobrecarregada e aceitar múltiplas sintaxes, todas as sintaxes válidas são listadas.  
As funções são fortemente dactilografadas e verificam se o tipo passado corresponde ao tipo documentado.  
Se o tipo não corresponder, é lançado um erro.

Os tipos são expressos com a seguinte sintaxe:

* **bin** – Binário
* **bool** – Boolean
* **dt** – Data/Hora utc
* **enum** – Enumeração de constantes conhecidas
* **exp** – Expressão, que se espera que avalie para um Boolean
* **mvbin** - Binário multi-valorado
* **mvstr** - Cadeia multi-valorizada
* **mvref** - Referência multi-valorizada
* **num** – Numérico
* **ref** – Referência
* **str** – Corda
* **var** – Uma variante de (quase) qualquer outro tipo
* **vazio** – não devolve um valor

As funções com os tipos **mvbin**, **mvstr**e **mvref** só podem funcionar em atributos multi-valorizados. Funções com **bin**, **str**, e **ref** trabalham em atributos de valor único e multi-valor.

## <a name="functions-reference"></a>Referência das Funções

* **Certificado**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [Nome CertFriendly](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [Número de Certificadoserial](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertsubjectNamedn](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [IsCert](#iscert)
* **Conversão**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConverterFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConverterFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **Data/Hora**
  * [DataAdd](#dateadd)
  * [DataFromNum](#datefromnum)
  * [FormatDatime](#formatdatetime)
  * [Agora,](#now)
  * [NumFromDate](#numfromdate)
* **Diretório**
  * [DNComponente](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **Avaliação**
  * [IsBitSet](#isbitset)
  * [IsDate](#isdate)
  * [IsEmpty](#isempty)
  * [IsGuid](#isguid)
  * [Isnull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [IsPresent](#ispresent)
  * [IsString](#isstring)
* **Matemática**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **Multi*valorizado**
  * [Contém](#contains)
  * [Contagem](#count)
  * [Item](#item)
  * [ItemOrNull](#itemornull)
  * [Join](#join)
  * [Removeduplicates](#removeduplicates)
  * [Divisão](#split)
* **Fluxo de Programas**
  * [Erro](#error)
  * [IIF](#iif)
  * [Selecionar](#select)
  * [Mudar](#switch)
  * [Onde](#where)
  * [Com](#with)
* **Texto**
  * [GUIADOR](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [Esquerda](#left)
  * [Len](#len)
  * [LTrim](#ltrim)
  * [Meio](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [Substituir](#replace)
  * [SubstituirChars](#replacechars)
  * [Direita](#right)
  * [RTrim](#rtrim)
  * [Guarnição](#trim)
  * [UCase](#ucase)
  * [Word](#word)

---
### <a name="bitand"></a>BitAnd
**Description:**  
A função BitAnd define bits especificados num valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

* valor1, valor2: valores numéricos que devem ser e'ed juntos

**Observações:**  
Esta função converte ambos os parâmetros para a representação binária e define um pouco para:

* 0 - se uma ou ambas as bits correspondentes no *valor1* e *no valor2* forem 0
* 1 - se ambas as partes correspondentes forem 1.

Por outras palavras, devolve 0 em todos os casos, exceto quando os pedaços correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
`BitAnd(&HF, &HF7)`  
Devoluções 7 porque hexadecimal "F" e "F7" avaliam este valor.

---
### <a name="bitor"></a>BitOr
**Description:**  
A função BitOr define bits especificados num valor.

**Sintaxe:**  
`num BitOr(num value1, num value2)`

* valor1, valor2: valores numéricos que devem ser or'ed juntos

**Observações:**  
Esta função converte ambos os parâmetros para a representação binária e define um pouco para 1 se um ou ambos os bits correspondentes na máscara e bandeira forem 1, e para 0 se ambos os bits correspondentes forem 0. Por outras palavras, devolve 1 em todos os casos, exceto quando os pedaços correspondentes de ambos os parâmetros são 0.

---
### <a name="cbool"></a>CBool
**Description:**  
A função CBool devolve um Boolean com base na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Observações:**  
Se a expressão avalia um valor não-zero, então o CBool devolve True, caso contrário retorna Falso.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Devoluções Verdadeiras se ambos os atributos tiverem o mesmo valor.

---
### <a name="cdate"></a>CDate
**Description:**  
A função CDate devolve uma data de utc hora de uma corda. DateTime não é um tipo de atributo nativo em Sync, mas é usado por algumas funções.

**Sintaxe:**  
`dt CDate(str value)`

* Valor: Uma cadeia com data, hora e opcionalmente fuso horário

**Observações:**  
A corda devolvida está sempre na UTC.

**Exemplo:**  
`CDate([employeeStartTime])`  
Retorna uma DataTime com base na hora de início do trabalhador

`CDate("2013-01-10 4:00 PM -8")`  
Devolve uma DataTime representando "2013-01-11 12:00 AM"


---
### <a name="certextensionoids"></a>CertExtensionOids
**Description:**  
Devolve os valores Oid de todas as extensões críticas de um objeto de certificado.

**Sintaxe:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certformat"></a>CertFormat
**Description:**  
Devolve o nome do formato deste certificado X.509v3.

**Sintaxe:**  
`str CertFormat(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certfriendlyname"></a>Nome CertFriendly
**Description:**  
Devolve o pseudónimo associado a um certificado.

**Sintaxe:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certhashstring"></a>CertHashString
**Description:**  
Devolve o valor de haxixe SHA1 para o certificado X.509v3 como uma corda hexadecimal.

**Sintaxe:**  
`str CertHashString(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certissuer"></a>CertIssuer
**Description:**  
Devolve o nome da autoridade de certificados que emitiu o certificado X.509v3.

**Sintaxe:**  
`str CertIssuer(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Description:**  
Devolve o nome distinto do emitente de certificado.

**Sintaxe:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certissueroid"></a>CertIssuerOid
**Description:**  
Devolve o Oid do emitente de certificado.

**Sintaxe:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Description:**  
Devolve a informação do algoritmo chave para este certificado X.509v3 como uma corda.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Description:**  
Devolve os parâmetros-chave do algoritmo para o certificado X.509v3 como uma cadeia hexadecimal.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certnameinfo"></a>CertNameInfo
**Description:**  
Devolve o nome do sujeito e do emitente de um certificado.

**Sintaxe:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.
*   X509NameType: O valor X509NameType para o sujeito.
*   inclui o nome do nome do emitente: verdadeiro para incluir o nome do emitente; caso contrário, falso.

---
### <a name="certnotafter"></a>CertNotAfter
**Description:**  
Devolve a data na hora local após a qual um certificado deixou de ser válido.

**Sintaxe:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certnotbefore"></a>CertNotBefore
**Description:**  
Devolve a data na hora local em que um certificado se torna válido.

**Sintaxe:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Description:**  
Devolve o Oid da chave pública para o certificado X.509v3.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Description:**  
Devolve o Oid dos parâmetros chave públicos para o certificado X.509v3.

**Sintaxe:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certserialnumber"></a>Número de Certificadoserial
**Description:**  
Devolve o número de série do certificado X.509v3.

**Sintaxe:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Description:**  
Devolve o Oid do algoritmo usado para criar a assinatura de um certificado.

**Sintaxe:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certsubject"></a>CertSubject
**Description:**  
Obtém o nome distinto do sujeito de um certificado.

**Sintaxe:**  
`str CertSubject(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certsubjectnamedn"></a>CertsubjectNamedn
**Description:**  
Devolve o nome distinto do sujeito de um certificado.

**Sintaxe:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Description:**  
Devolve o Oid do nome do sujeito a partir de um certificado.

**Sintaxe:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certthumbprint"></a>CertThumbprint
**Description:**  
Devolve a impressão digital de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="certversion"></a>CertVersion
**Description:**  
Devolve a versão em formato X.509 de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.

---
### <a name="cguid"></a>CGuid
**Description:**  
A função CGuid converte a representação de cordas de um GUID para a sua representação binária.

**Sintaxe:**  
`bin CGuid(str GUID)`

* Uma corda formatada neste padrão: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxx ou {xxxxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

---
### <a name="contains"></a>Contains
**Description:**  
A função Contém encontra uma corda dentro de um atributo multi-valor

**Sintaxe:**  
`num Contains (mvstring attribute, str search)` - sensível a casos  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` - sensível a casos

* atributo: o atributo multi-valor para a pesquisa.
* pesquisa: corda para encontrar no atributo.
* Casetype: CaseInsensitive ou CaseSensitive.

Índice de devoluções no atributo multi-valorado onde a cadeia foi encontrada. 0 é devolvido se a corda não for encontrada.

**Observações:**  
Para atributos de cadeia multi-valorizados, a pesquisa encontra sublagem nos valores.  
Para atributos de referência, a cadeia procurada deve corresponder exatamente ao valor a considerar uma correspondência.

**Exemplo:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Se o atributo proxyAddresses tiver um endereço de e-mail primário (indicado por maiúscula "SMTP:"), em seguida, devolva o atributo proxyAddress, caso contrário, devolva um erro.

---
### <a name="convertfrombase64"></a>ConverterFromBase64
**Description:**  
A função ConvertFromBase64 converte o valor codificado base 64 especificado para uma cadeia regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)` - assume o Unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* fonte: Base64 cadeia codificada  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos regressam "*Olá mundo!*"

---
### <a name="convertfromutf8hex"></a>ConverterFromUTF8Hex
**Description:**  
A função ConvertFromUTF8Hex converte o valor codificado utf8 000 para uma cadeia.

**Sintaxe:**  
`str ConvertFromUTF8Hex(str source)`

* fonte: UTF8 2 byte encodificado picada

**Observações:**  
A diferença entre esta função e a ConvertFromBase64([],UTF8) na qual o resultado é amigável para o atributo DN.  
Este formato é utilizado pelo Azure Ative Directory como DN.

**Exemplo:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Retorna "*Olá mundo!"*

---
### <a name="converttobase64"></a>ConvertToBase64
**Description:**  
A função ConvertToBase64 converte uma corda numa cadeia base Unicode64.  
Converte o valor de uma matriz de inteiros à sua representação de cordas equivalente que é codificada com dígitos base-64.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Devoluções "SABlAGwAbABvACAAdwBvAHIAbABKACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Description:**  
A função ConvertToUTF8Hex converte uma cadeia para um valor codificado utf8 Hex.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Observações:**  
O formato de saída desta função é utilizado pelo Azure Ative Directory como formato de atributo DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Devoluções 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Description:**  
A função Contagem devolve o número de elementos num atributo multi-valor

**Sintaxe:**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**Description:**  
A função CNum pega numa corda e devolve um tipo de dado numérico.

**Sintaxe:**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Description:**  
Converte uma cadeia para um atributo de referência

**Sintaxe:**  
`ref CRef(str value)`

**Exemplo:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**Description:**  
A função CStr converte-se num tipo de dados de cadeia.

**Sintaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* valor: Pode ser um valor numérico, atributo de referência ou Boolean.

**Exemplo:**  
`CStr([dn])`  
Poderia devolver "cn=Joe,dc=contoso,dc=com"

---
### <a name="dateadd"></a>DateAdd
**Description:**  
Retorna uma Data contendo uma data à qual foi adicionado um intervalo de tempo especificado.

**Sintaxe:**  
`dt DateAdd(str interval, num value, dt date)`

* intervalo: Expressão de corda que é o intervalo de tempo que pretende adicionar. A cadeia deve ter um dos seguintes valores:
  * yyyy Ano
  * q Trimestre
  * m Mês
  * y Dia do ano
  * d Dia
  * w Dia da Semana
  * Semana ww
  * h Hora
  * minuto
  * s Segundo
* valor: O número de unidades que pretende adicionar. Pode ser positivo (para obter datas no futuro) ou negativo (para obter datas no passado).
* data: DataGem representando data à qual o intervalo é adicionado.

**Exemplo:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Adiciona 3 meses e devolve uma DataTime representando "2001-04-01".

---
### <a name="datefromnum"></a>DataFromNum
**Description:**  
A função DataFromNum converte um valor no formato de data da AD para um tipo DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devoluções de uma DataTime representando 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponente
**Description:**  
A função DNComponent devolve o valor de um componente DN especificado que vai da esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: o atributo de referência para interpretar
* ComponentNumber: O componente no DN para devolver

**Exemplo:**  
`DNComponent(CRef([dn]),1)`  
Se dn é "cn=Joe,ou=...", devolve Joe

---
### <a name="dncomponentrev"></a>DNComponentRev
**Description:**  
A função DNComponentRev devolve o valor de um componente DN especificado que vai da direita (final).

**Sintaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: o atributo de referência para interpretar
* ComponentNumber - O componente no DN para devolver
* Opções: DC – Ignorar todos os componentes com "dc="

**Exemplo:**  
Se dn é "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com" então  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Ambos regressam aos EUA.

---
### <a name="error"></a>Erro
**Description:**  
A função Erro é utilizada para retornar um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se a conta de atributosName não estiver presente, atire um erro no objeto.

---
### <a name="escapedncomponent"></a>EscapeDNComponent
**Description:**  
A função EscapeDNComponent requer um componente de um DN e escapa-o para que possa ser representado em LDAP.

**Sintaxe:**  
`str EscapeDNComponent(str value)`

**Exemplo:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Certifique-se de que o objeto pode ser criado num diretório LDAP mesmo que o atributo displayName tenha caracteres que devem ser escapados em LDAP.

---
### <a name="formatdatetime"></a>FormatDatime
**Description:**  
A função FormatDateTime é usada para formatar uma DataTime para uma cadeia com um formato especificado

**Sintaxe:**  
`str FormatDateTime(dt value, str format)`

* valor: um valor no formato DateTime
* formato: uma cadeia que representa o formato a converter.

**Observações:**  
Os valores possíveis para o formato podem ser consultados aqui: [Formatos de data e hora personalizados para a função FORMAT](/dax/custom-date-and-time-formats-for-the-format-function).

**Exemplo:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resultados em "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Pode resultar em "20140905081453.0Z"

---
### <a name="guid"></a>GUID
**Description:**  
A função Guid gera um novo GUID aleatório

**Sintaxe:**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Description:**  
A função IIF devolve um de um conjunto de valores possíveis com base numa condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condição: qualquer valor ou expressão que possa ser avaliado para verdadeiro ou falso.
* valueIfTrue: Se a condição avaliar a verdade, o valor devolvido.
* valueIfFalse: Se a condição avaliar a falsa, o valor devolvido.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o utilizador for um estagiário, devolve o pseudónimo de um utilizador com "t-" adicionado ao início do mesmo, caso contrário, devolve o pseudónimo do utilizador como está.

---
### <a name="instr"></a>InStr
**Description:**  
A função InStr encontra a primeira ocorrência de um sublaming numa corda

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: cadeia a ser pesquisada
* stringmatch: corda a ser encontrada
* início: posição inicial para encontrar o sub-cording
* comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição onde o sublamento foi encontrado ou 0 se não for encontrado.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Evalências a 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia a 7

---
### <a name="instrrev"></a>InStrRev
**Description:**  
A função InStrRev encontra a última ocorrência de um substring em uma corda

**Sintaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: cadeia a ser pesquisada
* stringmatch: corda a ser encontrada
* início: posição inicial para encontrar o sub-cording
* comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição onde o sublamento foi encontrado ou 0 se não for encontrado.

**Exemplo:**  
`InStrRev("abbcdbbbef","bb")`  
Devoluções 7

---
### <a name="isbitset"></a>IsBitSet
**Description:**  
A função IsBitSet Testa se um pouco estiver definido ou não

**Sintaxe:**  
`bool IsBitSet(num value, num flag)`

* valor: um valor numérico que é avaliado.bandeira: um valor numérico que tem o bit a ser avaliado

**Exemplo:**  
`IsBitSet(&HF,4)`  
Devoluções Verdadeiras porque o bit "4" está definido no valor hexadecimal "F"

---
### <a name="isdate"></a>IsDate
**Description:**  
Se a expressão pode ser avaliada como um tipo de DateTime, então a função IsDate avalia para True.

**Sintaxe:**  
`bool IsDate(var Expression)`

**Observações:**  
Usado para determinar se o CDate pode ser bem sucedido.

---
### <a name="iscert"></a>IsCert
**Description:**  
Devoluções verdadeiras se os dados brutos puderem ser serializados em .NET X509Certificate2 certificate object.

**Sintaxe:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificadoRawData: Byte array representation de um certificado X.509. O conjunto byte pode ser codificado binário (DER) ou dados X.509 codificados pela Base64.
---
### <a name="isempty"></a>IsEmpty
**Description:**  
Se o atributo estiver presente no CS ou MV mas avaliar a uma corda vazia, então a função IsEmpty avalia a True.

**Sintaxe:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Description:**  
Se a corda pode ser convertida para um GUID, então a função IsGuid avaliada para ser verdadeira.

**Sintaxe:**  
`bool IsGuid(str GUID)`

**Observações:**  
Um GUID é definido como uma corda seguindo um destes padrões: xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxx ou {xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

Usado para determinar se cGuid pode ser bem sucedido.

**Exemplo:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Se o StrAttribute tiver um formato GUID, devolva uma representação binária, caso contrário, devolva um Nulo.

---
### <a name="isnull"></a>Isnull
**Description:**  
Se a expressão avaliar a Nulo, então a função IsNull retorna verdadeiramente.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Observações:**  
Para um atributo, um Nulo é expresso pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Devoluções Verdadeiras se o atributo não estiver presente no CS ou MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Description:**  
Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeiramente.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Observações:**  
Para um atributo, isto avaliaria a True se o atributo está ausente ou está presente, mas é uma corda vazia.  
O inverso desta função chama-se IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Devoluções Verdadeiras se o atributo não estiver presente ou se for uma corda vazia no CS ou MV.

---
### <a name="isnumeric"></a>IsNumeric
**Description:**  
A função Isnúrico devolve um valor Boolean indicando se uma expressão pode ser avaliada como um tipo de número.

**Sintaxe:**  
`bool IsNumeric(var Expression)`

**Observações:**  
Usado para determinar se o CNum pode ser bem sucedido para analisar a expressão.

---
### <a name="isstring"></a>IsString
**Description:**  
Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.

**Sintaxe:**  
`bool IsString(var expression)`

**Observações:**  
Usado para determinar se o CStr() pode ser bem sucedido para analisar a expressão.

---
### <a name="ispresent"></a>IsPresent
**Description:**  
Se a expressão avaliar a uma cadeia que não é nula e não está vazia, então a função IsPresent retorna verdadeiramente.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Observações:**  
O inverso desta função chama-se IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Description:**  
A função Item devolve um item de uma cadeia/atributo multi-valor.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

* atributo: atributo multi-valor
* índice: índice para um item na cadeia multi-valor.

**Observações:**  
A função 'Item' é útil juntamente com a função Conter, uma vez que esta última função devolve o índice a um item no atributo multi-valor.

Lança um erro se o índice estiver fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Devolve o endereço de e-mail primário.

---
### <a name="itemornull"></a>ItemOrNull
**Description:**  
A função ItemOrNull devolve um item de uma cadeia/atributo multi-valor.

**Sintaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

* atributo: atributo multi-valor
* índice: índice para um item na cadeia multi-valor.

**Observações:**  
A função ItemOrNull é útil juntamente com a função Conter, uma vez que esta última função devolve o índice a um item no atributo multi-valor.

Se o índice estiver fora dos limites, então devolve um valor nulo.

---
### <a name="join"></a>Associar
**Description:**  
A função 'Juntar' pega numa cadeia multi-valorizada e devolve uma corda de valor único com separador especificado inserida entre cada item.

**Sintaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* atributo: Atributo multi-valor que contém cordas a juntar.
* delimiter: Qualquer corda, usada para separar os sublípes na corda devolvida. Se omitido, o carácter espacial (" ") é usado. Se Delimiter é uma corda de comprimento zero (") ou nada, todos os itens da lista são concatenados sem delimiters.

**Observações**  
Existe paridade entre as funções De Junção e Divisão. A função 'Juntar' pega numa variedade de cordas e junta-as usando uma corda delimiter, para devolver uma única corda. A função Split pega numa corda e separa-a no delimiter, para devolver uma série de cordas. No entanto, uma diferença fundamental é que o Join pode concatenar cordas com qualquer corda delimiter, Split só pode separar cordas usando um único delimiter de carácter.

**Exemplo:**  
`Join([proxyAddresses],",")`  
Poderia voltar: " SMTP:john.doe@contoso.com smtp:jd@contoso.com " "

---
### <a name="lcase"></a>LCase
**Description:**  
A função LCase converte todos os caracteres numa corda para minúsculas.

**Sintaxe:**  
`str LCase(str value)`

**Exemplo:**  
`LCase("TeSt")`  
Devolve "teste".

---
### <a name="left"></a>Esquerda
**Description:**  
A função Esquerda retorna um número especificado de caracteres à esquerda de uma corda.

**Sintaxe:**  
`str Left(str string, num NumChars)`

* corda: a cadeia para devolver os caracteres de
* NumChars: um número que identifica o número de caracteres para regressar desde o início (à esquerda) da cadeia

**Observações:**  
Uma corda contendo os primeiros caracteres numChars em corda:

* Se numChars = 0, devolva a corda vazia.
* Se os numChars < 0, devolva a cadeia de entrada.
* Se a corda for nula, devolva a corda vazia.

Se a cadeia contiver menos caracteres do que o número especificado em numChars, é devolvida uma corda idêntica à corda (isto é, contendo todos os caracteres no parâmetro 1).

**Exemplo:**  
`Left("John Doe", 3)`  
Devolve "Joh".

---
### <a name="len"></a>Len
**Description:**  
A função Len devolve o número de caracteres numa corda.

**Sintaxe:**  
`num Len(str value)`

**Exemplo:**  
`Len("John Doe")`  
Devoluções 8

---
### <a name="ltrim"></a>LTrim
**Description:**  
A função LTrim remove os principais espaços brancos de uma corda.

**Sintaxe:**  
`str LTrim(str value)`

**Exemplo:**  
`LTrim(" Test ")`  
Devoluções "Teste"

---
### <a name="mid"></a>Mid
**Description:**  
A função Média retorna um número especificado de caracteres de uma posição especificada numa cadeia.

**Sintaxe:**  
`str Mid(str string, num start, num NumChars)`

* corda: a cadeia para devolver os caracteres de
* início: um número que identifica a posição inicial em cadeia para devolver caracteres de
* NumChars: um número que identifica o número de caracteres para regressar da posição em cadeia

**Observações:**  
Retorno dos caracteres numChars a partir da posição começar em cadeia.  
Uma corda contendo caracteres numChars da posição iniciada na cadeia:

* Se numChars = 0, devolva a corda vazia.
* Se os numChars < 0, devolva a cadeia de entrada.
* Se iniciar > o comprimento da corda, devolva a corda de entrada.
* Se iniciar <= 0, devolva a cadeia de entrada.
* Se a corda for nula, devolva a corda vazia.

Se não houver caracteres numChar que permaneçam em cadeia desde o início da posição, o maior número possível de caracteres são devolvidos.

**Exemplo:**  
`Mid("John Doe", 3, 5)`  
Devoluções "hn Do".

`Mid("John Doe", 6, 999)`  
Devoluções "Doe"

---
### <a name="now"></a>Now
**Description:**  
A função 'Agora' retorna uma DataTime especificando a data e hora atuais, de acordo com a data e hora do sistema do seu computador.

**Sintaxe:**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Description:**  
A função NumFromDate devolve uma data no formato de data da AD.

**Sintaxe:**  
`num NumFromDate(dt value)`

**Exemplo:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Devoluções 12969932400000000

---
### <a name="padleft"></a>PadLeft
**Description:**  
A função PadLeft deixa de ter uma corda a um comprimento especificado utilizando um carácter de enchimento fornecido.

**Sintaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

* corda: a corda para pad.
* comprimento: Um inteiro que representa o comprimento desejado da corda.
* padCharacter: Uma corda composta por um único personagem para usar como o personagem do pad

**Observações:**

* Se o comprimento da corda for inferior ao comprimento, então o padCharacter é repetidamente anexado ao início (à esquerda) da corda até ter um comprimento igual ao comprimento.
* PadCharacter pode ser um personagem espacial, mas não pode ser um valor nulo.
* Se o comprimento da corda for igual ou superior ao comprimento, a corda é devolvida inalterada.
* Se a corda tiver um comprimento maior ou igual ao comprimento, uma corda idêntica à corda é devolvida.
* Se o comprimento da corda for inferior ao comprimento, então uma nova corda do comprimento desejado é devolvida contendo uma corda acolchoada com um padCharacter.
* Se a corda for nula, a função devolve uma corda vazia.

**Exemplo:**  
`PadLeft("User", 10, "0")`  
Devolve "0000000User".

---
### <a name="padright"></a>PadRight
**Description:**  
A função PadRight ressa otenta uma corda a um comprimento especificado utilizando um carácter de enchimento fornecido.

**Sintaxe:**  
`str PadRight(str string, num length, str padCharacter)`

* corda: a corda para pad.
* comprimento: Um inteiro que representa o comprimento desejado da corda.
* padCharacter: Uma corda composta por um único personagem para usar como o personagem do pad

**Observações:**

* Se o comprimento da corda for inferior ao comprimento, então o padCharacter é repetidamente anexado à extremidade (direita) da corda até ter um comprimento igual ao comprimento.
* padCharacter pode ser um personagem espacial, mas não pode ser um valor nulo.
* Se o comprimento da corda for igual ou superior ao comprimento, a corda é devolvida inalterada.
* Se a corda tiver um comprimento maior ou igual ao comprimento, uma corda idêntica à corda é devolvida.
* Se o comprimento da corda for inferior ao comprimento, então uma nova corda do comprimento desejado é devolvida contendo uma corda acolchoada com um padCharacter.
* Se a corda for nula, a função devolve uma corda vazia.

**Exemplo:**  
`PadRight("User", 10, "0")`  
Devolve "User00000".

---
### <a name="pcase"></a>PCase
**Description:**  
A função PCase converte o primeiro carácter de cada palavra delimitada em um fio para maiúscula, e todos os outros caracteres são convertidos para maiúsculas.

**Sintaxe:**  
`String PCase(string)`

**Observações:**

* Esta função não fornece atualmente um invólucro adequado para converter uma palavra que é inteiramente maiúscula, como um acrónimo.

**Exemplo:**  
`PCase("TEsT")`  
Devolve "Teste".

`PCase(LCase("TEST"))`  
Devoluções "Teste"

---
### <a name="randomnum"></a>RandomNum
**Description:**  
A função RandomNum devolve um número aleatório entre um intervalo especificado.

**Sintaxe:**  
`num RandomNum(num start, num end)`

* início: um número que identifica o limite inferior do valor aleatório para gerar
* fim: um número que identifica o limite superior do valor aleatório para gerar

**Exemplo:**  
`Random(100,999)`  
Pode voltar 734.

---
### <a name="removeduplicates"></a>Removeduplicates
**Description:**  
A função RemoveDuplicates requer uma corda multi-valorizada e certifique-se de que cada valor é único.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devolve um atributo proxyAddress higienizado onde todos os valores duplicados foram removidos.

---
### <a name="replace"></a>Substituir
**Description:**  
A função Substituir substitui todas as ocorrências de uma corda para outra corda.

**Sintaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

* corda: Uma corda para substituir valores em.
* OldValue: A corda para procurar e substituir.
* NewValue: A corda para substituir.

**Observações:**  
A função reconhece os seguintes nomes especiais:

* \n – Nova Linha
* \r – Retorno da Carruagem
* \t – Separador

**Exemplo:**  
`Replace([address],"\r\n",", ")`  
Substitui CRLF por uma vírgula e espaço, e pode levar a "One Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>SubstituirChars
**Description:**  
A função ReplaceChars substitui todas as ocorrências de caracteres encontrados na cadeia ReplacePattern.

**Sintaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

* corda: Uma corda para substituir caracteres em.
* SubstituaPattern: uma corda que contém um dicionário com caracteres para substituir.

O formato é {source1}:{target1},{source2}:{target2},{sourceN},{targetN},{targetN} onde a fonte é o personagem para encontrar e direcionar a cadeia para substituir.

**Observações:**

* A função toma cada ocorrência de fontes definidas e substitui-as pelos alvos.
* A fonte deve ser exatamente um (unicode) caráter.
* A fonte não pode ser vazia ou mais comprida do que um carácter (erro de análise).
* O alvo pode ter vários caracteres, por exemplo ö:oe, β:ss.
* O alvo pode estar vazio indicando que o carácter deve ser removido.
* A fonte é sensível a casos e deve ser exatamente compatível.
* Os , (vírgula) e : (cólon) são caracteres reservados e não podem ser substituídos usando esta função.
* Os espaços e outros caracteres brancos na cadeia ReplacePattern são ignorados.

**Exemplo:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Devoluções Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Devoluções "ONeil", o tique-taque único é definido para ser removido.

---
### <a name="right"></a>Direita
**Description:**  
A função Direita devolve um número especificado de caracteres a partir da direita (extremidade) de uma corda.

**Sintaxe:**  
`str Right(str string, num NumChars)`

* corda: a cadeia para devolver os caracteres de
* NumChars: um número que identifica o número de caracteres para regressar do final (à direita) da cadeia

**Observações:**  
Os caracteres NumChars são devolvidos da última posição da corda.

Uma corda contendo os últimos caracteres numChars em corda:

* Se numChars = 0, devolva a corda vazia.
* Se os numChars < 0, devolva a cadeia de entrada.
* Se a corda for nula, devolva a corda vazia.

Se a cadeia contiver menos caracteres do que o número especificado em NumChars, é devolvida uma corda idêntica à corda.

**Exemplo:**  
`Right("John Doe", 3)`  
Devolve "Doe".

---
### <a name="rtrim"></a>RTrim
**Description:**  
A função RTrim remove espaços brancos de uma corda.

**Sintaxe:**  
`str RTrim(str value)`

**Exemplo:**  
`RTrim(" Test ")`  
Devoluções "Teste".

---
### <a name="select"></a>Selecione
**Description:**  
Processar todos os valores num atributo multi-valor (ou saída de uma expressão) com base na função especificada.

**Sintaxe:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: Representa um elemento no atributo multi-valor
* atributo: o atributo multi-valor
* expressão: uma expressão que devolve uma coleção de valores
* condição: qualquer função que possa processar um item no atributo

**Exemplos:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Devolva todos os valores no atributo multi-valore outros Telefones após a remoção de hífenes (-)

---
### <a name="split"></a>Dividir
**Description:**  
A função Split tem uma corda separada com um delimiter e torna-a uma corda multi-valorizada.

**Sintaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* valor: a cadeia com um carácter delimiter para separar.
* delimiter: personagem único para ser usado como o delimiter.
* limite: número máximo de valores que podem regressar.

**Exemplo:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Devolve uma cadeia multi-valorizada com 2 elementos úteis para o atributo proxyAddress.

---
### <a name="stringfromguid"></a>StringFromGuid
**Description:**  
A função StringFromGuid toma um GUID binário e converte-o numa corda

**Sintaxe:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Description:**  
A função StringFromSid converte uma matriz de byte contendo um identificador de segurança a uma corda.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Comutador
**Description:**  
A função Switch é utilizada para devolver um único valor com base em condições avaliadas.

**Sintaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Expressão variante que pretende avaliar.
* valor: Valor a devolver se a expressão correspondente for Verdadeira.

**Observações:**  
A lista de argumentos da função Switch consiste em pares de expressões e valores. As expressões são avaliadas da esquerda para a direita, e o valor associado à primeira expressão para avaliar a True é devolvido. Se as peças não estiverem corretamente emparelhadas, ocorre um erro de tempo de execução.

Por exemplo, se o expr1 for Verdadeiro, a Switch devolve o valor1. Se o expr-1 for falso, mas o expr-2 é verdadeiro, a Switch devolve o valor 2, e assim por diante.

A Switch devolve um Nada se:

* Nenhuma das expressões é verdadeira.
* A primeira expressão verdadeira tem um valor correspondente que é Nulo.

A Switch avalia todas as expressões, mesmo que retorne apenas uma delas. Por esta razão, deve ter cuidado com efeitos colaterais indesejáveis. Por exemplo, se a avaliação de qualquer expressão resultar numa divisão por erro zero, ocorre um erro.

O valor também pode ser a função Erro, que devolveria uma cadeia personalizada.

**Exemplo:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Devolve a língua falada em algumas grandes cidades, caso contrário devolve um Erro.

---
### <a name="trim"></a>Trim
**Description:**  
A função Trim remove espaços brancos de condução e de fuga de uma corda.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Devolve "Teste".

`Trim([proxyAddresses])`  
Remove espaços de liderança e de fuga para cada valor no atributo ProxyAddress.

---
### <a name="ucase"></a>UCase
**Description:**  
A função UCase converte todos os caracteres numa cadeia para a caixa superior.

**Sintaxe:**  
`str UCase(str string)`

**Exemplo:**  
`UCase("TeSt")`  
Devolve "TESTE".

---
### <a name="where"></a>Onde

**Description:**  
Devolve um subconjunto de valores a partir de um atributo multi-valor (ou saída de uma expressão) com base em condições específicas.

**Sintaxe:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: Representa um elemento no atributo multi-valor
* atributo: o atributo multi-valor
* condição: qualquer expressão que possa ser avaliada para verdadeiro ou falso
* expressão: uma expressão que devolve uma coleção de valores

**Exemplo:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Devolva os valores do certificado no atributo multi-valorCertificato que não estão caducados.

---
### <a name="with"></a>Com
**Description:**  
A função With proporciona uma forma de simplificar uma expressão complexa utilizando uma variável para representar uma subexpressão que aparece uma ou mais vezes na expressão complexa.

**Sintaxe:**
`With(var variable, exp subExpression, exp complexExpression)`  
* variável: Representa a subexpressão.
* subExpressão: subexpressão representada por variável.
* complexidadeExpressão: Uma expressão complexa.

**Exemplo:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
É funcionalmente equivalente a:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Que devolve apenas valores de certificado não percebidos no atributo userCertificate.


---
### <a name="word"></a>Word
**Description:**  
A função Word devolve uma palavra contida dentro de uma cadeia, com base em parâmetros que descrevem os delimitadores a utilizar e o número da palavra para devolver.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* corda: a corda para devolver uma palavra de.
* WordNumber: um número que identifica qual o número de palavra que deve ser retornante.
* delimiters: uma corda que representa o(s) delimiter(s) que deve ser usado para identificar palavras

**Observações:**  
Cada cadeia de caracteres em corda separada por um dos caracteres em delimiters são identificadas como palavras:

* Se o número < 1, retorna a corda vazia.
* Se a corda for nula, devolve a corda vazia.

Se a corda contiver menos de palavras numeradas, ou a corda não conter quaisquer palavras identificadas por delimiters, uma corda vazia é devolvida.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Devoluções "castanhas"

`Word("This,string!has&many separators",3,",!&#")`  
Voltaria "tem"

## <a name="additional-resources"></a>Recursos Adicionais
* [Compreender as Expressões de Aprovisionamento Declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: Personalizar opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)