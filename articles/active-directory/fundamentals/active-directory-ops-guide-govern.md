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
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74535460"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Guia de referência de operações de governação do Diretório Ativo Azure

Esta secção do guia de referência de [operações da AD Azure](active-directory-ops-guide-intro.md) descreve os controlos e ações que deve tomar para avaliar e atestar o acesso concedido a identidades não privilegiadas e privilegiadas, auditoria e alterações de controlo no ambiente.

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas de governação à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

A gestão do Diretório Ativo azure requer a execução contínua de tarefas e processos operacionais fundamentais, que podem não fazer parte de um projeto de lançamento. Ainda é importante que você configuraestas tarefas para otimizar o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Registos de auditoria da Archive Azure AD no sistema SIEM | Equipa de Operações infosec |
| Descubra aplicações que são geridas fora do cumprimento | Equipa de Operações do IAM |
| Rever regularmente o acesso às aplicações | Equipa de Arquitetura InfoSec |
| Reveja regularmente o acesso a identidades externas | Equipa de Arquitetura InfoSec |
| Reveja regularmente quem tem papéis privilegiados | Equipa de Arquitetura InfoSec |
| Defina portões de segurança para ativar papéis privilegiados | Equipa de Arquitetura InfoSec |
| Rever regularmente as bolsas de consentimento | Equipa de Arquitetura InfoSec |
| Catálogos de Design e Pacotes de Acesso para aplicações e recursos baseados para colaboradores da organização | Proprietários de aplicativos |
| Definir Políticas de Segurança para atribuir utilizadores a pacotes de acesso | InfoSec team + Proprietários de Aplicações |
| Se as políticas incluem fluxos de trabalho de aprovação, reveja regularmente as aprovações de fluxos de trabalho | Proprietários de aplicativos |
| Rever exceções nas políticas de segurança, tais como políticas de acesso condicional, utilizando avaliações de acesso | Equipa de Operações infosec |

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário a tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada do proprietário

- [Atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governação no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Teste de alterações de configuração

Existem alterações que requerem considerações especiais ao testar, desde técnicas simples como a elaboração de um subconjunto de utilizadores alvo até à implementação de uma alteração num inquilino de teste paralelo. Se não implementou uma estratégia de teste, deve definir uma abordagem de teste com base nas diretrizes da tabela abaixo:

| Cenário| Recomendação |
|-|-|
|Alteração do tipo de autenticação de federado para PHS/PTA ou vice-versa| Utilize o [lançamento encenado](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) para testar o impacto da alteração do tipo de autenticação.|
|Elaboração de uma nova política de acesso condicional (CA) ou política de proteção da identidade|Crie uma nova Política de CA e atribua aos utilizadores de teste.|
|Embarcar num ambiente de teste de uma aplicação|Adicione a aplicação a um ambiente de produção, esconda-a do painel MyApps e atribua-a aos utilizadores durante a fase de garantia de qualidade (QA).|
|Alteração das regras de sincronização|Execute as alterações num teste Azure AD Connect com a mesma configuração que está atualmente em produção, também conhecida como modo de encenação, e analise os Resultados da CSExport. Se estiver satisfeito, troque para a produção quando estiver pronto.|
|Mudança de marca|Teste em um inquilino de teste separado.|
|Lançando uma nova funcionalidade|Se a funcionalidade suportar, desenrolar-se para um conjunto de utilizadores alvo, identifique os utilizadores-piloto e construa-se. Por exemplo, o reset de palavra-passe de autosserviço e a autenticação de vários fatores podem visar utilizadores ou grupos específicos.|
|Corte de uma aplicação de um fornecedor de identidade no local (IDP), por exemplo, Diretório Ativo, para AD Azure|Se a aplicação suportar várias configurações de IDP, por exemplo, Salesforce, configurar tanto como testar a AD Azure durante uma janela de mudança (caso a aplicação introduza a página HRD). Se a aplicação não suportar vários IDPs, agende os testes durante uma janela de controlo de mudança e tempo de paragem do programa.|
|Atualizar regras dinâmicas do grupo|Crie um grupo dinâmico paralelo com a nova regra. Compare com o resultado calculado, por exemplo, executar PowerShell com a mesma condição.<br>Se passar o teste, troque os locais onde o grupo antigo foi utilizado (se possível).|
|Licenças de produtos de migração|Consulte [a alteração da licença para um único utilizador num grupo licenciado no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|Alterar regras da AD FS tais como Autorização, Emissão, MFA|Utilize a alegação do grupo para direcionar o subconjunto dos utilizadores.|
|Alterar a experiência de autenticação AD FS ou alterações semelhantes em toda a exploração|Crie uma quinta paralela com o mesmo nome de anfitrião, implemente alterações de config, teste de clientes usando ficheiro SAL, regras de encaminhamento NLB ou encaminhamento semelhante.<br>Se a plataforma alvo não suportar ficheiros HOSTS (por exemplo dispositivos móveis), alterar o controlo.|

## <a name="access-reviews"></a>Revisões de acesso

### <a name="access-reviews-to-applications"></a>Avaliações de acesso a aplicações

Com o tempo, os utilizadores podem acumular acesso aos recursos à medida que se deslocam através de diferentes equipas e posições. É importante que os proprietários de recursos revejam regularmente o acesso às aplicações e removam privilégios que já não são necessários ao longo do ciclo de vida dos utilizadores. As [análises](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) de acesso à AD Azure permitem às organizações gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. Os proprietários de recursos devem rever regularmente o acesso dos utilizadores para garantir que apenas as pessoas certas tenham acesso continuado. Idealmente, deve considerar a utilização de avaliações de acesso a AD Azure para esta tarefa.

![Página inicial de comentários de acesso](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Cada utilizador que interaja com avaliações de acesso deve ter uma licença Azure AD Premium P2 paga.

### <a name="access-reviews-to-external-identities"></a>Avaliações de acesso a identidades externas

É crucial manter o acesso a identidades externas limitadaamente aos recursos necessários, durante o tempo que é necessário. Estabeleça um processo regular de revisão automatizada de acesso para todas as identidades externas e acesso à aplicação utilizando [avaliações](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)de acesso a AD Azure . Se já existir um processo no local, considere utilizar as avaliações de acesso da AD Azure. Uma vez que uma aplicação seja retirada ou já não seja utilizada, remova todas as identidades externas que tenham acesso à aplicação.

> [!NOTE]
> Cada utilizador que interaja com avaliações de acesso deve ter uma licença Azure AD Premium P2 paga.

## <a name="privileged-account-management"></a>Gestão privilegiada de conta

### <a name="privileged-account-usage"></a>Utilização privilegiada da conta

Os hackers muitas vezes visam contas de administração e outros elementos de acesso privilegiado para obter rapidamente acesso a dados e sistemas sensíveis.Uma vez que os utilizadores com funções privilegiadas tendem a acumular-se ao longo do tempo, é importante rever e gerir regularmente o acesso à administração e proporcionar acesso privilegiado aos recursos Azure AD e Azure.

Se não existir nenhum processo na sua organização para gerir contas privilegiadas, ou se tem atualmente administradores que utilizam as suas contas regulares de utilizadores para gerir serviços e recursos, deve começar imediatamente a utilizar contas separadas, por exemplo, uma para atividades regulares do dia-a-dia; o outro para acesso privilegiado e configurado com MFA. Melhor ainda, se a sua organização tiver uma subscrição Azure AD Premium P2, então deverá implementar imediatamente a [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). No mesmo sinal, deve também rever essas contas privilegiadas e [atribuir papéis menos privilegiados,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) se aplicável.

Outro aspeto da gestão privilegiada da conta que deve ser implementada é a definição de [avaliações](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) de acesso para essas contas, manual ou [automatizada através da PIM.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)

#### <a name="privileged-account-management-recommended-reading"></a>Leitura recomendada pela gestão de conta privilegiada

- [Funções na Gestão de Identidade Privilegiada da Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Contas de acesso de emergência

As organizações devem criar contas de emergência para estarem [preparadas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) para gerir a AD Azure para casos como interrupções de autenticação como:

- Componentes de paragem das infraestruturas de autenticação (AD FS, On-premises AD, serviço MFA)
- Volume de negócios do pessoal administrativo

Para evitar que seja inadvertidamente bloqueado fora do seu inquilino porque não pode iniciar ou ativar a conta de um utilizador individual existente como administrador, deve criar duas ou mais contas de emergência e garantir que são implementadas e alinhadas com as [melhores práticas da Microsoft](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) e [quebrar procedimentos de vidro.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)

### <a name="privileged-access-to-azure-ea-portal"></a>Acesso privilegiado ao portal Azure EA

O [portal Azure Enterprise Agreement (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) permite criar subscrições Azure contra um master Enterprise Agreement, que é um papel poderoso dentro da empresa. É comum arrancar a criação deste portal antes mesmo de colocar a AD Azure no lugar, por isso é necessário usar identidades Azure AD para bloqueá-lo, remover contas pessoais do portal, garantir que a delegação adequada está no lugar, e mitigar o risco de bloqueio.

Para ser claro, se o nível de autorização do portal EA está atualmente definido para "modo misto", deve remover quaisquer contas da Microsoft de todos os [acessos privilegiados](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) no portal EA e configurar o portal EA para utilizar apenas contas Azure AD. Se as funções delegadas do portal EA não estiverem configuradas, também deve encontrar e implementar funções delegadas para departamentos e contas.

#### <a name="privileged-access-recommended-reading"></a>Leitura recomendada de acesso privilegiado

- [Administrator role permissions in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) (Permissões de cargos de administrador no Azure Active Directory)

## <a name="entitlement-management"></a>Gestão de direitos

[A gestão de direitos (EM)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) permite que os proprietários de aplicações agregem recursos e os atribuam a personalidades específicas da organização (interna e externa). A EM permite a inscrição de autosserviço e delegação aos proprietários de empresas, mantendo ao mesmo tempo políticas de governação para conceder acesso, definir durações de acesso e permitir fluxos de trabalho de aprovação. 

> [!NOTE]
> A Azure AD Entitlement Management requer licenças Azure AD Premium P2.

## <a name="summary"></a>Resumo

Há oito aspetos para uma governação de identidade segura. Esta lista irá ajudá-lo a identificar as ações que deve tomar para avaliar e atestar o acesso concedido a identidades não privilegiadas e privilegiadas, auditoria e alterações de controlo no ambiente.

- Atribuir os proprietários a tarefas-chave.
- Implementar uma estratégia de teste.
- Utilize as Análises de Acesso AD Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis.
- Estabeleça um processo regular de revisão de acesso automatizado para todos os tipos de identidades externas e acesso à aplicação.
- Estabeleça um processo de revisão de acesso para rever e gerir regularmente o acesso à administração e proporcionar acesso privilegiado justo aos recursos da Azure AD e Azure.
- Provisão de contas de emergência para estar preparado para gerir a AD Azure para interrupções inesperadas.
- Bloqueie o acesso ao portal Azure EA.
- Implemente a Gestão de Direitos para fornecer acesso regulado a uma coleção de recursos.

## <a name="next-steps"></a>Passos seguintes

Inicie-se com os [controlos operacionais e ações operacionais da AD Azure.](active-directory-ops-guide-ops.md)
