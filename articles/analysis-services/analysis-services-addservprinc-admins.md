---
title: Adicionar uma entidade de serviço à função de administrador do servidor Azure Analysis Services | Microsoft Docs
description: Saiba como adicionar uma entidade de serviço de automação à função de administrador do servidor
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e4650d3be5ce21a49b419577eaf83225b1c0d4d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298712"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Adicionar uma entidade de serviço à função de administrador do servidor 

 Para automatizar tarefas autônomas do PowerShell, uma entidade de serviço deve ter privilégios de **administrador do servidor** no Analysis Services servidor que está sendo gerenciado. Este artigo descreve como adicionar uma entidade de serviço à função Administradores de servidor em um servidor do Azure AS.

## <a name="before-you-begin"></a>Antes de começar
Antes de concluir essa tarefa, você deve ter uma entidade de serviço registrada em Azure Active Directory.

[Criar entidade de serviço-portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar principal de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Permissões obrigatórias
Para concluir essa tarefa, você deve ter permissões de [administrador do servidor](analysis-services-server-admins.md) no Azure como servidor. 

## <a name="add-service-principal-to-server-administrators-role"></a>Adicionar entidade de serviço à função de administradores do servidor

1. No SSMS, conecte-se ao servidor do Azure AS.
2. Em **Propriedades do servidor** > **segurança**, clique em **Adicionar**.
3. Em **selecionar um usuário ou grupo**, pesquise seu aplicativo registrado por nome, selecione e clique em **Adicionar**.

    ![Pesquisar conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique a ID da conta da entidade de serviço e clique em **OK**.
    
    ![Pesquisar conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Para operações de servidor usando cmdlets Azure PowerShell, o Agendador executando entidade de serviço também deve pertencer à função de **proprietário** para o recurso no [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Informações relacionadas

* [Baixar SQL Server PowerShell módulo](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


