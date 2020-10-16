---
title: 'Azure AD Connect: Declarative Provisioning Expressions / Microsoft Docs'
description: Explica as expressões declarativas.
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
ms.openlocfilehash: 02490839a9e35695ae2e8b3f750e139ad7413aa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89280218"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect sync: Understanding Declarative Provisioning Expressions
A azure AD Connect baseia-se no provisionamento declarativo introduzido pela primeira vez no Forefront Identity Manager 2010. Permite-lhe implementar a sua lógica de negócio de integração de identidade completa sem a necessidade de escrever código compilado.

Uma parte essencial do provisionamento declarativo é a linguagem de expressão utilizada nos fluxos de atributos. O idioma utilizado é um subconjunto de ® Visual Basic ® Microsoft para aplicações (VBA). Este idioma é utilizado no Microsoft Office e os utilizadores com experiência de VBScript também o reconhecerão. A Linguagem de Expressão Declarativa está apenas a usar funções e não é uma língua estruturada. Não há métodos nem declarações. As funções são, em vez disso, aninhadas para expressar o fluxo do programa.

Para mais detalhes, consulte [Welcome to the Visual Basic for Applications language reference for Office 2013](/office/vba/api/overview/language-reference).

Os atributos são fortemente dactilografados. Uma função só aceita atributos do tipo correto. É também sensível a casos. Ambos os nomes de funções e nomes de atributos devem ter invólucro adequado ou um erro é lançado.

## <a name="language-definitions-and-identifiers"></a>Definições linguísticas e identificadores
* As funções têm um nome seguido de argumentos nos parênteses: FunctionName (argumento 1, argumento N).
* Os atributos são identificados por parênteses quadrados: [atributoName]
* Os parâmetros são identificados por sinais por cento: %ParâmetroName%
* As constantes de cordas estão rodeadas de citações: Por exemplo, "Contoso" (Nota: deve usar citações retas "" e não citações inteligentes "")
* Os valores numéricos são expressos sem cotações e espera-se que sejam decimais. Os valores hexadecimais são prefixados com &H. Por exemplo, 98052, &HFF
* Os valores booleanos são expressos com constantes: Verdadeiro, Falso.
* Constantes e literais incorporados são expressos apenas com o seu nome: NULO, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funções
O provisionamento declarativo utiliza muitas funções para permitir a possibilidade de transformar valores de atributos. Estas funções podem ser aninhadas de modo que o resultado de uma função é passado para outra função.

`Function1(Function2(Function3()))`

A lista completa de funções pode ser encontrada na referência de [funções](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parâmetros
Um parâmetro é definido por um Conector ou por um administrador que utiliza o PowerShell. Os parâmetros geralmente contêm valores que são diferentes do sistema para o sistema, por exemplo o nome do domínio em que o utilizador está localizado. Estes parâmetros podem ser usados nos fluxos de atributos.

O Conector de Diretório Ativo forneceu os seguintes parâmetros para as regras de sincronização de entrada:

| Nome do Parâmetro | Comentário |
| --- | --- |
| Domain.Netbios |Formato Netbios do domínio atualmente importado, por exemplo FABRIKAMSALES |
| Domain.FQDN |O formato FQDN do domínio atualmente importado, por exemplo, sales.fabrikam.com |
| Domain.LDAP |Formato LDAP do domínio atualmente importado, por exemplo DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios |Formato netbios do nome da floresta atualmente importado, por exemplo FABRIKAMCORP |
| Floresta.FQDN |O formato FQDN do nome da floresta atualmente em mente importada, por exemplo, fabrikam.com |
| Forest.LDAP |Formato LDAP do nome da floresta atualmente importado, por exemplo DC=fabrikam,DC=com |

O sistema fornece o seguinte parâmetro, que é utilizado para obter o identificador do Conector atualmente em funcionamento:  
`Connector.ID`

Aqui está um exemplo que povoa o domínio do atributo metaverso com o nome netbios do domínio onde o utilizador está localizado:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operadores
Podem ser utilizados os seguintes operadores:

* **Comparação:**<, <= <>, =, >, >=
* **Matemática:**+, - \* -
* **Corda**: & (concatenato)
* **Lógico**: &&  e, [ / (ou)
* **Ordem de avaliação**: ( )

Os operadores são avaliados da esquerda para a direita e têm a mesma prioridade de avaliação. Ou seja, o \* (multiplicador) não é avaliado antes - (subtração). 2 \* (5+3) não é o mesmo que 2 \* 5+3. Os suportes ( ) são utilizados para alterar a ordem de avaliação quando a ordem de avaliação da esquerda para a direita não é apropriada.

## <a name="multi-valued-attributes"></a>Atributos multi-valorizados
As funções podem funcionar em atributos de valor único e multi-valor. Para atributos multi-valorizados, a função opera sobre cada valor e aplica a mesma função a cada valor.

Por exemplo:  
`Trim([proxyAddresses])` Faça uma guarnição de todos os valores no atributo ProxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Por cada valor por um @-sign , substitua o domínio por @contoso.com .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Procure o endereço SIP e retire-o dos valores.

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o modelo de configuração na [Compreensão declarativa.](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Veja como é utilizada a provisão declarativa fora da caixa na [compreensão da configuração padrão](concept-azure-ad-connect-sync-default-configuration.md).
* Veja como fazer uma alteração prática utilizando provisionamento declarativo em [Como fazer uma alteração na configuração padrão](how-to-connect-sync-change-the-configuration.md).

**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

**Tópicos de referência**

* [Azure AD Connect Sync: Funções Referência](reference-connect-sync-functions-reference.md)