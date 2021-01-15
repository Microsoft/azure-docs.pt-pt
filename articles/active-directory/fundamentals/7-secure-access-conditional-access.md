---
title: Gerir o acesso externo com o Azure Ative Directy Acesso Condicional
description: Como utilizar as políticas de acesso condicional do Azure Ative Para garantir o acesso externo aos recursos.
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
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222314"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Gerir o acesso externo com políticas de Acesso Condicional 

[O Acesso Condicional](../conditional-access/overview.md) é a ferramenta que o Azure AD utiliza para reunir sinais, impor políticas e determinar se um utilizador deve ter acesso aos recursos. Para obter informações detalhadas sobre como criar e utilizar políticas de acesso condicional (políticas de acesso condicional), consulte [Planear uma implementação de Acesso Condicional.](../conditional-access/plan-conditional-access.md) 

![Diagrama de sinais e decisões de acesso condicional](media/secure-external-access//7-conditional-access-signals.png)



Este artigo discute a aplicação de políticas de Acesso Condicional a utilizadores externos e assume que não tem acesso à funcionalidade [de Gestão de Direitos.](../governance/entitlement-management-overview.md) As políticas de acesso condicional podem ser e são usadas juntamente com a Gestão de Direitos.

No início deste conjunto de documentos, [criou um plano de segurança](3-secure-access-plan.md) que delineou:

* As aplicações e recursos têm os mesmos requisitos de segurança e podem ser agrupados para acesso.

* Requisitos de inscrição para utilizadores externos.

Utilizará esse plano para criar as suas políticas de Acesso Condicional para acesso externo. 

> [!IMPORTANT]
> Crie algumas contas de teste de utilizador externos para que possa testar as políticas que cria antes de as aplicar a todos os utilizadores externos.

## <a name="conditional-access-policies-for-external-access"></a>Políticas de acesso condicional para acesso externo

Seguem-se as melhores práticas relacionadas com o acesso externo com políticas de Acesso Condicional.

* Se não puder utilizar organizações conectadas em Gestão de Direitos, crie um grupo de segurança AZure AD ou um grupo Microsoft 365 para cada organização parceira com quem trabalha. Atribua todos os utilizadores desse parceiro ao grupo. Em seguida, pode utilizar esses grupos em políticas de Acesso Condicional.

* Crie o máximo de políticas de acesso condicional possível. Para aplicações que tenham as mesmas necessidades de acesso, adicione-as todas à mesma política.  
‎ 
   > [!NOTE]
   > As políticas de Acesso Condicional podem aplicar-se a um máximo de 250 aplicações. Se mais de 250 Apps tiverem as mesmas necessidades de acesso, crie políticas duplicadas. A política A aplicar-se-á às aplicações 1-250, a política B aplicar-se-á às aplicações 251-500, etc.

* Nomeie claramente políticas específicas do acesso externo a uma convenção de nomeação. Uma convenção de nomeação é *ExternalAccess_actiontaken_AppGroup.* Por exemplo, ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Bloquear todos os utilizadores externos dos recursos

Pode bloquear o acesso de utilizadores externos a conjuntos específicos de recursos com políticas de Acesso Condicional. Assim que determinar o conjunto de recursos a que quer bloquear o acesso, crie uma política.

Criar uma política que bloqueie o acesso dos utilizadores externos a um conjunto de aplicações:

1. Aceda ao **portal Azure**, selecione **Azure Ative Directory**, selecione **Security**, em seguida, selecione **Acesso Condicional**.

2. Selecione **Nova Política**, e introduza um **nome,** por exemplo ExternalAccess_Block_FinanceApps

3. Selecione **Utilizadores e grupo** s. No separador Incluir, escolha **utilizadores e grupos Selecione,** selecione **Todos os hóspedes e utilizadores externos**. 

4. **Selecione Excluir** e insira as contas de grupo de administrador e quaisquer contas de acesso de emergência (break-glass).

5. Selecione **aplicativos cloud ou ações**, escolha **Select Apps**, selecione todas as aplicações para as quais pretende bloquear o acesso externo e, em seguida, escolha **Selecione**.

6. Selecione **Condições**, selecione **Localizações**, em Configurar selecione **Sim**, e selecione **Qualquer localização**.

7. Nos controlos de Acesso, selecione **Grant**, altere o toggle para **Bloco**, e escolha **Select**.

8. Certifique-se de que a definição de política Enable está definida **apenas** para reportar e, em seguida, selecione **Criar**.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Bloquear o acesso externo a todos, exceto utilizadores externos específicos

Pode haver alturas em que pretende bloquear utilizadores externos, exceto um grupo específico. Por exemplo, pode querer bloquear todos os utilizadores externos, exceto aqueles que trabalham para a equipa financeira a partir das aplicações financeiras. Para efetuar este procedimento:

1. Criar um grupo de segurança para deter os utilizadores externos que devem aceder ao grupo financeiro.

2. Siga os passos 1-3 no bloco de acesso externo a partir de recursos acima.

3. No passo 4, adicione o grupo de segurança que pretende excluir de ser bloqueado das aplicações financeiras.

4. Execute o resto dos degraus.

## <a name="implement-conditional-access"></a>Implementar acesso condicional

Muitas políticas comuns de acesso condicional estão documentadas. Veja o seguinte que pode adaptar-se para utilizadores externos.

* [Requerem autenticação multi-factor para todos os utilizadores](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Acesso Condicional baseado no risco do utilizador](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Exigir autenticação multi-factor para acesso a redes não fidedquisas](../conditional-access/untrusted-networks.md) 

* [Exigir Termos de Utilização](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua desejada postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra o seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governação](3-secure-access-plan.md)

4. [Utilize grupos para segurança](4-secure-access-groups.md)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md)

6. [Acesso seguro com Gestão de Direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md) (Está aqui)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)
