---
title: Adicione utilizadores com licenças diretas ao licenciamento de grupo - Azure AD / Microsoft Docs
description: Como migrar de licenças individuais de utilizador para licenciamento baseado em grupo usando o Diretório Ativo Azure
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c06d81f2f3f6cee781889d05ae08a1fd125df52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025687"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Como migrar utilizadores com licenças individuais para grupos de licenciamento

Pode ter licenças existentes implantadas para utilizadores nas organizações através de atribuição direta; isto é, usando scripts PowerShell ou outras ferramentas para atribuir licenças individuais de utilizador. Antes de começar a usar licenças baseadas em grupo para gerir licenças na sua organização, pode usar este plano de migração para substituir sem problemas as soluções existentes por licenciamento baseado em grupo.

O mais importante a ter em mente é que deve evitar uma situação em que a migração para licenciamento em grupo resultará na perda temporária de licenças atribuídas aos utilizadores. Qualquer processo que possa resultar na remoção de licenças deve ser evitado para eliminar o risco de os utilizadores perderem o acesso aos serviços e aos seus dados.

## <a name="recommended-migration-process"></a>Processo de migração recomendado

1. Tem automatização existente (por exemplo, PowerShell) que gere a atribuição e remoção de licenças para os utilizadores. Deixe-o a funcionar como está.

1. Crie um novo grupo de licenciamento (ou decida quais os grupos existentes a utilizar) e certifique-se de que todos os utilizadores necessários são adicionados como membros.

1. Atribuir as licenças necessárias a esses grupos; o seu objetivo deve ser refletir o mesmo estado de licenciamento que a sua automatização existente (por exemplo, PowerShell) está a aplicar a esses utilizadores.

1. Verifique se as licenças foram aplicadas a todos os utilizadores desses grupos. Esta aplicação pode ser feita verificando o estado de processamento em cada grupo e verificando registos de auditoria.

   - Pode detetar a verificação de utilizadores individuais, analisando os seus dados de licença. Verá que têm as mesmas licenças atribuídas "diretamente" e "herdadas" de grupos.

   - Pode executar um script PowerShell para [verificar como as licenças são atribuídas aos utilizadores](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Quando a mesma licença de produto é atribuída ao utilizador, tanto diretamente como através de um grupo, apenas uma licença é consumida pelo utilizador. Por conseguinte, não são necessárias licenças adicionais para realizar a migração.

1. Verifique se nenhuma atribuição de licença falhou verificando cada grupo para utilizadores em estado de erro. Para obter mais informações, consulte [Identificar e resolver problemas](licensing-groups-resolve-problems.md)de licença para um grupo .

Considere remover as atribuições diretas originais. Recomendamos que o faça gradualmente e monitorize primeiro o resultado num subconjunto de utilizadores. Se puder deixar as atribuições diretas originais nos utilizadores, mas quando os utilizadores deixam os seus grupos licenciados, mantêm as licenças diretamente atribuídas, o que pode não ser o que pretende.

## <a name="an-example"></a>Um exemplo

Uma organização tem 1.000 utilizadores. Todos os utilizadores necessitam de licenças Do Office 365 Enterprise E3. Atualmente a organização tem um script PowerShell a funcionar nas instalações, adicionando e removendo licenças dos utilizadores à medida que eles vêm e vão. No entanto, a organização quer substituir o script por licenciamento baseado em grupo para que as licenças possam ser geridas automaticamente pela Azure AD.

Eis como pode ser o processo de migração:

1. Utilizando o portal Azure, atribua a licença Office 365 E3 ao grupo **Todos os utilizadores** em Azure AD.

1. Confirme que a atribuição de licença saqueou para todos os utilizadores. Vá à página de visão geral do grupo, selecione **Licenças,** e verifique o estado de processamento na parte superior da lâmina de **Licenças.**

   - Procure "As últimas alterações de licença foram aplicadas a todos os utilizadores" para confirmar que o processamento foi concluído.

   - Procure uma notificação em cima sobre quaisquer utilizadores para os quais as licenças possam não ter sido atribuídas com sucesso. Ficamos sem licença para alguns utilizadores? Alguns utilizadores têm planos de licença contraditórios que os impedem de herdar licenças de grupo?

1. Verifique se alguns utilizadores verificaram se têm as licenças diretas e de grupo aplicadas. Vá à página de perfil para um utilizador, selecione **Licenças,** e examine o estado das licenças.

   - Este é o estado de utilizador esperado durante a migração:

      ![o estado de utilizador esperado durante a migração](./media/licensing-groups-migrate-users/expected-user-state.png)

     Isto confirma que o utilizador tem licenças diretas e herdadas. Vemos que o Escritório 365 E3 está atribuído.

   - Selecione cada licença para ver quais os serviços ativados. Para verificar se as licenças diretas e de grupo permitem exatamente os mesmos serviços para o utilizador, selecione **Atribuições**.

1. Depois de confirmar que as licenças diretas e de grupo são equivalentes, pode começar a remover licenças diretas dos utilizadores. Pode testá-lo removendo-os para utilizadores individuais no portal e, em seguida, executar scripts de automação para removê-los a granel. Aqui está um exemplo do mesmo utilizador com as licenças diretas removidas através do portal. Note que o estado da licença permanece inalterado, mas já não vemos atribuições diretas.

   ![confirmar que as licenças diretas são removidas](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros cenários para gestão de licenças de grupo:

- [O que é o licenciamento baseado em grupo no Azure Ative Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar os utilizadores entre licenças de produtos utilizando licenciamento baseado em grupo no Diretório Ativo azure](licensing-groups-change-licenses.md)
- [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](licensing-group-advanced.md)
- [Exemplos da PowerShell para licenciamento baseado em grupo no Diretório Ativo Azure](licensing-ps-examples.md)
