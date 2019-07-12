---
title: Banidas dinamicamente as palavras-passe - Azure Active Directory
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
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703068"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminar palavras-passe incorretas na sua organização

Líderes da indústria a informá-lo para não utilizar a mesma palavra-passe em vários locais, para que seja complexo e não torná-lo simples como "Password123". Como as organizações a garantir que seus usuários estão a seguir as práticas recomendadas? Como eles tornar-se de que os utilizadores não estiverem a utilizar o senhas fracas, ou até mesmo variações em senhas fracas?

A etapa inicial que as palavras-passe mais fortes é fornecer orientações para os seus utilizadores. Documentação de orientação atual da Microsoft sobre esse tópico pode ser encontrada na seguinte hiperligação:

[Orientações de palavra-passe do Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Ter boas diretrizes é importante, mas, mesmo com que Sabemos que muitos usuários ainda terminarão Escolher senhas fracas. Proteção de palavra-passe do AD do Azure protege a sua organização, detectar e bloquear palavras-passe fracas conhecidas e suas variantes, bem como bloquear opcionalmente termos fracos adicionais que são específicos para sua organização.

Para obter mais informações sobre os esforços de segurança atual, consulte a [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Lista de palavra-passe banidas global

A equipa do Azure AD Identity Protection analisa constantemente a telemetria de segurança do Azure AD dados à procura para usadas senhas fracas ou comprometidas ou, mais especificamente, o fraco base termos que, muitas vezes, são utilizados como base para senhas fracas. Quando esses termos de fracos são encontrados, eles são adicionados à lista de palavra-passe banidas global. O conteúdo da lista de palavra-passe banidas global não se baseiam em qualquer origem de dados externa. A lista de palavra-passe banidas global baseia-se totalmente nos resultados em curso da telemetria de segurança do Azure AD e análise.

Sempre que uma nova palavra-passe é alterada ou reposta para qualquer utilizador em qualquer inquilino no Azure AD, a versão atual da lista de palavra-passe banidas global é utilizada como a chave ao validar a força da senha de entrada. Esta validação resulta em palavras-passe muito mais segura para todos os clientes do Azure AD.

> [!NOTE]
> Os cibercriminosos também utilizam estratégias semelhantes em seus ataques. Portanto, o Microsoft não publica o conteúdo desta lista publicamente.

## <a name="custom-banned-password-list"></a>Lista de palavra-passe de banida personalizado

Algumas organizações poderão querer melhorar ainda mais a segurança ao adicionar suas próprias personalizações na parte superior da lista de palavra-passe banidas global em que a Microsoft chama a lista de palavras-passe banidas personalizado. A Microsoft recomenda que adicionada a esta lista de termos estão concentrados principalmente em termos de organizacionais específicos, tais como:

- Nomes
- Nomes de produtos
- Localizações (por exemplo, por exemplo, a sede da empresa)
- Termos de internos específico da empresa
- Abreviaturas que tenham específica da empresa que significa que.

Depois de termos são adicionados à lista de palavras-passe banidas personalizado, estes serão adicionados à lista de palavra-passe banidas global ao validar as palavras-passe.

> [!NOTE]
> A lista de palavras-passe banidas personalizado está limitada a ter um máximo de 1000 termos. Não foi concebida para listas muito grandes de palavras-passe de bloqueio. Para aproveitar totalmente os benefícios da lista de palavras-passe banidas personalizado, a Microsoft recomenda que primeiro analisar e compreender o algoritmo de avaliação de palavra-passe (consulte [como a palavras-passe são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated)) antes de adicionar novos termos para o lista de banidas personalizada. Compreender como funciona o algoritmo irá ativar a sua empresa com eficiência, detetar e bloquear um grande número de palavras-passe fracas e suas variantes.

Por exemplo: considere um cliente com o nome "Contoso", que se baseia em Londres e que faz com que um produto com o nome "Widget". Para um cliente, seria inútil, bem como menos segura para tentar bloquear variações específicas sobre estes termos, tais como:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ...etcetera

Em vez disso, é muito mais eficiente e segura para bloquear apenas os termos de bases principais:

- "Contoso"
- "Londres"
- "Widget"

O algoritmo de validação da palavra-passe, em seguida, irá bloquear automaticamente variantes fracos e combinações de acima.

O personalizado banido a lista de palavra-passe e a capacidade de ativar no local do Active Directory, integração é gerida no portal do Azure.

![Modificar a lista de palavras-passe banidas personalizadas em métodos de autenticação](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Palavra-passe spray ataques e listas de palavra-passe comprometida de terceiros

A chave de um benefício de proteção de palavra-passe do Azure AD é que o ajudarão a se Defender contra ataques de spray de palavra-passe. A maioria dos ataques de spray de palavra-passe não tente atacar qualquer determinada conta individual mais do que algumas vezes, uma vez que esse comportamento aumenta significativamente a probabilidade de deteção de, por meio de bloqueio de conta ou outros meios. A maioria dos ataques de spray de palavra-passe, por conseguinte, baseiam-se sobre como enviar apenas um pequeno número das palavras-passe mais fraco conhecidos em relação a cada uma das contas numa empresa. Essa técnica permite que o invasor pesquisa rápida para uma conta podem ser facilmente comprometida e, ao mesmo tempo evitar potenciais limiares de deteção.

Proteção de palavra-passe do Azure AD foi concebida de forma eficiente bloquear todas as senhas fracas conhecidas que podem ser utilizadas em ataques de spray de palavra-passe, com base nos dados de telemetria de segurança do mundo real, como visto pelo Azure AD.  A Microsoft está ciente dos Web sites de terceiros que enumeram milhões de palavras-passe que foram comprometidas em falhas de segurança publicamente conhecidos anteriores. É comum para produtos de validação da palavra-passe de terceiros ser com base na comparação de força bruta contra esses milhões de palavras-passe. Microsoft é que essas técnicas não são a melhor forma de melhorar a força da senha geral tendo em conta as estratégias típicas utilizadas pelos atacantes spray de palavra-passe.

> [!NOTE]
> A lista de palavra-passe banidas global não é da Microsoft com base em quaisquer dados de terceiros de nenhuma forma origens, incluindo listas de palavra-passe comprometida.

Embora a lista de banidas global da Microsoft é pequena em comparação com algumas listas em massa de terceiros, seus efeitos de segurança são Sim os amplificou pelo fato de que ele é obtido a partir da telemetria de segurança do mundo real sobre a palavra-passe real spray ataques, além do fato de que a Microsoft algoritmo de validação da palavra-passe utiliza técnicas de correspondência difusa inteligentes. O resultado final é que com eficiência irá detetar e bloquear milhões das palavras-passe fracas mais comuns do que está a ser usado em sua empresa. Os clientes que optar por adicionar termos específicos da organização para a lista de palavras-passe banidas personalizado também se beneficiam do mesmo algoritmo.

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos no local

Proteger contas apenas na cloud é útil, mas muitas organizações mantêm cenários híbridos, incluindo no local Windows Server Active Directory. É possível instalar a proteção de palavra-passe do Azure AD para o Windows Server Active Directory agentes no local expandir as listas de palavra-passe banidas à sua infraestrutura existente. Agora, os utilizadores e administradores que alterar, definir ou repor palavras-passe no local é necessário para estar em conformidade com a mesma política de palavra-passe como os utilizadores apenas na cloud.

## <a name="how-are-passwords-evaluated"></a>Como são avaliadas as palavras-passe

Sempre que um utilizador for alterado ou repõe a palavra-passe, a nova palavra-passe é analisado relativamente à força e a complexidade, validando-as em relação a global e a lista de palavras-passe banidas personalizadas (se a última opção é configurada).

Mesmo que a palavra-passe de um utilizador contém uma palavra-passe banida, a palavra-passe ainda poderá aceite se a palavra-passe geral for forte o suficiente caso contrário. Uma palavra-passe recentemente configurada irá percorrer os passos seguintes para avaliar a força geral para determinar se deve ser aceites ou rejeitado.

### <a name="step-1-normalization"></a>Passo 1: Normalização

Uma nova palavra-passe primeiro passa por um processo de normalização. Essa técnica permite para um pequeno conjunto de palavras-passe banidas ser mapeada para um conjunto muito maior de palavras-passe potencialmente fracos.

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

Cada uma das palavras-passe acima não especificamente coincide com a palavra-passe banida "abcdef". No entanto, uma vez que cada exemplo é dentro de uma distância de edição de 1 do termo "abcdef" banido, eles são todos considerados uma correspondência para "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Subcadeia correspondente (em termos específicos)

Correspondência de subcadeia é utilizado na palavra-passe normalizada para verificar para o usuário primeiro e último nome, bem como o nome do inquilino (Observe que a correspondência de nomes de inquilino não é feita ao validar as palavras-passe no controlador de domínio do Active Directory).

Exemplo: partem do princípio de que existe um utilizador, Pol, o que quer repor a palavra-passe para "P0l123fb". Depois de normalização, esta palavra-passe poderia se tornar "pol123fb". Correspondência de subcadeia localiza a que a palavra-passe contém o nome do usuário "Pol". Embora "P0l123fb" não tenha sido especificamente de qualquer lista de palavra-passe banidas, correspondência de subcadeia encontrada "Pol" a palavra-passe. Por conseguinte, esta palavra-passe seria rejeitada.

#### <a name="score-calculation"></a>Cálculo de pontuação

A próxima etapa é identificar todas as instâncias de palavras-passe banidas do usuário normalizado nova palavra-passe. Em seguida:

1. Cada palavra-passe banida que se encontra na palavra-passe de um utilizador recebe um ponto.
2. Cada caractere exclusivo restante é atribuído um ponto.
3. Uma palavra-passe tem de ser, pelo menos, cinco (5) pontos para que ele aceite.

Para os próximos dois exemplos, vamos supor que a Contoso é utilizar a proteção de palavra-passe do Azure AD e tem "contoso" na respetiva lista personalizada. Vamos também assumir que "em branco" estiver na lista global.

Exemplo: um utilizador altera a palavra-passe para "C0ntos0Blank12"

Depois de normalização, esta palavra-passe, torna-se "contosoblank12". O processo de correspondência localiza a que esta palavra-passe contém duas palavras-passe banidas: contoso e em branco. Esta palavra-passe, em seguida, recebe uma pontuação:

[contoso] + [em branco] + [1] + [2] = 4 pontos, uma vez que esta palavra-passe é pontos em cinco (5), ele será rejeitado.

Exemplo: um utilizador altera a palavra-passe para "ContoS0Bl@nkf9!".

Depois de normalização, esta palavra-passe, torna-se "contosoblankf9!". O processo de correspondência localiza a que esta palavra-passe contém duas palavras-passe banidas: contoso e em branco. Esta palavra-passe, em seguida, recebe uma pontuação:

[contoso] + [em branco] + [c] + [9] + [!] = 5 pontos, uma vez que esta palavra-passe é pontos de, pelo menos, cinco (5), é aceite.

   > [!IMPORTANT]
   > Tenha em atenção que o algoritmo de palavra-passe banidas, juntamente com a lista global pode e alterar em qualquer altura no Azure com base na análise de segurança em curso e pesquisa. Para o serviço de agente do controlador de domínio no local, algoritmos atualizados só terão efeito depois do software do agente DC é instalado novamente.

## <a name="license-requirements"></a>Requisitos de licença

|   | Proteção de palavra-passe do Azure AD com a lista de palavra-passe banidas global | Proteção de palavra-passe do Azure AD com a lista de palavras-passe banidas personalizado|
| --- | --- | --- |
| Utilizadores apenas na cloud | Azure AD Gratuito | O Azure AD Premium P1 ou P2 |
| Os utilizadores sincronizados no local Windows Server Active Directory | O Azure AD Premium P1 ou P2 | O Azure AD Premium P1 ou P2 |

> [!NOTE]
> Utilizadores do Windows Server Active Directory no local que não sincronizado ao Azure Active Directory também aproveitar os benefícios da proteção de palavra-passe do Azure AD com base no licenciamento existentes para os utilizadores sincronizados.

Informações de licenciamento adicionais, incluindo os custos, podem ser encontradas no [do Azure Active Directory preços site](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>O que os utilizadores veem

Quando um utilizador tenta repor uma palavra-passe para algo que deve ser excluído, verão a seguinte mensagem de erro:

Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que faz com que a palavra-passe guessable facilmente. Tente novamente com uma palavra-passe diferente.

## <a name="next-steps"></a>Passos Seguintes

- [Configurar a lista de palavras-passe banidas personalizado](howto-password-ban-bad.md)
- [Ativar o Azure AD palavra-passe proteção agentes no local](howto-password-ban-bad-on-premises-deploy.md)
