---
title: 'Sincronização azure AD Connect: Referência de funções / Microsoft Docs'
description: Referência das expressões de provisionamento declarativa sincronia do Azure AD Connect.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69900049"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronização azure AD Connect: Referência de funções
No Azure AD Connect, as funções são usadas para manipular um valor de atributo durante a sincronização.  
A Sintaxe das funções é expressa utilizando o seguinte formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se uma função estiver sobrecarregada e aceitar várias sintaxes, todas as sintaxes válidas estão listadas.  
As funções são fortemente dactilografadas e verificam que o tipo passado corresponde ao tipo documentado.  
Se o tipo não corresponder, é lançado um erro.

Os tipos são expressos com a seguinte sintaxe:

* **bin** – Binário
* **bool** - Boolean
* **dT** – UtC Data/Hora
* **enum** – Enumeração de constantes conhecidas
* **exp** – Expressão, que se espera que avalie a um Boolean
* **mvbin** – Binário Multi-Valorizado
* **mvstr** – Corda Multi-Valorizada
* **mvref** - Referência multi-valorizada
* **num** – Numérico
* **ref** – Referência
* **str** – Corda
* **var** – Uma variante de (quase) qualquer outro tipo
* **vazio** – não devolve um valor

As funções com os tipos **mvbin,** **mvstr,** e **mvref** só podem funcionar em atributos de vários valores. Funções com **bin,** **str,** e **ref** trabalho em atributos de valor único e multivalorizados.

## <a name="functions-reference"></a>Referência das Funções

| Lista de funções |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certificado** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[Algoritmo CertKey](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotAntes](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Conversão** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[Cguid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConverttoBase64](#converttobase64) |[ConvertEFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[Cnum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromguid](#stringfromguid) |[StringFromSid](#stringfromsid) | |
| **Data/Hora** | | | | |
| [DataAdd](#dateadd) |[DataFromnum](#datefromnum) |[FormatoDateTime](#formatdatetime) |[Agora](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Diretório** | | | | |
| [Componente DNComponente](#dncomponent) |[DNComponentRev](#dncomponentrev) |[Componente EscapeDN](#escapedncomponent) | | |
| **Avaliação** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[Isguid](#isguid) | |
| [Isnull](#isnull) |[IsnullorEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matemática** | | | | |
| [Bitand](#bitand) |[Bitor](#bitor) |[Randomnum](#randomnum) | | |
| **Multi-valorizado** | | | | |
| [Contém](#contains) |[Contagem](#count) |[Item](#item) |[ItemOrNull](#itemornull) | |
| [Aderir](#join) |[Remover Duplicatos](#removeduplicates) |[Divisão](#split) | | |
| **Fluxo de Programa** | | | | |
| [Erro](#error) |[IIF](#iif) |[Selecionar](#select) |[Comutador](#switch) | |
| [Onde](#where) |[Com](#with) | | | |
| **Texto** | | | | |
| [GUIA](#guid) |[Instr](#instr) |[Rio Instrrev](#instrrev) |[LCase](#lcase) | |
| [Esquerda](#left) |[Len](#len) |[LTrim](#ltrim) |[Meados](#mid) | |
| [PadLeft](#padleft) |[Direito de padright](#padright) |[PCase](#pcase) |[Substituir](#replace) | |
| [Substituir Chars](#replacechars) |[Certo](#right) |[RTrim](#rtrim) |[Aparar](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

---
### <a name="bitand"></a>Bitand
**Description:**  
A função BitAnd define bits especificados num valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

* valor1, valor2: valores numéricos que devem ser E'ed juntos

**Observações:**  
Esta função converte ambos os parâmetros para a representação binária e define um pouco para:

* 0 - se uma ou ambas as partes correspondentes no *valor 1* e no *valor2* forem 0
* 1 - se ambas as partes correspondentes forem 1.

Por outras palavras, retorna a 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
`BitAnd(&HF, &HF7)`  
Devoluções 7 porque hexadecimal "F" e "F7" avaliam a este valor.

---
### <a name="bitor"></a>Bitor
**Description:**  
A função BitOr define bits especificados num valor.

**Sintaxe:**  
`num BitOr(num value1, num value2)`

* valor1, valor2: valores numéricos que devem ser OR'ed juntos

**Observações:**  
Esta função converte os parâmetros para a representação binária e define um pouco para 1 se uma ou ambas as partes correspondentes na máscara e na bandeira forem 1, e a 0 se ambas as partes correspondentes forem 0. Por outras palavras, devolve 1 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 0.

---
### <a name="cbool"></a>CBool
**Description:**  
A função CBool devolve uma Boolean com base na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Observações:**  
Se a expressão avaliar para um valor não zero, então CBool devolve True, caso contrário devolve Falso.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Devoluções Verdadeiras se ambos os atributos tiverem o mesmo valor.

---
### <a name="cdate"></a>CDate
**Description:**  
A função CDate devolve um Data-Data DA UTC de uma corda. DateTime não é um tipo de atributo nativo em Sync, mas é usado por algumas funções.

**Sintaxe:**  
`dt CDate(str value)`

* Valor: Uma corda com data, hora e opcionalfuso horário

**Observações:**  
A corda devolvida está sempre na UTC.

**Exemplo:**  
`CDate([employeeStartTime])`  
Devolve um DataTime com base na hora de início do empregado

`CDate("2013-01-10 4:00 PM -8")`  
Devoluções um DataTime que representa "2013-01-11 12:00 AM"


---
### <a name="certextensionoids"></a>CertExtensionOids
**Description:**  
Devolve os valores oid de todas as extensões críticas de um objeto de certificado.

**Sintaxe:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certformat"></a>CertFormat
**Description:**  
Devolve o nome do formato deste certificado X.509v3.

**Sintaxe:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Description:**  
Devolve o pseudónimo associado para um certificado.

**Sintaxe:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certhashstring"></a>CertHashString
**Description:**  
Devolve o valor do hash SHA1 para o certificado X.509v3 como uma cadeia hexadecimal.

**Sintaxe:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certissuer"></a>CertIssuer
**Description:**  
Devolve o nome da autoridade de certificados que emitiu o certificado X.509v3.

**Sintaxe:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Description:**  
Devolve o nome distinto do emitente do certificado.

**Sintaxe:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certissueroid"></a>CertIssuerOid
**Description:**  
Devolve o Oid do emitente de certificado.

**Sintaxe:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certkeyalgorithm"></a>Algoritmo CertKey
**Description:**  
Devolve a informação do algoritmo chave para este certificado X.509v3 como uma corda.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Description:**  
Devolve os parâmetros do algoritmo chave para o certificado X.509v3 como uma cadeia hexadecimal.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certnameinfo"></a>CertNameInfo
**Description:**  
Devolve os nomes do sujeito e do emitente de um certificado.

**Sintaxe:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.
*   X509NameType: O valor X509NameType para o assunto.
*   incluiONome do Emitida: fiel a incluir o nome emitente; caso contrário, falso.

---
### <a name="certnotafter"></a>CertNotAfter
**Description:**  
Devolve a data na hora local após a qual um certificado já não é válido.

**Sintaxe:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certnotbefore"></a>CertNotAntes
**Description:**  
Devolve a data na hora local em que um certificado se torna válido.

**Sintaxe:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Description:**  
Devolve o Oid da chave pública para o certificado X.509v3.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Description:**  
Devolve o Oid dos parâmetros de chave pública para o certificado X.509v3.

**Sintaxe:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Description:**  
Devolve o número de série do certificado X.509v3.

**Sintaxe:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Description:**  
Devolve o Oid do algoritmo usado para criar a assinatura de um certificado.

**Sintaxe:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certsubject"></a>CertSubject
**Description:**  
Obtém o nome distinto do sujeito de um certificado.

**Sintaxe:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Description:**  
Devolve o nome distinguido do sujeito de um certificado.

**Sintaxe:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Description:**  
Devolve o Oid do nome do assunto de um certificado.

**Sintaxe:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certthumbprint"></a>CertThumbprint
**Description:**  
Devolve a impressão digital de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="certversion"></a>CertVersion
**Description:**  
Devolve a versão de formato X.509 de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.

---
### <a name="cguid"></a>Cguid
**Description:**  
A função CGuid converte a representação de cordas de um GUID à sua representação binária.

**Sintaxe:**  
`bin CGuid(str GUID)`

* Uma corda formatada neste padrão: xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxx}

---
### <a name="contains"></a>Contains
**Description:**  
A função Contém encontra uma corda dentro de um atributo multi-valorizado

**Sintaxe:**  
`num Contains (mvstring attribute, str search)`- sensível a casos  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`- sensível a casos

* atributo: o atributo multivalorizado à procura.
* pesquisa: corda para encontrar no atributo.
* Tipo de caso: CaseInsensitive ou CaseSensitive.

Índice de devoluções no atributo multivalorizado onde a corda foi encontrada. 0 é devolvido se a corda não for encontrada.

**Observações:**  
Para atributos de cadeia sem valor, a pesquisa encontra substrings nos valores.  
Para atributos de referência, a cadeia pesquisada deve corresponder exatamente ao valor a ser considerado compatível.

**Exemplo:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Se o atributo proxyAddresss tiver um endereço de e-mail primário (indicado pela maiúscula "SMTP:"), em seguida, devolver o atributo proxyAddress, então devolva um erro.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Description:**  
A função ConvertFromBase64 converte o valor codificado de base 64 especificado para uma cadeia regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)`- assume unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* fonte: Cadeia codificada Base64  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos voltam "*Olá mundo!*"

---
### <a name="convertfromutf8hex"></a>ConvertEFromUTF8Hex
**Description:**  
A função ConvertFromUTF8Hex converte o valor codificado uTF8 hex especificado para uma cadeia.

**Sintaxe:**  
`str ConvertFromUTF8Hex(str source)`

* fonte: UTF8 2-byte picada codificada

**Observações:**  
A diferença entre esta função e o ConvertFromBase64([], UTF8) na forma como o resultado é amigável para o atributo dN.  
Este formato é utilizado pelo Azure Ative Diretório como DN.

**Exemplo:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Devoluções "*Olá mundo!*"

---
### <a name="converttobase64"></a>ConverttoBase64
**Description:**  
A função ConvertToBase64 converte uma cadeia para uma cadeia Unicode base64.  
Converte o valor de um conjunto de inteiros para a sua representação de cordas equivalente que é codificada com base-64 dígitos.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Devoluções "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Description:**  
A função ConvertToUTF8Hex converte uma cadeia para um valor codificado uTF8 Hex.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Observações:**  
O formato de saída desta função é utilizado pelo Azure Ative Directory como formato de atribuição dN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Devoluções 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Description:**  
A função Count devolve o número de elementos num atributo multivalorizado

**Sintaxe:**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>Cnum
**Description:**  
A função CNum pega numa corda e devolve um tipo de dados numéricos.

**Sintaxe:**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Description:**  
Converte uma corda para um atributo de referência

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
Devolve uma data contendo uma data à qual foi adicionado um intervalo de tempo especificado.

**Sintaxe:**  
`dt DateAdd(str interval, num value, dt date)`

* intervalo: Expressão de corda que é o intervalo de tempo que pretende adicionar. A corda deve ter um dos seguintes valores:
  * Yyyy Ano
  * q Trimestre
  * m Mês
  * y Dia do ano
  * d Dia
  * w Dia da Semana
  * semana ww
  * h Hora
  * n Minuto
  * s Segundo
* valor: O número de unidades que pretende adicionar. Pode ser positivo (para obter datas no futuro) ou negativo (para obter datas no passado).
* data: Data data representativa da data a que o intervalo é adicionado.

**Exemplo:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Soma 3 meses e devolve um DataTime que representa "2001-04-01".

---
### <a name="datefromnum"></a>DataFromnum
**Description:**  
A função DateFromNum converte um valor no formato de data da AD para um tipo DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devoluções DataTime representando 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>Componente DNComponente
**Description:**  
A função DNComponent devolve o valor de um componente DN especificado que vai da esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dN: o atributo de referência para interpretar
* ComponentNumber: O componente no DN para devolver

**Exemplo:**  
`DNComponent(CRef([dn]),1)`  
Se o DN é "cn=Joe,ou=...", devolve Joe

---
### <a name="dncomponentrev"></a>DNComponentRev
**Description:**  
A função DNComponentRev devolve o valor de um componente DN especificado que vai da direita (a extremidade).

**Sintaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dN: o atributo de referência para interpretar
* ComponentNumber - O componente no DN para devolver
* Opções: DC – Ignore todos os componentes com "dc="

**Exemplo:**  
Se o DN for "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com" então  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Ambos devolvem-nos.

---
### <a name="error"></a>Erro
**Description:**  
A função Error é utilizada para devolver um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se a conta de atributonome não estiver presente, deite um erro no objeto.

---
### <a name="escapedncomponent"></a>Componente EscapeDN
**Description:**  
A função EscapeDNComponent pega num componente de um DN e escapa-lhe para que possa ser representado em LDAP.

**Sintaxe:**  
`str EscapeDNComponent(str value)`

**Exemplo:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Certifica-se de que o objeto pode ser criado num diretório LDAP mesmo que o atributo do displayName tenha caracteres que devem ser escapados em LDAP.

---
### <a name="formatdatetime"></a>FormatoDateTime
**Description:**  
A função FormatDateTime é usada para formatar um DateTime para uma corda com um formato especificado

**Sintaxe:**  
`str FormatDateTime(dt value, str format)`

* valor: um valor no formato DateTime
* formato: uma cadeia que representa o formato para converter.

**Observações:**  
Os valores possíveis para o formato podem ser consultados aqui: [Formatos de data e hora personalizados para a função FORMAT](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

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
A função IIF devolve um conjunto de valores possíveis com base numa condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condição: qualquer valor ou expressão que possa ser avaliado a verdade ou falsa.
* valorIfTrue: Se a condição avaliar verdadeiramente, o valor devolvido.
* valorIfFalso: Se a condição avaliar a falso, o valor devolvido.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o utilizador for estagiário, devolve o pseudónimo de um utilizador com "t-" adicionado ao início do mesmo, então devolve o pseudónimo do utilizador tal como está.

---
### <a name="instr"></a>Instr
**Description:**  
A função InStr encontra a primeira ocorrência de uma substring numa corda

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: string a ser pesquisado
* stringmatch: corda a ser encontrada
* início: posição inicial para encontrar a subcadeia
* comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição onde o substring foi encontrado ou 0 se não for encontrado.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Valores e valores para 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia para 7

---
### <a name="instrrev"></a>Rio Instrrev
**Description:**  
A função InStrRev encontra a última ocorrência de uma substring numa corda

**Sintaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: string a ser pesquisado
* stringmatch: corda a ser encontrada
* início: posição inicial para encontrar a subcadeia
* comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição onde o substring foi encontrado ou 0 se não for encontrado.

**Exemplo:**  
`InStrRev("abbcdbbbef","bb")`  
Devoluções 7

---
### <a name="isbitset"></a>IsBitSet
**Description:**  
A função IsBitSet Tests se um pouco é definido ou não

**Sintaxe:**  
`bool IsBitSet(num value, num flag)`

* valor: um valor numérico que é avaliado.bandeira: um valor numérico que tem a parte a avaliar

**Exemplo:**  
`IsBitSet(&HF,4)`  
Devoluções Verdadeiraporque bit "4" está definido no valor hexadecimal "F"

---
### <a name="isdate"></a>IsDate
**Description:**  
Se a expressão pode ser avaliada como um tipo DateTime, então a função IsDate avalia para True.

**Sintaxe:**  
`bool IsDate(var Expression)`

**Observações:**  
Utilizado para determinar se o CDate() pode ser bem sucedido.

---
### <a name="iscert"></a>IsCert
**Description:**  
Devoluções verdadeiras se os dados brutos puderem ser serializados em .NET X509Certificate2 objeto de certificado.

**Sintaxe:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação de matriz byte de um certificado X.509. A matriz de byte pode ser codificada binária (DER) ou dados X.509 codificados por Base64.
---
### <a name="isempty"></a>IsEmpty
**Description:**  
Se o atributo estiver presente no CS ou MV mas avaliar uma corda vazia, então a função IsEmpty avalia para True.

**Sintaxe:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>Isguid
**Description:**  
Se a corda pode ser convertida para um GUID, então a função IsGuid avaliada como verdadeira.

**Sintaxe:**  
`bool IsGuid(str GUID)`

**Observações:**  
Um GUID é definido como uma corda seguindo um destes padrões: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxx}

Usado para determinar se o CGuid pode ser bem sucedido.

**Exemplo:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Se o StrAttribute tiver um formato GUID, devolva uma representação binária, caso contrário, devolva um Nulo.

---
### <a name="isnull"></a>Isnull
**Description:**  
Se a expressão avaliar a Nula, então a função IsNull retorna verdadeira.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Observações:**  
Para um atributo, um Nulo é expresso pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Devoluções Verdadeiras se o atributo não estiver presente no CS ou MV.

---
### <a name="isnullorempty"></a>IsnullorEmpty
**Description:**  
Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeira.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Observações:**  
Para um atributo, isto avaliaria a True se o atributo está ausente ou está presente, mas é uma corda vazia.  
O inverso desta função chama-se IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Devoluções Verdadeirase se o atributo não estiver presente ou se for uma corda vazia no CS ou MV.

---
### <a name="isnumeric"></a>IsNumeric
**Description:**  
A função isnumérica devolve um valor booleano indicando se uma expressão pode ser avaliada como um tipo de número.

**Sintaxe:**  
`bool IsNumeric(var Expression)`

**Observações:**  
Usado para determinar se o CNum() pode ser bem sucedido para analisar a expressão.

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
Se a expressão avaliar para uma cadeia que não é Nula e não está vazia, então a função IsPresent retorna verdadeira.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Observações:**  
O inverso desta função chama-se IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Description:**  
A função Item devolve um item de uma cadeia/atributo de vários valores.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

* atributo: atributo multivalorizado
* índice: índice para um item na cadeia multi-valorizada.

**Observações:**  
A função item é útil juntamente com a função Contém, uma vez que esta última função devolve o índice a um item no atributo multivalorizado.

Um erro se o índice estiver fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Devolve o endereço de e-mail principal.

---
### <a name="itemornull"></a>ItemOrNull
**Description:**  
A função ItemOrNull devolve um item de uma cadeia/atributo de vários valores.

**Sintaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

* atributo: atributo multivalorizado
* índice: índice para um item na cadeia multi-valorizada.

**Observações:**  
A função ItemOrNull é útil juntamente com a função Contém, uma vez que esta última função devolve o índice a um item no atributo multivalorizado.

Se o índice estiver fora dos limites, então devolve um valor Nulo.

---
### <a name="join"></a>Associar
**Description:**  
A função 'Juntar' pega numa corda de vários valores e devolve uma corda de valor único com separador especificado inserido entre cada item.

**Sintaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* atributo: Atributo multivalorizado contendo cordas a aderir.
* delimitador: Qualquer corda, usada para separar as subcordas na corda devolvida. Se omitida, o caracteres do espaço (" ") é utilizado. Se o Delimiter for uma corda de comprimento zero (") ou nada, todos os itens da lista são concatenados sem delimitadores.

**Observações**  
Há paridade entre as funções De Juntar e Divisão. A função Join pega numa série de cordas e junta-as usando uma corda delimitador, para devolver uma única corda. A função Split pega numa corda e separa-a no delimitador, para devolver uma série de cordas. No entanto, uma diferença fundamental é que Join pode concatenar cordas com qualquer corda delimitador, Split só pode separar cordas usando um único delimitador de caracteres.

**Exemplo:**  
`Join([proxyAddresses],",")`  
Poderia voltar"SMTP:john.doe@contoso.comsmtp:jd@contoso.com" " "

---
### <a name="lcase"></a>LCase
**Description:**  
A função LCase converte todos os caracteres numa corda para minúscula.

**Sintaxe:**  
`str LCase(str value)`

**Exemplo:**  
`LCase("TeSt")`  
Devolve "teste".

---
### <a name="left"></a>Esquerda
**Description:**  
A função Esquerda devolve um número especificado de caracteres da esquerda de uma corda.

**Sintaxe:**  
`str Left(str string, num NumChars)`

* cadeia: a corda para devolver os caracteres de
* NumChars: um número que identifica o número de caracteres a regressar do início (à esquerda) da corda

**Observações:**  
Uma corda contendo os primeiros personagens de numchars em cadeia:

* Se numChars = 0, volte a ficar vazio.
* Se numChars < 0, volte a ser corda de entrada.
* Se a corda for nula, volte a ficar vazia.

Se a corda contiver menos caracteres do que o número especificado em numChars, uma corda idêntica à corda (isto é, contendo todos os caracteres no parâmetro 1) é devolvida.

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
A função LTrim remove os espaços brancos principais de uma corda.

**Sintaxe:**  
`str LTrim(str value)`

**Exemplo:**  
`LTrim(" Test ")`  
Devoluções "Teste"

---
### <a name="mid"></a>Mid
**Description:**  
A função Mid devolve um número especificado de caracteres de uma posição especificada numa corda.

**Sintaxe:**  
`str Mid(str string, num start, num NumChars)`

* cadeia: a corda para devolver os caracteres de
* início: um número que identifica a posição inicial na cadeia para devolver caracteres de
* NumChars: um número que identifica o número de caracteres a regressar da posição em cadeia

**Observações:**  
Volte a colocar os caracteres numChars a partir da posição que começa na cadeia.  
Uma cadeia contendo personagens de numChars a partir da posição começa em cadeia:

* Se numChars = 0, volte a ficar vazio.
* Se numChars < 0, volte a ser corda de entrada.
* Se começar > comprimento da corda, volte a ser a corda de entrada.
* Se começar <= 0, devolva a cadeia de entrada.
* Se a corda for nula, volte a ficar vazia.

Se não houver personagens numChar que permaneçam em cadeia desde o início da posição, o maior número possível de caracteres são devolvidos.

**Exemplo:**  
`Mid("John Doe", 3, 5)`  
Devolve "hn Do".

`Mid("John Doe", 6, 999)`  
Devoluções "Doe"

---
### <a name="now"></a>Now
**Description:**  
A função Now devolve um DataTime especificando a data e a hora atuais, de acordo com a data e hora do sistema do computador.

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
Devoluções 1296993240000000000000000000000000000000000

---
### <a name="padleft"></a>PadLeft
**Description:**  
A função PadLeft é uma corda com um comprimento especificado utilizando um caracteres de enchimento fornecido.

**Sintaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

* corda: a corda para almofada.
* comprimento: Um inteiro que representa o comprimento desejado da corda.
* padCharacter: Uma corda composta por um único personagem para usar como o personagem pad

**Observações:**

* Se o comprimento da corda for inferior ao comprimento, o padCharacter é repetidamente anexado ao início (à esquerda) da corda até ter um comprimento igual ao comprimento.
* PadCharacter pode ser um personagem espacial, mas não pode ser um valor nulo.
* Se o comprimento da corda for igual ou superior ao comprimento, a corda é devolvida inalterada.
* Se a corda tiver um comprimento maior ou igual ao comprimento, uma corda idêntica à corda é devolvida.
* Se o comprimento da corda for inferior ao comprimento, então uma nova cadeia do comprimento desejado é devolvida contendo corda acolchoada com um padCharacter.
* Se a corda for nula, a função devolve uma corda vazia.

**Exemplo:**  
`PadLeft("User", 10, "0")`  
Devoluções "000000User".

---
### <a name="padright"></a>Direito de padright
**Description:**  
A função PadRight é de uma corda com um comprimento especificado utilizando um caracteres de enchimento fornecido.

**Sintaxe:**  
`str PadRight(str string, num length, str padCharacter)`

* corda: a corda para almofada.
* comprimento: Um inteiro que representa o comprimento desejado da corda.
* padCharacter: Uma corda composta por um único personagem para usar como o personagem pad

**Observações:**

* Se o comprimento da corda for inferior ao comprimento, o padCharacter é repetidamente anexado à extremidade (direita) da corda até ter um comprimento igual ao comprimento.
* padCharacter pode ser um personagem de espaço, mas não pode ser um valor nulo.
* Se o comprimento da corda for igual ou superior ao comprimento, a corda é devolvida inalterada.
* Se a corda tiver um comprimento maior ou igual ao comprimento, uma corda idêntica à corda é devolvida.
* Se o comprimento da corda for inferior ao comprimento, então uma nova cadeia do comprimento desejado é devolvida contendo corda acolchoada com um padCharacter.
* Se a corda for nula, a função devolve uma corda vazia.

**Exemplo:**  
`PadRight("User", 10, "0")`  
Devoluções "User0000000".

---
### <a name="pcase"></a>PCase
**Description:**  
A função PCase converte o primeiro personagem de cada palavra delimitada de cada espaço numa corda para a parte superior, e todos os outros caracteres são convertidos para minúsculas.

**Sintaxe:**  
`String PCase(string)`

**Observações:**

* Esta função não fornece atualmente um invólucro adequado para converter uma palavra inteiramente maiúscula, como um acrónimo.

**Exemplo:**  
`PCase("TEsT")`  
Devolve "Teste".

`PCase(LCase("TEST"))`  
Devoluções "Teste"

---
### <a name="randomnum"></a>Randomnum
**Description:**  
A função RandomNum devolve um número aleatório entre um intervalo especificado.

**Sintaxe:**  
`num RandomNum(num start, num end)`

* início: um número que identifica o limite inferior do valor aleatório para gerar
* fim: um número que identifica o limite superior do valor aleatório para gerar

**Exemplo:**  
`Random(100,999)`  
Pode devolver 734.

---
### <a name="removeduplicates"></a>Remover Duplicatos
**Description:**  
A função RemoveDuplicates pega numa cadeia de vários valores e certifica-se de que cada valor é único.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devolve um atributo proxyAddress sanitizado onde todos os valores duplicados foram removidos.

---
### <a name="replace"></a>Substituir
**Description:**  
A função Substituir substitui todas as ocorrências de uma corda a outra corda.

**Sintaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

* cadeia: Uma corda para substituir valores.
* OldValue: A corda para procurar e substituir.
* NewValue: A corda a substituir.

**Observações:**  
A função reconhece os seguintes apelidos especiais:

* \n – Nova Linha
* \r – Retorno do transporte
* \t - Separador

**Exemplo:**  
`Replace([address],"\r\n",", ")`  
Substitui o CRLF por uma vírvia e espaço, e pode levar a "One Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>Substituir Chars
**Description:**  
A função ReplaceChars substitui todas as ocorrências de caracteres encontrados na cadeia ReplacePattern.

**Sintaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

* cadeia: Uma corda para substituir os caracteres.
* Substitua O Padrão: uma cadeia contendo um dicionário com caracteres para substituir.

O formato é {source1}:{target1},{source2}:{target2},{sourceN},{targetN} onde a fonte é o personagem para encontrar e direcionar a cadeia para substituir.

**Observações:**

* A função toma cada ocorrência de fontes definidas e substitui-as por alvos.
* A fonte deve ser exatamente um (unicode) personagem.
* A fonte não pode estar vazia ou superior a um personagem (erro de análise).
* O alvo pode ter vários caracteres, por exemplo ö:oe, β:ss.
* O alvo pode estar vazio indicando que o caracteres deve ser removido.
* A fonte é sensível ao caso e deve ser uma correspondência exata.
* As (vírgulas) e : (cólon) são caracteres reservados e não podem ser substituídas com esta função.
* Os espaços e outros caracteres brancos na cadeia ReplacePattern são ignorados.

**Exemplo:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Devoluções Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Devoluções "ONeil", o tique-taque único está definido para ser removido.

---
### <a name="right"></a>Direita
**Description:**  
A função Direita devolve um número especificado de caracteres da extremidade direita (extremidade) de uma corda.

**Sintaxe:**  
`str Right(str string, num NumChars)`

* cadeia: a corda para devolver os caracteres de
* NumChars: um número que identifica o número de caracteres a regressar do fim (à direita) da corda

**Observações:**  
Os personagens de NumChars são devolvidos da última posição da corda.

Uma corda contendo os últimos personagens de numchars em corda:

* Se numChars = 0, volte a ficar vazio.
* Se numChars < 0, volte a ser corda de entrada.
* Se a corda for nula, volte a ficar vazia.

Se a corda contiver menos caracteres do que o número especificado no NumChars, uma corda idêntica à corda é devolvida.

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
Processe todos os valores num atributo multivalorizado (ou saída de uma expressão) com base na função especificada.

**Sintaxe:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: Representa um elemento no atributo multivalorizado
* atributo: o atributo multivalorizado
* expressão: uma expressão que devolve uma coleção de valores
* condição: qualquer função que possa processar um item no atributo

**Exemplos:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Devolva todos os valores do atributo multivalorizado outro Telefone depois de os hífenes (-) terem sido removidos.

---
### <a name="split"></a>Dividir
**Description:**  
A função Split tem uma corda separada com um delimitador e torna-a uma corda de vários valores.

**Sintaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* valor: a corda com um carácter delimitador para separar.
* delimitador: personagem único a ser usado como delimitador.
* limite: número máximo de valores que podem regressar.

**Exemplo:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Devolve uma cadeia de vários valores com 2 elementos úteis para o atributo proxyAddress.

---
### <a name="stringfromguid"></a>StringFromguid
**Description:**  
A função StringFromGuid toma um GUID binário e converte-o em uma corda

**Sintaxe:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Description:**  
A função StringFromSid converte um matriz de byte contendo um identificador de segurança numa corda.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Comutador
**Description:**  
A função Switch é utilizada para devolver um único valor com base nas condições avaliadas.

**Sintaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Expressão variante que pretende avaliar.
* valor: Valor a devolver se a expressão correspondente for Verdadeira.

**Observações:**  
A lista de argumentos de função Switch consiste em pares de expressões e valores. As expressões são avaliadas da esquerda para a direita, e o valor associado à primeira expressão para avaliar a Verdade é devolvido. Se as peças não estiverem devidamente emparelhadas, ocorre um erro de tempo de execução.

Por exemplo, se o expr1 for Verdadeiro, a Switch devolve valor1. Se o expr-1 for falso, mas expr-2 é verdade, a Switch devolve o valor-2, e assim por diante.

A Switch devolve um Nada se:

* Nenhuma das expressões é verdadeira.
* A primeira expressão verdadeira tem um valor correspondente que é Nulo.

A Switch avalia todas as expressões, mesmo que desista apenas de uma delas. Por esta razão, deve ter cuidado com os efeitos colaterais indesejáveis. Por exemplo, se a avaliação de qualquer expressão resultar numa divisão por erro zero, ocorre um erro.

O valor também pode ser a função Error, que devolveria uma corda personalizada.

**Exemplo:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Devolve a linguagem falada em algumas grandes cidades, caso contrário devolve um Erro.

---
### <a name="trim"></a>Trim
**Description:**  
A função Trim remove espaços brancos de condução e de saque de uma corda.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Devolve "Teste".

`Trim([proxyAddresses])`  
Remove espaços de liderança e de trailing para cada valor no atributo proxyAddress.

---
### <a name="ucase"></a>UCase
**Description:**  
A função UCase converte todos os caracteres numa corda para maiúscula.

**Sintaxe:**  
`str UCase(str string)`

**Exemplo:**  
`UCase("TeSt")`  
Devolve "TESTE".

---
### <a name="where"></a>Onde

**Description:**  
Devolve um subconjunto de valores a partir de um atributo multivalorizado (ou saída de uma expressão) com base em condições específicas.

**Sintaxe:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: Representa um elemento no atributo multivalorizado
* atributo: o atributo multivalorizado
* condição: qualquer expressão que possa ser avaliada a verdade ou falsa
* expressão: uma expressão que devolve uma coleção de valores

**Exemplo:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Devolva os valores do certificado no certificado de utilizador de atributos multivalorizados que não estejam caducados.

---
### <a name="with"></a>Com
**Description:**  
A função Com fornece uma forma de simplificar uma expressão complexa usando uma variável para representar uma subexpressão que aparece uma ou mais vezes na expressão complexa.

**Sintaxe:**
`With(var variable, exp subExpression, exp complexExpression)`  
* variável: Representa a subexpressão.
* subexpressão: subexpressão representada por variável.
* complexoExpressão: Uma expressão complexa.

**Exemplo:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
É funcionalmente equivalente a:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Que devolve apenas valores de certificado não expirados no atributo do Certificado de utilizador.


---
### <a name="word"></a>Word
**Description:**  
A função Word devolve uma palavra contida numa corda, baseada em parâmetros que descrevem os delimitadores a usar e o número de palavra sabotado.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* corda: a corda para devolver uma palavra de.
* Número de palavras: um número que identifica o número de palavra seletiva deve ser retornado.
* delimitadores: uma corda que representa o ou os delimitadores que devem ser utilizados para identificar palavras

**Observações:**  
Cada cadeia de caracteres em cadeia separada si uma das personagens em delimitadores é identificada como palavras:

* Se o número < 1, devolve corda vazia.
* Se a corda for nula, devolve corda vazia.

Se a corda contiver menos de palavras numéricas, ou a corda não contiver palavras identificadas pelos delimitadores, uma corda vazia é devolvida.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Devoluções "marrom"

`Word("This,string!has&many separators",3,",!&#")`  
Voltaria "tem"

## <a name="additional-resources"></a>Recursos Adicionais
* [Compreender as Expressões de Aprovisionamento Declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: Opções de sincronização personalizadas](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
