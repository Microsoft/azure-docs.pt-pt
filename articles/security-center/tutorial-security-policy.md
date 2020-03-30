---
title: Trabalhar com políticas de segurança Microsoft Docs
description: Este artigo descreve como trabalhar com políticas de segurança no Centro de Segurança Azure.
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
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906883"
---
# <a name="working-with-security-policies"></a>Trabalhar com políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como vê-las no Centro de Segurança. 

## <a name="introduction-to-security-policies"></a>Introdução a políticas de segurança

Uma política de segurança define a configuração desejada das suas cargas de trabalho e ajuda a garantir que está a cumprir os requisitos de segurança da sua empresa ou reguladores.

O Azure Security Center faz as suas recomendações de segurança com base nas suas políticas escolhidas. As políticas do Centro de Segurança baseiam-se em iniciativas políticas criadas na Política Azure. Pode utilizar a [Política Azure](../governance/policy/overview.md) para gerir as suas políticas e definir políticas entre grupos de Gestão e através de várias subscrições.

O Security Center oferece as seguintes opções para trabalhar com políticas de segurança:

* Ver e editar a política de **predefinição incorporada** - Quando ativa o Security Center, uma iniciativa incorporada chamada 'padrão ASC' é automaticamente atribuída a todas as subscrições registadas pelo Security Center (níveis gratuitos ou standard). Para personalizar esta iniciativa, pode ativar ou desativar políticas individuais dentro dela. Consulte a lista de [políticas de segurança incorporadas](security-center-policy-definitions.md) para entender as opções disponíveis fora da caixa.

* **Adicione as suas próprias políticas personalizadas** - Se quiser personalizar as iniciativas de segurança aplicadas à sua subscrição, pode fazê-lo dentro do Security Center. Em seguida, receberá recomendações se as suas máquinas não seguirem as políticas que cria. Para instruções sobre a construção e atribuição de políticas personalizadas, consulte [A utilização de políticas](custom-security-policies.md)de segurança personalizadas .

* **Adicione políticas** de conformidade regulamentar - O painel de controlo regulamentar do Security Center mostra o estado de todas as avaliações no seu ambiente no contexto de uma norma ou regulação específica (como o Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Para mais informações, consulte [Melhorar a sua conformidade regulamentar](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Gerir as suas políticas de segurança

Para ver as suas políticas de segurança no Centro de Segurança:

1. No painel do Centro de **Segurança,** selecione a política de **segurança.**

    ![O painel Gestão de Políticas](./media/security-center-policies/security-center-policy-mgt.png)

   No ecrã de gestão de **Políticas,** pode ver o número de grupos de gestão, subscrições e espaços de trabalho, bem como a estrutura do seu grupo de gestão.

1. Selecione o grupo de subscrição ou gestão cujas políticas pretende ver.

1. Aparece a página de política de segurança para essa subscrição ou grupo de gestão. Mostra as políticas disponíveis e atribuídas.

   ![tela de política](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Se existe uma etiqueta "MG Herdada" ao lado da sua política de incumprimento, significa que a apólice foi atribuída a um grupo de gestão e herdada pela subscrição que está a ver.


1. Escolha entre as opções disponíveis nesta página:

    1. Para trabalhar com as políticas da indústria, clique **em Adicionar mais padrões.** Para mais informações, consulte [Atualização para pacotes dinâmicos](update-regulatory-compliance-packages.md)de conformidade .

    1. Para atribuir e gerir iniciativas personalizadas, clique em **Adicionar iniciativas personalizadas.** Para mais informações, consulte [A utilização de políticas de segurança personalizadas](custom-security-policies.md).

    1. Para visualizar e editar a política de predefinição, clique em **Ver a política eficaz** e proceder como descrito abaixo. 

       ![tela de política](./media/security-center-policies/policy-screen.png)
       
       Este ecrã de política de **segurança** reflete a ação tomada pelas políticas atribuídas no grupo de subscrição ou gestão que selecionou.
       
       * Utilize os links no topo para abrir uma **atribuição** de política que se aplique no grupo de subscrição ou gestão. Estes links permitem-lhe aceder à atribuição e editar ou desativar a apólice. Por exemplo, se vir que uma determinada atribuição de políticas está efetivamente a negar a proteção de pontos finais, utilize o link para editar ou desativar a política.
       
       * Na lista de políticas, pode ver a aplicação efetiva da política no seu grupo de subscrição ou gestão. As definições de cada política aplicável ao âmbito de aplicação são tomadas em consideração e o resultado cumulativo das ações tomadas pela política é demonstrado. Por exemplo, se numa atribuição da apólice for desativada, mas noutra está definida para AuditIfNotExist, então o efeito cumulativo aplica-se AuditIfNotExist. O efeito mais ativo sempre tem precedência.
       
       * O efeito das políticas pode ser: Apêndice, Auditoria, AuditoriaIfNotExists, Negar, ImplementarIfNotExists, Desativado. Para obter mais informações sobre como os efeitos são aplicados, consulte [compreender os efeitos da Política](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Quando vê as políticas atribuídas, pode ver várias atribuições e pode ver como cada atribuição é configurada por si só.


## <a name="who-can-edit-security-policies"></a>Quem pode editar políticas de segurança?

Pode editar políticas de segurança através do portal Política Azure, através da Rest API ou utilizando o Windows PowerShell.

O Centro de Segurança utiliza o Controlo de Acesso Baseado em Funções (RBAC), que fornece funções incorporadas que podem ser atribuídas a utilizadores, grupos e serviços no Azure. Quando os utilizadores abrem o Security Center, vêem apenas informações relacionadas com os recursos a que têm acesso. O que significa que os utilizadores são atribuídos ao papel de *proprietário*, *contribuinte*ou *leitor* para a subscrição do recurso. Além destas funções, existem duas funções específicas do Centro de Segurança:

- **Leitor de segurança**: Tenha direitos de vista para o Centro de Segurança, que inclui recomendações, alertas, política e saúde, mas não podem fazer alterações.
- **Administração de segurança**: Ter os mesmos direitos de opinião que o leitor de *segurança,* e também podem atualizar a política de segurança e rejeitar recomendações e alertas.


## <a name="disable-security-policies"></a>Desativar as políticas de segurança
Se a política de segurança padrão estiver a gerar uma recomendação que não seja relevante para o seu ambiente, pode impedi-la desativando a definição de política que envia a recomendação.
Para obter mais informações sobre recomendações, consulte [a Gestão](security-center-recommendations.md)de recomendações de segurança.

1. No Centro de Segurança, a partir da secção **Política & Conformidade,** clique na política de **segurança**.

   ![gestão de políticas](./media/tutorial-security-policy/policy-management.png)

2. Clique no grupo de subscrição ou gestão para o qual pretende desativar a recomendação.

   > [!NOTE]
   > Lembre-se de que um grupo de gestão aplica as suas políticas às suas subscrições. Portanto, se desativar a política de uma subscrição e a subscrição pertencer a um grupo de gestão que ainda utiliza a mesma política, irá continuar a receber as recomendações de política. A política ainda será aplicada a partir do nível de gestão e as recomendações ainda serão geradas.

1. Clique em **Ver a política eficaz**.

   ![política de desativar](./media/tutorial-security-policy/view-effective-policy.png)

1. Clique na política atribuída.

   ![política de desativar](./media/tutorial-security-policy/security-policy.png)

1. Na secção **PARAMETERS,** procure a política que invoca a recomendação que pretende desativar, e a partir da lista de abandono, selecione **Disabled**

   ![política de desativar](./media/tutorial-security-policy/disable-policy.png)

1. Clique em **Guardar**.

   > [!NOTE]
   > As alterações da política de desativação podem demorar até 12 horas a entrar em vigor.



## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeste sobre políticas de segurança. Para obter informações relacionadas, consulte os seguintes artigos:

* Para obter instruções sobre como definir políticas utilizando powerShell, consulte [Quickstart: Criar uma atribuição de política para identificar recursos não conformes utilizando o módulo PowerShell Azure](../governance/policy/assign-policy-powershell.md)

* Para obter instruções sobre como editar uma política de segurança na Política Azure, consulte [Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md).

* Para obter instruções sobre como definir uma política entre subscrições ou grupos de gestão que utilizem a Política Azure, consulte o que é a [Política Azure?](../governance/policy/overview.md)