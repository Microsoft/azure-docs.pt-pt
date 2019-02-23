---
title: Gerir administradores de servidor no Azure Analysis Services | Documentos da Microsoft
description: Saiba como gerenciar administradores de servidor para um servidor de Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 25eb111871bbe3b18f59b0d6c123c72f3e55c859
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737115"
---
# <a name="manage-server-administrators"></a>Gerir administradores de servidor

Os administradores de servidor tem de ser um grupo de segurança ou de utilizador válido no Azure Active Directory (Azure AD) para o inquilino no qual reside o servidor. Pode usar **administradores do Analysis Services** para o seu servidor no portal do Azure, propriedades do servidor no SSMS, PowerShell ou REST API para gerir os administradores de servidor. 

> [!NOTE]
> Grupos de segurança tem de ter o `MailEnabled` definida como `True`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Para adicionar os administradores de servidor utilizando o portal do Azure

1. No portal, do servidor, clique em **administradores do Analysis Services**.
2. Na  **\<servername >-administradores do Analysis Services**, clique em **Add**.
3. Na **adicionar administradores de servidor**, selecione as contas de utilizador do seu Azure AD ou convidar utilizadores externos por endereço de e-mail.

    ![Administradores de servidor no portal do Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Para adicionar os administradores de servidor com o SSMS

1. Com o botão direito do servidor > **propriedades**.
2. Na **propriedades do servidor de Analysis**, clique em **segurança**.
3. Clique em **adicionar**e, em seguida, introduza o endereço de e-mail para um utilizador ou grupo no seu Azure AD.
   
    ![Adicionar administradores de servidor no SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Uso [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) cmdlet para especificar o parâmetro de administrador ao criar um novo servidor. <br>
Uso [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) cmdlet para modificar o parâmetro de administrador para um servidor existente.

## <a name="rest-api"></a>API REST

Uso [criar](https://docs.microsoft.com/rest/api/analysisservices/servers/create) para especificar a propriedade asAdministrator ao criar um novo servidor. <br>
Uso [atualização](https://docs.microsoft.com/rest/api/analysisservices/servers/update) para especificar a propriedade asAdministrator quando modificar um servidor existente. <br>



## <a name="next-steps"></a>Passos Seguintes 

[Autenticação e permissões de utilizador](analysis-services-manage-users.md)  
[Gerir funções de base de dados e utilizadores](analysis-services-database-users.md)  
[Controlo de Acesso Baseado em Funções](../role-based-access-control/overview.md)  

