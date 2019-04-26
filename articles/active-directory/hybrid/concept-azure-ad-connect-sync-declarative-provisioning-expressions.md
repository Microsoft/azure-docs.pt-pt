---
title: 'Azure AD Connect: Expressões de aprovisionamento declarativa | Documentos da Microsoft'
description: Explica as expressões do aprovisionamento declarativo.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/18/2017
ms.date: 11/08/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245510"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Sincronização do Azure AD Connect: Compreender as Expressões de Aprovisionamento Declarativas
Sincronização do Azure AD Connect se baseia no serviço de aprovisionamento declarativo introduzidos no Forefront Identity Manager 2010. Ele permite que implemente a lógica de negócio de integração de identidade completa sem a necessidade de escrever código compilado.

Uma parte essencial de aprovisionamento declarativo é a linguagem de expressão utilizada em fluxos de atributos. O idioma utilizado é um subconjunto do Microsoft® Visual Basic® for Applications (VBA). Este idioma é usado no Microsoft Office e os utilizadores com a experiência do VBScript também irão reconhecê-lo. A linguagem de expressão de aprovisionamento declarativa estiver a utilizar apenas as funções e não é uma linguagem estruturada. Não há métodos ou instruções. As funções em vez disso, estão aninhadas no fluxo do programa express.

Para obter mais detalhes, consulte [bem-vindo ao Visual Basic para referência de linguagem de aplicações do Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Os atributos tem rigidez de tipos. Uma função só aceita atributos do tipo correto. Também diferencia maiúsculas de minúsculas. Nomes de função e nomes de atributo tem de ter maiúsculas/minúsculas adequadas ou é gerado um erro.

## <a name="language-definitions-and-identifiers"></a>Definições de idioma e identificadores
- As funções têm um nome, seguido de argumentos entre parênteses Retos: FunctionName (argumento 1, o argumento N).
- Atributos são identificados por parênteses Retos: [attributeName]
- Parâmetros são identificados sinais de porcentagem: % ParameterName %
- Constantes de cadeia de caracteres são circundados por aspas: Por exemplo, "Contoso" (Nota: tem de utilizar retas aspas "" e não smart aspas "")
- Valores numéricos são expressados sem aspas e deve ser decimal. Valores hexadecimais têm o prefixo & H. Por exemplo, 98052 & HFF
- Valores booleanos são expressos com constantes: VERDADEIRO, FALSO.
- Constantes internas e literais são expressos com apenas o nome do utilizador: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funções
Aprovisionamento declarativo utiliza muitas funções para ativar a possibilidade de transformar valores de atributo. Estas funções podem ser aninhadas para que o resultado de uma função é passado para outra função.

`Function1(Function2(Function3()))`

A lista completa de funções pode ser encontrada no [referência de função](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parâmetros
Um parâmetro é definido por um conector ou por um administrador com o PowerShell. Parâmetros geralmente contém valores que são diferentes de sistema para sistema, por exemplo o nome do domínio, o utilizador está localizado em. Esses parâmetros podem ser utilizados em fluxos de atributos.

O conector do Active Directory fornecidos os seguintes parâmetros para regras de sincronização de entrada:

| Nome do Parâmetro | Comentário |
| --- | --- |
| Domain.Netbios |Formato de NetBIOS do domínio a ser importado, por exemplo FABRIKAMSALES |
| Domain.FQDN |Formato do FQDN do domínio a ser importado, por exemplo sales.fabrikam.com |
| Domain.LDAP |Formato LDAP do domínio a ser importado, por exemplo, DC = vendas, DC = fabrikam, DC = com |
| Forest.Netbios |Formato de NetBIOS, o nome de floresta a ser importado, por exemplo FABRIKAMCORP |
| Forest.FQDN |Formato do FQDN do nome floresta a ser importado, por exemplo, fabrikam.com |
| Forest.LDAP |Formato LDAP, o nome de floresta a ser importado, por exemplo, DC = fabrikam, DC = com |

O sistema fornece o seguinte parâmetro, o que é utilizado para obter o identificador do conector atualmente em execução:  
`Connector.ID`

Eis um exemplo que preenche o domínio de atributo do metaverso com o nome netbios do domínio onde o utilizador está localizado:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operadores
Podem ser utilizados os seguintes operadores:

- **Comparação**: <, < =, <>, =, >, > =
- **Matemática**: +, -, \*, -
- **Cadeia de caracteres**: & (concatenate)
- **Lógica**: & & (e), | | (ou)
- **Ordem de avaliação**:)

Os operadores são avaliados à esquerda para a direita e têm a mesma prioridade de avaliação. Ou seja, o \* (multiplicador) não é avaliado antes - (subtração). 2\*(5 + 3) não é igual a 2\*5 + 3. O (colchetes) são utilizados para alterar a ordem de avaliação quando esquerda para a ordem de avaliação correta não é apropriada.

## <a name="multi-valued-attributes"></a>Atributos com múltiplos valores
As funções podem operar nos atributos de valor único e com múltiplos valores. Para atributos com múltiplos valores, a função opera sobre cada valor e aplica-se a mesma função a cada valor.

Por exemplo:  
`Trim([proxyAddresses])` Fazer uma limitação de cada valor no atributo proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Para cada valor com um @-sign, substitua o domínio com @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Procure o endereço SIP e removê-lo de entre os valores.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o modelo de configuração [aprovisionamento declarativo da compreensão](concept-azure-ad-connect-sync-declarative-provisioning.md).
- Veja como declarativa de aprovisionamento é utilizado out-of-box na [entender a configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md).
- Veja como fazer uma alteração prática com o aprovisionamento declarativo [como fazer uma alteração à configuração padrão](how-to-connect-sync-change-the-configuration.md).

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
- [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

**Tópicos de referência**

- [Sincronização do Azure AD Connect: Referência das funções](reference-connect-sync-functions-reference.md)


