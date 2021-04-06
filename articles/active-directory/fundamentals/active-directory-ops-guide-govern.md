---
title: Guia de referência de operações de governação do Diretório Ativo Azure
description: Este guia de referência de operações descreve os controlos e ações que deve tomar para garantir a gestão da governação
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: ba3ca140abe36a31ffa03422420ea537bbe4f39a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935755"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Guia de referência de operações de governação do Diretório Ativo Azure

Esta secção do guia de referência de [operações Azure AD](active-directory-ops-guide-intro.md) descreve os controlos e ações que deve tomar para avaliar e atestar o acesso concedido identidades, auditoria e controlo ao ambiente.

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas de governação à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir os proprietários a tarefas-chave

A Gestão do Diretório Ativo Azure requer a execução contínua de tarefas e processos operacionais fundamentais, que podem não fazer parte de um projeto de implantação. Ainda é importante que crie estas tarefas para otimizar o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Registos de auditoria do Archive Azure AD no sistema SIEM | Equipa de Operações InfoSec |
| Descubra aplicações que são geridas fora do cumprimento | Equipa de Operações do IAM |
| Reveja regularmente o acesso às aplicações | Equipa de Arquitetura InfoSec |
| Reveja regularmente o acesso a identidades externas | Equipa de Arquitetura InfoSec |
| Reveja regularmente quem tem papéis privilegiados | Equipa de Arquitetura InfoSec |
| Definir portões de segurança para ativar funções privilegiadas | Equipa de Arquitetura InfoSec |
| Reveja regularmente as subvenções de consentimento | Equipa de Arquitetura InfoSec |
| Catálogos de Design e Pacotes de Acesso para aplicações e recursos baseados em colaboradores da organização | Proprietários de aplicativos |
| Definir Políticas de Segurança para atribuir aos utilizadores o acesso a pacotes | Equipa infoSec + Proprietários de Aplicações |
| Se as políticas incluem fluxos de trabalho de aprovação, reveja regularmente as aprovações do fluxo de trabalho | Proprietários de aplicativos |
| Rever exceções nas políticas de segurança, tais como políticas de acesso condicional, utilizando revisões de acesso | Equipa de Operações InfoSec |

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário para tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada pelo proprietário

- [Atribuir funções de administrador no Azure Active Directory](../roles/permissions-reference.md)
- [Governação no Azure](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>Teste de alterações de configuração

Existem alterações que requerem considerações especiais durante os testes, desde técnicas simples como a elaboração de um subconjunto-alvo dos utilizadores até à implementação de uma alteração num inquilino de teste paralelo. Se não implementou uma estratégia de teste, deve definir uma abordagem de teste com base nas diretrizes do quadro abaixo:

| Scenario| Recomendação |
|-|-|
|Alteração do tipo de autenticação de federado para PHS/PTA ou vice-versa| Utilize [o roll-out encenado](../hybrid/how-to-connect-staged-rollout.md) para testar o impacto da alteração do tipo de autenticação.|
|Elaborando uma nova política de acesso condicional (CA) ou Política de Proteção de Identidade|Crie uma nova política de Acesso Condicional e atribua aos utilizadores de teste.|
|Embarque num ambiente de teste de uma aplicação|Adicione a aplicação a um ambiente de produção, esconda-a do painel MyApps e atribua-a aos utilizadores de teste durante a fase de garantia de qualidade (QA).|
|Alteração das regras de sincronização|Execute as alterações num teste Azure AD Connect com a mesma configuração que está atualmente em produção, também conhecida como modo de encenação, e analise os Resultados do CSExport. Se estiver satisfeito, troque para a produção quando estiver pronto.|
|Mudança de marca|Teste em um inquilino de teste separado.|
|Lançando uma nova funcionalidade|Se o suporte da funcionalidade chegar a um conjunto de utilizadores alvo, identifique os utilizadores piloto e construa.a partir. Por exemplo, o reset da palavra-passe de autosserviço e a autenticação de vários fatores podem visar utilizadores ou grupos específicos.|
|Corte de um pedido de um fornecedor de identidade no local (IdP), por exemplo, Ative Directory, para Azure AD|Se a aplicação suportar várias configurações de IdP, por exemplo, Salesforce, configurar tanto e testar Azure AD durante uma janela de alteração (caso a aplicação introduza a página HRD). Se a aplicação não suportar vários IDPs, marque os testes durante uma janela de controlo de alteração e tempo de inatividade do programa.|
|Atualizar regras dinâmicas de grupo|Criar um grupo dinâmico paralelo com a nova regra. Compare com o resultado calculado, por exemplo, executar PowerShell com a mesma condição.<br>Se o teste passar, troque os locais onde o antigo grupo foi utilizado (se possível).|
|Licenças de produtos migram|Consulte a [alteração da licença para um único utilizador num grupo licenciado no Azure Ative Directory](../enterprise-users/licensing-groups-change-licenses.md).|
|Alterar regras de FS da AD, tais como Autorização, Emissão, MFA|Utilize a alegação do grupo para direcionar o subconjunto dos utilizadores.|
|Alterar experiência de autenticação AD FS ou alterações semelhantes em toda a exploração|Crie uma quinta paralela com o mesmo nome de anfitrião, implemente alterações config, teste de clientes usando ficheiro HOSTS, regras de encaminhamento NLB ou encaminhamento semelhante.<br>Se a plataforma alvo não suportar ficheiros HOSTS (por exemplo dispositivos móveis), altere a mudança de controlo.|

## <a name="access-reviews"></a>Revisões de acesso

### <a name="access-reviews-to-applications"></a>Aceder a comentários a aplicações

Com o tempo, os utilizadores podem acumular acesso a recursos à medida que se deslocam através de diferentes equipas e posições. É importante que os proprietários de recursos revejam regularmente o acesso às aplicações e removam privilégios que já não são necessários ao longo do ciclo de vida dos utilizadores. As [análises](../governance/access-reviews-overview.md) de acesso a AD do AZure permitem às organizações gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. Os proprietários de recursos devem rever regularmente o acesso dos utilizadores para garantir que apenas as pessoas certas tenham acesso continuado. Idealmente, você deve considerar a utilização de comentários de acesso Azure AD para esta tarefa.

![Aceder à página inicial das avaliações](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Cada utilizador que interage com comentários de acesso deve ter uma licença Azure AD Premium P2 paga.

### <a name="access-reviews-to-external-identities"></a>Aceder a comentários a identidades externas

É crucial manter o acesso a identidades externas limitadas apenas aos recursos necessários, durante o tempo que for necessário. Estabeleça um processo regular de revisão de acesso automatizado para todas as identidades externas e acesso a aplicações utilizando [avaliações de acesso](../governance/access-reviews-overview.md)Azure AD . Se um processo já existir no local, considere a utilização de comentários de acesso Azure AD. Uma vez que um pedido é aposentado ou deixou de ser utilizado, remova todas as identidades externas que tiveram acesso à aplicação.

> [!NOTE]
> Cada utilizador que interage com comentários de acesso deve ter uma licença Azure AD Premium P2 paga.

## <a name="privileged-account-management"></a>Gestão de conta privilegiada

### <a name="privileged-account-usage"></a>Utilização privilegiada da conta

Os hackers muitas vezes visam contas de administração e outros elementos de acesso privilegiado para obter rapidamente acesso a dados e sistemas sensíveis. Uma vez que os utilizadores com funções privilegiadas tendem a acumular-se ao longo do tempo, é importante rever e gerir o acesso administrativo regularmente e fornecer acesso privilegiado just-in-time aos recursos Azure AD e Azure.

Se não existir nenhum processo na sua organização para gerir contas privilegiadas, ou se atualmente tiver administradores que utilizem as suas contas regulares de utilizador para gerir serviços e recursos, deverá começar imediatamente a utilizar contas separadas, por exemplo, uma para atividades regulares do dia-a-dia; o outro para acesso privilegiado e configurado com MFA. Melhor ainda, se a sua organização tem uma subscrição Azure AD Premium P2, então deve implementar imediatamente [a Azure AD Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md#license-requirements) (PIM). No mesmo token, você também deve rever essas contas privilegiadas e [atribuir papéis menos privilegiados](../roles/security-planning.md) se aplicável.

Outro aspeto da gestão privilegiada das contas que deve ser implementada é a definição de [revisões](../governance/access-reviews-overview.md) de acesso para essas contas, manualmente ou [automatizadas através da PIM.](../privileged-identity-management/pim-how-to-perform-security-review.md)

#### <a name="privileged-account-management-recommended-reading"></a>Gestão de conta privilegiada leitura recomendada

- [Funções em Azure AD Gestão de Identidade Privilegiada](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>Contas de acesso de emergência

As organizações devem criar contas de emergência para estarem [preparadas](../roles/security-emergency-access.md) para gerir o AZure AD em casos como interrupções de autenticação como:

- Componentes de paralisação de infraestruturas de autenticação (AD FS, AD no local, serviço MFA)
- Volume de negócios do pessoal administrativo

Para evitar ser inadvertidamente bloqueado fora do seu inquilino porque não pode iniciar sessão ou ativar uma conta individual de um utilizador existente como administrador, deve criar duas ou mais contas de emergência e garantir que são implementadas e alinhadas com [as melhores práticas da Microsoft](../roles/security-planning.md) e quebrar os [procedimentos de vidro.](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency)

### <a name="privileged-access-to-azure-ea-portal"></a>Acesso privilegiado ao portal Azure EA

O [portal Azure Enterprise Agreement (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) permite-lhe criar subscrições Azure contra um master Enterprise Agreement, que é um papel poderoso dentro da empresa. É comum arrancar a criação deste portal antes mesmo de colocar o Azure AD no lugar, por isso é necessário usar identidades AZure AD para bloqueá-lo, remover contas pessoais do portal, garantir que a delegação adequada está no lugar, e mitigar o risco de bloqueio.

Para ser claro, se o nível de autorização do portal EA está atualmente definido como "modo misto", deve remover quaisquer contas da Microsoft de todos os [acessos privilegiados](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) no portal EA e configurar o portal EA para utilizar apenas as contas AD do Azure. Se as funções delegadas do portal EA não forem configuradas, também deverá encontrar e implementar funções delegadas para departamentos e contas.

#### <a name="privileged-access-recommended-reading"></a>Leitura recomendada de acesso privilegiado

- [Administrator role permissions in Azure Active Directory](../roles/permissions-reference.md) (Permissões de cargos de administrador no Azure Active Directory)

## <a name="entitlement-management"></a>Gestão de direitos

[A gestão de direitos (EM)](../governance/entitlement-management-overview.md) permite que os proprietários de aplicações aprovam recursos e os atribuam a personalidades específicas da organização (tanto internas como externas). A EM permite a inscrição e delegação de self-service aos empresários, mantendo ao mesmo tempo políticas de governação para conceder acesso, definir durações de acesso e permitir fluxos de trabalho de aprovação. 

> [!NOTE]
> A Azure AD Entitlement Management requer licenças Azure AD Premium P2.

## <a name="summary"></a>Resumo

Há oito aspetos para uma governação de identidade segura. Esta lista irá ajudá-lo a identificar as ações que deve tomar para avaliar e atestar o acesso concedido a identidades não privilegiadas e privilegiadas, auditoria e alterações de controlo ao ambiente.

- Atribua os proprietários a tarefas-chave.
- Implementar uma estratégia de teste.
- Use Azure AD Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções.
- Estabeleça um processo regular e automatizado de revisão de acessos para todos os tipos de identidades externas e acesso a aplicações.
- Estabeleça um processo de revisão de acesso para rever e gerir o acesso administrativo regularmente e fornecer acesso privilegiado just-in-time aos recursos Azure AD e Azure.
- Provisão de contas de emergência para ser preparado para gerir a Azure AD para interrupções inesperadas.
- Bloqueie o acesso ao portal Azure EA.
- Implementar a Gestão de Direitos para proporcionar acesso governado a uma recolha de recursos.

## <a name="next-steps"></a>Passos seguintes

Começa com as [verificações e ações operacionais da AZure AD](active-directory-ops-guide-ops.md).