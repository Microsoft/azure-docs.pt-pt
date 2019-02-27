---
title: 'Sincronização do Azure AD Connect: Referência de funções | Documentos da Microsoft'
description: Referência de expressões do aprovisionamento declarativo na sincronização do Azure AD Connect.
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
ms.openlocfilehash: 1f2837121c84081c77176b18758dfe1a83d6a070
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876913"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronização do Azure AD Connect: Referência das Funções
No Azure AD Connect, as funções são usadas para manipular um valor de atributo durante a sincronização.  
A sintaxe das funções é expresso com o seguinte formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se uma função está sobrecarregada e aceita diversas sintaxes, são listadas todas as sintaxes válidos.  
As funções tem rigidez de tipos e eles Certifique-se de que o tipo transmitido em correspondências o tipo de documentado.  
Se o tipo não corresponderem, ocorrerá um erro.

Os tipos são expressos com a seguinte sintaxe:

* **Bin** – binário
* **bool** – booleano
* **DT** – data/hora UTC
* **Enum** – enumeração de constantes conhecidos
* **EXP** – expressão, que poderá demorar a ser avaliado como um valor booleano
* **mvbin** – com vários valores binários
* **mvstr** – com vários valores de cadeia
* **mvref** – referência com vários valores
* **Núm.** – numérico
* **Ref** – referência
* **a str** – cadeia
* **var** – uma variante de (quase) qualquer outro tipo
* **void** – não retorna um valor

As funções com os tipos **mvbin**, **mvstr**, e **mvref** funciona somente em atributos com múltiplos valores. Funciona com **bin**, **str**, e **ref** profissional em atributos de valor único e com múltiplos valores.

## <a name="functions-reference"></a>Referência das Funções
| Lista de funções |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| **Certificado** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Conversão** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#StringFromGuid) |[StringFromSid](#stringfromsid) | |
| **Data / hora** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[agora](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Diretório** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Avaliação** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matemática** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Com múltiplos valores** | | | | |
| [Contém](#contains) |[Contagem](#count) |[Item](#item) |[ItemOrNull](#itemornull) | |
| [Associar](#join) |[RemoveDuplicates](#removeduplicates) |[Dividir](#split) | | |
| **Fluxo do programa** | | | | |
| [Error](#error) |[IIF](#iif) |[Selecionar](#select) |[Comutador](#switch) | |
| [Where](#where) |[With](#with) | | | |
| **Text** (Texto) | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [À esquerda](#left) |[Len](#len) |[LTrim](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[substituir](#replace) | |
| [ReplaceChars](#replacechars) |[Direita](#right) |[RTrim](#rtrim) |[Cortar](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Descrição:**  
A função de BitAnd define do bits especificada num valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

* value1, value2: valores numéricos que devem ser AND'ed em conjunto

**Observações:**  
Esta função converte os dois parâmetros para a representação binária e define um pouco para:

* 0 - se um ou ambos dos bits correspondentes *máscara* e *sinalizador* são 0
* 1 - se ambos os bits correspondentes são 1.

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
`BitAnd(&HF, &HF7)`  
Devolve 7 porque hexadecimal "F" e "F7" avaliar a este valor.

- - -
### <a name="bitor"></a>BitOr
**Descrição:**  
A função de BitOr define do bits especificada num valor.

**Sintaxe:**  
`num BitOr(num value1, num value2)`

* value1, value2: valores numéricos que devem ser juntas, com OR em conjunto

**Observações:**  
Esta função converte os dois parâmetros para a representação binária e define um pouco para 1 se um ou ambos os bits correspondente na máscara e o sinalizador são 1 e 0 se ambos os bits correspondentes são 0. Em outras palavras, ele retorna 1 em todos os casos, exceto em que os bits correspondentes de ambos os parâmetros são 0.

- - -
### <a name="cbool"></a>CBool
**Descrição:**  
A função de CBool retorna um Booleano baseado na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Observações:**  
Se a expressão é avaliada para um valor diferente de zero, em seguida, CBool retorna True, caso contrário, ele retorna False.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Atributos de devolve True se ambos têm o mesmo valor.

- - -
### <a name="cdate"></a>CDate
**Descrição:**  
A função de CDate devolve um DateTime UTC a partir de uma cadeia de caracteres. DateTime não é um tipo de atributo nativo em sincronia, mas é utilizada por algumas funções.

**Sintaxe:**  
`dt CDate(str value)`

* Valor: Uma cadeia de caracteres com uma data, hora e, opcionalmente, fuso horário

**Observações:**  
A cadeia de caracteres retornada sempre está em UTC.

**Exemplo:**  
`CDate([employeeStartTime])`  
Devolve que um DateTime com base no funcionário a hora de início

`CDate("2013-01-10 4:00 PM -8")`  
Devolve um DateTime que representa "2013-01-11 12:00 AM"


- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Descrição:**  
Devolve os valores de Oid de todas as extensões críticas de um objeto de certificado.

**Sintaxe:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certformat"></a>CertFormat
**Descrição:**  
Devolve o nome do formato deste certificado X.509v3.

**Sintaxe:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Descrição:**  
Devolve o alias associado para um certificado.

**Sintaxe:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certhashstring"></a>CertHashString
**Descrição:**  
Devolve o valor de hash SHA1 do certificado X.509v3 como uma cadeia hexadecimal.

**Sintaxe:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certissuer"></a>CertIssuer
**Descrição:**  
Devolve o nome de autoridade de certificação que emitiu o certificado X.509v3.

**Sintaxe:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Descrição:**  
Devolve o nome distinto do emissor do certificado.

**Sintaxe:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Descrição:**  
Devolve o Oid do emissor do certificado.

**Sintaxe:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Descrição:**  
Devolve as informações de algoritmo de chave para este certificado X.509v3 como uma cadeia de caracteres.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Descrição:**  
Devolve os parâmetros do algoritmo de chave do certificado X.509v3 como uma cadeia hexadecimal.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Descrição:**  
Devolve o assunto e o emissor nomes de um certificado.

**Sintaxe:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.
*   X509NameType: O valor de X509NameType para o assunto.
*   includesIssuerName: true para incluir o nome do emissor; caso contrário, FALSO.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Descrição:**  
Devolve a data na hora local após o qual um certificado já não é válido.

**Sintaxe:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Descrição:**  
Devolve a data na hora local em que um certificado fica válido.

**Sintaxe:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Descrição:**  
Devolve o Oid da chave pública do certificado X.509v3.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Descrição:**  
Devolve o Oid de parâmetros de chave públicos do certificado X.509v3.

**Sintaxe:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Descrição:**  
Devolve o número de série do certificado X.509v3.

**Sintaxe:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Descrição:**  
Devolve o Oid do algoritmo usado para criar a assinatura de um certificado.

**Sintaxe:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certsubject"></a>CertSubject
**Descrição:**  
Obtém o nome do requerente distingue-se de um certificado.

**Sintaxe:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Descrição:**  
Devolve o nome do requerente distinguido-se de um certificado.

**Sintaxe:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Descrição:**  
Devolve o Oid do nome do requerente de um certificado.

**Sintaxe:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certthumbprint"></a>CertThumbprint
**Descrição:**  
Devolve o thumbprint de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="certversion"></a>CertVersion
**Descrição:**  
Devolve a versão de formato X.509 de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.

- - -
### <a name="cguid"></a>CGuid
**Descrição:**  
A função de CGuid converte a representação de cadeia de caracteres de um GUID em sua representação binária.

**Sintaxe:**  
`bin CGuid(str GUID)`

* Uma cadeia de caracteres formatada neste padrão: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>Contains
**Descrição:**  
A função de Contains localiza uma cadeia de caracteres dentro de um atributo com múltiplos valor

**Sintaxe:**  
`num Contains (mvstring attribute, str search)` -maiúsculas e minúsculas  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` -maiúsculas e minúsculas

* atributo: o atributo com múltiplos valor para procurar.
* pesquisa: cadeia a localizar no atributo.
* Casetype: CaseInsensitive ou CaseSensitive.

Devolve o índice no atributo com múltiplos valor em que foi encontrada a cadeia de caracteres. 0 é devolvido se a cadeia de caracteres não for encontrada.

**Observações:**  
Para atributos com múltiplos valores de cadeia de caracteres, a pesquisa localiza subcadeias os valores.  
Para atributos de referência, a cadeia pesquisada deve corresponder exatamente o valor para ser considerado uma correspondência.

**Exemplo:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Se o atributo proxyAddresses tem um endereço de e-mail principal (indicado em maiúsculas "SMTP:"), em seguida, retornar o atributo proxyAddress, caso contrário, devolver um erro.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Descrição:**  
A função de ConvertFromBase64 converte o valor codificado em base64 especificado para uma cadeia de caracteres normal.

**Sintaxe:**  
`str ConvertFromBase64(str source)` -parte do princípio de Unicode para a codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* Origem: Cadeia codificada em Base64  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos podem devolver "*Olá, mundo!*"

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Descrição:**  
A função de ConvertFromUTF8Hex converte o valor hexadecimal de UTF8 codificado especificado para uma cadeia de caracteres.

**Sintaxe:**  
`str ConvertFromUTF8Hex(str source)`

* Origem: Sting codificado do UTF8 2 bytes

**Observações:**  
A diferença entre essa função e ConvertFromBase64([],UTF8) em que o resultado é amigável para o atributo de DN.  
Este formato é utilizado pelo Azure Active Directory como DN.

**Exemplo:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Devolve "*Olá, mundo!*"

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Descrição:**  
A função de ConvertToBase64 converte uma cadeia de caracteres numa cadeia de base64 do Unicode.  
Converte o valor de uma matriz de inteiros em sua representação de cadeia de caracteres equivalente está codificada com dígitos de base 64.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Devolve "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Descrição:**  
A função de ConvertToUTF8Hex converte uma cadeia de caracteres para um valor hexadecimal de UTF8 codificado.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Observações:**  
O formato de saída desta função é utilizado pelo Azure Active Directory como formato de atributo do DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Returns 48656C6C6F20776F726C6421

- - -
### <a name="count"></a>Contagem
**Descrição:**  
A função de contagem devolve o número de elementos num atributo com múltiplos valor

**Sintaxe:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Descrição:**  
A função de CNum usa uma cadeia de caracteres e retorna um tipo de dados numéricos.

**Sintaxe:**  
`num CNum(str value)`

- - -
### <a name="cref"></a>CRef
**Descrição:**  
Converte uma cadeia de caracteres para um atributo de referência

**Sintaxe:**  
`ref CRef(str value)`

**Exemplo:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

- - -
### <a name="cstr"></a>CStr
**Descrição:**  
A função CStr converte um tipo de dados de cadeia de caracteres.

**Sintaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* Valor: Pode ser um valor numérico, o atributo de referência ou o booleano.

**Exemplo:**  
`CStr([dn])`  
Poderia retornar "cn = João, dc = contoso, dc = com"

- - -
### <a name="dateadd"></a>DateAdd
**Descrição:**  
Devolve uma data que contém uma data para o qual foi adicionado um intervalo de tempo especificado.

**Sintaxe:**  
`dt DateAdd(str interval, num value, dt date)`

* interval: Expressão de cadeia de caracteres que é o intervalo de tempo que pretende adicionar. A cadeia tem de ter um dos seguintes valores:
  * AAAA ano
  * p trimestre
  * m mês
  * y dia do ano
  * 1!d dia
  * w dia da semana
  * ww semana
  * Hora de h
  * Minuto n
  * s segundo
* Valor: O número de unidades que pretende adicionar. Pode ser positivo (para obter as datas no futuro) ou negativo (para obter as datas no passado).
* Data: DateTime que representa a data à qual o intervalo é adicionado.

**Exemplo:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Adiciona três meses e devolve um valor DateTime que representa "2001-04-01".

- - -
### <a name="datefromnum"></a>DateFromNum
**Descrição:**  
Converte de função DateFromNum formato de um valor de data do AD para um tipo DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devolve um valor DateTime que representa 2012-01-01 23: 00:00

- - -
### <a name="dncomponent"></a>DNComponent
**Descrição:**  
A função de DNComponent devolve o valor de um componente especificado do DN do esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: o atributo de referência para interpretar
* ComponentNumber: O componente o DN a devolver

**Exemplo:**  
`DNComponent(CRef([dn]),1)`  
Se for o dn "cn = João, UO =...," ela retorna Joe

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Descrição:**  
A função de DNComponentRev devolve o valor de um componente especificado do DN do direito (final).

**Sintaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: o atributo de referência para interpretar
* ComponentNumber - o componente o DN a devolver
* Opções: Controlador de domínio – ignorar todos os componentes com "dc ="

**Exemplo:**  
Se for o dn "cn = João, UO = Atlanta, UO = GA, UO = US, dc = contoso, dc = com", em seguida,  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Ambos retornam-nos.

- - -
### <a name="error"></a>Erro
**Descrição:**  
A função de erro é usada para retornar um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se o accountName de atributo não estiver presente, emite um erro no objeto.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Descrição:**  
A função de EscapeDNComponent usa um componente de um DN e escapes-lo para que podem ser representado no LDAP.

**Sintaxe:**  
`str EscapeDNComponent(str value)`

**Exemplo:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Certifica-se de que o objeto pode ser criado num diretório LDAP, mesmo que o atributo displayName tem carateres que devem ser escritos no LDAP.

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Descrição:**  
A função FormatDateTime é utilizada para formatar um DateTime para uma cadeia de caracteres com um formato especificado

**Sintaxe:**  
`str FormatDateTime(dt value, str format)`

* valor: um valor no formato DateTime
* formato: uma cadeia de caracteres que representa o formato para converter.

**Observações:**  
Os valores possíveis para o formato podem ser encontrados aqui: [Os formatos de datas personalizadas e a hora para a função de formato](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Exemplo:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resultados na "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Pode resultar em "20140905081453.0Z"

- - -
### <a name="guid"></a>Guid
**Descrição:**  
A função Guid gera um novo GUID aleatório

**Sintaxe:**  
`str Guid()`

- - -
### <a name="iif"></a>IIF
**Descrição:**  
A função IIF devolve um de um conjunto de valores possíveis, com base numa condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condição: qualquer valor ou expressão que pode ser avaliada como verdadeira ou falsa.
* valueIfTrue: Se a condição for avaliada como true, o valor retornado.
* valueIfFalse: Se a condição for avaliada como false, o valor retornado.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o utilizador for um colaborador, devolve o alias de um utilizador com "t-" acrescentada ao início do mesmo, outra devolve o alias do utilizador que seja.

- - -
### <a name="instr"></a>InStr
**Descrição:**  
A função InStr encontra a primeira ocorrência de uma subcadeia numa cadeia de caracteres

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: cadeia de caracteres deve ser pesquisada
* stringmatch: cadeia de caracteres a serem encontradas
* iniciar: a partir de posição para determinar a subcadeia
* comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição em que a subcadeia foi encontrada ou 0 se não for encontrado.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Evalues para 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia a 7

- - -
### <a name="instrrev"></a>InStrRev
**Descrição:**  
A função de InStrRev localiza a última ocorrência de uma subcadeia numa cadeia de caracteres

**Sintaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: cadeia de caracteres deve ser pesquisada
* stringmatch: cadeia de caracteres a serem encontradas
* iniciar: a partir de posição para determinar a subcadeia
* comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição em que a subcadeia foi encontrada ou 0 se não for encontrado.

**Exemplo:**  
`InStrRev("abbcdbbbef","bb")`  
Devolve 7

- - -
### <a name="isbitset"></a>IsBitSet
**Descrição:**  
A função IsBitSet testes se um pouco está definida ou não

**Sintaxe:**  
`bool IsBitSet(num value, num flag)`

* valor: um valor numérico que é evaluated.flag: um valor numérico que tem o bit a ser avaliado

**Exemplo:**  
`IsBitSet(&HF,4)`  
Devolve VERDADEIRO se porque bit "4" está definida para o valor hexadecimal "F"

- - -
### <a name="isdate"></a>IsDate
**Descrição:**  
Se a expressão pode ser avalia como um tipo DateTime, em seguida, a função IsDate for avaliada como True.

**Sintaxe:**  
`bool IsDate(var Expression)`

**Observações:**  
Utilizado para determinar se CDate () pode ser concluída com êxito.

- - -
### <a name="iscert"></a>IsCert
**Descrição:**  
Devolve VERDADEIRO se os dados não processados podem ser serializados no objeto de certificado do .NET X509Certificate2.

**Sintaxe:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz de byte de um certificado X.509. A matriz de bytes pode ser com codificação binária (DER) ou dados X.509 com codificação Base64.
- - -
### <a name="isempty"></a>IsEmpty
**Descrição:**  
Se o atributo está presente na MV ou CS, mas é avaliada como uma cadeia vazia, a função de IsEmpty for avaliada como True.

**Sintaxe:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Descrição:**  
Se a cadeia de caracteres pode ser convertida para um GUID, em seguida, a função de IsGuid avaliada como true.

**Sintaxe:**  
`bool IsGuid(str GUID)`

**Observações:**  
Um GUID é definido como uma cadeia de caracteres a seguir um desses padrões: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Utilizado para determinar se CGuid() pode ser concluída com êxito.

**Exemplo:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Se o StrAttribute tem um formato GUID, retornar uma representação binária, caso contrário, devolver um valor nulo.

- - -
### <a name="isnull"></a>IsNull
**Descrição:**  
Se a expressão é avaliada como Null, em seguida, a função IsNull retorna true.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Observações:**  
Para um atributo, um valor nulo é expressa pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Devolve True se o atributo não está presente na MV ou CS.

- - -
### <a name="isnullorempty"></a>IsNullOrEmpty
**Descrição:**  
Se a expressão é nulo ou uma cadeia vazia, a função de IsNullOrEmpty devolve true.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Observações:**  
Para um atributo, isso seria avaliar como VERDADEIRO se o atributo está ausente ou estiver presente, mas é uma cadeia vazia.  
O inverso desta função é denominado IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Devolve True se o atributo não está presente ou é uma cadeia vazia na MV ou CS.

- - -
### <a name="isnumeric"></a>IsNumeric
**Descrição:**  
A função IsNumeric devolve um valor Booleano indicando se uma expressão pode ser avaliada como um tipo de número.

**Sintaxe:**  
`bool IsNumeric(var Expression)`

**Observações:**  
Utilizado para determinar se CNum() pode ser concluída com êxito para analisar a expressão.

- - -
### <a name="isstring"></a>IsString
**Descrição:**  
Se a expressão pode ser avaliada como um tipo de cadeia de caracteres, em seguida, a função de IsString for avaliada como True.

**Sintaxe:**  
`bool IsString(var expression)`

**Observações:**  
Utilizado para determinar se CStr() pode ser concluída com êxito para analisar a expressão.

- - -
### <a name="ispresent"></a>IsPresent
**Descrição:**  
Se a expressão for avaliada como uma cadeia de caracteres que não seja nulo e não está vazia, a função de IsPresent devolve true.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Observações:**  
O inverso desta função é denominado IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

- - -
### <a name="item"></a>Item
**Descrição:**  
A função Item devolve um item de um cadeia de caracteres/atributos com múltiplos valores.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

* atributo: atributo com múltiplos valor
* índice: índice para um item na cadeia de caracteres com múltiplos valor.

**Observações:**  
A função de Item é útil em conjunto com a função de Contains, uma vez que a última função retorna o índice para um item no atributo com múltiplos valor.

Emite um erro se index Je mimo rozsah.

**Exemplo:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Devolve o endereço de e-mail principal.

- - -
### <a name="itemornull"></a>ItemOrNull
**Descrição:**  
A função de ItemOrNull devolve um item de um cadeia de caracteres/atributos com múltiplos valores.

**Sintaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

* atributo: atributo com múltiplos valor
* índice: índice para um item na cadeia de caracteres com múltiplos valor.

**Observações:**  
A função de ItemOrNull é útil em conjunto com a função de Contains, uma vez que a última função retorna o índice para um item no atributo com múltiplos valor.

Se o índice está fora dos limites, em seguida, devolve um valor nulo.

- - -
### <a name="join"></a>Associar
**Descrição:**  
A função de associação usa uma cadeia de caracteres com múltiplos valor e devolve uma cadeia de valor único com separador especificado inserido entre cada item.

**Sintaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* atributo: Com múltiplos valor de atributo que contém cadeias de caracteres a ser associada.
* delimitador: Qualquer cadeia de caracteres, utilizada para separar as subcadeias de carateres na cadeia devolvida. Se for omitido, o caráter de espaço ("") é utilizado. Se o delimitador é uma cadeia de caracteres de comprimento zero ("") ou nada, todos os itens na lista são concatenados com sem delimitadores.

**Observações**  
Existe paridade entre as funções de associação e divisão. A função de associação assume uma matriz de cadeias de caracteres e associa-usando uma cadeia de caracteres do delimitador, para retornar uma única cadeia de caracteres. A função Split usa uma cadeia de caracteres e separa o delimitador, para retornar uma matriz de cadeias de caracteres. No entanto, uma diferença-chave é que a associação pode concatenar cadeias de caracteres com qualquer cadeia de caracteres do delimitador, divisão apenas pode separar cadeias de caracteres com um único caráter delimitador.

**Exemplo:**  
`Join([proxyAddresses],",")`  
Poderia retornar: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

- - -
### <a name="lcase"></a>LCase
**Descrição:**  
A função de LCase converte todos os caracteres numa cadeia de caracteres em minúsculas.

**Sintaxe:**  
`str LCase(str value)`

**Exemplo:**  
`LCase("TeSt")`  
Devolve "teste".

- - -
### <a name="left"></a>Esquerda
**Descrição:**  
A função à esquerda devolve um número especificado de carateres da esquerda de uma cadeia de caracteres.

**Sintaxe:**  
`str Left(str string, num NumChars)`

* cadeia de caracteres: a cadeia a devolver carateres de
* NumChars: um número que identifica o número de carateres a devolver desde o início (esquerdo) de cadeia de caracteres

**Observações:**  
Uma cadeia de caracteres que contém os carateres numChars primeiro na cadeia de caracteres:

* Se numChars = 0, retornar a cadeia de caracteres vazia.
* Se numChars < 0, retornar a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se a cadeia de caracteres contém menos carateres do que o numChars especificado no número, é devolvida uma cadeia de caracteres idêntica à cadeia de caracteres (que é, que contém todos os caracteres no parâmetro 1).

**Exemplo:**  
`Left("John Doe", 3)`  
Devolve "Joh".

- - -
### <a name="len"></a>Len
**Descrição:**  
A função Len retorna o número de carateres numa cadeia.

**Sintaxe:**  
`num Len(str value)`

**Exemplo:**  
`Len("John Doe")`  
Devolve 8

- - -
### <a name="ltrim"></a>LTrim
**Descrição:**  
A função de LTrim remove espaços em branco à esquerda de uma cadeia.

**Sintaxe:**  
`str LTrim(str value)`

**Exemplo:**  
`LTrim(" Test ")`  
Devolve "teste"

- - -
### <a name="mid"></a>Mid
**Descrição:**  
A função de Mid devolve um número especificado de carateres da posição especificada numa cadeia de caracteres.

**Sintaxe:**  
`str Mid(str string, num start, num NumChars)`

* cadeia de caracteres: a cadeia a devolver carateres de
* iniciar: um número que identifica a partir de posição na cadeia de caracteres para devolver carateres de
* NumChars: um número que identifica o número de carateres a devolver da posição na cadeia de caracteres

**Observações:**  
Devolva a partir do início de posição na cadeia de caracteres de numChars.  
Uma cadeia de caracteres que contém carateres numChars do início de posição na cadeia de caracteres:

* Se numChars = 0, retornar a cadeia de caracteres vazia.
* Se numChars < 0, retornar a cadeia de caracteres de entrada.
* Se iniciar > o comprimento da cadeia de caracteres, retornar a cadeia de caracteres de entrada.
* Se iniciar < = 0, retornar a cadeia de entrada.
* Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se não existirem carateres de numChar restantes na cadeia de caracteres do início da posição, como o número de carateres possível é devolvido.

**Exemplo:**  
`Mid("John Doe", 3, 5)`  
Devolve "hn fazer".

`Mid("John Doe", 6, 999)`  
Devolve "Doe"

- - -
### <a name="now"></a>Agora
**Descrição:**  
A função agora devolve um valor de DateTime especificando a data e hora atuais, de acordo com a data do sistema e a hora do seu computador.

**Sintaxe:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Descrição:**  
A função de NumFromDate devolve uma data no formato de data do AD.

**Sintaxe:**  
`num NumFromDate(dt value)`

**Exemplo:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Returns 129699324000000000

- - -
### <a name="padleft"></a>PadLeft
**Descrição:**  
Os padleft modelo função esquerda-pads uma cadeia de caracteres para um comprimento especificado com um caráter de preenchimento fornecido.

**Sintaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

* cadeia de caracteres: a cadeia de caracteres para preencher.
* comprimento: Um número inteiro que representa o comprimento desejado da cadeia de caracteres.
* padCharacter: Uma cadeia de caracteres consistindo num único caractere a ser usado como o caráter de preenchimento

**Observações:**

* Se o comprimento da cadeia de caracteres for menor que o comprimento, padCharacter repetidamente é acrescentada ao início (esquerda) de cadeia de caracteres até tem um comprimento igual ao comprimento.
* padCharacter pode ser um caractere de espaço, mas não pode ser um valor nulo.
* Se o comprimento da cadeia de caracteres é igual ou superior ao comprimento, a cadeia de caracteres é retornada sem alterações.
* Se a cadeia tem um comprimento maior que ou igual ao comprimento, é devolvida uma cadeia de caracteres idêntica à cadeia de caracteres.
* Se o comprimento da cadeia de caracteres for menor que o comprimento, é devolvida uma nova cadeia de caracteres do comprimento desejado cadeia de caracteres que contêm preenchida com um padCharacter.
* Se a cadeia de caracteres for nula, a função devolve uma cadeia vazia.

**Exemplo:**  
`PadLeft("User", 10, "0")`  
Devolve "000000User".

- - -
### <a name="padright"></a>PadRight
**Descrição:**  
Os PadRight função direito-pads uma cadeia de caracteres para um comprimento especificado com um caráter de preenchimento fornecido.

**Sintaxe:**  
`str PadRight(str string, num length, str padCharacter)`

* cadeia de caracteres: a cadeia de caracteres para preencher.
* comprimento: Um número inteiro que representa o comprimento desejado da cadeia de caracteres.
* padCharacter: Uma cadeia de caracteres consistindo num único caractere a ser usado como o caráter de preenchimento

**Observações:**

* Se o comprimento da cadeia de caracteres for menor que o comprimento, em seguida, padCharacter é repetidamente acrescentado ao fim (direito) de cadeia de caracteres até que tem um comprimento igual ao comprimento.
* PadCharacter pode ser um caractere de espaço, mas não pode ser um valor nulo.
* Se o comprimento da cadeia de caracteres é igual ou superior ao comprimento, a cadeia de caracteres é retornada sem alterações.
* Se a cadeia tem um comprimento maior que ou igual ao comprimento, é devolvida uma cadeia de caracteres idêntica à cadeia de caracteres.
* Se o comprimento da cadeia de caracteres for menor que o comprimento, é devolvida uma nova cadeia de caracteres do comprimento desejado cadeia de caracteres que contêm preenchida com um padCharacter.
* Se a cadeia de caracteres for nula, a função devolve uma cadeia vazia.

**Exemplo:**  
`PadRight("User", 10, "0")`  
Devolve "User000000".

- - -
### <a name="pcase"></a>PCase
**Descrição:**  
A função de PCase converte o primeiro caráter de cada palavra espaço delimitado por uma cadeia de caracteres em maiúsculas, e todos os outros carateres são convertidos em minúsculas.

**Sintaxe:**  
`String PCase(string)`

**Observações:**

* Esta função não fornece atualmente maiúsculas/minúsculas adequadas para converter uma palavra que é inteiramente em maiúsculas, como um acrônimo.

**Exemplo:**  
`PCase("TEsT")`  
Devolve "Teste".

`PCase(LCase("TEST"))`  
Devolve "teste"

- - -
### <a name="randomnum"></a>RandomNum
**Descrição:**  
A função de RandomNum devolve um número aleatório entre um intervalo especificado.

**Sintaxe:**  
`num RandomNum(num start, num end)`

* iniciar: um número que identifica o limite inferior do valor aleatório para gerar
* fim: um número que identifica o limite superior do valor aleatório para gerar

**Exemplo:**  
`Random(100,999)`  
Pode retornar 734.

- - -
### <a name="removeduplicates"></a>RemoveDuplicates
**Descrição:**  
A função de RemoveDuplicates usa uma cadeia de caracteres com múltiplos valor e certifique-se de cada valor é exclusivo.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devolve um atributo sanitized proxyAddress onde todos os valores duplicados foram removidos.

- - -
### <a name="replace"></a>Substituir
**Descrição:**  
A função Replace substitui todas as ocorrências de uma cadeia de caracteres para outra cadeia.

**Sintaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

* Cadeia de caracteres: Uma cadeia de caracteres para substituir valores.
* OldValue: A cadeia a procurar e substituir.
* NewValue: A cadeia a substituir a.

**Observações:**  
A função reconhece os monikers especiais seguintes:

* \n – nova linha
* \r – símbolo de retorno
* \t – Tab

**Exemplo:**  
`Replace([address],"\r\n",", ")`  
Substitui CRLF por uma vírgula e um espaço e poderia levar a "Um Microsoft forma, Redmond, WA, USA"

- - -
### <a name="replacechars"></a>ReplaceChars
**Descrição:**  
A função de ReplaceChars substitui todas as ocorrências de carateres foi encontrados na cadeia de caracteres ReplacePattern.

**Sintaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

* Cadeia de caracteres: Uma cadeia de caracteres para substituir os carateres no.
* ReplacePattern: uma cadeia que contém um dicionário com carateres a substituir.

O formato é {source1}: {target1}, {source2}: {target2}, {sourceN}, {targetN} em que a origem é o caractere para localizar e substituir a cadeia de caracteres de destino.

**Observações:**

* A função aceita cada ocorrência de origens definidas e substitui-los com os destinos.
* A origem tem de ter exatamente um caráter de (unicode).
* A origem não pode estar vazio ou ter mais de um caráter (erro de análise).
* O destino pode ter vários carateres, por exemplo, ö:oe, β:ss.
* O destino pode ser vazio que indica que o caráter deve ser removido.
* A origem diferencia maiúsculas de minúsculas e tem de ser uma correspondência exata.
* O, (vírgula) e: (vírgula) são caracteres reservados e não pode ser substituído usando essa função.
* Espaços e outros caracteres brancas na cadeia de caracteres ReplacePattern são ignorados.

**Exemplo:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Devolve Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Devolve "ONeil", de escala única é definido para ser removido.

- - -
### <a name="right"></a>Direita
**Descrição:**  
A função certa devolve um número especificado de carateres da direita (fim) de uma cadeia de caracteres.

**Sintaxe:**  
`str Right(str string, num NumChars)`

* cadeia de caracteres: a cadeia a devolver carateres de
* NumChars: um número que identifica o número de carateres a devolver do fim (direito) de cadeia de caracteres

**Observações:**  
NumChars carateres são devolvidos da última posição da cadeia de caracteres.

Uma cadeia de caracteres contendo os últimos carateres de numChars na cadeia de caracteres:

* Se numChars = 0, retornar a cadeia de caracteres vazia.
* Se numChars < 0, retornar a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se a cadeia de caracteres contém menos carateres do que o NumChars especificado no número, é devolvida uma cadeia de caracteres idêntica à cadeia de caracteres.

**Exemplo:**  
`Right("John Doe", 3)`  
Devolve "Doe".

- - -
### <a name="rtrim"></a>RTrim
**Descrição:**  
A função de RTrim remove espaços em branco à direita de uma cadeia.

**Sintaxe:**  
`str RTrim(str value)`

**Exemplo:**  
`RTrim(" Test ")`  
Devolve "Teste".

- - -
### <a name="select"></a>Selecione
**Descrição:**  
Processo de todos os valores num atributo com múltiplos valores (ou saída de uma expressão) com base na função especificada.

**Sintaxe:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: Representa um elemento no atributo com múltiplos valor
* atributo: o atributo com múltiplos valor
* expressão: uma expressão que retorna uma coleção de valores
* condição: qualquer função que pode processar um item no atributo

**Exemplos:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Devolve todos os valores otherPhone o atributo com múltiplos valores depois de ter sido removido hífens (-).

- - -
### <a name="split"></a>Dividir
**Descrição:**  
A função Split usa uma cadeia de caracteres separada com um delimitador e faz com que seja uma cadeia de caracteres com múltiplos valor.

**Sintaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* valor: a cadeia de caracteres com um caráter delimitador para separar.
* delimitador: caráter para ser utilizado como o delimitador individual.
* limite: número máximo de valores que podem devolver.

**Exemplo:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Devolve uma cadeia com múltiplos valor com 2 elementos úteis para o atributo proxyAddress.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Descrição:**  
A função de StringFromGuid usa um GUID de binário e converte-o para uma cadeia de caracteres

**Sintaxe:**  
`str StringFromGuid(bin GUID)`

- - -
### <a name="stringfromsid"></a>StringFromSid
**Descrição:**  
A função de StringFromSid converte uma matriz de bytes que contém um identificador de segurança para uma cadeia de caracteres.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

- - -
### <a name="switch"></a>Comutador
**Descrição:**  
A função de comutador é utilizada para devolver um valor único com base nas condições avaliadas.

**Sintaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Expressão de variante que pretende avaliar.
* Valor: Valor a devolver se a expressão correspondente é True.

**Observações:**  
A lista de argumentos da função de comutador consiste em pares de valores e expressões. As expressões são avaliadas da esquerda para a direita e é devolvido o valor associado a primeira expressão a avaliar como verdadeiro. Se as partes não estão corretamente emparelhadas, ocorrerá um erro de tempo de execução.

Por exemplo, se expr1 for True, o comutador retorna value1. Se expr-1 é False, mas expr-2 é True, o comutador devolve o valor 2 e assim por diante.

Comutador retorna um nada se:

* Nenhuma das expressões são True.
* A primeira expressão verdadeira tem um valor correspondente que má hodnotu Null.

Comutador avalia todas as expressões, mesmo que ele retorna apenas um deles. Por esse motivo, deve observar para efeitos de colaterais indesejáveis. Por exemplo, se a avaliação de qualquer expressão resultar numa divisão por zero erro, ocorrerá um erro.

Valor também pode ser a função de erro, que irá devolver uma string personalizada.

**Exemplo:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Devolve o idioma falado em algumas cidades principais, caso contrário, devolve um erro.

- - -
### <a name="trim"></a>Cortar
**Descrição:**  
A função de compactação remove e à direita de uma cadeia contém espaços em branco.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Devolve "Teste".

`Trim([proxyAddresses])`  
Remove espaços para cada valor no atributo proxyAddress à esquerda e.

- - -
### <a name="ucase"></a>UCase
**Descrição:**  
A função UCase converte todos os caracteres numa cadeia de caracteres em maiúsculas.

**Sintaxe:**  
`str UCase(str string)`

**Exemplo:**  
`UCase("TeSt")`  
Devolve "Teste".

- - -
### <a name="where"></a>Onde

**Descrição:**  
Devolve um subconjunto de valores de um atributo com múltiplos valores (ou saída de uma expressão) com base numa condição específica.

**Sintaxe:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: Representa um elemento no atributo com múltiplos valor
* atributo: o atributo com múltiplos valor
* condição: qualquer expressão que pode ser avaliada como verdadeira ou falsa
* expressão: uma expressão que retorna uma coleção de valores

**Exemplo:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Devolve os valores de certificado userCertificate o atributo com múltiplos valores que não estão a expirou.

- - -
### <a name="with"></a>Com
**Descrição:**  
A função de With fornece uma forma de simplificar uma expressão complexa, utilizando uma variável para representar um subexpressão, que é apresentado um ou mais vezes na expressão complexa.

**Sintaxe:**
`With(var variable, exp subExpression, exp complexExpression)`  
* variável: Representa a subexpressão.
* subexpressão: subexpressão representado pela variável.
* complexExpression: Uma expressão complexa.

**Exemplo:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
É funcionalmente equivalente:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Que retorna apenas os valores de certificado existe no atributo userCertificate.


- - -
### <a name="word"></a>Word
**Descrição:**  
A função do Word retorna uma palavra contida numa cadeia de caracteres, com base nos parâmetros que descrevem os delimitadores a utilizar e o número de palavras para retornar.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* cadeia de caracteres: a cadeia de caracteres para retornar uma palavra de.
* WordNumber: um número que identifica o número de word deve retornar.
* delimitadores: uma cadeia de caracteres que representa o delimiter(s) que deve ser utilizado para identificar palavras

**Observações:**  
Cada cadeia de caracteres na cadeia de caracteres separados por um dos caracteres delimitadores são identificados como palavras:

* Se número < 1, devolve vazio a cadeia de caracteres.
* Se a cadeia de caracteres for nula, devolve a cadeia de caracteres vazia.

Se a cadeia de caracteres conter menos do que as palavras número ou cadeia de caracteres não contém quaisquer palavras identificadas por delimitadores, é devolvida uma cadeia vazia.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Devolve "brown"

`Word("This,string!has&many separators",3,",!&#")`  
Retornaria "tem"

## <a name="additional-resources"></a>Recursos Adicionais
* [Entender as expressões do aprovisionamento declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Sincronização do Azure AD Connect: Personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
