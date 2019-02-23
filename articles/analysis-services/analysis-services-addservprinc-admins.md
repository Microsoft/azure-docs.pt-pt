---
title: Adicionar um principal de serviço à função de administrador de servidor Azure Analysis Services | Documentos da Microsoft
description: Saiba como adicionar um principal de serviço de automatização para a função de administrador do servidor
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 701be795ca217c4a2dc5a7dbaa3a3717d16c85bc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730227"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Adicionar um principal de serviço para a função de administrador do servidor 

 Para automatizar autónomas tarefas do PowerShell, tem de ter um principal de serviço **administrador do servidor** privilégios no servidor do Analysis Services a ser gerido. Este artigo descreve como adicionar um principal de serviço para a função de administradores de servidor num servidor do Azure.

## <a name="before-you-begin"></a>Antes de começar
Antes de concluir esta tarefa, tem de ter um principal de serviço registado no Azure Active Directory.

[Criar principal de serviço - portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar principal de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Permissões obrigatórias
Para concluir essa tarefa, tem de ter [administrador do servidor](analysis-services-server-admins.md) permissões ao servidor do Azure. 

## <a name="add-service-principal-to-server-administrators-role"></a>Adicionar principal de serviço à função de administradores de servidor

1. No SSMS, ligue ao seu servidor do Azure.
2. Na **propriedades do servidor** > **segurança**, clique em **adicionar**.
3. Na **selecionar um utilizador ou grupo**, procure a aplicação registada por nome, selecione e, em seguida, clique em **Add**.

    ![Pesquisar por conta do principal de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique se o ID de conta do principal de serviço e, em seguida, clique em **OK**.
    
    ![Pesquisar por conta do principal de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Para operações de servidor utilizando cmdlets do PowerShell do Azure, o agendador em execução principal de serviço também deve pertencer aos **proprietário** função para o recurso na [controlo de acesso de controlo (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Informações relacionadas

* [Transferir o módulo de PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixe o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


