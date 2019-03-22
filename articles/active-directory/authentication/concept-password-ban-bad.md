---
title: Palavras-passe banidas dinamicamente no Azure AD
description: Banir senhas fracas do seu ambiente com palavras-passe do Azure AD banida dinamicamente
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7dcac665dadef7f3f192e7f0e359b6b7c244bde
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287031"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminar palavras-passe incorretas na sua organização

Líderes da indústria a informá-lo para não utilizar a mesma palavra-passe em vários locais, para que seja complexo e não torná-lo simples como Password123. Como as organizações a garantir que seus usuários estão seguindo orientação? Como eles tornar-se de que os utilizadores não estiverem a utilizar as palavras-passe ou palavras-passe que são conhecidas a serem incluídos na recente violações de dados comuns?

## <a name="global-banned-password-list"></a>Lista de palavra-passe banidas global

A Microsoft está sempre a trabalhar para estar um passo à frente dos cibercriminosos. Portanto a equipa do Azure AD Identity Protection continuamente procure palavras-passe comprometidas e comumente utilizadas. Eles bloqueiam, em seguida, essas palavras-passe que são consideradas muito comuns em que é chamado a lista de palavra-passe banidas global. Os cibercriminosos também utilizam estratégias semelhantes em seus ataques, portanto Microsoft não publica o conteúdo desta lista publicamente. Estas palavras-passe vulneráveis é bloqueados antes de se tornarem uma ameaça real aos clientes da Microsoft. Para obter mais informações sobre os esforços de segurança atual, consulte a [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="custom-banned-password-list"></a>Lista de palavras-passe banidas personalizada

Algumas organizações poderão querer tirar ainda mais a etapa de segurança um adicionando suas próprias personalizações na parte superior da lista de palavra-passe banidas global em que a Microsoft chama a lista de palavras-passe banidas personalizado. Clientes empresariais, como a Contoso, em seguida, pode optar por bloquear variantes de seus nomes de marca, termos específicos da empresa ou outros itens.

O personalizado banido a lista de palavra-passe e a capacidade de ativar no local do Active Directory, integração é gerida no portal do Azure.

![Modificar a lista de palavras-passe banidas personalizadas em métodos de autenticação no portal do Azure](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos no local

Proteger contas apenas na cloud é útil, mas muitas organizações mantêm cenários híbridos, incluindo no local Windows Server Active Directory. É possível instalar a proteção de palavra-passe do Azure AD para o Windows Server Active Directory agentes no local expandir as listas de palavra-passe banidas à sua infraestrutura existente. Agora, os utilizadores e administradores que alterar, definir ou repor palavras-passe no local é necessário para estar em conformidade com a mesma política de palavra-passe como os utilizadores apenas na cloud.

## <a name="how-are-passwords-evaluated"></a>Como são avaliadas as palavras-passe

Sempre que um utilizador for alterado ou repõe a palavra-passe, a nova palavra-passe é analisado relativamente à força e a complexidade, validando-as em relação a global e a lista de palavras-passe banidas personalizadas (se a última opção é configurada).

Mesmo que a palavra-passe de um utilizador contém uma palavra-passe banida, a palavra-passe ainda poderá aceite se a palavra-passe geral for forte o suficiente caso contrário. Uma palavra-passe recentemente configurada irá percorrer os passos seguintes para avaliar a força geral para determinar se deve ser aceites ou rejeitado.

### <a name="step-1-normalization"></a>Passo 1: Normalização

Uma nova palavra-passe primeiro passa por um processo de normalização. Isso possibilita um pequeno conjunto de palavras-passe banidas ser mapeada para um conjunto muito maior de palavras-passe potencialmente fracos.

Normalização tem duas partes.  Primeiros, todas as letras maiúsculas letras são alteradas em minúsculas.  Substituições de caractere segundo, comuns são realizadas, por exemplo:  

| Letra original  | Letra serão substituída |
| --- | --- |
| '0'  | 'o' |
| '1'  | "l" |
| '$'  | 's' |
| "\@"  | 'a' |

Exemplo: partem do princípio de que a palavra-passe "em branco" é excluída, e um usuário tentar altere a palavra-passe para "Bl@nK". Embora "Bl@nk" é não especificamente banida, o processo de normalização converte esta palavra-passe para "em branco", que é uma palavra-passe banida.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passo 2: Verifique se a palavra-passe é considerado banida

#### <a name="fuzzy-matching-behavior"></a>Comportamento de correspondência difusa

Correspondência difusa é utilizado na palavra-passe normalizada para identificar se contém uma palavra-passe encontrada em qualquer um dos global ou o personalizado banida listas de palavra-passe. O processo de correspondência baseia-se uma distância de edição de comparação um (1).  

Exemplo: partem do princípio de que a palavra-passe "abcdef" é excluída, e um usuário tentar altere a palavra-passe para um dos seguintes procedimentos:

'abcdeg'    *(últimos carateres mudou de "f" para "g")* "abcdefg"   *' (g "acrescentada ao final)* "abcde"     *(à direita"f"foi eliminado do fim)*

Cada uma das palavras-passe acima não especificamente coincide com a palavra-passe banida "abcdef". No entanto, uma vez que cada exemplo é dentro de uma distância de edição de 1 do "abcdef" token banida, eles são todos considerados uma correspondência para "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Subcadeia correspondente (em termos específicos)

Correspondência de subcadeia é utilizado na palavra-passe normalizada para verificar para o usuário primeiro e último nome, bem como o nome do inquilino (Observe que a correspondência de nomes de inquilino não é feita ao validar as palavras-passe no controlador de domínio do Active Directory).

Exemplo: partem do princípio de que existe um utilizador João Silva que quer repor a palavra-passe para "J0hn123fb". Depois de normalização, esta palavra-passe poderia se tornar "john123fb". Correspondência de subcadeia localiza a que a palavra-passe contém o nome do utilizador "João". Embora "J0hn123fb" não tenha sido especificamente de qualquer lista de palavra-passe banidas, correspondência de subcadeia encontrada "João" a palavra-passe. Por conseguinte, esta palavra-passe seria rejeitada.

#### <a name="score-calculation"></a>Cálculo de pontuação

A próxima etapa é identificar todas as instâncias de palavras-passe banidas do usuário normalizado nova palavra-passe. Em seguida:

1. Cada palavra-passe banida que se encontra na palavra-passe de um utilizador recebe um ponto.
2. Cada caractere exclusivo restante é atribuído um ponto.
3. Uma palavra-passe tem de ser, pelo menos, 5 pontos, para que ele aceite.

Para os próximos dois exemplos, vamos supor que a Contoso é utilizar a proteção de palavra-passe do Azure AD e tem "contoso" na respetiva lista personalizada. Vamos também assumir que "em branco" estiver na lista global.

Exemplo: um utilizador altera a palavra-passe para "C0ntos0Blank12"

Depois de normalização, esta palavra-passe, torna-se "contosoblank12". O processo de correspondência localiza a que esta palavra-passe contém duas palavras-passe banidas: contoso e em branco. Esta palavra-passe, em seguida, recebe uma pontuação:

[contoso] + [em branco] + [1] + [2] = 4 pontos, uma vez que esta palavra-passe é em 5 pontos, será rejeitada.

Exemplo: um utilizador altera a palavra-passe para "ContoS0Bl@nkf9!".

Depois de normalização, esta palavra-passe, torna-se "contosoblankf9!". O processo de correspondência localiza a que esta palavra-passe contém duas palavras-passe banidas: contoso e em branco. Esta palavra-passe, em seguida, recebe uma pontuação:

[contoso] + [em branco] + [c] + [9] + [!] = 5 pontos, uma vez que esta palavra-passe é, pelo menos, 5 pontos, é aceite.

   > [!IMPORTANT]
   > Tenha em atenção que o algoritmo de palavra-passe banidas, juntamente com a lista global pode e alterar em qualquer altura no Azure com base na análise de segurança em curso e pesquisa. Para o serviço de agente do controlador de domínio no local, algoritmos atualizados só terão efeito depois do software do agente DC é instalado novamente.

## <a name="license-requirements"></a>Requisitos de licença

|   | Proteção de palavra-passe do Azure AD com a lista de palavra-passe banidas global | Proteção de palavra-passe do Azure AD com a lista de palavras-passe banidas personalizado|
| --- | --- | --- |
| Utilizadores apenas na cloud | Azure AD Gratuito | Azure AD Básico |
| Os utilizadores sincronizados no local Windows Server Active Directory | O Azure AD Premium P1 ou P2 | O Azure AD Premium P1 ou P2 |

Informações de licenciamento adicionais, incluindo os custos, podem ser encontradas no [do Azure Active Directory preços site](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>O que os utilizadores veem

Quando um utilizador tenta repor uma palavra-passe para algo que deve ser excluído, verão a seguinte mensagem de erro:

Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que faz com que a palavra-passe guessable facilmente. Tente novamente com uma palavra-passe diferente.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar a lista de palavras-passe banidas personalizado](howto-password-ban-bad.md)
* [Ativar o Azure AD palavra-passe proteção agentes no local](howto-password-ban-bad-on-premises-deploy.md)
