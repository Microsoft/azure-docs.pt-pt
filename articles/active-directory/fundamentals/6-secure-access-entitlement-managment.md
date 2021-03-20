---
title: Gerir o acesso externo com a Azure Ative Directory Entitlement Management
description: Como utilizar a Azure Ative Directory Entitlement Management como parte do seu plano de segurança de acesso externo.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f06a54f59405d9833194b2e7d4488bc93d2437ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725183"
---
# <a name="manage-external-access-with-entitlement-management"></a>Gerir o acesso externo com a Gestão de Direitos 


[A gestão de direitos](../governance/entitlement-management-overview.md) é uma capacidade de governação de identidade que permite às organizações gerir a identidade e aceder ao ciclo de vida em escala, automatizando fluxos de trabalho de pedidos de acesso, atribuições de acesso, revisões e expiração. A gestão de direitos permite que os não administradores delegados criem pacotes de acesso aos que utilizadores [externos](../governance/entitlement-management-overview.md) de outras organizações possam solicitar acesso. Fluxos de trabalho de aprovação de uma e várias fases podem ser configurados para avaliar pedidos, e [providenciar aos](../governance/what-is-provisioning.md) utilizadores acesso limitado a tempo com revisões recorrentes. A gestão de direitos permite o provisionamento e desprovisionamento de contas externas por políticas.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Conceitos-chave para permitir a gestão de direitos

Os seguintes conceitos-chave são importantes para a gestão dos direitos.

### <a name="access-packages"></a>Pacotes de Acesso

Um [pacote de acesso](../governance/entitlement-management-overview.md) é a base da gestão de direitos. Os pacotes de acesso são agrupamentos de recursos governados por políticas que um utilizador precisa para colaborar num projeto ou fazer outras tarefas. Por exemplo, um pacote de acesso pode incluir:

* acesso a sites específicos do SharePoint.

* aplicações empresariais, incluindo as suas aplicações personalizadas dentro de casa e SaaS, como a Salesforce.

* Canais Microsoft Teams.

* Microsoft 365 Grupos. 

### <a name="catalogs"></a>Catálogos

Os pacotes de acesso residem em [catálogos.](../governance/entitlement-management-catalog-create.md) Cria um catálogo quando pretende agrupar recursos relacionados e aceder a pacotes e delegar a capacidade de os gerir. Primeiro adiciona recursos a um catálogo, e depois pode adicionar esses recursos para aceder a pacotes. Por exemplo, talvez queira criar um catálogo "Finanças" e delegar a [sua gestão](../governance/entitlement-management-delegate.md) num membro da equipa de finanças. Essa pessoa pode então [adicionar recursos,](../governance/entitlement-management-catalog-create.md)criar pacotes de acesso e gerir a aprovação de acesso a esses pacotes.

O diagrama seguinte mostra um ciclo de vida de governação típico para um utilizador externo que tem acesso a um pacote de acesso que tem uma expiração.

![Um diagrama do ciclo externo de governação do utilizador.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Acesso externo de autosserviço

Pode aceder a pacotes de acesso através do [Portal Azure AD My Access](../governance/entitlement-management-request-access.md) para permitir que utilizadores externos solicitem acesso. As políticas determinam quem pode solicitar um pacote de acesso. Especifica quem pode solicitar o pacote de acesso:

* Organizações [conectadas específicas](../governance/entitlement-management-organization.md)

* Todas as organizações conectadas configuradas

* Todos os utilizadores de qualquer organização

* Membro ou utilizadores convidados já no seu inquilino

### <a name="approvals"></a>Aprovações   
‎Access packages can include mandatory approval for access. **Implementar sempre processos de aprovação para utilizadores externos.** As aprovações podem ser uma aprovação única ou em várias fases. As aprovações são determinadas por políticas. Se os utilizadores internos e externos precisarem de aceder ao mesmo pacote, é provável que crie diferentes políticas de acesso para diferentes categorias de organizações conectadas e para utilizadores internos.

### <a name="expiration"></a>Expiração  
‎Access packages can include an expiration date. A expiração pode ser definida para um dia específico ou dar ao utilizador um número específico de dias de acesso. Quando o pacote de acesso expirar e o utilizador não tiver outro acesso, o objeto de utilizador convidado B2B que representa o utilizador pode ser eliminado ou bloqueado da sua assinatura. Recomendamos que imponha a expiração dos pacotes de acesso para utilizadores externos. Nem todos os pacotes de acesso têm expirações. Para aqueles que não o fizerem, certifique-se de que realiza avaliações de acesso.

### <a name="access-reviews"></a>Revisões de acesso

Os pacotes de acesso podem exigir revisões periódicas de [acesso,](../governance/manage-guest-access-with-access-reviews.md)que exigem que o proprietário do pacote ou um designee ateste a necessidade contínua de acesso dos utilizadores. 

Antes de configurar a sua revisão, determine o seguinte.

* Quem?

   * Quais os critérios para o acesso continuado?

   * Quem são os revisores especificados?

* Com que frequência devem ocorrer avaliações programadas?

   * As opções construídas incluem mensalmente, trimestrais, bianuas ou anuais. 

   * Recomendamos trimestralmente ou mais frequentemente para pacotes que suportem o acesso externo. 

 

> [!IMPORTANT]
> As revisões de acesso aos pacotes de acesso apenas revêem o acesso concedido através da Gestão de Direitos. Por isso, deve configurar outros processos para rever qualquer acesso prestado a utilizadores externos fora da Gestão de Direitos.

Para obter mais informações sobre comentários de acesso, consulte [a implementação de Azure AD Access Reviews](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Utilização da automatização em Gestão de Direitos

Pode executar [funções de Gestão de Direitos utilizando o Microsoft Graph,](/graph/tutorial-access-package-api)incluindo

* [Gerir pacotes de acesso](/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Gerir comentários de acesso](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Gerir organizações conectadas](/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Gerir definições de Gestão de Direitos](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Recomendações 

Recomendamos as práticas para reger o acesso externo com a Gestão de Direitos.

**Para projetos com um ou mais parceiros de negócio, [crie e utilize pacotes](../governance/entitlement-management-access-package-create.md) de acesso a bordo e asse revisão dos utilizadores desses parceiros para os recursos.** 

* Se já tem utilizadores B2B no seu diretório, também pode atribuí-los diretamente aos pacotes de acesso apropriados.

* Pode atribuir acesso no [portal Azure,](../governance/entitlement-management-access-package-assignments.md)ou através do [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta).

**Utilize as definições de Governação de Identidade para remover os utilizadores do seu diretório quando os seus pacotes de acesso expirarem.**

![Screenshot de configuração gerencie o ciclo de vida dos utilizadores externos.](media/secure-external-access/6-manage-external-lifecycle.png)

Estas definições aplicam-se apenas aos utilizadores que estavam a bordo através da Gestão de Direitos.

**[Delegar gestão de catálogos e pacotes de acesso a empresários,](../governance/entitlement-management-delegate.md) que tenham mais informações sobre a quem devem aceder.**

![Screenshot de configurar um catálogo.](media/secure-external-access/6-catalog-management.png)

**‎[Enforce expiration of access packages](../governance/entitlement-management-access-package-lifecycle-policy.md) to which external users have access.**


![Screenshot de configurar a expiração do pacote de acesso.](media/secure-external-access/6-access-package-expiration.png)

* Se souber a data final de um pacote de acesso baseado no projeto, utilize a Data Para definir a data específica. 

* Caso contrário, recomendamos que a expiração não seja mais de 365 dias, a menos que se saiba que é um compromisso pluário.

* Permitir que os utilizadores aumentem o acesso.

* Requer aprovação para conceder a extensão.

**[Imponha revisões de acesso de pacotes](../governance/manage-guest-access-with-access-reviews.md) para evitar acesso inadequado aos hóspedes.**

![Screenshot de criar um novo pacote de acesso.](media/secure-external-access/6-new-access-package.png)

* Impor as críticas trimestrais.

* Para os projetos sensíveis à conformidade, os revisores são revisores específicos, em vez de autorrever os utilizadores externos. Os utilizadores que são gestores de pacotes de acesso são um bom lugar para começar para os revisores. 

* Para projetos menos sensíveis, ter a auto-revisão dos utilizadores reduzirá o fardo da organização para remover o acesso dos utilizadores que já não estão com a sua organização doméstica.

Para obter mais informações, consulte [Regule o acesso a utilizadores externos na Gestão de Direitos AD Azure](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra o seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governação](3-secure-access-plan.md)

4. [Utilize grupos para segurança](4-secure-access-groups.md)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md)

6. [Acesso seguro com a Gestão de Direitos](6-secure-access-entitlement-managment.md) (Você está aqui.)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)

 

