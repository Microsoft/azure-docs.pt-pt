---
title: Adicionar utilizadores com licenças diretas para agrupar licenciamento - Azure AD ! Microsoft Docs
description: Como migrar de licenças individuais de utilizador para licenciamento baseado em grupo usando O Diretório Ativo Azure
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546510"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Como migrar utilizadores com licenças individuais para grupos para licenciamento

Pode ter licenças existentes implantadas para utilizadores nas organizações através de atribuição direta; isto é, utilizando scripts PowerShell ou outras ferramentas para atribuir licenças individuais de utilizador. Antes de começar a usar o licenciamento baseado em grupo para gerir licenças na sua organização, pode usar este plano de migração para substituir sem problemas as soluções existentes por licenciamento baseado em grupo.

O mais importante a ter em mente é que deve evitar uma situação em que migrar para licenciamento baseado em grupo resultará em utilizadores que perdem temporariamente as licenças atualmente atribuídas. Qualquer processo que possa resultar na remoção de licenças deve ser evitado para eliminar o risco de os utilizadores perderem o acesso aos serviços e aos seus dados.

## <a name="recommended-migration-process"></a>Processo de migração recomendado

1. Tem automatização existente (por exemplo, PowerShell) a gerir a atribuição de licenças e a remoção para os utilizadores. Deixe-o funcionando como está.

1. Crie um novo grupo de licenciamento (ou decida quais os grupos existentes a utilizar) e certifique-se de que todos os utilizadores necessários são adicionados como membros.

1. Atribuir as licenças necessárias a esses grupos; o seu objetivo deve ser refletir o mesmo estado de licenciamento que a sua automatização existente (por exemplo, PowerShell) está a aplicar-se a esses utilizadores.

1. Verifique se foram aplicadas licenças a todos os utilizadores desses grupos. Esta aplicação pode ser feita verificando o estado de processamento em cada grupo e verificando os Registos de Auditoria.

   - Pode verificar os utilizadores individuais olhando para os detalhes da licença. Verá que têm as mesmas licenças atribuídas "diretamente" e "herdadas" de grupos.

   - Pode executar um script PowerShell para [verificar como as licenças são atribuídas aos utilizadores](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Quando a mesma licença de produto é atribuída ao utilizador tanto diretamente como através de um grupo, apenas uma licença é consumida pelo utilizador. Por conseguinte, não são necessárias licenças adicionais para realizar a migração.

1. Verifique se nenhuma atribuição de licença falhou verificando cada grupo de utilizadores em estado de erro. Para obter mais informações, consulte [identificar e resolver problemas de licença para um grupo.](licensing-groups-resolve-problems.md)

Considere remover as missões diretas originais. Recomendamos que o faça gradualmente e monitorize primeiro o resultado num subconjunto de utilizadores. Se puder deixar as atribuições diretas originais nos utilizadores, mas quando os utilizadores deixam os seus grupos licenciados, retêm as licenças diretamente atribuídas, o que pode não ser o que pretende.

## <a name="an-example"></a>Um exemplo

Uma organização tem 1.000 utilizadores. Todos os utilizadores requerem licenças E3 do Office 365. Atualmente, a organização tem um script PowerShell a decorrer nas instalações, adicionando e removendo licenças dos utilizadores à medida que vão e vêm. No entanto, a organização quer substituir o script por licenciamento baseado em grupo para que as licenças possam ser geridas automaticamente pela Azure AD.

Eis como pode ser o processo de migração:

1. Utilizando o portal Azure, atribua a licença Office 365 E3 ao grupo **de todos os utilizadores** em Azure AD.

1. Confirme que a atribuição da licença foi concluída para todos os utilizadores. Vá à página geral do grupo, selecione **Licenças** e verifique o estado de processamento na parte superior da lâmina **licenças.**

   - Procure "As últimas alterações de licença foram aplicadas a todos os utilizadores" para confirmar que o processamento foi concluído.

   - Procure uma notificação em cima sobre quaisquer utilizadores para os quais as licenças podem não ter sido atribuídas com sucesso. Ficamos sem licença para alguns utilizadores? Alguns utilizadores têm planos de licença contraditórios que os impedem de herdar licenças de grupo?

1. Verifique o local em alguns utilizadores para verificar se têm as licenças diretas e de grupo aplicadas. Vá à página de perfil de um utilizador, selecione **Licenças** e examine o estado das licenças.

   - Este é o estado de utilizador esperado durante a migração:

      ![o estado de utilizador esperado durante a migração](./media/licensing-groups-migrate-users/expected-user-state.png)

     Isto confirma que o utilizador tem licenças diretas e herdadas. Vemos que o Office 365 E3 está designado.

   - Selecione cada licença para ver quais os serviços que estão ativados. Para verificar se as licenças diretas e de grupo permitem exatamente os mesmos serviços para o utilizador, selecione **Assignments**.

1. Depois de confirmar que as licenças diretas e de grupo são equivalentes, pode começar a remover licenças diretas dos utilizadores. Pode testá-lo removendo-os para utilizadores individuais no portal e, em seguida, executar scripts de automação para que sejam removidos a granel. Aqui está um exemplo do mesmo utilizador com as licenças diretas removidas através do portal. Note que o estado da licença permanece inalterado, mas já não vemos atribuições diretas.

   ![confirmar que as licenças diretas são removidas](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros cenários para a gestão de licenças de grupo:

- [O que é o licenciamento baseado em grupo no Azure Ative Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar utilizadores entre licenças de produtos utilizando licenças baseadas em grupo no Azure Ative Directory](licensing-groups-change-licenses.md)
- [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](licensing-group-advanced.md)
- [Exemplos powerShell para licenciamento baseado em grupo no Azure Ative Directory](licensing-ps-examples.md)
