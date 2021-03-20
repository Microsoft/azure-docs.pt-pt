---
title: Adicione o principal do serviço à função de administrador dos Serviços de Análise Azure | Microsoft Docs
description: Saiba como adicionar um principal de serviço de automação à função de administração de servidores Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b1d5f8ec628245756c53e4dfeeecdeb3a4bebc2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100733"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Adicione um principal de serviço à função de administrador do servidor 

 Para automatizar tarefas sem supervisão do PowerShell, um diretor de serviço deve ter privilégios de administrador de **servidores** no servidor de Serviços de Análise a ser gerido. Este artigo descreve como adicionar um principal de serviço à função de administradores do servidor num servidor Azure AS. Pode fazê-lo usando o SQL Server Management Studio ou um modelo de Gestor de Recursos. 

> [!NOTE]
> Os princípios de serviço devem ser adicionados diretamente à função de administrador do servidor. Adicionar um principal de serviço a um grupo de segurança e, em seguida, adicionar que o grupo de segurança à função de administrador do servidor não é suportado. 

## <a name="before-you-begin"></a>Antes de começar
Antes de completar esta tarefa, deve ter um diretor de serviço registado no Azure Ative Directory.

[Criar um principal de serviço – portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar principal de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Utilizar o SQL Server Management Studio

Pode configurar administradores de servidores utilizando o SQL Server Management Studio (SSMS). Para completar esta tarefa, tem de ter permissões [de administrador de servidores](analysis-services-server-admins.md) no servidor Azure AS. 

1. No SSMS, ligue-se ao seu servidor Azure AS.
2. Na Segurança **das Propriedades do Servidor,**  >  clique em **Adicionar**.
3. Em **Selecionar um Utilizador ou Grupo,** procure a sua aplicação registada pelo nome, selecione e, em seguida, clique em **Adicionar**.

    ![Pesquisa de conta principal de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique o ID da conta principal do serviço e, em seguida, clique **em OK**.
    
    ![Screenshot que mostra o ID da conta principal do serviço e realça o botão OK.](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Utilizar um modelo do Resource Manager

Também pode configurar administradores de servidores implantando o servidor Serviços de Análise utilizando um modelo de Gestor de Recursos Azure. A identidade que executa a implantação deve pertencer ao papel **do Contribuinte** para o recurso no controlo de acesso baseado em [funções Azure (Azure RBAC)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> O diretor de serviço deve ser adicionado utilizando o formato `app:{service-principal-client-id}@{azure-ad-tenant-id}` .

O modelo seguinte do Gestor de Recursos implementa um servidor de Serviços de Análise com um principal de serviço especificado adicionado à função de Administração de Serviços de Análise:

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

## <a name="using-managed-identities"></a>Utilização de identidades geridas

Uma identidade gerida também pode ser adicionada à lista de Administradores de Serviços de Análise. Por exemplo, pode ter uma App Lógica com uma identidade gerida atribuída ao sistema , e pretende [conceder-lhe](../logic-apps/create-managed-service-identity.md)a capacidade de administrar o seu servidor de Serviços de Análise.

Na maioria das partes do portal Azure e APIs, identidades geridas são identificadas usando o seu iD principal de serviço. No entanto, os Serviços de Análise exigem que sejam identificados utilizando a identificação do seu cliente. Para obter a ID do cliente para um principal de serviço, pode utilizar o Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Alternativamente, pode utilizar o PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Em seguida, pode utilizar este ID do cliente em conjunto com o ID do inquilino para adicionar a identidade gerida à lista de Administradores de Serviços de Análise, como descrito acima.

## <a name="related-information"></a>Informações relacionadas

* [Baixar módulo PowerShell do servidor SQL](/sql/ssms/download-sql-server-ps-module)   
* [Baixar SSMS](/sql/ssms/download-sql-server-management-studio-ssms)