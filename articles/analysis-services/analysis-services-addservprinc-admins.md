---
title: Adicionar serviço principal ao papel de administrador dos Serviços de Análise Azure  Microsoft Docs
description: Saiba como adicionar um serviço de automação principal ao papel de administração do servidor do Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b75740e9bff714ad68c93bea7e387e60da2f1c59
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212507"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Adicione um principal de serviço à função de administrador do servidor 

 Para automatizar tarefas PowerShell não atendidas, um diretor de serviço deve ter privilégios de administrador de **servidor** no servidor serviços de análise sendo geridos. Este artigo descreve como adicionar um principal de serviço ao papel de administradores do servidor num servidor Azure AS. Pode fazê-lo usando o Estúdio de Gestão de Servidores SQL ou um modelo de Gestor de Recursos.
 
> [!NOTE]
> Para operações de servidor utilizando cmdlets Azure PowerShell, o diretor de serviço também deve pertencer à função **Proprietário** para o recurso no Controlo de Acesso baseado em [papel Azure (RBAC)](../role-based-access-control/overview.md). 

## <a name="before-you-begin"></a>Antes de começar
Antes de completar esta tarefa, deve ter um diretor de serviço registado no Diretório Ativo Azure.

[Criar o diretor de serviço - portal Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar principal de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Utilizar o SQL Server Management Studio

Pode configurar administradores de servidores utilizando o Estúdio de Gestão de Servidores SQL (SSMS). Para completar esta tarefa, deve ter permissões de administrador de [servidor](analysis-services-server-admins.md) no servidor Azure AS. 

1. No SSMS, ligue-se ao seu servidor Azure AS.
2. Nas **propriedades do servidor** > **Security,** clique em **Adicionar**.
3. Em **Selecione um Utilizador ou Grupo,** procure a sua aplicação registada pelo nome, selecione e, em seguida, clique em **Adicionar**.

    ![Procurar conta principal de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique o ID da conta principal do serviço e, em seguida, clique **ok**.
    
    ![Procurar conta principal de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Usando um modelo do Resource Manager

Também pode configurar os administradores do servidor implementando o servidor de Serviços de Análise utilizando um modelo de Gestor de Recursos Azure. A identidade que executa a implantação deve pertencer ao papel **de Contribuinte** para o recurso no Controlo de Acesso baseado em [Funções Azure (RBAC)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> O diretor de serviço deve ser adicionado utilizando o formato `app:{service-principal-client-id}@{azure-ad-tenant-id}`.

O seguinte modelo de Gestor de Recursos implementa um servidor de Serviços de Análise com um principal de serviço especificado adicionado à função de Administração de Serviços de Análise:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="related-information"></a>Informações relacionadas

* [Descarregue o módulo PowerShell do servidor SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


