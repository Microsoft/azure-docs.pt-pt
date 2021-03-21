---
title: Proteção de palavras-passe no Diretório Ativo Azure
description: Saiba como proibir dinamicamente palavras-passe fracas do seu ambiente com a Azure Ative Directory Password Protection
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 740201b84323f5a01ab01bb21ec458a19832e499
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652025"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Eliminar palavras-passe más usando a Azure Ative Directory Password Protection

Muitas orientações de segurança recomendam que não use a mesma palavra-passe em vários locais, para torná-la complexa, e para evitar palavras-passe simples como *a Password123*. Pode fornecer aos seus utilizadores [orientações sobre como escolher palavras-passe,](https://www.microsoft.com/research/publication/password-guidance)mas muitas vezes ainda são utilizadas senhas fracas ou inseguras. A Azure AD Password Protection deteta e bloqueia senhas fracas conhecidas e suas variantes, e também pode bloquear termos fracos adicionais que são específicos da sua organização.

Com a Azure AD Password Protection, as listas de senhas proibidas globais são automaticamente aplicadas a todos os utilizadores de um inquilino AD Azure. Para suportar as suas necessidades de negócio e segurança, pode definir entradas numa lista de senhas proibidas sob medida. Quando os utilizadores alteram ou reiniciam as suas palavras-passe, estas listas de senhas proibidas são verificadas para impor a utilização de senhas fortes.

Deve utilizar funcionalidades adicionais como [autenticação multi-factor Azure AD](concept-mfa-howitworks.md), e não apenas confiar em palavras-passe fortes aplicadas pela Azure AD Password Protection. Para obter mais informações sobre a utilização de várias camadas de segurança para os seus eventos de entrada, consulte [o seu Pa$$word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> Este artigo conceptual explica a um administrador como funciona a Proteção de PasswordS Azure AD. Se é um utilizador final já registado para redefinição da palavra-passe de autosserviço e precisa de voltar à sua conta, vá a [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Se a sua equipa de TI não tiver ativado a capacidade de redefinir a sua própria palavra-passe, contacte o seu helpdesk para obter assistência adicional.

## <a name="global-banned-password-list"></a>Lista global de senhas proibidas

A equipa de Proteção de Identidade Azure AD analisa constantemente os dados de telemetria de segurança Azure AD à procura de senhas fracas ou comprometidas comumente usadas. Especificamente, a análise procura termos base que muitas vezes são usados como base para senhas fracas. Quando os termos fracos são encontrados, são adicionados à *lista global de senhas proibidas.* O conteúdo da lista global de palavras-passe proibida não se baseia em nenhuma fonte de dados externo, mas sim nos resultados da telemetria e análise de segurança Azure AD.

Quando uma palavra-passe é alterada ou reiniciada para qualquer utilizador num inquilino AZure AD, a versão atual da lista global de senhas proibidas é usada para validar a força da palavra-passe. Esta verificação de validação resulta em senhas mais fortes para todos os clientes AD Azure.

A lista global de palavras-passe proibida é aplicada automaticamente a todos os utilizadores de um inquilino AZure AD. Não há nada que permita ou configuure, e não pode ser desativado. Esta lista global de palavras-passe proibidas é aplicada aos utilizadores quando alteram ou reiniciam a sua própria palavra-passe através do Azure AD.

> [!NOTE]
> Os criminosos virtuais também usam estratégias semelhantes nos seus ataques para identificar senhas e variações fracas comuns. Para melhorar a segurança, a Microsoft não publica o conteúdo da lista global de senhas proibidas.

## <a name="custom-banned-password-list"></a>Lista de senhas proibidas personalizadas

Algumas organizações querem melhorar a segurança e adicionar as suas próprias personalizações no topo da lista global de senhas proibidas. Para adicionar as suas próprias entradas, pode utilizar a *lista de palavras-passe proibidas sob medida.* Os termos adicionados à lista de palavras-passe proibidas personalizadas devem ser focados em termos específicos da organização, tais como os seguintes exemplos:

- Nomes de marcas
- Nomes dos produtos
- Localizações, como sede da empresa
- Termos internos específicos da empresa
- Abreviaturas que têm significado específico da empresa

Quando os termos são adicionados à lista de senhas proibidas personalizadas, são combinados com os termos da lista global de senhas proibidas. Os eventos de alteração de palavra-passe ou de reset são então validados contra o conjunto combinado destas listas de senhas proibidas.

> [!NOTE]
> A lista de senhas proibidas personalizadas está limitada a um máximo de 1000 termos. Não é projetado para bloquear listas extremamente grandes de senhas.
>
> Para aproveitar plenamente os benefícios da lista de senhas proibidas personalizadas, primeiro entenda [como são avaliadas as palavras-passe](#how-are-passwords-evaluated) antes de adicionar termos à lista proibida personalizada. Esta abordagem permite detetar e bloquear de forma eficiente um grande número de senhas fracas e suas variantes.

![Modifique a lista de palavras-passe proibidas sob métodos de autenticação](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Vamos considerar um cliente chamado *Contoso.* A empresa tem sede em Londres e faz um produto chamado *Widget.* Para este cliente, por exemplo, seria desperdiçado e menos seguro tentar bloquear variações específicas destes termos, tais como:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso.
- "LondonHQ"

Em vez disso, é muito mais eficiente e seguro bloquear apenas os termos-chave de base, tais como os seguintes exemplos:

- "Contoso"
- "Londres"
- "Widget"

O algoritmo de validação de palavra-passe bloqueia automaticamente variantes e combinações fracas.

Para começar a usar uma lista de senhas proibidas personalizadas, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Configurar senhas personalizadas proibidas](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataques de spray de palavra-passe e listas de senhas comprometidas de terceiros

A Azure AD Password Protection ajuda-o a defender-se contra ataques de spray de senha. A maioria dos ataques de spray de palavra-passe não tentam atacar qualquer conta individual mais do que algumas vezes. Este comportamento aumentaria a probabilidade de deteção, seja através do bloqueio de conta ou de outros meios.

Em vez disso, a maioria dos ataques de spray de palavra-passe submete apenas um pequeno número das palavras-passe mais fracas conhecidas contra cada uma das contas de uma empresa. Esta técnica permite ao intruso procurar rapidamente uma conta facilmente comprometida e evitar possíveis limiares de deteção.

A Azure AD Password Protection bloqueia eficientemente todas as palavras-passe fracas conhecidas suscetíveis de serem usadas em ataques de spray de palavra-passe. Esta proteção baseia-se em dados de telemetria de segurança no mundo real da Azure AD para construir a lista global de senhas proibidas.

Existem alguns sites de terceiros que enumeram milhões de senhas que foram comprometidas em falhas de segurança anteriores conhecidas publicamente. É comum que os produtos de validação de senhas de terceiros sejam baseados na comparação bruta-força com aqueles milhões de senhas. No entanto, essas técnicas não são a melhor maneira de melhorar a força geral da palavra-passe, dadas as estratégias típicas usadas pelos atacantes do spray de palavra-passe.

> [!NOTE]
> A lista global de palavras-passe proibidas não se baseia em fontes de dados de terceiros, incluindo listas de senhas comprometidas.

Embora a lista global proibida seja pequena em comparação com algumas listas de terceiros, é proveniente de telemetria de segurança real em ataques de spray de senha real. Esta abordagem melhora a segurança e eficácia globais, e o algoritmo de validação de palavras-passe também usa técnicas inteligentes de correspondência fuzzy. Como resultado, a Azure AD Password Protection deteta e bloqueia milhões das palavras-passe fracas mais comuns de serem usadas na sua empresa.

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos no local

Muitas organizações têm um modelo de identidade híbrida que inclui ambientes de Serviços de Domínio de Diretório Ativo (DS) no local. Para estender os benefícios de segurança da Azure AD Password Protection para o seu ambiente DS AD, pode instalar componentes nos seus servidores no local. Estes agentes requerem eventos de mudança de palavra-passe no ambiente AD DS no local para cumprir a mesma política de senha que no Azure AD.

Para obter mais informações, consulte [a Proteção de Passwords AZure AD para DS AD](concept-password-ban-bad-on-premises.md).

## <a name="how-are-passwords-evaluated"></a>Como são avaliadas as palavras-passe

Quando um utilizador altera ou repõe a sua palavra-passe, a nova palavra-passe é verificada para obter força e complexidade, validando-a com a lista combinada de termos das listas de senhas proibidas global e personalizada.

Mesmo que a palavra-passe de um utilizador contenha uma palavra-passe proibida, a palavra-passe pode ser aceite se a palavra-passe geral for suficientemente forte. Uma palavra-passe recentemente configurada passa pelas seguintes etapas para avaliar a sua força global para determinar se deve ser aceite ou rejeitada:

### <a name="step-1-normalization"></a>Passo 1: Normalização

Uma nova senha passa primeiro por um processo de normalização. Esta técnica permite que um pequeno conjunto de senhas proibidas seja mapeado para um conjunto muito maior de senhas potencialmente fracas.

A normalização tem as seguintes duas partes:

* Todas as letras maiúsculas são alteradas para minúsculas.
* Em seguida, são realizadas substituições comuns de caracteres, tais como no exemplo seguinte:

   | Carta original | Carta substituída |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | t                  |
   | \@              | um                  |

Considere o exemplo seguinte:

* A palavra-passe "em branco" está proibida.
* Um utilizador tenta alterar a sua palavra-passe para Bl@nK " "
* Mesmo que Bl@nk " não seja proibido, o processo de normalização converte esta palavra-passe em "branco".
* Esta palavra-passe seria rejeitada.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passo 2: Verifique se a palavra-passe é considerada proibida

Uma palavra-passe é então examinada para outros comportamentos correspondentes, e uma pontuação é gerada. Esta pontuação final determina se o pedido de alteração da palavra-passe é aceite ou rejeitado.

#### <a name="fuzzy-matching-behavior"></a>Comportamento de correspondência difusa

A correspondência fuzzy é usada na palavra-passe normalizada para identificar se contém uma palavra-passe encontrada nas listas de senhas proibidas a nível global ou personalizado. O processo de correspondência baseia-se numa distância de edição de uma (1) comparação.

Considere o exemplo seguinte:

* A palavra-passe "abcdef" está proibida.
* Um utilizador tenta alterar a sua palavra-passe para uma das seguintes:

   * 'abcdeg' - *último personagem mudou de 'f' para 'g'*
   * 'abcdefg' - *'g' anexado ao fim*
   * 'abcde' - *trailing 'f' foi apagado do fim*

* Cada uma das palavras-passe acima não corresponde especificamente à palavra-passe proibida "abcdef".

    No entanto, uma vez que cada exemplo está a uma distância de edição de 1 do termo proibido 'abcdef', todos eles são considerados como uma correspondência para "abcdef".
* Estas palavras-passe seriam rejeitadas.

#### <a name="substring-matching-on-specific-terms"></a>Correspondência de sub-adstring (em termos específicos)

A correspondência de sub-cordas é usada na palavra-passe normalizada para verificar o primeiro e último nome do utilizador, bem como o nome do inquilino. A correspondência do nome do inquilino não é feita quando se validam palavras-passe num controlador de domínio AD DS para cenários híbridos no local.

> [!IMPORTANT]
> A correspondência de substring é aplicada apenas para nomes, e outros termos, que têm pelo menos quatro caracteres de comprimento.

Considere o exemplo seguinte:

* Um utilizador chamado Poll que quer redefinir a sua palavra-passe para "p0LL23fb".
* Após a normalização, esta palavra-passe tornar-se-ia "poll23fb".
* A correspondência de substring conclui que a palavra-passe contém o primeiro nome do utilizador "Poll".
* Apesar de "poll23fb" não estar especificamente na lista de palavras-passe proibidas, a sub-base encontrou "Poll" na palavra-passe.
* Esta palavra-passe seria rejeitada.

#### <a name="score-calculation"></a>Cálculo de pontuação

O próximo passo é identificar todas as instâncias de senhas proibidas na nova senha normalizada do utilizador. Os pontos são atribuídos com base nos seguintes critérios:

1. Cada palavra-passe proibida encontrada na senha de um utilizador é dada um ponto.
1. Cada personagem restante que não faz parte de uma senha proibida é dado um ponto.
1. Uma palavra-passe deve ter pelo menos cinco (5) pontos a aceitar.

Para os próximos dois cenários de exemplo, o Contoso está a usar a Azure AD Password Protection e tem "contoso" na sua lista de senhas proibidas sob medida. Vamos também supor que "em branco" está na lista global.

No seguinte cenário de exemplo, um utilizador altera a sua palavra-passe para "C0ntos0Blank12":

* Após a normalização, esta palavra-passe torna-se "contosoblank12".
* O processo de correspondência conclui que esta palavra-passe contém duas palavras-passe proibidas: "contoso" e "blank".
* Esta palavra-passe é então dada a seguinte pontuação:

    *[contoso] + [em branco] + [1] + [2] = 4 pontos*

* Como esta palavra-passe tem menos de cinco (5) pontos, é rejeitada.

Vamos olhar um exemplo ligeiramente diferente para mostrar como a complexidade adicional numa palavra-passe pode construir o número necessário de pontos a serem aceites. No seguinte cenário de exemplo, um utilizador altera a sua palavra-passe para " ContoS0Bl@nkf9 ":

* Após a normalização, esta palavra-passe torna-se "contosoblankf9!".
* O processo de correspondência conclui que esta palavra-passe contém duas palavras-passe proibidas: "contoso" e "blank".
* Esta palavra-passe é então dada a seguinte pontuação:

    *[contoso] + [em branco] + [f] + [9] + [!] = 5 pontos*

* Como esta palavra-passe é de pelo menos cinco (5) pontos, é aceite.

> [!IMPORTANT]
> O algoritmo de senha proibido, juntamente com a lista global de senhas proibidas, pode e fazer mudanças a qualquer momento em Azure com base em análises e pesquisas de segurança em curso.
>
> Para o serviço de agente DC no local em cenários híbridos, os algoritmos atualizados só fazem efeito após a atualização do software do agente DC.

## <a name="what-do-users-see"></a>O que os utilizadores veem

Quando um utilizador tenta redefinir uma palavra-passe para algo que seria proibido, é apresentada a seguinte mensagem de erro:

*"Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que torna a sua palavra-passe facilmente adivinhável. Por favor, tente novamente com uma senha diferente.*

## <a name="license-requirements"></a>Requisitos de licença

| Utilizadores | Proteção de senha AZure AD com lista global de senhas proibidas | Proteção de senha AZure AD com lista de senhas proibidas personalizadas|
|-------------------------------------------|---------------------------|---------------------------|
| Utilizadores apenas em nuvem                          | Azure AD Gratuito             | Azure AD Premium P1 ou P2 |
| Utilizadores sincronizados a partir de AD DS no local | Azure AD Premium P1 ou P2 | Azure AD Premium P1 ou P2 |

> [!NOTE]
> Os utilizadores de DS AD no local que não são sincronizados com Azure AD também beneficiam da Proteção de Password AD AZure com base no licenciamento existente para utilizadores sincronizados.

Informações adicionais de licenciamento, incluindo custos, podem ser encontradas no site de preços do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Passos seguintes

Para começar a usar uma lista de senhas proibidas personalizadas, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Configurar senhas personalizadas proibidas](tutorial-configure-custom-password-protection.md)

Também pode [ativar no local a proteção de senha azure AD](howto-password-ban-bad-on-premises-deploy.md).
