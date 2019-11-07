---
title: Gerenciar administradores de servidor no Azure Analysis Services | Microsoft Docs
description: Este artigo descreve como gerenciar administradores de servidor para um Azure Analysis Services Server usando o portal do Azure, o PowerShell ou as APIs REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572614"
---
# <a name="manage-server-administrators"></a>Gerir administradores de servidor

Os administradores de servidor devem ser um usuário ou grupo de segurança válido no Azure Active Directory (Azure AD) para o locatário no qual o servidor reside. Você pode usar os **Administradores de Analysis Services** para o servidor em portal do Azure, propriedades de servidor no SSMS, PowerShell ou API REST para gerenciar administradores de servidor. 

Os **grupos de segurança** devem ser [habilitados para email](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) com a propriedade `MailEnabled` definida como `True`. Ao especificar um grupo por endereço de email, use `obj:groupid@tenantid`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Para adicionar administradores de servidor usando portal do Azure

1. No portal, para seu servidor, clique em **Analysis Services administradores**.
2. Em **\<servername >-Analysis Services administradores**, clique em **Adicionar**.
3. Em **adicionar administradores de servidor**, selecione contas de usuário do Azure ad ou convide usuários externos por endereço de email.

    ![Administradores de servidor no portal do Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Para adicionar administradores de servidor usando o SSMS

1. Clique com o botão direito do mouse no servidor > **Propriedades**.
2. Em **Propriedades do Analysis Server**, clique em **segurança**.
3. Clique em **Adicionar**e insira o endereço de email para um usuário ou grupo no Azure AD.
   
    ![Adicionar administradores de servidor no SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Use o cmdlet [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) para especificar o parâmetro de administrador ao criar um novo servidor. <br>
Use o cmdlet [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) para modificar o parâmetro de administrador de um servidor existente.

## <a name="rest-api"></a>API REST

Use [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) para especificar a propriedade asadministrator ao criar um novo servidor. <br>
Use [Atualizar](https://docs.microsoft.com/rest/api/analysisservices/servers/update) para especificar a propriedade asadministrator ao modificar um servidor existente. <br>



## <a name="next-steps"></a>Passos seguintes 

[Autenticação e permissões de utilizador](analysis-services-manage-users.md)  
[Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md)  
[Controlo de Acesso Baseado em Funções](../role-based-access-control/overview.md)  

