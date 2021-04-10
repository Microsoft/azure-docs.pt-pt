---
title: Atribuir etiquetas de sensibilidade a grupos - Azure AD | Microsoft Docs
description: Saiba como atribuir rótulos de sensibilidade a grupos. Consulte as informações sobre resolução de problemas e consulte os recursos disponíveis adicionais.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a78cf9b7d78078030ac0db8bd2f0fddb93a8dda4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881401"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Atribuir etiquetas de sensibilidade a grupos Microsoft 365 no Azure Ative Directory

O Azure Ative Directory (Azure AD) suporta a aplicação de etiquetas de sensibilidade publicadas pelo [Microsoft 365 compliance center](https://sip.protection.office.com/homepage) aos grupos Microsoft 365. As etiquetas de sensibilidade aplicam-se ao grupo em todos os serviços como Outlook, Microsoft Teams e SharePoint. Para obter mais informações sobre o suporte de aplicações da Microsoft 365, consulte [o suporte do Microsoft 365 para etiquetas de sensibilidade.](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)

> [!IMPORTANT]
> Para configurar esta funcionalidade, deve existir pelo menos uma licença ativa do Azure Ative Directory Premium P1 na sua organização Azure AD.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Permitir suporte de etiqueta de sensibilidade no PowerShell

Para aplicar rótulos publicados em grupos, tem primeiro de ativar a funcionalidade. Estes passos permitem a funcionalidade em Azure AD.

1. Abra uma janela Windows PowerShell no seu computador. Podes abri-la sem privilégios elevados.
1. Execute os seguintes comandos para preparar a execução dos cmdlets.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Na página **'Iniciar s-in' na página da sua conta,** insira a sua conta de administração e a palavra-passe para o ligar ao seu serviço e selecione **Iniciar sôm.**
1. Pegue as definições de grupo atuais para a organização Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Se não tiverem sido criadas configurações de grupo para esta organização Azure AD, obterá um erro no cmdlet acima que diz "Não posso vincular o argumento ao parâmetro 'Id' porque é nulo". Neste caso, deve primeiro criar as definições. Siga os [passos em cmdlets do Azure Ative Directory para configurar as definições de grupo](../enterprise-users/groups-settings-cmdlets.md) para criar configurações de grupo para esta organização Azure AD.

1. Em seguida, apresente as definições de grupo atuais.

    ```PowerShell
    $Setting.Values
    ```

1. Em seguida, ative a funcionalidade:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Em seguida, guarde as alterações e aplique as definições:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Também terá de sincronizar as suas etiquetas de sensibilidade com o Azure AD. Para obter instruções, consulte [como permitir etiquetas de sensibilidade para recipientes e sincronizar etiquetas](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels).

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Atribua um rótulo a um novo grupo no portal Azure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com).
1. Selecione **Grupos** e, em seguida, selecione **Novo grupo**.
1. Na página New **Group,** selecione **Office 365** e, em seguida, preencha as informações necessárias para o novo grupo e selecione uma etiqueta de sensibilidade da lista.

   ![Atribua um rótulo de sensibilidade na página de novos grupos](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Guarde as alterações e selecione **Criar**.

O seu grupo é criado e as definições de site e grupo associadas à etiqueta selecionada são automaticamente aplicadas.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Atribua um rótulo a um grupo existente no portal Azure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta de administração de grupos, ou como proprietário do grupo.
1. Selecione **Grupos**.
1. Na página **Todos os grupos,** selecione o grupo que pretende rotular.
1. Na página do grupo selecionado, selecione **Propriedades** e selecione uma etiqueta de sensibilidade da lista.

   ![Atribua um rótulo de sensibilidade na página geral de um grupo](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Selecione **Guardar** para guardar as alterações.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Remova uma etiqueta de um grupo existente no portal Azure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta administração global ou grupos, ou como proprietário do grupo.
1. Selecione **Grupos**.
1. Na página **Todos os grupos,** selecione o grupo de onde pretende remover a etiqueta.
1. Na página **do Grupo,** selecione **Propriedades**.
1. Selecione **Remover**.
1. Selecione **Guardar** para aplicar as suas alterações.

## <a name="using-classic-azure-ad-classifications"></a>Usando classificações clássicas de AD AZure

Depois de ativar esta funcionalidade, as classificações "clássicas" para grupos aparecerão apenas em grupos e sites existentes, e deverá usá-las apenas para novos grupos se criar grupos em apps que não suportem rótulos de sensibilidade. O seu administrador pode convertê-los em etiquetas de sensibilidade mais tarde, se necessário. Classificações clássicas são as antigas classificações que definiu definindo valores para a `ClassificationList` definição em Azure AD PowerShell. Quando esta funcionalidade estiver ativada, essas classificações não serão aplicadas a grupos.

## <a name="troubleshooting-issues"></a>Resolução de problemas

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Rótulos de sensibilidade não estão disponíveis para atribuição em um grupo

A opção de etiqueta de sensibilidade só é apresentada para grupos quando todas as seguintes condições estiverem satisfeitas:

1. As etiquetas são publicadas no Microsoft 365 Compliance Center para esta organização AZure AD.
1. A funcionalidade está ativada, o EnableMIPLabels está definido para True a partir do módulo Azure AD PowerShell.
1. Os Lables são sincronizados com o Azure AD com o cmdlet Execute-AzureAdLabelSync no módulo PowerShell de conformidade & de segurança.
1. O grupo é um grupo Microsoft 365.
1. A organização tem uma licença ativa do Azure Ative Directory Premium P1.
1. O atual utilizador inscrito tem privilégios suficientes para atribuir etiquetas. O utilizador deve ser administrador global, administrador de grupo ou proprietário do grupo.

Certifique-se de que todas as condições estão reunidas para atribuir etiquetas a um grupo.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>A etiqueta que quero atribuir não está na lista

Se a etiqueta que procura não estiver na lista, este poderá ser o caso por uma das seguintes razões:

- A etiqueta pode não ser publicada no Microsoft 365 Compliance Center. Isto poderia igualmente aplicar-se aos rótulos que já não são publicados. Por favor, consulte o seu administrador para mais informações.
- A etiqueta pode ser publicada, no entanto, não está disponível para o utilizador que está inscrito. Consulte o seu administrador para obter mais informações sobre como obter acesso à etiqueta.

### <a name="how-to-change-the-label-on-a-group"></a>Como alterar o rótulo de um grupo

As etiquetas podem ser trocadas a qualquer momento utilizando os mesmos passos que atribuir um rótulo a um grupo existente, da seguinte forma:

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta de administrador global ou do grupo ou como proprietário do grupo.
1. Selecione **Grupos**.
1. Na página **Todos os grupos,** selecione o grupo que pretende rotular.
1. Na página do grupo selecionado, selecione **Propriedades** e selecione uma nova etiqueta de sensibilidade da lista.
1. Selecione **Guardar**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>As alterações na definição do grupo às etiquetas publicadas não são atualizadas nos grupos

Como uma boa prática, não recomendamos que altere as definições de grupo para uma etiqueta depois de a etiqueta ser aplicada a grupos. Quando escoda alterações nas definições de grupo associadas a etiquetas publicadas no [Microsoft 365 compliance center,](https://sip.protection.office.com/homepage)essas alterações de política não são aplicadas automaticamente nos grupos impactados.

Se tiver de fazer uma alteração, utilize um [script Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) para aplicar manualmente atualizações aos grupos impactados. Este método assegura que todos os grupos existentes impõem a nova definição.

## <a name="next-steps"></a>Passos seguintes

- [Utilize etiquetas de sensibilidade com as equipas da Microsoft, grupos Microsoft 365 e sites SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Atualizar grupos após a alteração da política do rótulo manualmente com o script Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Editar as suas definições de grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir grupos através de comandos do PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
