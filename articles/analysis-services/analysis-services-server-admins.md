---
title: Gerir administradores de servidores em Azure Analysis Services / Microsoft Docs
description: Este artigo descreve como gerir os administradores de servidores para um servidor Azure Analysis Services utilizando o portal Azure, PowerShell ou REST APIs.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bc17d27837d5b96f06b5172fb019db873418db94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922954"
---
# <a name="manage-server-administrators"></a>Gerir administradores de servidor

Os administradores do servidor devem ser um utilizador válido, principal de serviço ou grupo de segurança no Azure Ative Directory (Azure AD) para o inquilino em que o servidor reside. Pode utilizar **os Administradores de Serviços de Análise** para o seu servidor no portal Azure, Server Properties em SSMS, PowerShell ou REST API para gerir administradores de servidores. 

Ao adicionar um **grupo de segurança,** utilize `obj:groupid@tenantid` . Os principais de serviço não são suportados em grupos de segurança adicionados à função de administrador do servidor.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Para adicionar administradores de servidores utilizando o portal Azure

1. No portal, para o seu servidor, clique em **Administração de Serviços de Análise**.
2. In ** \<servername> - Departamentos de Serviços de Análise,** clique em **Adicionar**.
3. No **Add Server Administrators**, selecione as contas de utilizador do seu AD Azure ou convide utilizadores externos por endereço de e-mail.

    ![Administradores de servidores no portal Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Para adicionar administradores de servidores utilizando SSMS

1. Clique com o botão direito no servidor > **Propriedades**.
2. Nas **propriedades do servidor de análise,** clique em **Segurança**.
3. Clique em **Adicionar**e, em seguida, insira o endereço de e-mail para um utilizador ou grupo no seu AD Azure.
   
    ![Adicionar administradores de servidores em SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilize o cmdlet [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) para especificar o parâmetro do Administrador ao criar um novo servidor. <br>
Utilize [o cmdlet Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) para modificar o parâmetro de administrador para um servidor existente.

## <a name="rest-api"></a>API REST

Use [Criar](https://docs.microsoft.com/rest/api/analysisservices/servers/create) para especificar a propriedade asAdministrator ao criar um novo servidor. <br>
Utilize [a Atualização](https://docs.microsoft.com/rest/api/analysisservices/servers/update) para especificar a propriedade asAdministrator ao modificar um servidor existente. <br>



## <a name="next-steps"></a>Passos seguintes 

[Autenticação e permissões de utilizador](analysis-services-manage-users.md)  
[Gerir funções de base de dados e utilizadores](analysis-services-database-users.md)  
[Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../role-based-access-control/overview.md)  
