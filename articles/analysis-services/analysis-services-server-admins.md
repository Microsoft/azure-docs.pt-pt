---
title: Gerir administradores de servidores em Serviços de Análise Azure [ Microsoft Docs
description: Este artigo descreve como gerir os administradores de servidores para um servidor de Serviços de Análise Azure utilizando o portal Azure, PowerShell ou REST APIs.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572614"
---
# <a name="manage-server-administrators"></a>Gerir administradores de servidor

Os administradores do servidor devem ser um utilizador ou grupo de segurança válido no Diretório Ativo Azure (Azure AD) para o inquilino em que o servidor reside. Pode utilizar administradores de **serviços** de análise para o seu servidor no portal Azure, Propriedades do Servidor em SSMS, PowerShell ou REST API para gerir administradores de servidores. 

**Os grupos** de segurança devem `MailEnabled` ser `True` [ativados por correio](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) com a propriedade definida para . Ao especificar um grupo por `obj:groupid@tenantid`e-mail a utilizar .

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Para adicionar administradores de servidores utilizando o portal Azure

1. No portal, para o seu servidor, clique em Administrações de **Serviços**de Análise .
2. Em ** \<nome de servidor> - Administrações**de Serviços de Análise, clique em **Adicionar**.
3. Em Adicionar Administradores de **Servidores,** selecione contas de utilizador do seu AD Azure ou convide utilizadores externos através de endereço de e-mail.

    ![Servidor Administradores no portal Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Para adicionar administradores de servidores utilizando SSMS

1. Clique à direita no servidor > **Propriedades**.
2. Na **Análise de Propriedades do Servidor,** clique em **Segurança**.
3. Clique em **Adicionar**e, em seguida, introduza o endereço de e-mail para um utilizador ou grupo no seu Anúncio Azure.
   
    ![Adicionar administradores de servidores em SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilize o [cmdlet New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) para especificar o parâmetro do Administrador ao criar um novo servidor. <br>
Utilize o [Cmdlet Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) para modificar o parâmetro do Administrador para um servidor existente.

## <a name="rest-api"></a>API REST

Use [criar](https://docs.microsoft.com/rest/api/analysisservices/servers/create) para especificar a propriedade asAdministrator ao criar um novo servidor. <br>
Utilize o [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) para especificar a propriedade asAdministrator ao modificar um servidor existente. <br>



## <a name="next-steps"></a>Passos seguintes 

[Autenticação e permissões de utilizador](analysis-services-manage-users.md)  
[Gerir funções de base de dados e utilizadores](analysis-services-database-users.md)  
[Controlo de Acesso baseado em papéis](../role-based-access-control/overview.md)  

