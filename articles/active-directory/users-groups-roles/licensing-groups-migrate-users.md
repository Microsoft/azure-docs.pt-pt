---
title: Adicionar usuários com licenças diretas ao licenciamento de grupo-Azure AD | Microsoft Docs
description: Como migrar de licenças de usuário individuais para licenciamento baseado em grupo usando o Azure Active Directory
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025687"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Como migrar usuários com licenças individuais para grupos para licenciamento

Você pode ter licenças existentes implantadas para usuários nas organizações por meio de atribuição direta; ou seja, usando scripts do PowerShell ou outras ferramentas para atribuir licenças de usuário individuais. Antes de começar a usar o licenciamento baseado em grupo para gerenciar licenças em sua organização, você pode usar esse plano de migração para substituir diretamente as soluções existentes com o licenciamento baseado em grupo.

A coisa mais importante a ser lembrada é que você deve evitar uma situação em que a migração para o licenciamento baseado em grupo fará com que os usuários percam temporariamente suas licenças atualmente atribuídas. Qualquer processo que possa resultar na remoção de licenças deve ser evitado para remover o risco de usuários perderem o acesso a serviços e seus dados.

## <a name="recommended-migration-process"></a>Processo de migração recomendado

1. Você tem a automação existente (por exemplo, o PowerShell) gerenciando a atribuição e a remoção de licenças para os usuários. Deixe-o em execução como está.

1. Crie um novo grupo de licenciamento (ou decida quais grupos existentes serão usados) e certifique-se de que todos os usuários necessários sejam adicionados como membros.

1. Atribuir as licenças necessárias a esses grupos; seu objetivo deve ser refletir o mesmo estado de licenciamento que sua automação existente (por exemplo, PowerShell) está se aplicando a esses usuários.

1. Verifique se as licenças foram aplicadas a todos os usuários nesses grupos. Esse aplicativo pode ser feito verificando o estado de processamento em cada grupo e verificando os logs de auditoria.

   - Você pode identificar a verificação de usuários individuais examinando seus detalhes de licença. Você verá que eles têm as mesmas licenças atribuídas "diretamente" e "herdadas" dos grupos.

   - Você pode executar um script do PowerShell para [verificar como as licenças são atribuídas aos usuários](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Quando a mesma licença de produto é atribuída ao usuário diretamente e por meio de um grupo, apenas uma licença é consumida pelo usuário. Portanto, nenhuma licença adicional é necessária para executar a migração.

1. Verifique se não houve nenhuma atribuição de licença verificando cada grupo quanto aos usuários no estado de erro. Para obter mais informações, consulte [identificando e resolvendo problemas de licença para um grupo](licensing-groups-resolve-problems.md).

Considere remover as atribuições diretas originais. É recomendável que você o faça gradualmente e monitore o resultado em um subconjunto de usuários primeiro. Se você pode deixar as atribuições diretas originais nos usuários, mas quando os usuários deixam seus grupos licenciados, eles retêm as licenças atribuídas diretamente, o que pode não ser o que você deseja.

## <a name="an-example"></a>Um exemplo

Uma organização tem 1.000 usuários. Todos os usuários exigem licenças do Office 365 Enterprise E3. Atualmente, a organização tem um script do PowerShell em execução local, adicionando e removendo licenças dos usuários à medida que elas chegam e vão. No entanto, a organização deseja substituir o script pelo licenciamento baseado em grupo para que as licenças possam ser gerenciadas automaticamente pelo Azure AD.

Veja como seria a aparência do processo de migração:

1. Usando o portal do Azure, atribua a licença do Office 365 E3 ao grupo **todos os usuários** no Azure AD.

1. Confirme se a atribuição de licença foi concluída para todos os usuários. Vá para a página Visão geral do grupo, selecione **licenças**e verifique o status de processamento na parte superior da folha **licenças** .

   - Procure "últimas alterações de licença foram aplicadas a todos os usuários" para confirmar que o processamento foi concluído.

   - Procure uma notificação na parte superior de todos os usuários para os quais as licenças podem não ter sido atribuídas com êxito. Ficou sem licenças para alguns usuários? Alguns usuários têm planos de licença conflitantes que os impedem de herdar licenças de grupo?

1. Identificar Verifique alguns usuários para verificar se eles têm as licenças diretas e de grupo aplicadas. Acesse a página de perfil de um usuário, selecione **licenças**e examine o estado das licenças.

   - Este é o estado do usuário esperado durante a migração:

      ![o estado de usuário esperado durante a migração](./media/licensing-groups-migrate-users/expected-user-state.png)

     Isso confirma que o usuário tem licenças diretas e herdadas. Vemos que o Office 365 E3 está atribuído.

   - Selecione cada licença para ver quais serviços estão habilitados. Para verificar se as licenças diretas e de grupo permitem exatamente os mesmos serviços para o usuário, selecione **atribuições**.

1. Depois de confirmar que as licenças diretas e de grupo são equivalentes, você pode começar a remover licenças diretas dos usuários. Você pode testar isso removendo-os para usuários individuais no portal e, em seguida, executar scripts de automação para que eles sejam removidos em massa. Aqui está um exemplo do mesmo usuário com as licenças diretas removidas por meio do Portal. Observe que o estado da licença permanece inalterado, mas não vemos mais as atribuições diretas.

   ![confirmar se as licenças diretas foram removidas](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros cenários de gerenciamento de licenças de Grupo:

- [O que é o licenciamento baseado em grupo no Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar os utilizadores entre licenças de produto através do licenciamento com o botão com base em grupo no Azure Active Directory](licensing-groups-change-licenses.md)
- [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](licensing-group-advanced.md)
- [Exemplos do PowerShell para licenciamento com o botão com base em grupo no Azure Active Directory](licensing-ps-examples.md)
