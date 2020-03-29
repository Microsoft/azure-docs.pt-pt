---
title: 'Azure AD Connect: Expressões declarativas de provisionamento / Microsoft Docs'
description: Explica as expressões de disposição declarativas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245510"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Sincronização Azure AD Connect: Compreender expressões de provisionamento declarativas
A sincronização Azure AD Connect baseia-se no fornecimento declarativo introduzido pela primeira vez no Forefront Identity Manager 2010. Permite-lhe implementar a sua lógica de negócio de integração de identidade completa sem a necessidade de escrever código compilado.

Uma parte essencial do provisionamento declarativo é a linguagem de expressão utilizada nos fluxos de atributos. O idioma utilizado é um subconjunto de Base® Visual ® Microsoft para aplicações (VBA). Este idioma é utilizado no Microsoft Office e os utilizadores com experiência de VBScript também o reconhecerão. A Linguagem de Expressão de Disposição Declarativa só está a usar funções e não é uma linguagem estruturada. Não há métodos ou declarações. As funções são, em vez disso, aninhadas para expressar o fluxo do programa.

Para mais detalhes, consulte [Welcome to the Visual Basic for Applications language reference for Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Os atributos são fortemente digitados. Uma função só aceita atributos do tipo correto. É também sensível aos casos. Ambos os nomes de funções e nomes de atributos devem ter invólucro adequado ou um erro é lançado.

## <a name="language-definitions-and-identifiers"></a>Definições linguísticas e identificadores
* As funções têm um nome seguido de argumentos nos parênteses: Nome de função (argumento 1, argumento N).
* Os atributos são identificados por parênteses quadrados: [nome de atributo]
* Os parâmetros são identificados por sinais percentuais: %ParameterName%
* As constantes de cordas estão rodeadas de citações: Por exemplo, "Contoso" (Nota: deve usar citações retas "" e não citações inteligentes "")
* Os valores numéricos são expressos sem citações e espera-se que sejam decimais. Os valores hexadecimais são pré-fixados com &H. Por exemplo, 98052, &HFF
* Os valores booleanos são expressos com constantes: Verdade, Falso.
* Constantes incorporadas e literais são expressos apenas com o seu nome: NULO, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funções
O fornecimento declarativo utiliza muitas funções para permitir a possibilidade de transformar valores de atributos. Estas funções podem ser aninhadas para que o resultado de uma função seja passado para outra função.

`Function1(Function2(Function3()))`

A lista completa de funções pode ser encontrada na referência da [função](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parâmetros
Um parâmetro é definido por um Conector ou por um administrador que utilize o PowerShell. Os parâmetros geralmente contêm valores diferentes de sistema para sistema, por exemplo o nome do domínio em que o utilizador está localizado. Estes parâmetros podem ser utilizados em fluxos de atributos.

O Conector de Diretório Ativo forneceu os seguintes parâmetros para as regras de sincronização de entrada:

| Nome do Parâmetro | Comentário |
| --- | --- |
| Domínio.Netbios |Formato netbios do domínio atualmente importado, por exemplo FABRIKAMSALES |
| Domínio.FQDN |Formato FQDN do domínio atualmente importado, por exemplo, sales.fabrikam.com |
| Domínio.LDAP |Formato LDAP do domínio atualmente importado, por exemplo DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios |Formato netbios do nome florestal atualmente importado, por exemplo FABRIKAMCORP |
| Forest.FQDN |Formato FQDN do nome florestal atualmente importado, por exemplo, fabrikam.com |
| Floresta.LDAP |Formato LDAP do nome florestal atualmente importado, por exemplo DC=fabrikam,DC=com |

O sistema fornece o seguinte parâmetro, que é utilizado para obter o identificador do Conector atualmente em funcionamento:  
`Connector.ID`

Aqui está um exemplo que povoa o domínio metaverso do atributo com o nome netbios do domínio onde o utilizador está localizado:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operadores
Podem ser utilizados os seguintes operadores:

* **Comparação**: <, <=, <>, =, >, >=
* **Matemática:**+, \*- -
* **Cadeia**: & (concatenato)
* **Lógica**: && (e), (ou)
* **Ordem de avaliação:**( )

Os operadores são avaliados da esquerda para a direita e têm a mesma prioridade de avaliação. Ou seja, \* o (multiplicador) não é avaliado antes - (subtração). 2\*(5+3) não é\*o mesmo que 2 5+3. Os parênteses () são utilizados para alterar a ordem de avaliação quando a ordem de avaliação da esquerda para a direita não é adequada.

## <a name="multi-valued-attributes"></a>Atributos de vários valores
As funções podem funcionar em atributos de valor único e multivalorizados. Para atributos de valor multivalorizado, a função funciona sobre cada valor e aplica a mesma função a cada valor.

Por exemplo:  
`Trim([proxyAddresses])`Faça uma guarnição de todos os valores do atributo proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Por cada valor @-signpor um, @contoso.comsubstitua o domínio por .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Procure o endereço SIP e retire-o dos valores.

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o modelo de configuração em [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Veja como o provisionamento declarativo é usado fora da caixa na [compreensão da configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md).
* Veja como fazer uma alteração prática usando o fornecimento declarativo em [Como fazer uma alteração à configuração padrão](how-to-connect-sync-change-the-configuration.md).

**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

**Tópicos de referência**

* [Sincronização azure AD Connect: Referência de funções](reference-connect-sync-functions-reference.md)

