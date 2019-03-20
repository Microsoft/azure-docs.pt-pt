---
title: Migrar utilizadores para licenciamento baseado no grupo - Azure Active Directory | Documentos da Microsoft
description: Como alternar de licenças de utilizador individuais para licenciamento baseado em grupo com o Azure Active Directory
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd800097ca5107f7df1e67c91ff4c96b9cfcd74
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107751"
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Como adicionar os utilizadores licenciados para um grupo para o licenciamento no Azure Active Directory

Pode ter licenças existentes implementadas para os utilizadores em organizações por meio de "atribuição direta"; ou seja, utilizar scripts do PowerShell ou outras ferramentas para atribuir licenças de utilizador individuais. Se gostaria de começar a utilizar o licenciamento baseado em grupo para gerir licenças na sua organização, terá um plano de migração para substituir facilmente as soluções existentes com o licenciamento baseado em grupo.

A coisa mais importante a lembrar é que deve evitar uma situação em que a migração para o licenciamento baseado em grupo resultará em utilizadores temporariamente perder cujas licenças são atribuídas atualmente. Qualquer processo que pode resultar na remoção de licenças deve ser evitado para remover o risco dos utilizadores perderem o acesso aos seus dados e serviços.

## <a name="recommended-migration-process"></a>Processo de migração recomendado

1. Terá de automatização existente (por exemplo, o PowerShell), gestão de atribuição de licenças e remoção para os utilizadores. Deixe a funcionar como está.

2. Criar um novo grupo de licenciamento (ou decidir quais existentes de grupos para utilizar) e certifique-se de que todas as necessárias que os utilizadores são adicionados como membros.

3. Atribuir as licenças necessárias para esses grupos; sua meta deve ser refletir o estado de licenciamento mesmo que sua automação existente (por exemplo, o PowerShell) está a ser aplicada aos usuários.

4. Certifique-se de que licenças foram aplicadas a todos os utilizadores esses grupos. Esta aplicação pode ser feita ao verificar o estado de processamento em cada grupo e verificando os registos de auditoria.

   - Pode verificar de usuários individuais ao consultar os detalhes da licença. Verá que tiverem o mesmo licenças atribuídas "diretamente" e "herdado" de grupos.

   - Pode executar um script do PowerShell para [Certifique-se de como as licenças são atribuídas aos utilizadores](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Quando a mesma licença de produto é atribuída ao utilizador diretamente e através de um grupo, somente uma licença é consumida pelo utilizador. Por conseguinte, não existem licenças adicionais são necessários para efetuar a migração.

5. Certifique-se de que não existem atribuições de licenças falhou ao verificar a cada grupo de utilizadores no Estado com erros. Para obter mais informações, consulte [identificando e resolver problemas de licença para um grupo](licensing-groups-resolve-problems.md).

6. Considere remover as atribuições de diretas originais; poderá fazê-lo gradualmente, em "fases", para monitorizar o resultado num subconjunto de utilizadores em primeiro lugar.

   Poderia deixar as atribuições de diretas originais em utilizadores, mas quando os utilizadores deixam seus grupos licenciados ainda irá reter a licença original, o que é, possivelmente, não o que deseja.

## <a name="an-example"></a>Um exemplo

Uma organização tem 1.000 usuários. Todos os utilizadores necessitam de licenças de Security (EMS) do Enterprise Mobility +. 200 utilizadores são no departamento financeiro e necessitam de licenças do Office 365 Enterprise E3. Atualmente, a organização tem um script do PowerShell em execução no local, adicionar e remover licenças de utilizadores, à medida que eles venham e voltem. No entanto, a organização pretende substituir o script com o licenciamento baseado em grupo para que licenças possam ser gerenciadas automaticamente pelo Azure AD.

Eis o que o processo de migração deve é semelhante ao:

1. Utilizar o portal do Azure, atribuir a licença de EMS para o **todos os utilizadores** grupo no Azure AD. Atribuir a licença E3 para o **departamento financeiro** grupo que contém todos os utilizadores.

2. Para cada grupo, certifique-se que a atribuição de licenças foi concluída para todos os utilizadores. Aceda ao painel para cada grupo, selecione **licenças**e verificar o estado de processamento na parte superior a **licenças** painel.

   - Procure "Licença mais recentes alterações foram aplicadas a todos os utilizadores" para confirmar o processamento foi concluído.

   - Procure uma notificação na parte superior sobre todos os utilizadores aos quais licenças podem ter não foi atribuídas com êxito. Executámos mais licenças para alguns usuários? Alguns utilizadores têm a licença em conflito SKUs impedi-los de herdar de licenças de grupo?

3. Lugar verificar alguns utilizadores para verificar que têm ambas as diretas e de grupo licenças aplicadas. Aceda ao painel para um utilizador, selecione **licenças**e examinar o estado de licenças.

   - Este é o estado de utilizador esperado durante a migração:

      ![Estado do utilizador esperado](./media/licensing-groups-migrate-users/expected-user-state.png)

   Isto confirma que o utilizador tem licenças herdadas tanto diretas. Podemos ver que ambos **EMS** e **E3** são atribuídos.

   - Selecione cada licença de mostrar os detalhes sobre os serviços ativados. Isso pode ser usado para verificar se as licenças diretas e de grupo permitem exatamente os mesmo os planos de serviço para o utilizador.

      ![Verifique os planos de serviço](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Depois de confirmar que as licenças diretas e de grupo são equivalentes, pode começar a remover licenças diretas dos utilizadores. Pode testar isso, removendo-os para usuários individuais no portal e, em seguida, executar scripts de automatização tê-los removido em massa. Eis um exemplo do mesmo usuário com as licenças diretos removido através do portal. Tenha em atenção que o estado da licença permanece inalterado, mas já não vemos atribuições diretas.

   ![licenças diretas removidas](./media/licensing-groups-migrate-users/direct-licenses-removed.png)


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre outros cenários para gestão de licenças através de grupos, leia

* [O que é o licenciamento no Azure Active Directory com base em grupo?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
* [Como migrar os utilizadores entre licenças de produto através do licenciamento com o botão com base em grupo no Azure Active Directory](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento com o botão com base em grupo no Azure Active Directory](licensing-ps-examples.md)
