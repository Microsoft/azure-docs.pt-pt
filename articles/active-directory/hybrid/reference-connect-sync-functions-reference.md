---
title: 'Sincronização de Azure AD Connect: Referência de funções | Microsoft Docs'
description: Referência de expressões de provisionamento declarativo no Azure AD Connect sincronização.
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
ms.openlocfilehash: 5c3102480e316c634930c356ae02f769767b7d08
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900049"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronização de Azure AD Connect: Referência das Funções
No Azure AD Connect, as funções são usadas para manipular um valor de atributo durante a sincronização.  
A sintaxe das funções é expressa usando o seguinte formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se uma função estiver sobrecarregada e aceitar várias sintaxes, todas as sintaxes válidas serão listadas.  
As funções são fortemente tipadas e verificam se o tipo passado corresponde ao tipo documentado.  
Se o tipo não corresponder, um erro será gerado.

Os tipos são expressos com a seguinte sintaxe:

* **compartimento** – binário
* **bool** – booliano
* **dt** – data/hora UTC
* **enum** – enumeração de constantes conhecidas
* **exp** – expressão, que deve ser avaliada como um booliano
* **mvbin** – binário de valores múltiplos
* **mvstr** – cadeia de caracteres de valores múltiplos
* **mvstr** – referência de vários valores
* **número** – numérico
* **ref** – referência
* **Str** – cadeia de caracteres
* **var** – uma variante de (quase) qualquer outro tipo
* **void** – não retorna um valor

As funções com os tipos **mvbin**, **mvstr**e **mvstr** só podem funcionar em atributos com valores múltiplos. Funções com **bin**, **Str**e **ref** funcionam em atributos de valor único e de valores múltiplos.

## <a name="functions-reference"></a>Referência das Funções

| Lista de funções |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certificado** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Conversa** | | | | |
| [CBool](#cbool) |[CDATA](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#stringfromguid) |[StringFromSid](#stringfromsid) | |
| **Data/hora** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Seguida](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Active** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Período** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matemática** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Valores múltiplos** | | | | |
| [Terá](#contains) |[Contar](#count) |[Item](#item) |[ItemOrNull](#itemornull) | |
| [Associar](#join) |[RemoveDuplicates](#removeduplicates) |[Divisão](#split) | | |
| **Fluxo do programa** | | | | |
| [Ao](#error) |[IIF](#iif) |[Selecionar](#select) |[Comutador](#switch) | |
| [Posição](#where) |[Por](#with) | | | |
| **Text** | | | | |
| [VOLUME](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [À esquerda](#left) |[Len](#len) |[LTrim](#ltrim) |[Meio](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[Substitua](#replace) | |
| [ReplaceChars](#replacechars) |[Certo](#right) |[RTrim](#rtrim) |[Trim](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

---
### <a name="bitand"></a>BitAnd
**Descrição:**  
A função BitAnd define os bits especificados em um valor.

**Sintaxe**  
`num BitAnd(num value1, num value2)`

* value1, value2: valores numéricos que devem ser AND'ed juntos

**Comentários**  
Essa função converte ambos os parâmetros na representação binária e define um bit para:

* 0-se um ou ambos os bits correspondentes em *value1* e *value2* forem 0
* 1-se ambos os bits correspondentes forem 1.

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Example:**  
`BitAnd(&HF, &HF7)`  
Retorna 7 porque o hexadecimal "F" e "F7" são avaliados para esse valor.

---
### <a name="bitor"></a>BitOr
**Descrição:**  
A função BitOr define os bits especificados em um valor.

**Sintaxe**  
`num BitOr(num value1, num value2)`

* value1, value2: valores numéricos que devem ser OR'ed juntos

**Comentários**  
Essa função converte ambos os parâmetros na representação binária e define um bit como 1 se um ou ambos os bits correspondentes em Mask e Flag são 1 e como 0 se ambos os bits correspondentes forem 0. Em outras palavras, ele retorna 1 em todos os casos, exceto onde os bits correspondentes de ambos os parâmetros são 0.

---
### <a name="cbool"></a>CBool
**Descrição:**  
A função CBool retorna um booliano com base na expressão avaliada

**Sintaxe**  
`bool CBool(exp Expression)`

**Comentários**  
Se a expressão for avaliada como um valor diferente de zero, CBool retornará true, caso contrário retornará false.

**Example:**  
`CBool([attrib1] = [attrib2])`  

Retorna true se ambos os atributos têm o mesmo valor.

---
### <a name="cdate"></a>CDATA
**Descrição:**  
A função CDate retorna um DateTime UTC de uma cadeia de caracteres. DateTime não é um tipo de atributo nativo em Sync, mas é usado por algumas funções.

**Sintaxe**  
`dt CDate(str value)`

* Valor Uma cadeia de caracteres com uma data, hora e, opcionalmente, o fuso horário

**Comentários**  
A cadeia de caracteres retornada sempre está em UTC.

**Example:**  
`CDate([employeeStartTime])`  
Retorna um DateTime com base na hora de início do funcionário

`CDate("2013-01-10 4:00 PM -8")`  
Retorna um DateTime que representa "2013-01-11 12:00 AM"


---
### <a name="certextensionoids"></a>CertExtensionOids
**Descrição:**  
Retorna os valores de OID de todas as extensões críticas de um objeto de certificado.

**Sintaxe**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certformat"></a>CertFormat
**Descrição:**  
Retorna o nome do formato deste certificado X. 509v3.

**Sintaxe**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Descrição:**  
Retorna o alias associado para um certificado.

**Sintaxe**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certhashstring"></a>CertHashString
**Descrição:**  
Retorna o valor de hash SHA1 para o certificado X. 509v3 como uma cadeia de caracteres hexadecimal.

**Sintaxe**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certissuer"></a>CertIssuer
**Descrição:**  
Retorna o nome da autoridade de certificação que emitiu o certificado X. 509v3.

**Sintaxe**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Descrição:**  
Retorna o nome distinto do emissor do certificado.

**Sintaxe**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certissueroid"></a>CertIssuerOid
**Descrição:**  
Retorna o OID do emissor do certificado.

**Sintaxe**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Descrição:**  
Retorna as informações de algoritmo de chave para este certificado X. 509v3 como uma cadeia de caracteres.

**Sintaxe**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Descrição:**  
Retorna os parâmetros de algoritmo de chave para o certificado X. 509v3 como uma cadeia de caracteres hexadecimal.

**Sintaxe**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certnameinfo"></a>CertNameInfo
**Descrição:**  
Retorna os nomes de assunto e emissor de um certificado.

**Sintaxe**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.
*   X509NameType: O valor de X509NameType para o assunto.
*   includesIssuerName: true para incluir o nome do emissor; caso contrário, false.

---
### <a name="certnotafter"></a>CertNotAfter
**Descrição:**  
Retorna a data na hora local após a qual um certificado não é mais válido.

**Sintaxe**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certnotbefore"></a>CertNotBefore
**Descrição:**  
Retorna a data na hora local em que um certificado se torna válido.

**Sintaxe**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Descrição:**  
Retorna o OID da chave pública para o certificado X. 509v3.

**Sintaxe**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Descrição:**  
Retorna o OID dos parâmetros de chave pública para o certificado X. 509v3.

**Sintaxe**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Descrição:**  
Retorna o número de série do certificado X. 509v3.

**Sintaxe**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Descrição:**  
Retorna o OID do algoritmo usado para criar a assinatura de um certificado.

**Sintaxe**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certsubject"></a>CertSubject
**Descrição:**  
Obtém o nome distinto da entidade de um certificado.

**Sintaxe**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Descrição:**  
Retorna o nome distinto da entidade de um certificado.

**Sintaxe**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Descrição:**  
Retorna o OID do nome da entidade de um certificado.

**Sintaxe**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certthumbprint"></a>CertThumbprint
**Descrição:**  
Retorna a impressão digital de um certificado.

**Sintaxe**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="certversion"></a>CertVersion
**Descrição:**  
Retorna a versão do formato X. 509 de um certificado.

**Sintaxe**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.

---
### <a name="cguid"></a>CGuid
**Descrição:**  
A função CGuid converte a representação de cadeia de caracteres de um GUID em sua representação binária.

**Sintaxe**  
`bin CGuid(str GUID)`

* Uma cadeia de caracteres formatada neste padrão: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Contém
**Descrição:**  
A função Contains localiza uma cadeia de caracteres dentro de um atributo de valores múltiplos

**Sintaxe**  
`num Contains (mvstring attribute, str search)`-diferencia maiúsculas de minúsculas  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-diferencia maiúsculas de minúsculas

* atributo: o atributo de valores múltiplos a ser pesquisado.
* Search: cadeia de caracteres a ser localizada no atributo.
* Casetype: CaseInsensitive ou CaseSensitive.

Retorna o índice no atributo de valores múltiplos em que a cadeia de caracteres foi encontrada. 0 será retornado se a cadeia de caracteres não for encontrada.

**Comentários**  
Para atributos de cadeia de caracteres de valores múltiplos, a pesquisa localiza subcadeias de caracteres nos valores.  
Para atributos de referência, a cadeia de caracteres pesquisada deve corresponder exatamente ao valor a ser considerado uma correspondência.

**Example:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Se o atributo proxyAddresses tiver um endereço de email principal (indicado por letras maiúsculas "SMTP:"), retorne o atributo proxyAddress, caso contrário, retornará um erro.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Descrição:**  
A função ConvertFromBase64 converte o valor codificado base64 especificado em uma cadeia de caracteres regular.

**Sintaxe**  
`str ConvertFromBase64(str source)`-presume que o Unicode seja codificado  
`str ConvertFromBase64(str source, enum Encoding)`

* original Cadeia de caracteres codificada em base64  
* Mecanismo Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos retornam "*Olá mundo!* "

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Descrição:**  
A função ConvertFromUTF8Hex converte o valor codificado hexadecimal UTF8 especificado em uma cadeia de caracteres.

**Sintaxe**  
`str ConvertFromUTF8Hex(str source)`

* original Stinger codificado de 2 bytes UTF8

**Comentários**  
A diferença entre essa função e ConvertFromBase64 ([], UTF8) em que o resultado é amigável para o atributo DN.  
Esse formato é usado pelo Azure Active Directory como DN.

**Example:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Retorna "*Olá mundo!* "

---
### <a name="converttobase64"></a>ConvertToBase64
**Descrição:**  
A função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Base64 Unicode.  
Converte o valor de uma matriz de inteiros em sua representação de cadeia de caracteres equivalente que é codificada com dígitos de base 64.

**Sintaxe**  
`str ConvertToBase64(str source)`

**Example:**  
`ConvertToBase64("Hello world!")`  
Retorna "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Descrição:**  
A função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado hexadecimal UTF8.

**Sintaxe**  
`str ConvertToUTF8Hex(str source)`

**Comentários**  
O formato de saída dessa função é usado pelo Azure Active Directory como formato de atributo DN.

**Example:**  
`ConvertToUTF8Hex("Hello world!")`  
Returns 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**Descrição:**  
A função Count retorna o número de elementos em um atributo de valores múltiplos

**Sintaxe**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**Descrição:**  
A função CNum usa uma cadeia de caracteres e retorna um tipo de dados numérico.

**Sintaxe**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Descrição:**  
Converte uma cadeia de caracteres em um atributo de referência

**Sintaxe**  
`ref CRef(str value)`

**Example:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**Descrição:**  
A função CStr converte em um tipo de dados de cadeia de caracteres.

**Sintaxe**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* valor Pode ser um valor numérico, um atributo de referência ou um booliano.

**Example:**  
`CStr([dn])`  
Poderia retornar "CN = Joe, DC = contoso, DC = com"

---
### <a name="dateadd"></a>DateAdd
**Descrição:**  
Retorna uma data que contém uma data à qual um intervalo de tempo especificado foi adicionado.

**Sintaxe**  
`dt DateAdd(str interval, num value, dt date)`

* intervalo Expressão de cadeia de caracteres que é o intervalo de tempo que você deseja adicionar. A cadeia de caracteres deve ter um dos seguintes valores:
  * aaaa ano
  * q trimestre
  * Mês m
  * Dia y do ano
  * d dia
  * Dia da semana
  * WW Week
  * h hora
  * n minutos
  * s segundo
* valor O número de unidades que você deseja adicionar. Pode ser positivo (para obter datas no futuro) ou negativo (para obter datas no passado).
* Date DateTime que representa a data à qual o intervalo é adicionado.

**Example:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Adiciona 3 meses e retorna um DateTime que representa "2001-04-01".

---
### <a name="datefromnum"></a>DateFromNum
**Descrição:**  
A função DateFromNum converte um valor no formato de data do AD em um tipo DateTime.

**Sintaxe**  
`dt DateFromNum(num value)`

**Example:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Retorna um DateTime que representa 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponent
**Descrição:**  
A função DNComponent retorna o valor de um componente DN especificado indo da esquerda.

**Sintaxe**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: o atributo de referência a ser interpretado
* ComponentNumber O componente no DN a ser retornado

**Example:**  
`DNComponent(CRef([dn]),1)`  
Se DN for "CN = Joe, ou =...", ele retornará Joe

---
### <a name="dncomponentrev"></a>DNComponentRev
**Descrição:**  
A função DNComponentRev retorna o valor de um componente DN especificado indo da direita (o final).

**Sintaxe**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: o atributo de referência a ser interpretado
* ComponentNumber-o componente no DN a ser retornado
* Opções: DC – ignorar todos os componentes com "DC ="

**Example:**  
Se DN for "CN = Joe, ou = Atlanta, ou = GA, ou = US, DC = contoso, DC = com", então  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Ambos retornam nós.

---
### <a name="error"></a>Erro
**Descrição:**  
A função Error é usada para retornar um erro personalizado.

**Sintaxe**  
`void Error(str ErrorMessage)`

**Example:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se o atributo accountName não estiver presente, acione um erro no objeto.

---
### <a name="escapedncomponent"></a>EscapeDNComponent
**Descrição:**  
A função EscapeDNComponent usa um componente de um DN e o escapa para que ele possa ser representado no LDAP.

**Sintaxe**  
`str EscapeDNComponent(str value)`

**Example:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Verifica se o objeto pode ser criado em um diretório LDAP, mesmo que o atributo displayName tenha caracteres que devam ser ignorados no LDAP.

---
### <a name="formatdatetime"></a>FormatDateTime
**Descrição:**  
A função FormatDateTime é usada para formatar um DateTime para uma cadeia de caracteres com um formato especificado

**Sintaxe**  
`str FormatDateTime(dt value, str format)`

* valor: um valor no formato DateTime
* Format: uma cadeia de caracteres que representa o formato para o qual converter.

**Comentários**  
Os valores possíveis para o formato podem ser encontrados aqui: [Formatos de data e hora personalizados para a função de formato](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Example:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resulta em "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Pode resultar em "20140905081453.0 Z"

---
### <a name="guid"></a>Guid
**Descrição:**  
O GUID da função gera um novo GUID aleatório

**Sintaxe**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Descrição:**  
A função IIF retorna um de um conjunto de valores possíveis com base em uma condição especificada.

**Sintaxe**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* Condition: qualquer valor ou expressão que possa ser avaliada como true ou false.
* ValueIfTrue Se a condição for avaliada como true, o valor retornado.
* valueIfFalse: Se a condição for avaliada como false, o valor retornado.

**Example:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o usuário for um estagiário, retorna o alias de um usuário com "t-" adicionado ao início dele, caso contrário retorna o alias do usuário como está.

---
### <a name="instr"></a>InStr
**Descrição:**  
A função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* StringCheck: cadeia de caracteres a ser pesquisada
* StringMatch: cadeia de caracteres a ser encontrada
* Start: posição inicial para localizar a subcadeia de caracteres
* comparar: vbTextCompare ou vbBinaryCompare

**Comentários**  
Retorna a posição onde a subcadeia de caracteres foi encontrada ou 0 se não for encontrada.

**Example:**  
`InStr("The quick brown fox","quick")`  
Avalia para 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia para 7

---
### <a name="instrrev"></a>InStrRev
**Descrição:**  
A função InStrRev localiza a última ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* StringCheck: cadeia de caracteres a ser pesquisada
* StringMatch: cadeia de caracteres a ser encontrada
* Start: posição inicial para localizar a subcadeia de caracteres
* comparar: vbTextCompare ou vbBinaryCompare

**Comentários**  
Retorna a posição onde a subcadeia de caracteres foi encontrada ou 0 se não for encontrada.

**Example:**  
`InStrRev("abbcdbbbef","bb")`  
Retorna 7

---
### <a name="isbitset"></a>IsBitSet
**Descrição:**  
A função IsBitSet testa se um bit está definido ou não

**Sintaxe**  
`bool IsBitSet(num value, num flag)`

* valor: um valor numérico que é avaliado. sinalizador: um valor numérico que tem o bit a ser avaliado

**Example:**  
`IsBitSet(&HF,4)`  
Retorna true porque o bit "4" está definido no valor hexadecimal "F"

---
### <a name="isdate"></a>IsDate
**Descrição:**  
Se a expressão puder ser avaliada como um tipo DateTime, a função IsDate será avaliada como true.

**Sintaxe**  
`bool IsDate(var Expression)`

**Comentários**  
Usado para determinar se CDate () pode ser bem-sucedido.

---
### <a name="iscert"></a>IsCert
**Descrição:**  
Retornará true se os dados brutos puderem ser serializados no objeto de certificado X509Certificate2 do .NET.

**Sintaxe**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de bytes de um certificado X. 509. A matriz de bytes pode ser codificada em binário (DER) ou dados X. 509 codificados em base64.
---
### <a name="isempty"></a>IsEmpty
**Descrição:**  
Se o atributo estiver presente no CS ou no MV, mas for avaliado como uma cadeia de caracteres vazia, a função IsEmpty será avaliada como true.

**Sintaxe**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Descrição:**  
Se a cadeia de caracteres puder ser convertida em um GUID, a função IsGuid será avaliada como true.

**Sintaxe**  
`bool IsGuid(str GUID)`

**Comentários**  
Um GUID é definido como uma cadeia de caracteres após um destes padrões: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Usado para determinar se CGuid () pode ser bem-sucedido.

**Example:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Se o StrAttribute tiver um formato GUID, retornará uma representação binária; caso contrário, retornará um valor nulo.

---
### <a name="isnull"></a>IsNull
**Descrição:**  
Se a expressão for avaliada como NULL, a função IsNull retornará true.

**Sintaxe**  
`bool IsNull(var Expression)`

**Comentários**  
Para um atributo, um NULL é expresso pela ausência do atributo.

**Example:**  
`IsNull([displayName])`  
Retornará true se o atributo não estiver presente no CS ou no MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Descrição:**  
Se a expressão for nula ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retornará true.

**Sintaxe**  
`bool IsNullOrEmpty(var Expression)`

**Comentários**  
Para um atributo, isso será avaliado como true se o atributo estiver ausente ou estiver presente, mas for uma cadeia de caracteres vazia.  
O inverso dessa função é chamado ispresente.

**Example:**  
`IsNullOrEmpty([displayName])`  
Retornará true se o atributo não estiver presente ou for uma cadeia de caracteres vazia no CS ou no MV.

---
### <a name="isnumeric"></a>IsNumeric
**Descrição:**  
A função ISNUMERIC retorna um valor booliano que indica se uma expressão pode ser avaliada como um tipo de número.

**Sintaxe**  
`bool IsNumeric(var Expression)`

**Comentários**  
Usado para determinar se CNum () pode ser bem-sucedido ao analisar a expressão.

---
### <a name="isstring"></a>IsString
**Descrição:**  
Se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função isString será avaliada como true.

**Sintaxe**  
`bool IsString(var expression)`

**Comentários**  
Usado para determinar se CStr () pode ser bem-sucedido para analisar a expressão.

---
### <a name="ispresent"></a>IsPresent
**Descrição:**  
Se a expressão for avaliada como uma cadeia de caracteres que não é nula e não estiver vazia, a função IsPresent retornará true.

**Sintaxe**  
`bool IsPresent(var expression)`

**Comentários**  
O inverso dessa função é chamado de IsNullOrEmpty.

**Example:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Descrição:**  
A função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Sintaxe**  
`var Item(mvstr attribute, num index)`

* atributo: atributo de valores múltiplos
* índice: índice para um item na cadeia de caracteres de valores múltiplos.

**Comentários**  
A função item é útil junto com a função Contains, uma vez que a última função retorna o índice para um item no atributo de valores múltiplos.

Gera um erro se o índice está fora dos limites.

**Example:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Retorna o endereço de email principal.

---
### <a name="itemornull"></a>ItemOrNull
**Descrição:**  
A função ItemOrNull retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Sintaxe**  
`var ItemOrNull(mvstr attribute, num index)`

* atributo: atributo de valores múltiplos
* índice: índice para um item na cadeia de caracteres de valores múltiplos.

**Comentários**  
A função ItemOrNull é útil junto com a função Contains, pois a última função retorna o índice para um item no atributo de valores múltiplos.

Se o índice estiver fora dos limites, retorna um valor nulo.

---
### <a name="join"></a>Associar
**Descrição:**  
A função Join usa uma cadeia de caracteres com valores múltiplos e retorna uma cadeia de caracteres de valor único com separador especificado inserido entre cada item.

**Sintaxe**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* Attribute Atributo de valores múltiplos que contém cadeias de caracteres a serem unidas.
* delimitador Qualquer cadeia de caracteres, usada para separar as subcadeias de caracteres na cadeia de caracteres retornada. Se omitido, o caractere de espaço ("") será usado. Se o delimitador for uma cadeia de caracteres de comprimento zero ("") ou nada, todos os itens da lista serão concatenados sem delimitadores.

**Observações**  
Há paridade entre as funções Join e Split. A função Join usa uma matriz de cadeias de caracteres e as une usando uma cadeia de caracteres delimitadora para retornar uma única cadeia de caracteres. A função Split usa uma cadeia de caracteres e a separa no delimitador para retornar uma matriz de cadeias de caracteres. No entanto, uma diferença importante é que a junção pode concatenar cadeias de caracteres com qualquer cadeia delimitadora, pode separar apenas cadeias de caracteres usando um único delimitador de caractere.

**Example:**  
`Join([proxyAddresses],",")`  
Poderia retornar: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

---
### <a name="lcase"></a>LCase
**Descrição:**  
A função LCase converte todos os caracteres em uma cadeia de caracteres em letras minúsculas.

**Sintaxe**  
`str LCase(str value)`

**Example:**  
`LCase("TeSt")`  
Retorna "Test".

---
### <a name="left"></a>Esquerda
**Descrição:**  
A função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres.

**Sintaxe**  
`str Left(str string, num NumChars)`

* String: a cadeia de caracteres da qual retornar os caracteres
* NumChars: um número que identifica o número de caracteres a serem retornados do início (à esquerda) da cadeia de caracteres

**Comentários**  
Uma cadeia de caracteres que contém os primeiros caracteres numChars na cadeia de caracteres:

* Se numChars = 0, retornará uma cadeia de caracteres vazia.
* Se numChars < 0, retornará a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retornará uma cadeia de caracteres vazia.

Se a cadeia de caracteres contiver menos caracteres do que o número especificado em numChars, uma cadeia de caracteres idêntica à cadeia de caracteres (ou seja, contendo todos os caracteres no parâmetro 1) será retornada.

**Example:**  
`Left("John Doe", 3)`  
Retorna "Joh".

---
### <a name="len"></a>Len
**Descrição:**  
A função Len retorna o número de caracteres em uma cadeia de caracteres.

**Sintaxe**  
`num Len(str value)`

**Example:**  
`Len("John Doe")`  
Retorna 8

---
### <a name="ltrim"></a>LTrim
**Descrição:**  
A função LTrim remove espaços em branco à esquerda de uma cadeia de caracteres.

**Sintaxe**  
`str LTrim(str value)`

**Example:**  
`LTrim(" Test ")`  
Retorna "Test"

---
### <a name="mid"></a>Mid
**Descrição:**  
A função mid retorna um número especificado de caracteres de uma posição especificada em uma cadeia de caracteres.

**Sintaxe**  
`str Mid(str string, num start, num NumChars)`

* String: a cadeia de caracteres da qual retornar os caracteres
* Start: um número que identifica a posição inicial na cadeia de caracteres da
* NumChars: um número que identifica o número de caracteres a serem retornados da posição na cadeia de caracteres

**Comentários**  
Retornar os caracteres numChars começando da posição inicial na cadeia de caracteres.  
Uma cadeia de caracteres que contém os caracteres numChars da posição inicial na cadeia de caracteres:

* Se numChars = 0, retornará uma cadeia de caracteres vazia.
* Se numChars < 0, retornará a cadeia de caracteres de entrada.
* Se Iniciar > o comprimento da cadeia de caracteres, retorne a cadeia de caracteres de entrada.
* Se Start < = 0, retornará a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retornará uma cadeia de caracteres vazia.

Se não houver numChar caracteres restantes na cadeia de caracteres do início da posição, o máximo de caracteres possível será retornado.

**Example:**  
`Mid("John Doe", 3, 5)`  
Retorna "HN do".

`Mid("John Doe", 6, 999)`  
Retorna "Doe"

---
### <a name="now"></a>Agora
**Descrição:**  
A função Now retorna um DateTime especificando a data e hora atuais, de acordo com a data e a hora do sistema do seu computador.

**Sintaxe**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Descrição:**  
A função NumFromDate retorna uma data no formato de data do AD.

**Sintaxe**  
`num NumFromDate(dt value)`

**Example:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Retorna 129699324000000000

---
### <a name="padleft"></a>PadLeft
**Descrição:**  
A função PadLeft preenche deixa os pads à esquerda de uma cadeia de caracteres para um comprimento especificado usando um caractere de preenchimento fornecido.

**Sintaxe**  
`str PadLeft(str string, num length, str padCharacter)`

* String: a cadeia de caracteres a ser conpercussão.
* muito Um inteiro que representa o comprimento de cadeia de caracteres desejado.
* PadCharacter Uma cadeia de caracteres que consiste em um único caractere a ser usado como o caractere de preenchimento

**Comentários**

* Se o comprimento da cadeia de caracteres for menor que o comprimento, padCharacter será acrescentado repetidamente ao início (à esquerda) da cadeia de caracteres até ter um comprimento igual ao comprimento.
* PadCharacter pode ser um caractere de espaço, mas não pode ser um valor nulo.
* Se o comprimento da cadeia de caracteres for igual ou maior que o comprimento, a cadeia de caracteres será retornada inalterada.
* Se a cadeia de caracteres tiver um comprimento maior ou igual ao comprimento, uma cadeia de caracteres idêntica à cadeia de caracteres será retornada.
* Se o comprimento da cadeia de caracteres for menor que o comprimento, uma nova cadeia de caracteres do comprimento desejado será retornada contendo a cadeia de caracteres preenchida com um padCharacter.
* Se a cadeia de caracteres for nula, a função retornará uma cadeia de caracteres vazia.

**Example:**  
`PadLeft("User", 10, "0")`  
Retorna "000000User".

---
### <a name="padright"></a>PadRight
**Descrição:**  
A função PadRight os painéis à direita de uma cadeia de caracteres para um comprimento especificado usando um caractere de preenchimento fornecido.

**Sintaxe**  
`str PadRight(str string, num length, str padCharacter)`

* String: a cadeia de caracteres a ser conpercussão.
* muito Um inteiro que representa o comprimento de cadeia de caracteres desejado.
* PadCharacter Uma cadeia de caracteres que consiste em um único caractere a ser usado como o caractere de preenchimento

**Comentários**

* Se o comprimento da cadeia de caracteres for menor que o comprimento, padCharacter será repetidamente acrescentado ao final (à direita) da cadeia de caracteres até que tenha um comprimento igual ao comprimento.
* padCharacter pode ser um caractere de espaço, mas não pode ser um valor nulo.
* Se o comprimento da cadeia de caracteres for igual ou maior que o comprimento, a cadeia de caracteres será retornada inalterada.
* Se a cadeia de caracteres tiver um comprimento maior ou igual ao comprimento, uma cadeia de caracteres idêntica à cadeia de caracteres será retornada.
* Se o comprimento da cadeia de caracteres for menor que o comprimento, uma nova cadeia de caracteres do comprimento desejado será retornada contendo a cadeia de caracteres preenchida com um padCharacter.
* Se a cadeia de caracteres for nula, a função retornará uma cadeia de caracteres vazia.

**Example:**  
`PadRight("User", 10, "0")`  
Retorna "user000000".

---
### <a name="pcase"></a>PCase
**Descrição:**  
A função PCase converte o primeiro caractere de cada palavra delimitada por espaço em uma cadeia de caracteres em letras maiúsculas e todos os outros caracteres são convertidos em letras minúsculas.

**Sintaxe**  
`String PCase(string)`

**Comentários**

* Atualmente, essa função não fornece a capitalização adequada para converter uma palavra totalmente em maiúscula, como um acrônimo.

**Example:**  
`PCase("TEsT")`  
Retorna "Test".

`PCase(LCase("TEST"))`  
Retorna "Test"

---
### <a name="randomnum"></a>RandomNum
**Descrição:**  
A função RandomNum retorna um número aleatório entre um intervalo especificado.

**Sintaxe**  
`num RandomNum(num start, num end)`

* Start: um número que identifica o limite inferior do valor aleatório a ser gerado
* End: um número que identifica o limite superior do valor aleatório a ser gerado

**Example:**  
`Random(100,999)`  
Pode retornar 734.

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Descrição:**  
A função RemoveDuplicates usa uma cadeia de caracteres com valores múltiplos e garante que cada valor seja exclusivo.

**Sintaxe**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Example:**  
`RemoveDuplicates([proxyAddresses])`  
Retorna um atributo proxyAddress limpo em que todos os valores duplicados foram removidos.

---
### <a name="replace"></a>Substituir
**Descrição:**  
A função Replace substitui todas as ocorrências de uma cadeia de caracteres para outra cadeia de caracteres.

**Sintaxe**  
`str Replace(str string, str OldValue, str NewValue)`

* Strings Uma cadeia de caracteres na qual substituir valores.
* OldValue A cadeia de caracteres a ser pesquisada e substituída.
* NewValue A cadeia de caracteres para a qual substituir.

**Comentários**  
A função reconhece os seguintes monikers especiais:

* \n – nova linha
* \r – retorno de carro
* \t – guia

**Example:**  
`Replace([address],"\r\n",", ")`  
Substitui CRLF por vírgula e espaço e pode levar a "One Microsoft Way, Redmond, WA, EUA"

---
### <a name="replacechars"></a>ReplaceChars
**Descrição:**  
A função ReplaceChars substitui todas as ocorrências de caracteres encontradas na cadeia de caracteres ReplacePattern.

**Sintaxe**  
`str ReplaceChars(str string, str ReplacePattern)`

* Strings Uma cadeia de caracteres para substituir os caracteres.
* ReplacePattern: uma cadeia de caracteres que contém um dicionário com caracteres a serem substituídos.

O formato é {origem1}: {target1}, {origem2}: {TARGET2}, {sourcen}, {targetn}, em que Source é o caractere a ser localizado e alvo da cadeia de caracteres com a qual substituir.

**Comentários**

* A função usa cada ocorrência de fontes definidas e as substitui pelos destinos.
* A origem deve ser exatamente um caractere (Unicode).
* A origem não pode estar vazia nem ter mais de um caractere (erro de análise).
* O destino pode ter vários caracteres, por exemplo ö: OE, β: SS.
* O destino pode estar vazio, indicando que o caractere deve ser removido.
* A origem diferencia maiúsculas de minúsculas e deve ser uma correspondência exata.
* O, (vírgula) e: (dois-pontos) são caracteres reservados e não podem ser substituídos usando essa função.
* Espaços e outros caracteres brancos na cadeia de caracteres ReplacePattern são ignorados.

**Example:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Retorna Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Retorna "ONeil", o único tique é definido para ser removido.

---
### <a name="right"></a>Direita
**Descrição:**  
A função Right retorna um número especificado de caracteres a partir do lado direito (final) de uma cadeia de caracteres.

**Sintaxe**  
`str Right(str string, num NumChars)`

* String: a cadeia de caracteres da qual retornar os caracteres
* NumChars: um número que identifica o número de caracteres a serem retornados do final (à direita) da cadeia de caracteres

**Comentários**  
Os caracteres NumChars são retornados da última posição da cadeia de caracteres.

Uma cadeia de caracteres que contém os últimos caracteres numChars na cadeia de caracteres:

* Se numChars = 0, retornará uma cadeia de caracteres vazia.
* Se numChars < 0, retornará a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retornará uma cadeia de caracteres vazia.

Se a cadeia de caracteres contiver menos caracteres do que o número especificado em NumChars, uma cadeia de caracteres idêntica à cadeia de caracteres será retornada.

**Example:**  
`Right("John Doe", 3)`  
Retorna "Doe".

---
### <a name="rtrim"></a>RTrim
**Descrição:**  
A função RTrim remove espaços em branco à direita de uma cadeia de caracteres.

**Sintaxe**  
`str RTrim(str value)`

**Example:**  
`RTrim(" Test ")`  
Retorna "Test".

---
### <a name="select"></a>Selecionar
**Descrição:**  
Processar todos os valores em um atributo com valores múltiplos (ou saída de uma expressão) com base na função especificada.

**Sintaxe**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item Representa um elemento no atributo de valores múltiplos
* atributo: o atributo de valores múltiplos
* expressão: uma expressão que retorna uma coleção de valores
* Condition: qualquer função que possa processar um item no atributo

**Exemplos:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Retorna todos os valores no atributo de valores múltiplos otherPhone após os hifens (-) terem sido removidos.

---
### <a name="split"></a>Dividir
**Descrição:**  
A função Split usa uma cadeia de caracteres separada com um delimitador e a transforma em uma cadeia de caracteres com valores múltiplos.

**Sintaxe**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* valor: a cadeia de caracteres com um caractere delimitador para separar.
* delimitador: caractere único a ser usado como o delimitador.
* limite: número máximo de valores que podem ser retornados.

**Example:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Retorna uma cadeia de caracteres com valores múltiplos com 2 elementos úteis para o atributo proxyAddress.

---
### <a name="stringfromguid"></a>StringFromGuid
**Descrição:**  
A função StringFromGuid usa um GUID binário e o converte em uma cadeia de caracteres

**Sintaxe**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Descrição:**  
A função StringFromSid converte uma matriz de bytes que contém um identificador de segurança em uma cadeia de caracteres.

**Sintaxe**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Comutador
**Descrição:**  
A função switch é usada para retornar um único valor com base nas condições avaliadas.

**Sintaxe**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr Expressão Variant que você deseja avaliar.
* valor Valor a ser retornado se a expressão correspondente for true.

**Comentários**  
A lista de argumentos da função switch consiste em pares de expressões e valores. As expressões são avaliadas da esquerda para a direita e o valor associado à primeira expressão a ser avaliada como true é retornado. Se as partes não forem emparelhadas corretamente, ocorrerá um erro em tempo de execução.

Por exemplo, se expr1 for true, Switch retornará value1. Se expr-1 for false, mas expr-2 for true, Switch retornará Value-2 e assim por diante.

O switch não retornará nada se:

* Nenhuma das expressões é verdadeira.
* A primeira expressão true tem um valor correspondente que é NULL.

A opção avalia todas as expressões, mesmo que retorne apenas uma delas. Por esse motivo, você deve observar efeitos colaterais indesejáveis. Por exemplo, se a avaliação de qualquer expressão resultar em um erro de divisão por zero, ocorrerá um erro.

O valor também pode ser a função de erro, que retornaria uma cadeia de caracteres personalizada.

**Example:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Retorna o idioma falado em algumas cidades principais, caso contrário, retorna um erro.

---
### <a name="trim"></a>Recortar
**Descrição:**  
A função Trim remove espaços em branco à esquerda e à direita de uma cadeia de caracteres.

**Sintaxe**  
`str Trim(str value)`  

**Example:**  
`Trim(" Test ")`  
Retorna "Test".

`Trim([proxyAddresses])`  
Remove espaços à esquerda e à direita para cada valor no atributo proxyAddress.

---
### <a name="ucase"></a>UCase
**Descrição:**  
A função UCase converte todos os caracteres em uma cadeia de caracteres em letras maiúsculas.

**Sintaxe**  
`str UCase(str string)`

**Example:**  
`UCase("TeSt")`  
Retorna "TEST".

---
### <a name="where"></a>Onde

**Descrição:**  
Retorna um subconjunto de valores de um atributo com vários valores (ou saída de uma expressão) com base em uma condição específica.

**Sintaxe**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item Representa um elemento no atributo de valores múltiplos
* atributo: o atributo de valores múltiplos
* Condition: qualquer expressão que possa ser avaliada como verdadeira ou falsa
* expressão: uma expressão que retorna uma coleção de valores

**Example:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Retornar os valores de certificado no atributo de vários valores userCertificate que não expirou.

---
### <a name="with"></a>Com
**Descrição:**  
A função with fornece uma maneira de simplificar uma expressão complexa usando uma variável para representar uma subexpressão que aparece uma ou mais vezes na expressão complexa.

**Sintaxe**
`With(var variable, exp subExpression, exp complexExpression)`  
* Ela Representa a subexpressão.
* subexpressão: subexpressão representada por variável.
* complexy: Uma expressão complexa.

**Example:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
É funcionalmente equivalente a:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Que retorna somente valores de certificado não expirados no atributo userCertificate.


---
### <a name="word"></a>Word
**Descrição:**  
A função Word retorna uma palavra contida em uma cadeia de caracteres, com base nos parâmetros que descrevem os delimitadores a serem usados e o número da palavra a ser retornado.

**Sintaxe**  
`str Word(str string, num WordNumber, str delimiters)`

* String: a cadeia de caracteres da qual retornar uma palavra.
* WordNumber: um número que identifica qual número de palavras deve retornar.
* delimitadores: uma cadeia de caracteres que representa os delimitadores que devem ser usados para identificar palavras

**Comentários**  
Cada cadeia de caracteres em seqüência separada por um dos caracteres nos delimitadores é identificada como palavras:

* Se o número < 1, retorna uma cadeia de caracteres vazia.
* Se a cadeia de caracteres for nula, retornará uma cadeia de caracteres vazia.

Se a cadeia de caracteres contiver menos que palavras de número ou a cadeia de caracteres não contiver palavras identificadas por delimitadores, uma cadeia de caracteres vazia será retornada.

**Example:**  
`Word("The quick brown fox",3," ")`  
Retorna "Brown"

`Word("This,string!has&many separators",3,",!&#")`  
Retornaria "tem"

## <a name="additional-resources"></a>Recursos Adicionais
* [Noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Sincronização de Azure AD Connect: Personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
