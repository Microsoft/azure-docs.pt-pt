---
title: Planear políticas de acesso condicional no Azure Active Directory | Documentos da Microsoft
description: Neste artigo, irá aprender a planear políticas de acesso condicional do Azure Active Directory.
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
ms.openlocfilehash: 3fca84a71e1ede572e3889f973248db158115bec
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655506"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Como: Planear a implementação de acesso condicional no Azure Active Directory

Planear a implementação de acesso condicional é fundamental certificar-se de que obter a estratégia de acesso necessária para aplicações e recursos na sua organização. Passam a maior parte do seu tempo durante a fase de planeamento da implementação para criar várias políticas que necessita para conceder ou bloquear o acesso aos seus utilizadores sob as condições que escolher. Este documento explica os passos que deve efetuar para implementar políticas de acesso condicional seguras e eficientes. Antes de começar, certifique-se de que compreende como [acesso condicional](overview.md) funciona e quando deve utilizá-lo.

## <a name="what-you-should-know"></a>O que deve saber

Pense o acesso condicional como uma estrutura que permite-lhe controlar o acesso a aplicações e recursos, em vez de uma funcionalidade autónoma da sua organização. Consequentemente, algumas definições de acesso condicional requerem recursos adicionais para ser configurado. Por exemplo, pode configurar uma política que responde a um específico [nível de risco de início de sessão](../identity-protection/howto-sign-in-risk-policy.md#what-is-the-sign-in-risk-policy). No entanto, uma política que se baseia num nível de risco de início de sessão requer [proteção de identidade do Azure Active Directory](../identity-protection/overview.md) seja ativado.

Se são necessárias funcionalidades adicionais, também precisará de obter relacionados de licenças. Por exemplo, enquanto o acesso condicional é uma funcionalidade do Azure AD Premium P1, proteção de identidade necessita de uma licença do Azure AD Premium P2.

Existem dois tipos de políticas de acesso condicional: linha de base e padrão. R [política de linha de base](baseline-protection.md) é uma política de acesso condicional predefinida. O objetivo dessas diretivas é certificar-se de que tem, pelo menos, o nível de linha de base de segurança ativada. Políticas de linha de base. Políticas de linha de base estão disponíveis em todas as edições do Azure AD e fornecem apenas as opções de personalização limitada. Se um cenário necessitar de mais flexibilidade, desativar a política de linha de base e implementar os seus requisitos numa política personalizada do padrão.

Numa política de acesso condicional padrão, é possível personalizar todas as definições para ajustar a política aos seus requisitos empresariais. As políticas padrão requerem uma licença do Azure AD Premium P1.

## <a name="draft-policies"></a>Políticas de rascunho

Active Directory acesso condicional do Azure permite-lhe colocar a proteção das suas aplicações na cloud para um novo nível. Nesse novo nível, como pode aceder a uma aplicação na cloud de se baseia numa avaliação de política dinâmica em vez de uma configuração de acesso estático. Com uma política de acesso condicional, define uma resposta (**fazê-lo**) para uma condição de acesso (**quando isso acontece,** ).

![Motivo e resposta](./media/plan-conditional-access/10.png)

Defina cada política de acesso condicional que pretende implementar utilizando este modelo de planeamento. O exercício de planeamento:

- Ajuda a descrever as respostas e as condições para cada política.
- Resultados num catálogo de política de acesso condicional bem documentado para a sua organização. 

Pode utilizar o seu catálogo para avaliar se a sua implementação de política reflete os requisitos de negócios de sua organização. 

Utilize o modelo de exemplo seguinte para criar políticas de acesso condicional para a sua organização:

|Quando *isso* acontece:|Em seguida, efetue *isso*:|
|-|-|
|É efetuada uma tentativa de acesso:<br>-Para uma aplicação na cloud *<br>– por utilizadores e grupos*<br>A utilizar:<br>-A condição 1 (por exemplo, fora da rede empresarial)<br>-Condição 2 (por exemplo, plataformas de dispositivos)|Bloquear o acesso à aplicação|
|É efetuada uma tentativa de acesso:<br>-Para uma aplicação na cloud *<br>– por utilizadores e grupos*<br>A utilizar:<br>-A condição 1 (por exemplo, fora da rede empresarial)<br>-Condição 2 (por exemplo, plataformas de dispositivos)|Conceder acesso com (e):<br>-Requisito 1 (por exemplo, MFA)<br>-Requisito 2 (por exemplo, a conformidade de dispositivos)|
|É efetuada uma tentativa de acesso:<br>-Para uma aplicação na cloud *<br>– por utilizadores e grupos*<br>A utilizar:<br>-A condição 1 (por exemplo, fora da rede empresarial)<br>-Condição 2 (por exemplo, plataformas de dispositivos)|Conceder acesso com (ou):<br>-Requisito 1 (por exemplo, MFA)<br>-Requisito 2 (por exemplo, a conformidade de dispositivos)|

No mínimo, **quando isso acontece** define o principal (**quem**) que tenta aceder a uma aplicação da cloud (**o que**). Se necessário, também pode incluir **como** é efetuada uma tentativa de acesso. No acesso condicional, os elementos que definem quem, o que e como são conhecidos como condições. Para obter mais informações, consulte [quais são condições no Azure Active Directory condicional acesso?](conditions.md) 

Com o **, em seguida, fazer isso**, definir a resposta da sua política a uma condição de acesso. Em sua resposta, bloquear ou conceder acesso com requisitos adicionais, por exemplo, o multi-factor authentication (MFA). Para obter uma descrição completa, consulte [o que são acedidos controles no Azure Active Directory condicional acesso?](controls.md)  

A combinação de condições com controlos de acesso representa uma política de acesso condicional.

![Motivo e resposta](./media/plan-conditional-access/51.png)

Para obter mais informações, consulte [o que é necessário para tornar uma política de trabalho](best-practices.md#whats-required-to-make-a-policy-work).

Neste momento, é um bom momento para decidir qual a um padrão de nomeação para as suas políticas. O padrão de nomenclatura ajuda-o a encontrar as políticas e compreender suas finalidades sem abri-las no portal de administração do Azure. Nomeie a sua política para mostrar:

- Um número de sequência
- A aplicação na cloud aplica-se a
- A resposta
- Quem se aplica
- Quando aplica-se de (se aplicável)
 
![Padrão de nomenclatura](./media/plan-conditional-access/11.png)

Enquanto um nome descritivo ajuda-o a manter uma descrição geral da sua implementação de acesso condicional, o número de sequência é útil se precisa fazer referência uma política numa conversa. Por exemplo, se conversa com um colega administrador no telefone, pode pedi-las para abrir a política EM063 resolver um problema.

Por exemplo, o seguinte nome indica que a política exige a MFA para utilizadores em redes externas através da aplicação de CRP de Dynamics de marketing:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Para além das políticas das Active Directory, é recomendável implementar desativada também políticas que atuam como secundárias [controlos de acesso resiliente em cenários de falha/emergência](../authentication/concept-resilient-controls.md). O padrão de nomenclatura para as políticas de contingência deve incluir alguns itens: 

- `ENABLE IN EMERGENCY` no início para tornar o nome se destacam entre as outras políticas.
- O nome da interrupção que se devem aplicar para.
- Um número de sequência de ordenação para ajudar o administrador para saber a ordem pela qual as políticas devem ser ativadas. 

Por exemplo, o seguinte nome indica que esta política é a primeira diretiva fora de quatro que deve ativar se houver uma interrupção de MFA:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Plano de políticas

Quando planear a sua solução de política de acesso condicional, avalie se são necessários criar políticas para alcançar os resultados seguintes. 

### <a name="block-access"></a>Bloquear o acesso

A opção de bloquear o acesso é poderosa porque ele:

- Supera todas as outras atribuições de um utilizador
- Tem o poder de bloquear toda a organização de iniciar sessão seu inquilino
 
Se desejar bloquear o acesso para todos os utilizadores, deve excluir, pelo menos, um usuário (contas de acesso de emergência normalmente) da política. Para obter mais informações, consulte [selecionar utilizadores e grupos](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Requerer MFA

Para simplificar a experiência de início de sessão dos utilizadores, pode querer permitir-lhes iniciar sessão suas aplicações na cloud com um nome de utilizador e uma palavra-passe. No entanto, normalmente, há, pelo menos, alguns cenários para o qual é recomendado para exigir uma forma mais forte de verificação da conta. Com uma política de acesso condicional, pode limitar o requisito para a MFA para determinados cenários. 

Casos de utilização comuns para exigir a MFA se o acesso:

- [Por administradores](howto-baseline-protect-administrators.md)
- [Para aplicações específicas](app-based-mfa.md) 
- [A partir de localizações de rede, não confia](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Responder a contas potencialmente comprometidas

Com as políticas de acesso condicional, é possível implementar respostas automáticas para inícios de sessão a partir de identidades potencialmente comprometidas. A probabilidade de que uma conta foi comprometida é expressa na forma de níveis de risco. Há dois níveis de risco calculados pela proteção de identidade: início de sessão de risco e o risco de utilizador. Para implementar uma resposta a um risco de início de sessão, tem duas opções:

- [A condição de início de sessão de risco](conditions.md#sign-in-risk) na política de acesso condicional
- [A política de risco de início de sessão](../identity-protection/howto-sign-in-risk-policy.md) na proteção de identidade 

O risco de início de sessão como condição de endereçamento é o método preferencial, uma vez que lhe dá mais opções de personalização.

O nível de risco de utilizador apenas está disponível como [política de risco do utilizador](../identity-protection/howto-user-risk-policy.md) na proteção de identidade. 

Para obter mais informações, consulte [o que é o Azure Active Directory Identity Protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Exigir dispositivos geridos

A proliferação de dispositivos suportados para aceder aos recursos da cloud ajuda a aumentar a produtividade dos seus utilizadores. Por outro lado, provavelmente não quer determinados recursos no seu ambiente para ser acedida por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, deve exigir que os utilizadores só podem acedê-los usando um dispositivo gerido. Para obter mais informações, consulte [como exigir que os dispositivos geridos para aceder à aplicação de cloud com o acesso condicional](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Exigir aplicações aprovados do cliente

Uma das primeiras decisões que precisa fazer trazer os seus próprios dispositivos cenários (BYOD), é a necessidade de gerir todo o dispositivo ou apenas os dados no mesmo. Os seus funcionários utilizem os dispositivos móveis para tarefas pessoais e profissionais. Certificar-se de que seus funcionários são produtivo, também quer evitar perda de dados. Com o acesso condicional do Azure Active Directory (Azure AD), pode restringir o acesso às suas aplicações na cloud para aplicações aprovadas do cliente que podem proteger dados da sua empresa. Para obter mais informações, consulte [como requerer aplicações aprovadas do cliente de acesso à aplicação de cloud com o acesso condicional](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada

O Azure AD suporta vários protocolos de autenticação e autorização mais amplamente utilizados incluindo antigos de autenticação. Como pode impedir a aplicações que utilizam autenticação legada de aceder a recursos do seu inquilino? A recomendação é simplesmente bloqueá-los com uma política de acesso condicional. Se necessário, permite que apenas determinados utilizadores e localizações de rede específicas utilizar as aplicações que se baseiam em antigos de autenticação. Para obter mais informações, consulte [como bloquear antigos de autenticação para o Azure AD com o acesso condicional](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Testar a política

Antes de implementar uma política para a produção, deve testar é verificar se funciona conforme o esperado.

1. Criar utilizadores de teste
1. Criar um plano de teste
1. Configurar a política
1. Avaliar um simulado início de sessão
1. Testar a política
1. Limpeza

### <a name="create-test-users"></a>Criar utilizadores de teste

Para testar uma política, crie um conjunto de utilizadores que é semelhante aos utilizadores no seu ambiente. Criar utilizadores de teste permite-lhe verificar se as suas políticas funcionam conforme esperado antes de afetar os utilizadores reais e interromper potencialmente o respetivo acesso a aplicações e recursos. 

Algumas organizações têm inquilinos de teste para esta finalidade. No entanto, pode ser difícil recriar todas as condições e aplicações num inquilino de teste para testar totalmente o resultado de uma política. 

### <a name="create-a-test-plan"></a>Criar um plano de teste

O plano de teste é importante ter uma comparação entre os resultados esperados e os resultados reais. Deve ter sempre uma expectativa antes de testar algo. A tabela seguinte descreve casos de teste de exemplo. Ajuste os cenários e os resultados esperados, com base em como as políticas de AC são configuradas.

|Política |Cenário |Resultado esperado | Resultado |
|---|---|---|---|
|[Exigir a MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Autorizado o utilizador iniciar sessão no *aplicação* enquanto estiverem num local confiável / de trabalho|Não é pedido ao utilizador para a MFA| |
|[Exigir a MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Autorizado o utilizador iniciar sessão no *aplicação* enquanto estiverem não num local confiável / de trabalho|É-lhe pedido para MFA de utilizador e pode iniciar sessão com êxito| |
|[Exigir a MFA (por administrador)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Administrador global, iniciar sessão numa *aplicação*|Administrador é solicitado a MFA| |
|[Inícios de sessão de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Utilizador inicia sessão na *App* usando um [browser de Tor](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|Administrador é solicitado a MFA| |
|[Gestão de Dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Utilizador autorizado tenta iniciar sessão a partir de um dispositivo autorizado|Acesso concedido| |
|[Gestão de Dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Autorizado o utilizador tentou iniciar sessão a partir de um dispositivo não autorizado|Acesso bloqueado| |
|[Alteração de palavra-passe para utilizadores de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Autorizado tentativas de utilizador para iniciar sessão com credenciais comprometidas (alto risco iniciar sessão)|É pedido ao utilizador para alterar a palavra-passe ou o acesso é bloqueado com base na sua política| |

### <a name="configure-the-policy"></a>Configurar a política

Gestão de políticas de acesso condicional é uma tarefa manual. No portal do Azure, pode gerir as políticas de acesso condicional num local central – a página de acesso condicional. Um ponto de entrada para a página de acesso condicional é o **Security** secção a **do Active Directory** painel de navegação. 

![Acesso Condicional](media/plan-conditional-access/03.png)

Se quiser saber mais sobre como criar políticas de acesso condicional, consulte [exigir MFA para aplicações específicas com o Azure Active Directory condicional acesso](app-based-mfa.md). Este início rápido ajuda-o a:

- Familiarize-se com a interface do usuário.
- Obtenha sua primeira impressão do funcionamento do acesso condicional. 

### <a name="evaluate-a-simulated-sign-in"></a>Avaliar um simulado início de sessão

Agora que configurou a política de acesso condicional, provavelmente quer saber se ele funciona conforme esperado. Como primeiro passo, utilize o acesso condicional [e se a ferramenta de política](what-if-tool.md) para simular um início de sessão do seu utilizador de teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.

>[!NOTE]
> Embora uma execução simulada-lhe impressão do impacto que tem uma política de acesso condicional, não substitua um execução de teste real.

### <a name="test-your-policy"></a>Testar a política

Executa casos de teste, de acordo com seu plano de teste. Neste passo, vai executar através de um teste de ponto-a-ponto de cada política para os seus utilizadores de teste para se certificar de que cada política funciona corretamente. Utilize os cenários criados acima, para executar cada teste.

É importante certificar-se de que os critérios de exclusão de uma política de teste. Por exemplo, pode excluir um utilizador ou grupo de uma política que requer a MFA. Teste se os utilizadores excluídos são lhe for pedidos para a MFA, porque a combinação de outras políticas poderá exigir a MFA para os utilizadores.

### <a name="cleanup"></a>Limpeza

O procedimento de limpeza é composta pelos seguintes passos:

1. Desative a política.
1. Remova os utilizadores e grupos atribuídos.
1. Elimine os utilizadores de teste.  

## <a name="move-to-production"></a>Mover para produção

Quando as novas políticas são prontas para o seu ambiente, implantá-los em fases:

- Fornece comunicação de alteração internas aos utilizadores finais.
- Comece com um pequeno conjunto de utilizadores e certifique-se de que a política funciona conforme esperado.
- Quando expande uma política para incluir mais usuários, continue excluir todos os administradores. Excluir os administradores garante que alguém, ainda tem acesso a uma política se for necessária uma alteração.
- Aplica uma política a todos os utilizadores apenas se é obrigatório.

Como melhor prática, crie pelo menos uma conta que é:

- Dedicada à administração da diretiva
- Excluído da todas as suas políticas

## <a name="rollback-steps"></a>Passos de reversão

Caso precise reverter as políticas recentemente implementadas, utilize um ou mais das seguintes opções para reverter:

1. **Desativar a política** -desativar uma política torna-se de que não se aplica quando um utilizador tenta iniciar sessão. Pode sempre voltar atrás e ativar a política quando pretende utilizá-lo.

   ![Desativar política](media/plan-conditional-access/07.png)

1. **Excluir um usuário / de uma política de grupo** -se um utilizador não consegue aceder à aplicação, pode optar por excluir o utilizador da política

   ![Utilizadores de Exluce](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Esta opção deve ser usada com moderação, somente em situações em que o utilizador é fidedigno. O utilizador deve ser adicionado novamente para a política ou grupo logo que possível.

1. **Eliminar a política** - se a política já não é necessária, elimine-o.

## <a name="next-steps"></a>Passos Seguintes

Confira [documentação de acesso condicional do Azure AD](index.yml) para obter uma descrição geral das informações disponíveis.
