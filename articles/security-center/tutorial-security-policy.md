---
title: Trabalhar com políticas de segurança Microsoft Docs
description: Este artigo descreve como trabalhar com políticas de segurança no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 22885d9d29bfbfc008b668889f7813ece1df8918
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945754"
---
# <a name="working-with-security-policies"></a>Trabalhar com políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como vê-las no Centro de Segurança. 

## <a name="introduction-to-security-policies"></a>Introdução às políticas de segurança

Uma política de segurança define a configuração desejada das suas cargas de trabalho e ajuda a garantir que está a cumprir os requisitos de segurança da sua empresa ou reguladores.

O Azure Security Center faz as suas recomendações de segurança com base nas suas políticas escolhidas. As políticas do Centro de Segurança baseiam-se em iniciativas políticas criadas na Política Azure. Pode utilizar [a Azure Policy](../governance/policy/overview.md) para gerir as suas políticas e definir políticas entre grupos de Gestão e várias subscrições.

O Centro de Segurança oferece as seguintes opções para trabalhar com políticas de segurança:

* **Ver e editar a política de padrão incorporada** - Quando ativar o Security Center, uma iniciativa incorporada chamada "ASC default" é automaticamente atribuída a todas as subscrições registadas do Security Center. Para personalizar esta iniciativa, pode ativar ou desativar políticas individuais dentro dela. Consulte a lista de [políticas de segurança incorporadas](security-center-policy-definitions.md) para entender as opções disponíveis fora da caixa.

* **Adicione as suas próprias políticas personalizadas** - Se quiser personalizar as iniciativas de segurança aplicadas à sua subscrição, pode fazê-lo dentro do Security Center. Em seguida, receberá recomendações se as suas máquinas não seguirem as políticas que cria. Para obter instruções sobre a construção e atribuição de políticas personalizadas, consulte [utilizando políticas de segurança personalizadas](custom-security-policies.md).

* **Adicionar políticas de conformidade regulamentar** - O painel de conformidade regulamentar do Centro de Segurança mostra o estado de todas as avaliações dentro do seu ambiente no contexto de uma determinada norma ou regulamento (como Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Para obter mais informações, consulte [Melhorar a sua conformidade regulamentar.](security-center-compliance-dashboard.md)


## <a name="manage-your-security-policies"></a>Gerir as suas políticas de segurança

Para ver as suas políticas de segurança no Centro de Segurança:

1. No painel do **Centro de Segurança,** selecione **a política de segurança.**

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="A página de gestão de políticas&quot;:::

   No ecrã de gestão de **políticas,** pode ver o número de grupos de gestão, subscrições e espaços de trabalho, bem como a estrutura do seu grupo de gestão.

1. Selecione o grupo de subscrição ou gestão cujas políticas pretende ver.

1. Aparece a página de política de segurança para esse grupo de subscrição ou gestão. Mostra as políticas disponíveis e atribuídas.

   ![página de política](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Se houver um rótulo &quot;MG Herdado" ao lado da sua política de incumprimento, significa que a apólice foi atribuída a um grupo de gestão e herdada pela subscrição que está a ver.


1. Escolha entre as opções disponíveis nesta página:

    1. Para trabalhar com as políticas da indústria, **selecione Adicione mais padrões**. Para obter mais informações, consulte [Update to dynamic compliance packages](update-regulatory-compliance-packages.md).

    1. Para atribuir e gerir iniciativas personalizadas, **selecione Adicionar iniciativas personalizadas.** Para obter mais informações, consulte [utilizando políticas de segurança personalizadas.](custom-security-policies.md)

    1. Para visualizar e editar a política predefinitiva, **selecione Ver política eficaz** e proceda como descrito abaixo. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="A página de gestão de políticas&quot;:::

   No ecrã de gestão de **políticas,** pode ver o número de grupos de gestão, subscrições e espaços de trabalho, bem como a estrutura do seu grupo de gestão.

1. Selecione o grupo de subscrição ou gestão cujas políticas pretende ver.

1. Aparece a página de política de segurança para esse grupo de subscrição ou gestão. Mostra as políticas disponíveis e atribuídas.

   ![página de política](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Se houver um rótulo &quot;MG Herdado":::

       Este ecrã **de política de segurança** reflete as ações tomadas pelas políticas atribuídas no grupo de subscrição ou gestão que selecionou.
       
       * Utilize os links no topo para abrir uma **atribuição** de política que se aplica no grupo de subscrição ou gestão. Estes links permitem-lhe aceder à atribuição e editar ou desativar a apólice. Por exemplo, se vir que uma determinada atribuição de políticas está efetivamente a negar a proteção do ponto final, use o link para editar ou desativar a política.
       
       * Na lista de políticas, pode ver a aplicação efetiva da política no seu grupo de subscrição ou gestão. São tomados em consideração as definições de cada política que se aplicam ao âmbito de aplicação e mostra-se o resultado cumulativo das ações tomadas pela política. Por exemplo, se numa atribuição da apólice for desativada, mas noutra está definida para AuditIfNotExist, então o efeito cumulativo aplica-se AuditIfNotExist. O efeito mais ativo tem sempre precedência.
       
       * O efeito das políticas pode ser: Apêndice, Auditoria, AuditIfNotExists, Deny, DeployIfNotExists, Desativado. Para obter mais informações sobre a forma como os efeitos são [aplicados,](../governance/policy/concepts/effects.md)consulte os efeitos da Política de Compreensão .

       > [!NOTE]
       > Quando vê as políticas atribuídas, pode ver várias atribuições e pode ver como cada atribuição é configurada por si só.


## <a name="who-can-edit-security-policies"></a>Quem pode editar políticas de segurança?

Pode editar políticas de segurança através do portal Azure Policy, através da REST API ou utilizando o Windows PowerShell.

O Security Center utiliza Role-Based Access Control (RBAC), que fornece funções incorporadas que pode atribuir a utilizadores, grupos e serviços do Azure. Quando os utilizadores abrem o Centro de Segurança, vêem apenas informações relacionadas com os recursos a que podem aceder. O que significa que os utilizadores são atribuídos ao papel de *proprietário,* *contribuinte*ou *leitor* à subscrição do recurso. Há também duas funções específicas do Centro de Segurança:

- **Leitor de segurança**: Tem direito a visualizar itens do Centro de Segurança, tais como recomendações, alertas, política e saúde. Não posso fazer mudanças.
- **Administrador de segurança**: Tem os mesmos direitos de visão que *o leitor de segurança.* Também pode atualizar a política de segurança e dispensar alertas.


## <a name="disable-security-policies-and-disable-recommendations"></a>Desativar as políticas de segurança e desativar recomendações

Quando a sua iniciativa de segurança desencadear uma recomendação que é irrelevante para o seu ambiente, pode impedir que essa recomendação volte a aparecer. Para desativar uma recomendação, desative a política específica que gera a recomendação.

A recomendação que pretende desativar continuará a aparecer se for necessária para um padrão regulamentar que tenha aplicado com as ferramentas de conformidade regulamentar do Centro de Segurança. Mesmo que tenha desativado uma política na iniciativa incorporada, uma política por iniciativa do padrão regulamentar ainda irá desencadear a recomendação se for necessária para o cumprimento. Não se pode desativar as políticas de iniciativas normais regulamentares.

Para obter mais informações sobre recomendações, consulte [recomendações de segurança de gestão.](security-center-recommendations.md)

1. No Centro de Segurança, a partir da secção **de Conformidade & Política,** selecione **a política de Segurança**.

   ![gestão de políticas](./media/tutorial-security-policy/policy-management.png)

2. Selecione o grupo de subscrição ou gestão para o qual pretende desativar a recomendação.

   > [!NOTE]
   > Lembre-se de que um grupo de gestão aplica as suas políticas às suas subscrições. Portanto, se desativar a política de uma subscrição e a subscrição pertencer a um grupo de gestão que ainda utiliza a mesma política, irá continuar a receber as recomendações de política. A política ainda será aplicada a partir do nível de gestão e as recomendações ainda serão geradas.

1. **Selecione Ver política eficaz**.

   ![ver política](./media/tutorial-security-policy/view-effective-policy.png)

1. Selecione a política atribuída.

   ![selecionar política](./media/tutorial-security-policy/security-policy.png)

1. Na secção **PARÂMETROS,** procure a política que invoca a recomendação que pretende desativar, e da lista de abandono, selecione **Disabled**

   ![política de desativação](./media/tutorial-security-policy/disable-policy.png)

1. Selecione **Guardar**.

   > [!NOTE]
   > As alterações à política de desativação podem demorar até 12 horas a produzir efeitos.



## <a name="next-steps"></a>Próximos passos
Este artigo explicava as políticas de segurança. Para obter informações relacionadas, consulte os seguintes artigos:

- [Saiba como definir políticas usando o PowerShell](../governance/policy/assign-policy-powershell.md) - 
- [Saiba como editar uma política de segurança na Política Azure](../governance/policy/tutorials/create-and-manage.md) - 
- [Saiba como definir uma política entre subscrições ou grupos de Gestão utilizando a Política Azure.](../governance/policy/overview.md)
- [Saiba como ativar o Centro de Segurança em todas as subscrições de um grupo de gestão](onboard-management-group.md)