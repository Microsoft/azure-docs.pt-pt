---
title: Senhas banidas dinamicamente-Azure Active Directory
description: Proíba senhas fracas do seu ambiente com senhas banidas dinamicamente do Azure AD
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
ms.openlocfilehash: fe2b4ed91969248bc0818f98306a108555eac424
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853058"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminar senhas inadequadas em sua organização

Líderes do setor informam que você não usa a mesma senha em vários lugares, para torná-lo complexo e para não torná-lo simples como "Password123". Como as organizações podem garantir que seus usuários estejam seguindo as diretrizes de práticas recomendadas? Como eles podem garantir que os usuários não estejam usando senhas fracas ou até mesmo variações de senhas fracas?

A etapa inicial de ter senhas mais fortes é fornecer orientação aos seus usuários. As diretrizes atuais da Microsoft sobre este tópico podem ser encontradas no seguinte link:

[Diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Ter uma boa orientação é importante, mas mesmo com isso, sabemos que muitos usuários ainda acabarão escolhendo senhas fracas. A proteção de senha do Azure AD protege sua organização detectando e bloqueando senhas fracas conhecidas e suas variantes, bem como, opcionalmente, bloqueando termos fracos adicionais que são específicos para sua organização.

Para obter mais informações sobre os esforços de segurança atuais, consulte o [relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Lista de senhas banidas globais

A equipe de Azure AD Identity Protection analisa constantemente os dados de telemetria de segurança do Azure AD procurando senhas fracas ou comprometidas comumente usadas, ou mais especificamente, os termos de base fracos que geralmente são usados como base para senhas fracas. Quando esses termos fracos são encontrados, eles são adicionados à lista de senhas globais banidas. O conteúdo da lista de senhas excluídas global não se baseia em nenhuma fonte de dados externa. A lista de senhas globais banidas baseia-se inteiramente nos resultados contínuos da telemetria e análise de segurança do Azure AD.

Sempre que uma nova senha for alterada ou redefinida para qualquer usuário em qualquer locatário no Azure AD, a versão atual da lista de senhas excluídas global será usada como a entrada de chave ao validar a força da senha. Essa validação resulta em senhas muito mais fortes para todos os clientes do Azure AD.

> [!NOTE]
> Os criminosos virtuais também usam estratégias semelhantes em seus ataques. Portanto, a Microsoft não publica o conteúdo dessa lista publicamente.

## <a name="custom-banned-password-list"></a>Lista personalizada de senhas banidas

Algumas organizações talvez queiram melhorar ainda mais a segurança adicionando suas próprias personalizações sobre a lista de senhas globais banidas, em que a Microsoft chama a lista personalizada de senhas banidas. A Microsoft recomenda que os termos adicionados a essa lista se concentram principalmente em termos específicos da organização, como:

- Nomes de marca
- Nomes de produtos
- Locais (por exemplo, como sede da empresa)
- Termos internos específicos da empresa
- Abreviações que têm significado específico da empresa.

Depois que os termos forem adicionados à lista de senhas excluídas personalizada, eles serão combinados com os termos na lista de senhas globais banidas ao validar senhas.

> [!NOTE]
> A lista de senhas excluídas personalizada é limitada a ter no máximo 1000 termos. Ele não foi projetado para bloquear listas de senhas extremamente grandes. Para aproveitar totalmente os benefícios da lista personalizada de senhas banidas, a Microsoft recomenda que você primeiro revise e entenda o algoritmo de avaliação de senha (consulte [como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated)) antes de adicionar novos termos à lista de proibidos personalizada. Entender como o algoritmo funciona permitirá que sua empresa detecte e bloqueie com eficiência grandes números de senhas fracas e suas variantes.

Por exemplo: Considere um cliente chamado "contoso", que se baseia em Londres e que torna um produto chamado "widget". Para esse cliente, seria um desperdício, bem como menos seguro, tentar bloquear variações específicas desses termos, como:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Funcionam
- "LondonHQ"
- ...etcetera

Em vez disso, é muito mais eficiente e seguro Bloquear apenas os termos básicos da chave:

- Funcionam
- Londres
- Widget

O algoritmo de validação de senha bloqueará automaticamente as variantes e as combinações fracas das anteriores.

A lista de senhas excluídas personalizada e a capacidade de habilitar a integração de Active Directory local é gerenciada usando o portal do Azure.

![Modificar a lista personalizada de senhas banidas em métodos de autenticação](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataques de irrigação de senha e listas de senhas comprometidas de terceiros

Um dos principais benefícios da proteção de senha do Azure AD é ajudá-lo a se defender contra ataques de irrigação de senha. A maioria dos ataques de irrigação de senha não tenta atacar uma determinada conta individual mais do que algumas vezes, pois esse comportamento aumenta muito a probabilidade de detecção, seja por meio de bloqueio de conta ou outros meios. A maioria dos ataques de irrigação de senha, portanto, depende do envio apenas de um pequeno número de senhas mais fracas conhecidas em relação a cada uma das contas de uma empresa. Essa técnica permite que o invasor pesquise rapidamente uma conta facilmente comprometida e, ao mesmo tempo, evite limites de detecção potenciais.

A proteção de senha do Azure AD é projetada para bloquear com eficiência todas as senhas fracas conhecidas que provavelmente serão usadas em ataques de irrigação de senha, com base em dados de telemetria de segurança do mundo real, como visto pelo Azure AD.  A Microsoft está ciente de sites de terceiros que enumeram milhões de senhas que foram comprometidas em violações de segurança publicamente conhecidas anteriores. É comum que produtos de validação de senha de terceiros sejam baseados em comparação de força bruta contra essas milhões de senhas. A Microsoft acredita que essas técnicas não são a melhor maneira de melhorar a intensidade geral da senha, considerando as estratégias típicas usadas pelos invasores de irrigação de senha.

> [!NOTE]
> A lista de senhas globais banidas da Microsoft não se baseia em nenhuma fonte de dados de terceiros, incluindo listas de senhas comprometidas.

Embora a lista global de proibidos da Microsoft seja pequena em comparação a algumas listas em massa de terceiros, seus efeitos de segurança são amplificados pelo fato de que ele é originado da telemetria de segurança do mundo real em ataques de irrigação de senha reais, além do fato de que a Microsoft o algoritmo de validação de senha usa técnicas de correspondência de fuzzing inteligente. O resultado final é que ele detectará com eficiência e bloqueará a utilização de milhões de senhas fracas mais comuns em sua empresa. Os clientes que optarem por adicionar termos específicos da organização à lista de senhas excluídas personalizada também se beneficiarão do mesmo algoritmo.

Informações adicionais sobre problemas de segurança baseados em senha podem ser examinadas em [seu PA $ $Word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos locais

A proteção de contas somente em nuvem é útil, mas muitas organizações mantêm cenários híbridos, incluindo o Windows Server Active Directory local. Os benefícios de segurança da proteção de senha do Azure AD também podem ser estendidos para o ambiente de Active Directory do Windows Server por meio da instalação de agentes locais. Agora, os usuários e administradores que alteram ou redefinem senhas no Active Directory precisam estar em conformidade com a mesma política de senha que os usuários somente de nuvem.

## <a name="how-are-passwords-evaluated"></a>Como as senhas são avaliadas

Sempre que um usuário altera ou redefine sua senha, a nova senha é verificada quanto à força e à complexidade, validando-a em relação à lista combinada de termos das listas de senhas excluídas globais e personalizadas (se o último estiver configurado).

Mesmo se a senha de um usuário contiver uma senha banida, a senha ainda poderá ser aceita se a senha geral for forte o suficiente de outra forma. Uma senha recentemente configurada passará pelas etapas a seguir para avaliar sua força geral para determinar se ela deve ser aceita ou rejeitada.

### <a name="step-1-normalization"></a>Passo 1: Normalização

Uma nova senha passa pela primeira vez por um processo de normalização. Essa técnica permite que um pequeno conjunto de senhas banidas seja mapeado para um conjunto muito maior de senhas potencialmente fracas.

A normalização tem duas partes.  Primeiro, todas as letras maiúsculas são alteradas para minúsculas.  Em segundo lugar, são executadas substituições de caracteres comuns, por exemplo:  

| Letra original  | Letra substituída |
| --- | --- |
| '0'  | minúscula |
| '1'  | debug |
| '$'  | do |
| "\@"  | um |

Exemplo: Suponha que a senha "blank" seja banida e um usuário tente alterar sua senha para "Bl@nK". Embora "Bl@nk" não seja especificamente banido, o processo de normalização Converte essa senha em "blank", que é uma senha banida.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passo 2: Verificar se a senha é considerada banida

#### <a name="fuzzy-matching-behavior"></a>Comportamento de correspondência difusa

A correspondência difusa é usada na senha normalizada para identificar se ela contém uma senha encontrada nas listas global ou de senhas banidas personalizadas. O processo de correspondência é baseado em uma distância de edição de uma (1) comparação.  

Exemplo: Suponha que a senha "abcdef" seja banida e um usuário tente alterar sua senha para uma das seguintes opções:

' abcdeg '    *(último caractere alterado de ' f ' para ' g ')* ' abcdefg '   *' (g ' acrescentado ao final)* ' abcde '     *(' f ' à direita foi excluído do final)*

Cada uma das senhas acima não corresponde especificamente à senha banida "abcdef". No entanto, como cada exemplo está dentro de uma distância de edição de 1 do termo banido ' abcdef ', eles são considerados como uma correspondência para "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Correspondência de subcadeia de caracteres (em termos específicos)

A correspondência de subcadeia de caracteres é usada na senha normalizada para verificar o nome e o sobrenome do usuário, bem como o nome do locatário (Observe que a correspondência do nome do locatário não é feita ao validar as senhas em um controlador de domínio Active Directory).

Exemplo: Suponha que tenhamos um User, pol, que deseja redefinir sua senha para "P0l123fb". Após a normalização, essa senha se tornaria "pol123fb". A correspondência de subcadeia de caracteres descobre que a senha contém o nome "pol" do usuário. Embora "P0l123fb" não tenha sido especificamente em uma lista de senhas banidas, a correspondência de subcadeia de caracteres encontrou "pol" na senha. Portanto, essa senha seria rejeitada.

#### <a name="score-calculation"></a>Cálculo de Pontuação

A próxima etapa é identificar todas as instâncias de senhas banidas na nova senha normalizada do usuário. Clique

1. Cada senha banida encontrada na senha de um usuário recebe um ponto.
2. Cada caractere exclusivo restante recebe um ponto.
3. Uma senha deve ter pelo menos cinco (5) pontos para ser aceita.

Para os próximos dois exemplos, vamos supor que a Contoso está usando a proteção de senha do Azure AD e tem "contoso" em sua lista personalizada. Vamos supor também que "blank" está na lista global.

Exemplo: um usuário altera sua senha para "C0ntos0Blank12"

Após a normalização, essa senha se torna "contosoblank12". O processo de correspondência descobre que essa senha contém duas senhas banidas: contoso e Blank. Em seguida, essa senha recebe uma Pontuação:

[contoso] + [blank] + [1] + [2] = 4 pontos já que essa senha está abaixo de cinco (5) pontos, ele será rejeitado.

Exemplo: um usuário altera sua senha para "ContoS0Bl@nkf9!".

Após a normalização, essa senha se torna "contosoblankf9!". O processo de correspondência descobre que essa senha contém duas senhas banidas: contoso e Blank. Em seguida, essa senha recebe uma Pontuação:

[contoso] + [blank] + [f] + [9] + [!] = 5 pontos, pois essa senha tem pelo menos cinco (5) pontos, é aceita.

   > [!IMPORTANT]
   > Observe que o algoritmo de senha banido, junto com a lista global, pode e faz alterações a qualquer momento no Azure com base na análise e pesquisa de segurança contínuas. Para o serviço de agente DC local, os algoritmos atualizados só terão efeito depois que o software do agente DC for reinstalado.

## <a name="license-requirements"></a>Requisitos de licença

|   | Proteção de senha do Azure AD com a lista de senhas banida global | Proteção de senha do Azure AD com a lista de senhas banidas personalizada|
| --- | --- | --- |
| Usuários somente de nuvem | Azure AD Gratuito | Azure AD Premium P1 ou P2 |
| Usuários sincronizados do Windows Server Active Directory local | Azure AD Premium P1 ou P2 | Azure AD Premium P1 ou P2 |

> [!NOTE]
> Os usuários locais do Windows Server Active Directory que não foram sincronizados com Azure Active Directory também têm os benefícios da proteção de senha do Azure AD com base no licenciamento existente para usuários sincronizados.

Informações adicionais de licenciamento, incluindo custos, podem ser encontradas no [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>O que os utilizadores veem

Quando um usuário tenta redefinir uma senha para algo que seria banido, ele vê a seguinte mensagem de erro:

Infelizmente, sua senha contém uma palavra, frase ou padrão que torna sua senha facilmente adivinhada. Tente novamente com uma senha diferente.

## <a name="next-steps"></a>Passos seguintes

- [Configurar a lista personalizada de senhas banidas](howto-password-ban-bad.md)
- [Habilitar agentes de proteção de senha do Azure AD no local](howto-password-ban-bad-on-premises-deploy.md)
