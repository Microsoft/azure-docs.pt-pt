---
title: Planejar políticas de acesso condicional no Azure Active Directory | Microsoft Docs
description: Neste artigo, você aprenderá a planejar políticas de acesso condicional para Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894530aa9624af18f2f33a061d5cde683e9f01be
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880264"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Como planejar sua implantação de acesso condicional no Azure Active Directory

Planejar sua implantação de acesso condicional é essencial para garantir que você obtenha a estratégia de acesso necessária para aplicativos e recursos em sua organização. Passe a maior parte do tempo durante a fase de planejamento de sua implantação para projetar as várias políticas necessárias para conceder ou bloquear o acesso aos usuários sob as condições escolhidas. Este documento explica as etapas que você deve seguir para implementar políticas de acesso condicional seguras e eficazes. Antes de começar, verifique se você entendeu como o [acesso condicional](overview.md) funciona e quando você deve usá-lo.

## <a name="what-you-should-know"></a>O que deve saber

Considere o acesso condicional como uma estrutura que permite controlar o acesso aos aplicativos e recursos de sua organização, em vez de um recurso autônomo. Consequentemente, algumas configurações de acesso condicional exigem recursos adicionais a serem configurados. Por exemplo, você pode configurar uma política que responde a um [nível de risco de entrada](../identity-protection/howto-identity-protection-configure-risk-policies.md)específico. No entanto, uma política baseada em um nível de risco de entrada requer que [Azure Active Directory a proteção de identidade](../identity-protection/overview-identity-protection.md) seja habilitada.

Se forem necessários recursos adicionais, você também poderá precisar obter licenças relacionadas. Por exemplo, embora o acesso condicional seja Azure AD Premium recurso P1, a proteção de identidade requer uma licença Azure AD Premium P2.

Há dois tipos de políticas de acesso condicional: linha de base e padrão. Uma [política de linha de base](baseline-protection.md) é uma política de acesso condicional predefinida. O objetivo dessas políticas é garantir que você tenha pelo menos o nível de linha de base de segurança habilitado. Políticas de linha de base. As políticas de linha de base estão disponíveis em todas as edições do Azure AD e fornecem apenas opções de personalização limitadas. Se um cenário exigir mais flexibilidade, desabilite a política de linha de base e implemente seus requisitos em uma política padrão personalizada.

Em uma política de acesso condicional padrão, você pode personalizar todas as configurações para ajustar a política aos seus requisitos de negócios. As políticas padrão exigem uma licença Azure AD Premium P1.

## <a name="draft-policies"></a>Políticas de rascunho

Azure Active Directory acesso condicional permite que você traga a proteção de seus aplicativos de nuvem para um novo nível. Nesse novo nível, como você pode acessar um aplicativo de nuvem com base em uma avaliação de política dinâmica em vez de uma configuração de acesso estático. Com uma política de acesso condicional, você define uma resposta (**faça isso**) para uma condição de acesso (**quando isso acontecer**).

![Motivo e resposta](./media/plan-conditional-access/10.png)

Defina cada política de acesso condicional que você deseja implementar usando este modelo de planejamento. O exercício de planejamento:

- Ajuda a descrever as respostas e as condições de cada política.
- Resulta em um catálogo de política de acesso condicional bem documentado para sua organização. 

Você pode usar seu catálogo para avaliar se sua implementação de política reflete os requisitos de negócios de sua organização. 

Use o modelo de exemplo a seguir para criar políticas de acesso condicional para sua organização:

|Quando *isso* acontece:|Em seguida, faça *o*seguinte:|
|-|-|
|É feita uma tentativa de acesso:<br>-Para um aplicativo de nuvem *<br>-por usuários e grupos*<br>Usando<br>-Condição 1 (por exemplo, fora da rede Corp)<br>-Condição 2 (por exemplo, plataformas de dispositivo)|Bloquear o acesso ao aplicativo|
|É feita uma tentativa de acesso:<br>-Para um aplicativo de nuvem *<br>-por usuários e grupos*<br>Usando<br>-Condição 1 (por exemplo, fora da rede Corp)<br>-Condição 2 (por exemplo, plataformas de dispositivo)|Conceder acesso com (e):<br>-Requisito 1 (por exemplo, MFA)<br>-Requisito 2 (por exemplo, conformidade do dispositivo)|
|É feita uma tentativa de acesso:<br>-Para um aplicativo de nuvem *<br>-por usuários e grupos*<br>Usando<br>-Condição 1 (por exemplo, fora da rede Corp)<br>-Condição 2 (por exemplo, plataformas de dispositivo)|Conceder acesso com (ou):<br>-Requisito 1 (por exemplo, MFA)<br>-Requisito 2 (por exemplo, conformidade do dispositivo)|

No mínimo, **quando isso acontece** define a entidade (**quem**) que tenta acessar um aplicativo de nuvem (**o que**). Se necessário, você também pode incluir **como** uma tentativa de acesso é executada. No acesso condicional, os elementos que definem quem, o que e como são conhecidos como condições. Para obter mais informações, consulte [o que são condições em Azure Active Directory acesso condicional?](conditions.md) 

Com **isso**, você define a resposta de sua política para uma condição de acesso. Em sua resposta, você bloqueia ou concede acesso com requisitos adicionais, por exemplo, a MFA (autenticação multifator). Para obter uma visão geral completa, consulte [o que são controles de acesso no Azure Active Directory acesso condicional?](controls.md)  

A combinação de condições com seus controles de acesso representa uma política de acesso condicional.

![Motivo e resposta](./media/plan-conditional-access/51.png)

Para obter mais informações, consulte [o que é necessário para fazer uma política funcionar](best-practices.md#whats-required-to-make-a-policy-work).

Neste ponto, é um bom momento para decidir sobre um padrão de nomenclatura para suas políticas. O padrão de nomenclatura ajuda a encontrar políticas e a entender sua finalidade sem abri-las no portal de administração do Azure. Nomeie sua política para mostrar:

- Um número de sequência
- O aplicativo de nuvem ao qual se aplica
- A resposta
- A quem ele se aplica
- Quando se aplica (se aplicável)
 
![Padrão de nomenclatura](./media/plan-conditional-access/11.png)

Embora um nome descritivo ajude você a manter uma visão geral da sua implementação de acesso condicional, o número de sequência será útil se você precisar fazer referência a uma política em uma conversa. Por exemplo, se você falar com um colega de administrador no telefone, solicite a eles que abram o Policy EM063 para resolver um problema.

Por exemplo, o nome a seguir informa que a política requer MFA para usuários de marketing em redes externas usando o aplicativo Dynamics CRP:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Além de suas políticas ativas, é aconselhável também implementar políticas desabilitadas que atuem como [controles de acesso resilientes secundários em cenários de interrupção/emergência](../authentication/concept-resilient-controls.md). Seu padrão de nomenclatura para as políticas de contingência deve incluir mais alguns itens: 

- `ENABLE IN EMERGENCY` no início para que o nome se destaque entre as outras políticas.
- O nome da interrupção à qual ele deve se aplicar.
- Um número de sequência de ordenação para ajudar o administrador a saber em quais políticas de ordem devem ser habilitadas. 

Por exemplo, o seguinte nome indica que essa política é a primeira política de quatro que você deve habilitar se houver uma interrupção de MFA:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Planejar políticas

Ao planejar sua solução de política de acesso condicional, avalie se você precisa criar políticas para obter os resultados a seguir. 

### <a name="block-access"></a>Bloquear acesso

A opção de bloquear o acesso é eficiente porque:

- Supera todas as outras atribuições de um usuário
- Tem o poder de bloquear toda a sua organização de se conectar ao seu locatário
 
Se você quiser bloquear o acesso para todos os usuários, deverá, pelo menos, excluir um usuário (normalmente contas de acesso de emergência) da política. Para obter mais informações, consulte [Selecionar usuários e grupos](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Requerer MFA

Para simplificar a experiência de entrada de seus usuários, talvez você queira permitir que eles entrem em seus aplicativos de nuvem usando um nome de usuário e uma senha. No entanto, normalmente, há pelo menos alguns cenários para os quais é aconselhável exigir uma forma mais forte de verificação de conta. Com uma política de acesso condicional, você pode limitar o requisito de MFA para determinados cenários. 

Casos de uso comuns para exigir MFA são o acesso:

- [Por administradores](howto-baseline-protect-administrators.md)
- [Para aplicativos específicos](app-based-mfa.md) 
- [Em locais de rede, você não confia](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Responder a contas potencialmente comprometidas

Com as políticas de acesso condicional, você pode implementar respostas automatizadas para entradas de identidades potencialmente comprometidas. A probabilidade de que uma conta tenha sido comprometida é expressa em forma de níveis de risco. Há dois níveis de risco calculados pela proteção de identidade: risco de entrada e risco do usuário. Para implementar uma resposta a um risco de entrada, você tem duas opções:

- [A condição de risco de entrada](conditions.md#sign-in-risk) na política de acesso condicional
- [A política de risco de entrada](../identity-protection/howto-sign-in-risk-policy.md) na proteção de identidade 

Abordar o risco de entrada como condição é o método preferencial porque fornece mais opções de personalização.

O nível de risco do usuário está disponível apenas como [política de risco do usuário](../identity-protection/howto-user-risk-policy.md) na proteção de identidade. 

Para obter mais informações, consulte [o que é Azure Active Directory Identity Protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Exigir dispositivos geridos

A proliferação de dispositivos com suporte para acessar seus recursos de nuvem ajuda a melhorar a produtividade de seus usuários. No lado do inverso, você provavelmente não quer que determinados recursos em seu ambiente sejam acessados por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, você deve exigir que os usuários só possam acessá-los usando um dispositivo gerenciado. Para obter mais informações, consulte [como exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Exigir aplicações aprovados do cliente

Uma das primeiras decisões que você precisa tomar para cenários de BYOD (traga seus próprios dispositivos) é se você precisa gerenciar todo o dispositivo ou apenas os dados nele. Seus funcionários usam dispositivos móveis para tarefas pessoais e de trabalho. Ao garantir que seus funcionários possam ser produtivos, você também deseja evitar a perda de dados. Com o acesso condicional do Azure Active Directory (AD do Azure), você pode restringir o acesso aos aplicativos de nuvem a aplicativos cliente aprovados que podem proteger seus dados corporativos. Para obter mais informações, consulte [como exigir aplicativos cliente aprovados para acesso de aplicativo de nuvem com acesso condicional](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada

O Azure AD dá suporte a vários dos protocolos de autenticação e autorização mais amplamente usados, incluindo autenticação herdada. Como você pode impedir que aplicativos que usam autenticação herdada acessem os recursos do seu locatário? A recomendação é simplesmente bloqueá-los com uma política de acesso condicional. Se necessário, você permite que apenas determinados usuários e locais de rede específicos usem aplicativos baseados na autenticação herdada. Para obter mais informações, consulte [como bloquear a autenticação herdada no Azure AD com acesso condicional](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Testar sua política

Antes de distribuir uma política para produção, você deve testá-la para verificar se ela se comporta conforme o esperado.

1. Criar usuários de teste
1. Criar um plano de teste
1. Configurar a política
1. Avaliar uma entrada simulada
1. Testar sua política
1. Limpeza

### <a name="create-test-users"></a>Criar usuários de teste

Para testar uma política, crie um conjunto de usuários que seja semelhante aos usuários em seu ambiente. A criação de usuários de teste permite que você verifique se as políticas funcionam conforme o esperado antes de afetar os usuários reais e potencialmente interromper o acesso a aplicativos e recursos. 

Algumas organizações têm locatários de teste para essa finalidade. No entanto, pode ser difícil recriar todas as condições e aplicativos em um locatário de teste para testar completamente o resultado de uma política. 

### <a name="create-a-test-plan"></a>Criar um plano de teste

O plano de teste é importante para ter uma comparação entre os resultados esperados e os resultados reais. Você deve sempre ter uma expectativa antes de testar algo. A tabela a seguir descreve exemplos de casos de teste. Ajuste os cenários e os resultados esperados com base em como suas políticas de autoridade de certificação são configuradas.

|Política |Cenário |Resultado esperado | Resultado |
|---|---|---|---|
|[Exigir MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|O usuário autorizado entra no *aplicativo* em um local/trabalho confiável|O usuário não é solicitado a MFA| |
|[Exigir MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|O usuário autorizado entra no *aplicativo* enquanto não está em um local/trabalho confiável|O usuário recebe uma solicitação de MFA e pode entrar com êxito| |
|[Exigir MFA (para admin)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|O administrador global entra no *aplicativo*|O administrador é solicitado a MFA| |
|[Inícios de sessão de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|O usuário entra no *aplicativo* usando um [navegador Tor](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|O administrador é solicitado a MFA| |
|[Gestão de Dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|O usuário autorizado tenta entrar por meio de um dispositivo autorizado|Acesso concedido| |
|[Gestão de Dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|O usuário autorizado tenta entrar por meio de um dispositivo não autorizado|Acesso bloqueado| |
|[Alteração de senha para usuários arriscados](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|O usuário autorizado tenta entrar com credenciais comprometidas (entrada de alto risco)|O usuário é solicitado a alterar a senha ou o acesso é bloqueado com base em sua política| |

### <a name="configure-the-policy"></a>Configurar a política

O gerenciamento de políticas de acesso condicional é uma tarefa manual. No portal do Azure, você pode gerenciar suas políticas de acesso condicional em um local central-a página de acesso condicional. Um ponto de entrada para a página de acesso condicional é a seção **segurança** no painel de navegação **Active Directory** . 

![Acesso Condicional](media/plan-conditional-access/03.png)

Se você quiser saber mais sobre como criar políticas de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md). Este guia de início rápido ajuda você a:

- Familiarize-se com a interface do usuário.
- Obtenha uma primeira impressão de como o acesso condicional funciona. 

### <a name="evaluate-a-simulated-sign-in"></a>Avaliar uma entrada simulada

Agora que você configurou sua política de acesso condicional, provavelmente desejará saber se ela funciona conforme o esperado. Como uma primeira etapa, use a ferramenta de política de acesso condicional [What If](what-if-tool.md) para simular uma entrada de seu usuário de teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.

>[!NOTE]
> Embora uma execução simulada lhe dê a impressão do impacto de uma política de acesso condicional, ela não substitui uma execução de teste real.

### <a name="test-your-policy"></a>Testar sua política

Execute casos de teste de acordo com seu plano de teste. Nesta etapa, você executa um teste de ponta a ponta de cada política para os usuários de teste para garantir que cada política se comporta corretamente. Use os cenários criados acima para executar cada teste.

É importante certificar-se de testar os critérios de exclusão de uma política. Por exemplo, você pode excluir um usuário ou grupo de uma política que requer MFA. Teste se os usuários excluídos são solicitados pela MFA, porque a combinação de outras políticas pode exigir MFA para esses usuários.

### <a name="cleanup"></a>Limpeza

O procedimento de limpeza consiste nas seguintes etapas:

1. Desabilite a política.
1. Remova os usuários e grupos atribuídos.
1. Exclua os usuários de teste.  

## <a name="move-to-production"></a>Mover para produção

Quando novas políticas estiverem prontas para seu ambiente, implante-as em fases:

- Fornecer comunicação de alteração interna aos usuários finais.
- Comece com um pequeno conjunto de usuários e verifique se a política se comporta conforme o esperado.
- Quando você expande uma política para incluir mais usuários, continue a excluir todos os administradores. A exclusão de administradores garante que alguém ainda tenha acesso a uma política se uma alteração for necessária.
- Aplique uma política a todos os usuários somente se ele for necessário.

Como prática recomendada, crie pelo menos uma conta de usuário que seja:

- Dedicado à administração de política
- Excluído de todas as suas políticas

## <a name="rollback-steps"></a>Etapas de reversão

Caso você precise reverter suas políticas implementadas recentemente, use uma ou mais das seguintes opções para reverter:

1. **Desabilitar a política** -desabilitar uma política garante que ela não se aplica quando um usuário tenta entrar. Você sempre pode voltar e habilitar a política quando quiser usá-la.

   ![desabilitar política](media/plan-conditional-access/07.png)

1. **Excluir um usuário/grupo de uma política** – se um usuário não puder acessar o aplicativo, você poderá optar por excluir o usuário da política

   ![Exluce usuários](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Essa opção deve ser usada com moderação apenas em situações em que o usuário é confiável. O usuário deve ser adicionado de volta à política ou ao grupo assim que possível.

1. **Excluir a política** – se a política não for mais necessária, exclua-a.

## <a name="next-steps"></a>Passos seguintes

Confira a [documentação de acesso condicional do Azure ad](index.yml) para obter uma visão geral das informações disponíveis.
