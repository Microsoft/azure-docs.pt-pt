---
title: Plan Conditional Access policies in Azure Ative Directory  Microsoft Docs
description: Neste artigo, aprende-se a planear políticas de Acesso Condicional para o Diretório Ativo Azure.
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
ms.openlocfilehash: 266fa2403ef96e808a0c1f1eb46b4f7065c06252
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185891"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Como: Planear a sua implantação de Acesso Condicional no Diretório Ativo Azure

Planear a sua implementação de Acesso Condicional é fundamental para garantir que obtém a estratégia de acesso necessária para apps e recursos na sua organização. Passe a maior parte do seu tempo durante a fase de planeamento da sua implementação para conceber as várias políticas que necessita para conceder ou bloquear o acesso aos seus utilizadores nas condições que escolher. Este documento explica os passos que deve tomar para implementar políticas de acesso condicional seguras e eficazes. Antes de começar, certifique-se de que compreende como funciona o [Acesso Condicional](overview.md) e quando deve usá-lo.

## <a name="what-you-should-know"></a>O que deve saber

Pense no Acesso Condicional como um quadro que lhe permite controlar o acesso às aplicações e recursos da sua organização, em vez de uma funcionalidade autónoma. Consequentemente, algumas definições de Acesso Condicional requerem funcionalidades adicionais para serem configuradas. Por exemplo, pode configurar uma política que responda a um [nível](../identity-protection/howto-identity-protection-configure-risk-policies.md)de risco específico de entrada . No entanto, uma política baseada num nível de risco de entrada requer a proteção da [identidade do Diretório Ativo Azure.](../identity-protection/overview-identity-protection.md)

Se forem necessárias funcionalidades adicionais, poderá também ter de obter licenças relacionadas. Por exemplo, enquanto o Acesso Condicional é a funcionalidade Azure AD Premium P1, a proteção de identidade requer uma licença Azure AD Premium P2.

Existem dois tipos de políticas de Acesso Condicional: linha de base e padrão. Uma [política de base](baseline-protection.md) é uma política de acesso condicional predefinida. O objetivo destas políticas é garantir que tem pelo menos o nível de segurança de base habilitado. Políticas de base. As políticas de base estão disponíveis em todas as edições do Azure AD, e fornecem apenas opções de personalização limitadas. Se um cenário requer mais flexibilidade, desative a política de base e implemente os seus requisitos numa política padrão personalizada.

Numa política padrão de Acesso Condicional, pode personalizar todas as definições para ajustar a política aos seus requisitos de negócio. As políticas padrão requerem uma licença Azure AD Premium P1.

## <a name="draft-policies"></a>Projeto de políticas

O Acesso Condicional do Diretório Ativo Azure permite-lhe levar a proteção das suas aplicações em nuvem para um novo nível. Neste novo nível, a forma como pode aceder a uma aplicação em nuvem baseia-se numa avaliação política dinâmica em vez de uma configuração de acesso estática. Com uma política de Acesso Condicional, define-se uma resposta **(faça isto)** a uma condição de acesso **(quando isso acontece).**

![Razão e resposta](./media/plan-conditional-access/10.png)

Defina todas as políticas de Acesso Condicional que pretende implementar utilizando este modelo de planeamento. O exercício de planeamento:

- Ajuda-o a delinear as respostas e condições para cada política.
- Resulta num catálogo de políticas de acesso condicional bem documentado para a sua organização. 

Pode utilizar o seu catálogo para avaliar se a sua implementação de políticas reflete os requisitos de negócio da sua organização. 

Utilize o seguinte modelo de exemplo para criar políticas de Acesso Condicional para a sua organização:

|Quando *isto* acontecer:|Então faça *isto:*|
|-|-|
|É feita uma tentativa de acesso:<br>- Para uma aplicação na nuvem *<br>- Por utilizadores e grupos*<br>Utilização:<br>- Condição 1 (por exemplo, fora da rede Corp)<br>- Condição 2 (por exemplo, plataformas de dispositivos)|Bloquear o acesso à aplicação|
|É feita uma tentativa de acesso:<br>- Para uma aplicação na nuvem *<br>- Por utilizadores e grupos*<br>Utilização:<br>- Condição 1 (por exemplo, fora da rede Corp)<br>- Condição 2 (por exemplo, plataformas de dispositivos)|Acesso de concessão com (E):<br>- Requisito 1 (por exemplo, MFA)<br>- Requisito 2 (por exemplo, conformidade com o dispositivo)|
|É feita uma tentativa de acesso:<br>- Para uma aplicação na nuvem *<br>- Por utilizadores e grupos*<br>Utilização:<br>- Condição 1 (por exemplo, fora da rede Corp)<br>- Condição 2 (por exemplo, plataformas de dispositivos)|Acesso de concessão com (OR):<br>- Requisito 1 (por exemplo, MFA)<br>- Requisito 2 (por exemplo, conformidade com o dispositivo)|

No mínimo, **quando isso acontece** define o principal (**que**) que tenta aceder a uma aplicação em nuvem (**o quê).** Se necessário, também pode incluir **como** uma tentativa de acesso é realizada. No Acesso Condicional, os elementos que definem quem, o quê e como são conhecidos como condições. Para mais informações, consulte Quais são as condições no Acesso Condicional do [Diretório Ativo do Azure?](concept-conditional-access-conditions.md) 

Com **isso,** define a resposta da sua política a uma condição de acesso. Na sua resposta, bloqueia ou concede acesso com requisitos adicionais, por exemplo, a autenticação de vários fatores (MFA). Para uma visão geral completa, consulte [O que são os controlos de acesso no Acesso Condicional do Diretório Ativo Azure?](controls.md)  

A combinação de condições com os seus controlos de acesso representa uma política de Acesso Condicional.

![Razão e resposta](./media/plan-conditional-access/51.png)

Para mais informações, veja [o que é necessário para fazer uma política funcionar.](best-practices.md#whats-required-to-make-a-policy-work)

Neste momento, é uma boa altura para decidir sobre um padrão de nomeação para as suas políticas. O padrão de nomeação ajuda-o a encontrar políticas e a compreender o seu propósito sem as abrir no portal de administração Azure. Diga o nome da sua política para mostrar:

- Um número de sequência
- A aplicação cloud que se aplica
- A resposta
- A quem se aplica
- Quando se aplica (se aplicável)
 
![Padrão de nomeação](./media/plan-conditional-access/11.png)

Enquanto um nome descritivo o ajuda a manter uma visão geral da sua implementação de Acesso Condicional, o número de sequência é útil se precisar de referenciar uma política numa conversa. Por exemplo, se falar com um colega administrador ao telefone, pode pedir-lhes que abram a política EM063 para resolver um problema.

Por exemplo, o seguinte nome afirma que a política requer MFA para utilizadores de marketing em redes externas usando a aplicação Dynamics CRP:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Para além das suas políticas ativas, é aconselhável também implementar políticas de desativação que atuam como controlos de [acesso resilientes secundários em cenários de paragem/emergência.](../authentication/concept-resilient-controls.md) O seu padrão de nomeação para as políticas de contingência deve incluir mais alguns itens: 

- `ENABLE IN EMERGENCY` no início para fazer com que o nome se destaque entre as outras políticas.
- O nome da perturbação deve aplicar-se.
- Um número de sequência de ordenação para ajudar o administrador a saber em que políticas de ordem devem ser ativadas. 

Por exemplo, o seguinte nome indica que esta política é a primeira em quatro que deve permitir se houver uma perturbação do MFA:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Políticas de planos

Ao planear a sua solução política de Acesso Condicional, avalie se precisa de criar políticas para alcançar os seguintes resultados. 

### <a name="block-access"></a>Acesso ao bloco

A opção de bloquear o acesso é poderosa porque:

- Trumps todas as outras atribuições para um utilizador
- Tem o poder de impedir toda a sua organização de se inscrever no seu inquilino.
 
Se pretender bloquear o acesso a todos os utilizadores, deve pelo menos excluir um utilizador (normalmente contas de acesso de emergência) da apólice. Para mais informações, consulte [utilizadores e grupos selecionados.](block-legacy-authentication.md#select-users-and-cloud-apps)  

### <a name="require-mfa"></a>Requerer MFA

Para simplificar a experiência de entrada dos seus utilizadores, é melhor permitir que eles acedam às suas aplicações na nuvem usando um nome de utilizador e uma palavra-passe. No entanto, tipicamente, existem pelo menos alguns cenários para os quais é aconselhável exigir uma forma mais forte de verificação de conta. Com uma política de Acesso Condicional, pode limitar a exigência de MFA a determinados cenários. 

Os casos de utilização comum para exigir mfa são de acesso:

- [Por administradores](howto-baseline-protect-administrators.md)
- [Para aplicações específicas](app-based-mfa.md) 
- [A partir de locais de rede, não se confia.](untrusted-networks.md)

### <a name="respond-to-potentially-compromised-accounts"></a>Responder a contas potencialmente comprometidas

Com as políticas de Acesso Condicional, pode implementar respostas automatizadas a inscrições a partir de identidades potencialmente comprometidas. A probabilidade de uma conta ter sido comprometida é expressa sob a forma de níveis de risco. Existem dois níveis de risco calculados pela proteção da identidade: risco de inscrição e risco do utilizador. Para implementar uma resposta a um risco de inscrição, tem duas opções:

- [A condição de risco de inscrição](concept-conditional-access-conditions.md#sign-in-risk) na política de acesso condicional
- [A política de risco de inscrição](../identity-protection/howto-sign-in-risk-policy.md) na proteção da identidade 

Abordar o risco de inscrição como condição é o método preferido porque lhe dá mais opções de personalização.

O nível de risco do utilizador só está disponível como política de [risco do utilizador](../identity-protection/howto-user-risk-policy.md) na proteção de identidade. 

Para mais informações, consulte o que é a Proteção de [Identidade do Diretório Ativo azure?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Exigir dispositivos geridos

A proliferação de dispositivos suportados para aceder aos seus recursos na nuvem ajuda a melhorar a produtividade dos seus utilizadores. Por outro lado, provavelmente não quer que certos recursos no seu ambiente sejam acedidos por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, deve exigir que os utilizadores só possam aceder a eles através de um dispositivo gerido. Para mais informações, consulte [Como exigir dispositivos geridos para acesso](require-managed-devices.md)a aplicações na nuvem com Acesso Condicional . 

### <a name="require-approved-client-apps"></a>Exigir aplicações aprovados do cliente

Uma das primeiras decisões que precisa de tomar para trazer os cenários dos seus próprios dispositivos (BYOD), é se precisa de gerir todo o dispositivo ou apenas os dados do mesmo. Os seus empregados utilizam dispositivos móveis para tarefas pessoais e profissionais. Ao mesmo tempo que se certifica de que os seus colaboradores podem ser produtivos, também pretende evitar a perda de dados. Com o Acesso Condicional do Diretório Ativo Azure (Azure AD), pode restringir o acesso às suas aplicações na nuvem a aplicações de clientes aprovadas que possam proteger os seus dados corporativos. Para mais informações, consulte [Como exigir aplicações de clientes aprovadas para acesso](app-based-conditional-access.md)a aplicações na nuvem com Acesso Condicional .

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada

A Azure AD suporta vários dos protocolos de autenticação e autorização mais utilizados, incluindo a autenticação do legado. Como evitar que as aplicações que utilizem a autenticação do legado acedam aos recursos do seu inquilino? A recomendação é apenas bloqueá-los com uma política de acesso condicional. Se necessário, permite que apenas certos utilizadores e localizações específicas da rede utilizem aplicações baseadas na autenticação do legado. Para mais informações, consulte [Como bloquear a autenticação do legado à Azure AD com Acesso Condicional](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Teste a sua política

Antes de lançar uma política em produção, deve testar-se para verificar se se comporta como esperado.

1. Criar utilizadores de teste
1. Criar um plano de teste
1. Configurar a política
1. Avaliar um sessão de inscrição simulada
1. Teste a sua política
1. Limpeza

### <a name="create-test-users"></a>Criar utilizadores de teste

Para testar uma política, crie um conjunto de utilizadores semelhantes aos utilizadores no seu ambiente. A criação de utilizadores de testes permite-lhe verificar se as suas políticas funcionam como esperado antes de afetar utilizadores reais e potencialmente perturbar o seu acesso a apps e recursos. 

Algumas organizações têm inquilinos de teste para este fim. No entanto, pode ser difícil recriar todas as condições e aplicações num inquilino de teste para testar totalmente o resultado de uma política. 

### <a name="create-a-test-plan"></a>Criar um plano de teste

O plano de teste é importante para ter uma comparação entre os resultados esperados e os resultados reais. Deve ter sempre uma expectativa antes de testar algo. A tabela que se segue descreve casos de teste exemplo. Ajuste os cenários e resultados esperados com base na configuração das suas políticas ca.

|Política |Cenário |Resultado esperado | Resultado |
|---|---|---|---|
|[Exigir MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Utilizador autorizado assina na *App* enquanto está numa localização/trabalho confiável|Utilizador não é solicitado ao MFA| |
|[Exigir MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Utilizador autorizado assina na *App* sem localização/trabalho confiável|O utilizador é solicitado ao MFA e pode iniciar sessão com sucesso| |
|[Exigir MFA (para administrador)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Global Admin assina na *App*|Administrador é solicitado ao MFA| |
|[Inícios de sessão de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|User assina na *App* usando um [navegador Tor](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|Administrador é solicitado ao MFA| |
|[Gestão de Dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Utilizador autorizado tenta iniciar sessão a partir de um dispositivo autorizado|Acesso Concedido| |
|[Gestão de Dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Utilizador autorizado tenta iniciar sessão a partir de um dispositivo não autorizado|Acesso bloqueado| |
|[Alteração de palavra-passe para utilizadores de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Utilizador autorizado tenta iniciar sessão com credenciais comprometidas (sinal de alto risco)|O utilizador é solicitado a alterar a palavra-passe ou o acesso é bloqueado com base na sua política| |

### <a name="configure-the-policy"></a>Configurar a política

Gerir as políticas de Acesso Condicional é uma tarefa manual. No portal Azure, pode gerir as suas políticas de Acesso Condicional numa localização central - a página de Acesso Condicional. Um ponto de entrada na página de Acesso Condicional é a secção **de Segurança** no painel de navegação **do Diretório Ativo.** 

![Acesso Condicional](media/plan-conditional-access/03.png)

Se quiser saber mais sobre como criar políticas de Acesso Condicional, consulte [RequireM para aplicações específicas com Acesso Condicional de Diretório Ativo Azure](app-based-mfa.md). Este arranque rápido ajuda-o a:

- Familiarize-se com a interface do utilizador.
- Obtenha uma primeira impressão de como funciona o Acesso Condicional. 

### <a name="evaluate-a-simulated-sign-in"></a>Avaliar um sessão de inscrição simulada

Agora que configuraste a tua política de Acesso Condicional, provavelmente queres saber se funciona como esperado. Como primeiro passo, utilize o Acesso Condicional e se a [ferramenta política](what-if-tool.md) simular um sessão do utilizador do teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.

>[!NOTE]
> Embora uma execução simulada lhe dê a impressão do impacto que uma política de Acesso Condicional tem, não substitui um teste real.

### <a name="test-your-policy"></a>Teste a sua política

Execute os casos de teste de acordo com o seu plano de teste. Neste passo, você executa um teste de ponta a ponta de cada política para os seus utilizadores de teste para garantir que cada política se comporta corretamente. Utilize os cenários acima criados para executar cada teste.

É importante certificar-se de que testa os critérios de exclusão de uma política. Por exemplo, pode excluir um utilizador ou grupo de uma política que exija MFA. Teste se os utilizadores excluídos forem solicitados para mfa, porque a combinação de outras políticas pode exigir MFA para esses utilizadores.

### <a name="cleanup"></a>Limpeza

O procedimento de limpeza consiste nos seguintes passos:

1. Desativar a apólice.
1. Remova os utilizadores e grupos designados.
1. Elimine os utilizadores do teste.  

## <a name="move-to-production"></a>Mover para produção

Quando as novas políticas estiverem prontas para o seu ambiente, implemente-as por fases:

- Fornecer comunicação de mudança interna aos utilizadores finais.
- Comece com um pequeno conjunto de utilizadores e verifique se a apólice se comporta como esperado.
- Quando expande uma política para incluir mais utilizadores, continue a excluir todos os administradores. Excluindo os administradores garante que alguém ainda tem acesso a uma apólice se for necessária uma mudança.
- Aplicar uma política a todos os utilizadores apenas se for necessário.

Como uma boa prática, crie pelo menos uma conta de utilizador que seja:

- Dedicado à administração de políticas
- Excluído de todas as suas políticas

## <a name="rollback-steps"></a>Etapas de reversão

Caso precise de reverter as suas políticas recentemente implementadas, use uma ou mais das seguintes opções para reverter:

1. **Desativar a política** - Desativar uma apólice garante que não se aplica quando um utilizador tenta iniciar sessão. Podesempre voltar e ativar a apólice quando quiser usá-la.

   ![Desativar política](media/plan-conditional-access/07.png)

1. **Excluir um utilizador/grupo de uma apólice** - Se um utilizador não conseguir aceder à aplicação, pode optar por excluir o utilizador da política

   ![Exluce utilizadores](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Esta opção deve ser utilizada com moderação, apenas em situações em que o utilizador é de confiança. O utilizador deve ser adicionado de volta à política ou grupo o mais rapidamente possível.

1. **Apagar a política** - Se a política já não for necessária, apague-a.

## <a name="next-steps"></a>Passos seguintes

Consulte a Documentação de [Acesso Condicional da Azure AD](index.yml) para obter uma visão geral das informações disponíveis.
