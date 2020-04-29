---
title: Atribuir etiquetas de sensibilidade a grupos - Azure AD / Microsoft Docs
description: Como criar regras de adesão para povoar automaticamente grupos, e uma referência de regras.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78329737"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Atribuir etiquetas de sensibilidade aos grupos do Office 365 no Diretório Ativo azure (pré-visualização)

O Azure Ative Directory (Azure AD) suporta a aplicação de etiquetas de sensibilidade publicadas pelo Centro de [Conformidade Microsoft 365](https://sip.protection.office.com/homepage) para os grupos office 365. As etiquetas de sensibilidade aplicam-se ao grupo através de serviços como Outlook, Microsoft Teams e SharePoint. Esta funcionalidade encontra-se atualmente em pré-visualização pública. Para obter mais informações sobre o suporte de aplicações do Office 365, consulte o [suporte do Office 365 para etiquetas](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)de sensibilidade .

> [!IMPORTANT]
> Para configurar esta funcionalidade, deve existir pelo menos uma licença Azure Ative Directory Premium P1 ativa na sua organização Azure AD.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Ativar suporte de etiqueta de sensibilidade no PowerShell

Para aplicar rótulos publicados em grupos, tem primeiro de ativar a funcionalidade. Estes passos permitem a funcionalidade em Azure AD.

1. Abra uma janela Do Windows PowerShell no seu computador. Pode abri-la sem privilégios elevados.
1. Execute os seguintes comandos para preparar a execução dos cmdlets.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    No **'Iniciar sessão' na página** da sua conta, insira a sua conta de administração e a sua palavra-passe para o ligar ao seu serviço e selecione **Iniciar sessão**.
1. Pegue as definições atuais do grupo para a organização Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Se não foram criadas configurações de grupo para esta organização Azure AD, primeiro deve criar as definições. Siga os passos em [Cmdlets de Diretório Ativo Azure para configurar as configurações](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) do grupo para criar configurações de grupo para esta organização Azure AD.

1. Em seguida, exiba as definições atuais do grupo.

    ```PowerShell
    $Setting.Values
    ```

1. Em seguida, ativar a funcionalidade:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Em seguida, guarde as alterações e aplique as definições:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Já está. Ativou a funcionalidade e pode aplicar rótulos publicados em grupos.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Atribuir um rótulo a um novo grupo no portal Azure

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com).
1. Selecione **Grupos**, e, em **seguida,** selecione Novo grupo .
1. Na página do **Novo Grupo,** selecione o **Office 365**e, em seguida, preencha as informações necessárias para o novo grupo e selecione um rótulo de sensibilidade da lista.

   ![Atribuir uma etiqueta de sensibilidade na página dos novos grupos](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Guarde as suas alterações e selecione **Criar**.

O seu grupo é criado e as definições do site e do grupo associadas à etiqueta selecionada são aplicadas automaticamente.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Atribuir um rótulo a um grupo existente no portal Azure

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta de administração de Grupos, ou como proprietário de grupo.
1. Selecione **Grupos**.
1. A partir da página **Todos os grupos,** selecione o grupo que pretende rotular.
1. Na página do grupo selecionado, selecione **Propriedades** e selecione uma etiqueta de sensibilidade da lista.

   ![Atribuir uma etiqueta de sensibilidade na página geral para um grupo](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Selecione **Guardar** para guardar as alterações.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Remova um rótulo de um grupo existente no portal Azure

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta de administração global ou de grupos, ou como proprietário de grupo.
1. Selecione **Grupos**.
1. A partir da página **Todos os grupos,** selecione o grupo de que pretende remover a etiqueta.
1. Na página **do Grupo,** selecione **Propriedades**.
1. Selecione **Remover**.
1. Selecione **Guardar** para aplicar as suas alterações.

## <a name="using-classic-azure-ad-classifications"></a>Usando classificações clássicas de AD Azure

Depois de ativar esta funcionalidade, as classificações "clássicas" para grupos aparecerão apenas grupos e sites existentes, e você deve usá-los para novos grupos apenas se criar grupos em apps que não suportem rótulos de sensibilidade. A sua administração pode convertê-las em etiquetas de sensibilidade mais tarde, se necessário. Classificações clássicas são as classificações antigas que `ClassificationList` configura, definindo valores para a definição em Azure AD PowerShell. Quando esta funcionalidade estiver ativada, essas classificações não serão aplicadas aos grupos.

## <a name="troubleshooting-issues"></a>Resolução de problemas

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Etiquetas de sensibilidade não estão disponíveis para atribuição num grupo

A opção de etiqueta de sensibilidade só é apresentada para grupos quando todas as seguintes condições forem satisfeitas:

1. As etiquetas são publicadas no Microsoft 365 Compliance Center para este inquilino.
1. A funcionalidade está ativada, o EnableMIPLabels está definido para True in PowerShell.
1. O grupo é um grupo do Office 365.
1. O inquilino tem uma licença ativa azure Ative Directory Premium P1.
1. O atual utilizador inscrito tem privilégios suficientes para atribuir etiquetas. O utilizador deve ser um Administrador Global, Administrador de Grupo ou o proprietário do grupo.

Certifique-se de que todas as condições estão reunidas para atribuir etiquetas a um grupo.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>O rótulo que quero atribuir não está na lista.

Se o rótulo que procura não estiver na lista, pode ser o caso de uma das seguintes razões:

- A etiqueta pode não ser publicada no Microsoft 365 Compliance Center. Isto também pode aplicar-se às etiquetas que já não são publicadas. Por favor, consulte o seu administrador para mais informações.
- No entanto, a etiqueta pode ser publicada, não disponível para o utilizador que esteja inscrito. Consulte o seu administrador para obter mais informações sobre como obter acesso à etiqueta.

### <a name="how-to-change-the-label-on-a-group"></a>Como alterar o rótulo em um grupo

As etiquetas podem ser trocadas a qualquer momento utilizando os mesmos passos que a atribuição de uma etiqueta a um grupo existente, da seguinte forma:

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta de administrador global ou de grupo ou como proprietário do grupo.
1. Selecione **Grupos**.
1. A partir da página **Todos os grupos,** selecione o grupo que pretende rotular.
1. Na página do grupo selecionado, selecione **Properties** e selecione um novo rótulo de sensibilidade da lista.
1. Selecione **Guardar**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>As alterações à definição de grupos para rótulos publicados não são atualizadas nos grupos

Como uma boa prática, não recomendamos que altere as definições de grupo para uma etiqueta após a aplicação da etiqueta aos grupos. Quando efaz alterações nas definições de grupo associadas às etiquetas publicadas no Centro de Conformidade microsoft [365,](https://sip.protection.office.com/homepage)essas alterações de política não são aplicadas automaticamente nos grupos impactados.

Se tiver de fazer uma alteração, utilize um [script Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) para aplicar manualmente atualizações aos grupos afetados. Este método assegura que todos os grupos existentes apliquem a nova configuração.

## <a name="next-steps"></a>Passos seguintes

- [Utilize etiquetas de sensibilidade com microsoft teams, grupos do Office 365 e sites SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Atualizar grupos após mudança de política de etiquetamanualmente com script Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Editar as suas definições de grupo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Gerir grupos através de comandos do PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
