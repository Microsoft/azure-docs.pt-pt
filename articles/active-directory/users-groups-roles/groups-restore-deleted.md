---
title: Restaurar um grupo eliminado do Office 365 - Azure AD [ AD ] Microsoft Docs
description: Como restaurar um grupo eliminado, ver grupos restoráveis e eliminar permanentemente um grupo no Diretório Ativo Azure
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d212df51a58125e3b959a18f5cf2ac9d391d30
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74422370"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Restaurar um grupo eliminado do Office 365 no Azure Active Directory

Quando elimina um grupo do Office 365 no Azure Active Directory (Azure AD), o grupo eliminado é retido, mas não é visível durante 30 dias a contar da data de eliminação. Este comportamento é assim para que o grupo e o respetivo conteúdo possam ser restaurados, se necessário. Esta funcionalidade está restringida exclusivamente a grupos do Office 365 no Azure AD. Não está disponível para grupos de segurança e grupos de distribuição. Por favor, note que o período de restauração do grupo de 30 dias não é personalizável.

> [!NOTE]
> Não utilize `Remove-MsolGroup` porque remove permanentemente o grupo. Utilize `Remove-AzureADMSGroup` sempre para eliminar um grupo do Office 365.

As permissões necessárias para restaurar um grupo podem ser qualquer um dos seguintes procedimentos:

Função | Permissões
--------- | ---------
Administrador global, administrador do Grupo, suporte partner tier2 e administrador intune | Pode restaurar qualquer grupo do Office 365 eliminado
Administrador de utilizador e suporte parceiro tier1 | Pode restaurar qualquer grupo eliminado do Office 365, exceto os grupos atribuídos ao papel de Administrador da Empresa
Utilizador | Pode restaurar qualquer grupo eliminado do Office 365 que possuam

## <a name="view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore"></a>Ver e gerir os grupos eliminados do Office 365 que estão disponíveis para restaurar

1. Inscreva-se no [centro de administração da AD Azure](https://aad.portal.azure.com) com uma conta de administrador do Utilizador.

2. Selecione **Grupos**, então selecione **Grupos Eliminados** para visualizar os grupos eliminados que estão disponíveis para restaurar.

    ![ver grupos que estão disponíveis para restaurar](media/groups-lifecycle/deleted-groups3.png)

3. Na lâmina dos **grupos Apagados,** pode:

   - Restaurar o grupo eliminado e os seus conteúdos selecionando o **grupo Restaurar**.
   - Retire permanentemente o grupo eliminado selecionando **apagar permanentemente**. Para remover permanentemente um grupo, deve ser administrador.

## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore-using-powershell"></a>Ver os grupos eliminados do Office 365 que estão disponíveis para restaurar usando powershell

Os seguintes cmdlets podem ser utilizados para ver os grupos eliminados, para verificar se os que lhe interessam ainda não foram removidos permanentemente. Estes cmdlets fazem parte do [Módulo do PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureAD/). Pode encontrar mais informações sobre este módulo no artigo [Versão 2 do PowerShell do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

1.  Execute o cmdlet seguinte para apresentar todos os grupos eliminados do Office 365 no seu inquilino que ainda estão disponíveis para restaurar.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Em alternativa, se conhecer o objectID de um grupo específico (e pode obtê-lo a partir do cmdlet no passo 1), execute o cmdlet seguinte para verificar se o grupo eliminado específico ainda não foi permanentemente removido.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-office-365-group-using-powershell"></a>Como restaurar o seu grupo eliminado Office 365 usando powershell

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

Para verificar se restaurou com êxito um grupo do Office 365, execute o cmdlet `Get-AzureADGroup –ObjectId <objectId>` para apresentar informações sobre o grupo. Após o restauro, o pedido é concluído:

- O grupo é apresentado na barra de navegação esquerda no Exchange
- O plano para o grupo será apresentado no Planner
- Quaisquer sites do SharePoint e todos os seus conteúdos estarão disponíveis
- O grupo pode ser acedido a partir de qualquer um dos pontos finais do Exchange e outras cargas de trabalho do Office 365 que suportam grupos de trabalho do Office 365

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre os grupos do Azure Active Directory.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
