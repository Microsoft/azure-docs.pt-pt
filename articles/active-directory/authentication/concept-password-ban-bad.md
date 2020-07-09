---
title: Palavras-passe dinamicamente proibidas - Azure Ative Directory
description: Proibir palavras-passe fracas do seu ambiente com Azure AD dinamicamente proibidas palavras-passe
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
ms.openlocfilehash: 0f905b3eb6d1675f0bc252c3500169b3144287d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550704"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminate bad passwords in your organization (Eliminar palavras-passe más na sua organização)

Os líderes do setor dizem-lhe para não usar a mesma palavra-passe em vários lugares, para torná-la complexa, e para não torná-la simples como "Password123". Como podem as organizações garantir que os seus utilizadores estão a seguir orientações de boas práticas? Como podem garantir que os utilizadores não estão a usar senhas fracas ou mesmo variações em senhas fracas?

O primeiro passo para ter senhas mais fortes é fornecer orientação aos seus utilizadores. A orientação atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Orientação da palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Ter uma boa orientação é importante, mas mesmo com isso sabemos que muitos utilizadores ainda vão acabar por escolher senhas fracas. O Azure AD Password Protection protege a sua organização detetando e bloqueando senhas fracas conhecidas e suas variantes, bem como bloqueando opcionalmente termos fracos adicionais específicos da sua organização.

Para obter mais informações sobre os esforços de segurança atuais, consulte o Relatório de Inteligência de Segurança da [Microsoft.](https://www.microsoft.com/security/operations/security-intelligence-report)

## <a name="global-banned-password-list"></a>Lista global de senhas proibidas

A equipa de Proteção de Identidade Azure AD analisa constantemente os dados de telemetria de segurança Azure AD à procura de senhas fracas ou comprometidas comumente usadas, ou mais especificamente, os termos básicos fracos que muitas vezes são usados como base para senhas fracas. Quando tais termos fracos são encontrados, eles são adicionados à lista global de senhas proibidas. O conteúdo da lista global de palavras-passe proibida não se baseia em nenhuma fonte de dados externa. A lista global de palavras-passe proibidas baseia-se inteiramente nos resultados em curso da telemetria e análise de segurança Azure AD.

Sempre que uma nova palavra-passe é alterada ou reiniciada para qualquer utilizador em qualquer inquilino em Azure AD, a versão atual da lista global de senhas proibidas é usada como entrada chave ao validar a força da palavra-passe. Esta validação resulta em senhas muito mais fortes para todos os clientes AZURE AD.

> [!NOTE]
> Os criminosos virtuais também usam estratégias semelhantes nos seus ataques. Portanto, a Microsoft não publica publicamente o conteúdo desta lista.

## <a name="custom-banned-password-list"></a>Lista de senhas proibidas personalizadas

Algumas organizações podem querer melhorar ainda mais a segurança adicionando as suas próprias personalizações no topo da lista global de senhas proibidas naquilo a que a Microsoft chama a lista de palavras-passe proibidas por medida. A Microsoft recomenda que os termos adicionados a esta lista estejam principalmente focados em termos específicos da organização, tais como:

- Nomes de marcas
- Nomes dos produtos
- Localizações (por exemplo, como sede da empresa)
- Termos internos específicos da empresa
- Abreviaturas que têm significado específico da empresa.

Uma vez adicionados os termos à lista de senhas proibidas personalizadas, serão combinados com os termos da lista global de senhas proibidas ao validar palavras-passe.

> [!NOTE]
> A lista de senhas proibidas personalizadas limita-se a ter um máximo de 1000 termos. Não foi concebido para bloquear listas extremamente grandes de senhas. Para aproveitar plenamente os benefícios da lista de palavras-passe proibidas por medida, a Microsoft recomenda que analise primeiro e compreenda o algoritmo de avaliação de [palavras-passe (ver como são avaliadas as palavras-passe)](concept-password-ban-bad.md#how-are-passwords-evaluated)antes de adicionar novos termos à lista proibida personalizada. Compreender como o algoritmo funciona permitirá à sua empresa detetar e bloquear de forma eficiente um grande número de senhas fracas e suas variantes.

Por exemplo: considere um cliente chamado "Contoso", que tem sede em Londres, e que faz um produto chamado "Widget". Para tal cliente, seria um desperdício, bem como menos seguro, tentar bloquear variações específicas destes termos, tais como:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso.
- "LondonHQ"
- ... etcetera

Em vez disso, é muito mais eficiente e seguro bloquear apenas os termos-chave de base:

- "Contoso"
- "Londres"
- "Widget"

O algoritmo de validação de palavra-passe bloqueará automaticamente variantes e combinações fracas do acima.

A lista de palavras-passe proibidas personalizadas e a capacidade de permitir a integração do Ative Directory no local são geridas através do portal Azure.

![Modifique a lista de palavras-passe proibidas sob métodos de autenticação](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataques de spray de palavra-passe e listas de senhas comprometidas de terceiros

Um dos principais benefícios de proteção da palavra-passe AZure É ajudá-lo a defender-se contra ataques de spray de senha. A maioria dos ataques de spray de palavra-passe não tentam atacar qualquer conta individual mais do que algumas vezes, uma vez que tal comportamento aumenta consideravelmente a probabilidade de deteção, seja através do bloqueio de conta ou de outros meios. A maioria dos ataques de spray de palavra-passe depende, portanto, de enviar apenas um pequeno número das palavras-passe mais fracas conhecidas contra cada uma das contas de uma empresa. Esta técnica permite ao intruso procurar rapidamente uma conta facilmente comprometida, evitando ao mesmo tempo possíveis limiares de deteção.

A proteção de senha AD AD é projetada para bloquear eficientemente todas as palavras-passe fracas conhecidas que são suscetíveis de ser usadas em ataques de spray de palavra-passe, com base em dados de telemetria de segurança real como visto pelo Azure AD.  A Microsoft tem conhecimento de websites de terceiros que enumeram milhões de senhas que foram comprometidas em falhas de segurança anteriores conhecidas publicamente. É comum que os produtos de validação de senhas de terceiros se baseiem na comparação bruta-força com esses milhões de palavras-passe. A Microsoft considera que estas técnicas não são a melhor forma de melhorar a força geral da palavra-passe, dadas as estratégias típicas utilizadas pelos atacantes do spray de palavras-passe.

> [!NOTE]
> A lista global de palavras-passe proibida da Microsoft não se baseia em quaisquer fontes de dados de terceiros, incluindo listas de palavras-passe comprometidas.

Embora a lista global da Microsoft seja pequena em comparação com algumas listas de terceiros, os seus efeitos de segurança são amplificados pelo facto de ser proveniente de telemetria de segurança no mundo real em ataques de spray de senha real, além do facto de o algoritmo de validação de palavras-passe da Microsoft utilizar técnicas inteligentes de correspondência. O resultado final é que irá detetar e bloquear de forma eficiente milhões das palavras-passe fracas mais comuns de serem usadas na sua empresa. Os clientes que optam por adicionar termos específicos da organização à lista de senhas proibidas personalizadas também beneficiam do mesmo algoritmo.

Informações adicionais sobre problemas de segurança baseados em palavras-passe podem ser revistas no [Seu Pa$$word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos no local

Proteger contas apenas em nuvem é útil, mas muitas organizações mantêm cenários híbridos, incluindo no local, o Windows Server Ative Directory. Os benefícios de segurança da proteção de senha azure AD também podem ser estendidos ao ambiente do Windows Server Ative Directory através da instalação de agentes no local. Agora, os utilizadores e administradores que alterem ou reiniciem palavras-passe no Ative Directory são obrigados a cumprir a mesma política de palavra-passe que os utilizadores apenas na nuvem.

## <a name="how-are-passwords-evaluated"></a>Como são avaliadas as palavras-passe

Sempre que um utilizador altera ou reinicia a sua palavra-passe, a nova palavra-passe é verificada para obter força e complexidade, validando-a com a lista combinada de termos das listas de senhas proibidas global e personalizada (se esta for configurada).

Mesmo que a palavra-passe de um utilizador contenha uma palavra-passe proibida, a palavra-passe ainda pode ser aceite se a palavra-passe geral for suficientemente forte de outra forma. Uma palavra-passe recentemente configurada passará pelas seguintes etapas para avaliar a sua força global para determinar se deve ser aceite ou rejeitada.

### <a name="step-1-normalization"></a>Passo 1: Normalização

Uma nova senha passa primeiro por um processo de normalização. Esta técnica permite que um pequeno conjunto de senhas proibidas seja mapeado para um conjunto muito maior de senhas potencialmente fracas.

A normalização tem duas partes.  Primeiro, todas as letras maiúsculas são alteradas para minúsculas.  Em segundo lugar, são realizadas substituições comuns de caracteres, por exemplo:  

| Carta original  | Carta substituída |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'L' |
| '$'  | 's' |
| '\@'  | 'a' |

Exemplo: assuma que a palavra-passe "em branco" é proibida, e um utilizador tenta alterar a sua palavra-passe para Bl@nK " . Mesmo que " Bl@nk " não seja especificamente proibido, o processo de normalização converte esta palavra-passe em "branco", que é uma senha proibida.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passo 2: Verifique se a palavra-passe é considerada proibida

#### <a name="fuzzy-matching-behavior"></a>Comportamento de correspondência difusa

A correspondência fuzzy é usada na palavra-passe normalizada para identificar se contém uma palavra-passe encontrada nas listas de senhas proibidas a nível global ou personalizado. O processo de correspondência baseia-se numa distância de edição de uma (1) comparação.  

Exemplo: assuma que a palavra-passe "abcdef" é proibida e um utilizador tenta alterar a sua palavra-passe para uma das seguintes:

'abcdeg' *(último carácter alterado de 'f' para 'g')* 'abcdefg' *'(g' anexado ao fim)* 'abcde' *(trailing 'f' foi eliminado do fim)*

Cada uma das palavras-passe acima não corresponde especificamente à palavra-passe proibida "abcdef". No entanto, uma vez que cada exemplo está a uma distância de edição de 1 do termo proibido 'abcdef', todos eles são considerados como uma correspondência para "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Correspondência de sub-adstring (em termos específicos)

A correspondência de sub-cordas é utilizada na palavra-passe normalizada para verificar o primeiro e último nome do utilizador, bem como o nome do inquilino (note que a correspondência do nome do inquilino não é feita ao validar palavras-passe num controlador de domínio do Diretório Ativo).

Exemplo: assuma que temos um utilizador, Pol, que quer redefinir a sua palavra-passe para "P0l123fb". Após a normalização, esta palavra-passe tornar-se-ia "pol123fb". A correspondência de substring descobre que a palavra-passe contém o primeiro nome do utilizador "Pol". Apesar de "P0l123fb" não estar especificamente na lista de palavras-passe proibidas, a correspondência de substring encontrou "Pol" na palavra-passe. Portanto, esta palavra-passe seria rejeitada.

#### <a name="score-calculation"></a>Cálculo de pontuação

O próximo passo é identificar todas as instâncias de senhas proibidas na nova senha normalizada do utilizador. Em seguida:

1. Cada palavra-passe proibida que é encontrada na palavra-passe de um utilizador é dada um ponto.
2. Cada personagem único restante é dado um ponto.
3. Uma palavra-passe deve ser de pelo menos cinco (5) pontos para que seja aceite.

Para os próximos dois exemplos, vamos supor que o Contoso está a usar a Azure AD Password Protection e tem "contoso" na sua lista personalizada. Vamos também supor que "em branco" está na lista global.

Exemplo: um utilizador altera a sua palavra-passe para "C0ntos0Blank12"

Após a normalização, esta palavra-passe torna-se "contosoblank12". O processo de correspondência conclui que esta palavra-passe contém duas senhas proibidas: contoso e em branco. Esta palavra-passe é então dada uma pontuação:

[contoso] + [em branco] + [1] + [2] = 4 pontos Uma vez que esta palavra-passe está abaixo de cinco (5) pontos, será rejeitada.

Exemplo: um utilizador altera a sua palavra-passe para " ContoS0Bl@nkf9 !".

Após a normalização, esta palavra-passe torna-se "contosoblankf9!". O processo de correspondência conclui que esta palavra-passe contém duas senhas proibidas: contoso e em branco. Esta palavra-passe é então dada uma pontuação:

[contoso] + [em branco] + [f] + [9] + [!] = 5 pontos Uma vez que esta palavra-passe é de pelo menos cinco (5) pontos, é aceite.

   > [!IMPORTANT]
   > Por favor, note que o algoritmo de senha banido juntamente com a lista global pode e fazer mudanças a qualquer momento em Azure com base em análises e pesquisas de segurança em curso. Para o serviço de agente DC no local, os algoritmos atualizados só entrarão em vigor após a reinstalação do software do agente DC.

## <a name="license-requirements"></a>Requisitos de licença

| Utilizadores | Proteção de senha AZure AD com lista global de senhas proibidas | Proteção de senha AD AZure com lista de senhas proibidas personalizadas|
| --- | --- | --- |
| Utilizadores apenas em nuvem | Azure AD Gratuito | Azure AD Premium P1 ou P2 |
| Utilizadores sincronizados a partir de Windows Server Ative Directory | Azure AD Premium P1 ou P2 | Azure AD Premium P1 ou P2 |

> [!NOTE]
> No local, os utilizadores do Windows Server Ative Directory que não estejam sincronizados com o Azure Ative Directory também beneficiam da proteção de senha AD AZure com base no licenciamento existente para utilizadores sincronizados.

Informações adicionais de licenciamento, incluindo custos, podem ser encontradas no site de preços do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>O que os utilizadores veem

Quando um utilizador tenta redefinir uma palavra-passe para algo que seria proibido, vê a seguinte mensagem de erro:

Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que torna a sua palavra-passe facilmente adivinhável. Por favor, tente novamente com uma senha diferente.

## <a name="next-steps"></a>Próximos passos

- [Configurar a lista de senhas proibidas personalizadas](howto-password-ban-bad.md)
- [Ativar agentes de proteção de passwords AZure no local](howto-password-ban-bad-on-premises-deploy.md)
