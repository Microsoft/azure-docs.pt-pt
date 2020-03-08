---
title: Senhas dinamicamente proibidas - Diretório Ativo Azure
description: Proíba palavras-passe fracas do seu ambiente com palavras-passe do Azure AD dinamicamente proibidas
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377895"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Elimine as más palavras-passe na sua organização

Os líderes do setor dizem-lhe para não usar a mesma senha em vários lugares, para torná-la complexa, e para não torná-la simples como "Password123". Como podem as organizações garantir que os seus utilizadores estão a seguir orientações de boas práticas? Como podem certificar-se de que os utilizadores não estão a usar senhas fracas ou mesmo variações em palavras-passe fracas?

O passo inicial para ter senhas mais fortes é fornecer orientação aos seus utilizadores. A orientação atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Orientação da palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Ter uma boa orientação é importante, mas mesmo com isso sabemos que muitos utilizadores ainda vão acabar por escolher senhas fracas. A Proteção de Passwords Azure AD protege a sua organização detetando e bloqueando senhas fracas conhecidas e suas variantes, bem como bloqueando opcionalmente termos fracos adicionais específicos da sua organização.

Para obter mais informações sobre os esforços de segurança atuais, consulte o Relatório de Inteligência de Segurança da [Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Lista global de senhas proibidas

A equipa de Proteção de Identidade da Azure AD analisa constantemente os dados de telemetria de segurança da Azure AD à procura de senhas fracas ou comprometidas comumente usadas, ou mais especificamente, os fracos termos de base que muitas vezes são usados como base para senhas fracas. Quando estes termos fracos são encontrados, são adicionados à lista global de senhas proibidas. O conteúdo da lista global de senhas proibidas não se baseia em nenhuma fonte de dados externo. A lista global de senhas proibidas baseia-se inteiramente nos resultados em curso da telemetria e análise de segurança da AD Azure.

Sempre que uma nova palavra-passe é alterada ou reposta para qualquer utilizador em qualquer inquilino em Azure AD, a versão atual da lista global de passwords proibida é usada como a entrada chave ao validar a força da palavra-passe. Esta validação resulta em senhas muito mais fortes para todos os clientes da AD Azure.

> [!NOTE]
> Os cibercriminosos também usam estratégias semelhantes nos seus ataques. Por isso, a Microsoft não publica publica publica publicamente o conteúdo desta lista.

## <a name="custom-banned-password-list"></a>Lista de senha selada personalizada

Algumas organizações podem querer melhorar ainda mais a segurança adicionando as suas próprias personalizações para além da lista global de senhas proibidas no que a Microsoft chama de lista de senhas proibidas personalizadas. A Microsoft recomenda que os termos adicionados a esta lista estejam principalmente focados em termos específicos da organização, tais como:

- Nomes de marcas
- Nomes do produto
- Localizações (por exemplo, como sede da empresa)
- Termos internos específicos da empresa
- Abreviaturas que têm significado específico da empresa.

Uma vez adicionados termos à lista de senhas proibidas personalizadas, serão combinados com os termos na lista global de senhas proibidas ao validar palavras-passe.

> [!NOTE]
> A lista de senhas proibida sércida por costume limita-se a ter um máximo de 1000 termos. Não foi concebido para bloquear listas extremamente grandes de senhas. De forma a alavancar totalmente os benefícios da lista de senhas proibidas personalizadas, a Microsoft recomenda que reveja e compreenda primeiro o algoritmo de avaliação de passwords (ver [como são avaliadas as palavras-passe)](concept-password-ban-bad.md#how-are-passwords-evaluated)antes de adicionar novos termos à lista proibida personalizada. Compreender como funciona o algoritmo permitirá à sua empresa detetar e bloquear eficientemente um grande número de palavras-passe fracas e suas variantes.

Por exemplo: considere um cliente chamado "Contoso", que tem sede em Londres, e que faz um produto chamado "Widget". Para tal cliente, seria um desperdício e menos seguro tentar bloquear variações específicas destes termos tais como:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ...etcetera

Em vez disso, é muito mais eficiente e seguro bloquear apenas os termos-chave da base:

- "Contoso"
- "Londres"
- "Widget"

O algoritmo de validação de palavra-passe bloqueará automaticamente variantes e combinações fracas das acima referidas.

A lista de senhas proibida saldada personalizada e a capacidade de ativar a integração do Ative Directory no local é gerida através do portal Azure.

![Modificar a lista de senhas proibidas personalizadas ao abrigo dos Métodos de Autenticação](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataques de spray de senha e listas de senhas comprometidas de terceiros

Um dos principais benefícios de proteção de senhas Azure AD é ajudá-lo a defender-se contra ataques de spray de senha. A maioria dos ataques de spray de senha não tenta atacar qualquer conta individual mais do que algumas vezes, uma vez que tal comportamento aumenta consideravelmente a probabilidade de deteção, seja através do bloqueio de conta ou de outros meios. A maioria dos ataques de spray de senha depende, portanto, de submeter apenas um pequeno número das palavras-passe mais fracas conhecidas contra cada uma das contas de uma empresa. Esta técnica permite ao intruso procurar rapidamente uma conta facilmente comprometida, evitando ao mesmo tempo potenciais limiares de deteção.

A proteção de senhas Azure AD foi concebida para bloquear eficientemente todas as palavras-passe fracas conhecidas que são suscetíveis de serem usadas em ataques com spray de senha, com base em dados de telemetria de segurança no mundo real, como visto pela Azure AD.  A Microsoft está ciente de sites de terceiros que enumeram milhões de senhas que foram comprometidas em anteriores falhas de segurança conhecidas publicamente. É comum que os produtos de validação de palavras-passe de terceiros se baseiem numa comparação de força bruta com esses milhões de palavras-passe. A Microsoft considera que estas técnicas não são a melhor forma de melhorar a força geral da palavra-passe, dadas as estratégias típicas utilizadas pelos atacantes de spray de palavra-passe.

> [!NOTE]
> A lista global de passwords proibida sem acordo da Microsoft não se baseia em quaisquer fontes de dados de terceiros, incluindo listas de senhas comprometidas.

Embora a lista global proibida da Microsoft seja pequena em comparação com algumas listas a granel de terceiros, os seus efeitos de segurança são amplificados pelo facto de ser proveniente de telemetria de segurança real em ataques de spray de senha real, além do facto de a Microsoft ser obtida. o algoritmo de validação de palavra-passe utiliza técnicas inteligentes de correspondência de difusão. O resultado final é que irá detetar e bloquear eficientemente milhões das senhas fracas mais comuns de serem usadas na sua empresa. Os clientes que optam por adicionar termos específicos da organização à lista de senhas proibidas personalizadas também beneficiam do mesmo algoritmo.

Informações adicionais sobre questões de segurança baseadas em palavras-passe podem ser revistas no [Seu Pa$$word não importa.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos no local

Proteger contas apenas na nuvem é útil, mas muitas organizações mantêm cenários híbridos, incluindo no local o Windows Server Ative Directory. Os benefícios de segurança da proteção de passwords Azure AD também podem ser estendidos ao seu ambiente de Diretório Ativo do Windows Server através da instalação de agentes no local. Agora, os utilizadores e administradores que alteram ou reporem palavras-passe no Diretório Ativo são obrigados a cumprir a mesma política de palavra-passe que os utilizadores apenas na nuvem.

## <a name="how-are-passwords-evaluated"></a>Como são avaliadas as palavras-passe

Sempre que um utilizador muda ou repõe a sua palavra-passe, a nova palavra-passe é verificada para obter força e complexidade, validando-a contra a lista combinada de termos das listas de palavras-passe proibidas globais e personalizadas (se esta estiver configurada).

Mesmo que a palavra-passe de um utilizador contenha uma palavra-passe proibida, a palavra-passe pode ainda ser aceite se a palavra-passe geral for suficientemente forte de outra forma. Uma senha recentemente configurada passará pelos seguintes passos para avaliar a sua força global para determinar se deve ser aceite ou rejeitada.

### <a name="step-1-normalization"></a>Passo 1: Normalização

Uma nova senha passa primeiro por um processo de normalização. Esta técnica permite que um pequeno conjunto de senhas proibidas seja mapeada para um conjunto muito maior de senhas potencialmente fracas.

A normalização tem duas partes.  Primeiro, todas as letras maiúsculas são alteradas para minúsculas.  Em segundo lugar, são realizadas substituições comuns de caracteres, por exemplo:  

| Carta original  | Carta substituída |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| "\@"  | 'A' |

Exemplo: suponha que a palavra-passe "em branco" é proibida e que um utilizador tente alterar a sua palavra-passe para "Bl@nK". Embora "Bl@nk" não seja especificamente proibido, o processo de normalização converte esta palavra-passe em "blank", que é uma senha proibida.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passo 2: Verifique se a palavra-passe é considerada proibida

#### <a name="fuzzy-matching-behavior"></a>Comportamento de correspondência fuzzy

A correspondência fuzzy é usada na senha normalizada para identificar se contém uma senha encontrada nas listas de senhas proibidas no global ou nas listas de senhas proibidas personalizadas. O processo de correspondência baseia-se numa distância de edição de uma (1) comparação.  

Exemplo: assuma que a palavra-passe "abcdef" é proibida e que um utilizador tente alterar a sua palavra-passe para uma das seguintes palavras::

'abcdeg' *(última personagem passou de 'f' para 'g')* 'abcdefg' *'(g' anexado ao fim)* 'abcde' *(o trailing 'f' foi apagado do fim)*

Cada uma das palavras-passe acima não corresponde especificamente à senha proibida "abcdef". No entanto, uma vez que cada exemplo está a uma distância de edição de 1 do termo proibido 'abcdef', todos eles são considerados como um jogo com "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Correspondência de substring (em termos específicos)

A correspondência de substring é usada na palavra-passe normalizada para verificar o primeiro e último nome do utilizador, bem como o nome do inquilino (note que a correspondência do nome do inquilino não é feita quando valida palavras-passe num controlador de domínio ative directory).

Exemplo: assuma que temos um utilizador, Pol, que quer redefinir a sua palavra-passe para "P0l123fb". Após a normalização, esta senha tornar-se-ia "pol123fb". A correspondência de subcordas conclui que a palavra-passe contém o primeiro nome do utilizador "Pol". Apesar de "P0l123fb" não estar especificamente na lista de senhas proibida, a correspondência de subcordas encontrou "Pol" na palavra-passe. Portanto, esta palavra-passe seria rejeitada.

#### <a name="score-calculation"></a>Cálculo da pontuação

O próximo passo é identificar todos os casos de senhas proibidas na nova senha normalizada do utilizador. Em seguida:

1. Cada palavra-passe proibida que é encontrada na palavra-passe de um utilizador é dada um ponto.
2. Cada personagem único restante é dado um ponto.
3. Uma palavra-passe deve ser de pelo menos cinco (5) pontos para que seja aceite.

Para os próximos dois exemplos, vamos supor que Contoso está a usar a Proteção de Passwords Azure AD e tem "contoso" na sua lista personalizada. Vamos também assumir que "em branco" está na lista global.

Exemplo: um utilizador altera a sua palavra-passe para "C0ntos0Blank12"

Após a normalização, esta palavra-passe torna-se "contosoblank12". O processo de correspondência conclui que esta palavra-passe contém duas senhas proibidas: contoso e em branco. Esta palavra-passe é então dada uma pontuação:

[contoso] + [blank] + [1] + [2] = 4 pontos Uma vez que esta senha está abaixo de cinco (5) pontos, será rejeitada.

Exemplo: um utilizador muda a sua palavra-passe para "ContoS0Bl@nkf9!".

Após a normalização, esta palavra-passe torna-se "contosoblankf9!". O processo de correspondência conclui que esta palavra-passe contém duas senhas proibidas: contoso e em branco. Esta palavra-passe é então dada uma pontuação:

[contoso] + [em branco] + [f] + [9] + [!] = 5 pontos Uma vez que esta senha é de pelo menos cinco (5) pontos, é aceite.

   > [!IMPORTANT]
   > Por favor, note que o algoritmo de senha banido juntamente com a lista global pode e pode mudar a qualquer momento em Azure com base na análise e pesquisa de segurança em curso. Para o serviço de agente DC no local, os algoritmos atualizados só entrarão em vigor após a reinstalação do software do agente DC.

## <a name="license-requirements"></a>Requisitos de licença

|   | Proteção de senha da AD Azure com lista global de senhas proibidas | Proteção de senha da AD Azure com lista de senha proibida personalizada|
| --- | --- | --- |
| Utilizadores apenas em nuvem | Azure AD Gratuito | Azure AD Premium P1 ou P2 |
| Utilizadores sincronizados a partir do Diretório Ativo do Servidor do Windows no local | Azure AD Premium P1 ou P2 | Azure AD Premium P1 ou P2 |

> [!NOTE]
> No local, os utilizadores do Windows Server Ative Directory que não estejam sincronizados com o Diretório Ativo do Azure também beneficiam da proteção de passwords Azure AD com base no licenciamento existente para utilizadores sincronizados.

Informações adicionais de licenciamento, incluindo custos, podem ser encontradas no site de [preços do Diretório Ativo Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="what-do-users-see"></a>O que os utilizadores veem

Quando um utilizador tenta redefinir uma palavra-passe para algo que seria proibido, vê a seguinte mensagem de erro:

Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que torna a sua palavra-passe facilmente adiável. Por favor, tente de novo com uma senha diferente.

## <a name="next-steps"></a>Passos seguintes

- [Configure a lista de senhas proibidas personalizadas](howto-password-ban-bad.md)
- [Ativar agentes de proteção de senhas da AD Azure no local](howto-password-ban-bad-on-premises-deploy.md)
