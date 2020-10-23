---
title: Restaurar um grupo Microsoft 365 eliminado - Azure AD / Microsoft Docs
description: Como restaurar um grupo eliminado, ver grupos restauradores e eliminar permanentemente um grupo no Azure Ative Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 092288d320874488cee4d2f097d7406c0757e8a0
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379236"
---
# <a name="restore-a-deleted-microsoft-365-group-in-azure-active-directory"></a>Restaurar um grupo Microsoft 365 eliminado no Azure Ative Directory

Quando elimina um grupo Microsoft 365 no Azure Ative Directory (Azure AD), o grupo eliminado é mantido mas não visível durante 30 dias a partir da data de eliminação. Este comportamento é assim para que o grupo e o respetivo conteúdo possam ser restaurados, se necessário. Esta funcionalidade é restrita exclusivamente aos grupos Microsoft 365 em AZure AD. Não está disponível para grupos de segurança e grupos de distribuição. Por favor, note que o período de restauração do grupo de 30 dias não é personalizável.

> [!NOTE]
> Não utilize `Remove-MsolGroup` porque remove permanentemente o grupo. Utilize sempre `Remove-AzureADMSGroup` para eliminar um grupo Microsoft 365.

As permissões necessárias para restaurar um grupo podem ser qualquer um dos seguintes procedimentos:

Função | Permissões
--------- | ---------
Administrador global, administrador do grupo, suporte de Partner Tier2 e administrador intune | Pode restaurar qualquer grupo eliminado do Microsoft 365
Administrador do utilizador e suporte ao Partner Tier1 | Pode restaurar qualquer grupo microsoft 365 eliminado, exceto os grupos atribuídos à função de Administrador da Empresa
Utilizador | Pode restaurar qualquer grupo microsoft 365 eliminado que possuam

## <a name="view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore"></a>Ver e gerir os grupos microsoft 365 eliminados que estão disponíveis para restaurar

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta de administrador do Utilizador.

2. Selecione **Grupos**, em seguida, **selecione grupos eliminados** para visualizar os grupos eliminados que estão disponíveis para restaurar.

    ![ver grupos que estão disponíveis para restaurar](./media/groups-restore-deleted/deleted-groups3.png)

3. Na lâmina dos **grupos eliminados,** pode:

   - Restaurar o grupo eliminado e o seu conteúdo selecionando **o grupo Restore**.
   - Remova permanentemente o grupo eliminado selecionando **Eliminar permanentemente**. Para remover permanentemente um grupo, tem de ser administrador.

## <a name="view-the-deleted-microsoft-365-groups-that-are-available-to-restore-using-powershell"></a>Ver os grupos microsoft 365 eliminados que estão disponíveis para restaurar usando Powershell

Os seguintes cmdlets podem ser utilizados para ver os grupos eliminados, para verificar se os que lhe interessam ainda não foram removidos permanentemente. Estes cmdlets fazem parte do [Módulo do PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureAD/). Pode encontrar mais informações sobre este módulo no artigo [Versão 2 do PowerShell do Azure Active Directory](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

1.  Execute o cmdlet seguinte para exibir todos os grupos microsoft 365 eliminados na sua organização Azure AD que ainda estão disponíveis para restaurar.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Em alternativa, se conhecer o objectID de um grupo específico (e pode obtê-lo a partir do cmdlet no passo 1), execute o cmdlet seguinte para verificar se o grupo eliminado específico ainda não foi permanentemente removido.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-microsoft-365-group-using-powershell"></a>Como restaurar o seu grupo Microsoft 365 eliminado usando o Powershell

Assim que tiver verificado que o grupo ainda está disponível para restaurar, restaure o grupo eliminado com um dos seguintes passos. Se o grupo contiver documentos, sites de SP ou outros objetos persistentes, poderá demorar até 24 horas para que um grupo e o respetivo conteúdo sejam completamente restaurados.

1. Execute o cmdlet seguinte para restaurar o grupo e o respetivo conteúdo.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. Em alternativa, o cmdlet seguinte pode ser executado para remover o grupo eliminado permanentemente.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Como sei se funcionou?

Para verificar se restaurou com sucesso um grupo Microsoft 365, execute o `Get-AzureADGroup –ObjectId <objectId>` cmdlet para mostrar informações sobre o grupo. Após o restauro, o pedido é concluído:

- O grupo é apresentado na barra de navegação esquerda no Exchange
- O plano para o grupo será apresentado no Planner
- Quaisquer sites sharePoint e todos os seus conteúdos estarão disponíveis
- O grupo pode ser acedido a partir de qualquer um dos pontos finais do Exchange e outras cargas de trabalho do Microsoft365 que suportam grupos Microsoft 365

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre os grupos do Azure Active Directory.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
