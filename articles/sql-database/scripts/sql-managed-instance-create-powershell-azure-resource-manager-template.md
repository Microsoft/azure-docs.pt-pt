---
title: Exemplo do PowerShell - criar uma instância gerida na SQL Database do Azure | Documentos da Microsoft
description: Script de exemplo do PowerShell do Azure para criar uma instância gerida na base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 33a0e90f1a0b63138168c44b87385c05aac39c69
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57869766"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Utilize o PowerShell com o modelo Azure Resource Manager para criar uma instância gerida na base de dados do Azure SQL

Instância de gerida de base de dados de SQL do Azure podem ser criada usando a biblioteca do PowerShell do Azure e os modelos Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o PowerShell de AZ 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

Comandos do PowerShell do Azure podem começar a implantação usando o modelo predefinido do Azure Resource Manager. As seguintes propriedades podem ser especificadas no modelo:

- Nome da Instância
- Nome de utilizador de administrador SQL e a palavra-passe.
- Tamanho da instância (número de núcleos e o tamanho de armazenamento máximo).
- VNet e sub-rede onde a instância será colocada.
- Agrupamento de ao nível do servidor da instância (pré-visualização).

Nome da instância, o nome de utilizador do administrador do SQL, o VNet/sub-rede e o agrupamento não não possível alterar mais tarde. Outras propriedades da instância podem ser alteradas.

## <a name="prerequisites"></a>Pré-requisitos

Este exemplo pressupõe que tenha [criado um ambiente de rede válido](../sql-database-managed-instance-create-vnet-subnet.md) ou [modificado VNet existente](../sql-database-managed-instance-configure-vnet-subnet.md) da sua instância gerida. Este exemplo utiliza os commandlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) por isso, certifique-se de que tem instalado os módulos do PowerShell seguintes:

```
Install-Module AzureRM.Network
Install-Module AzureRM.Resources
```

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

O seguinte conteúdo deve ser colocado num arquivo que representa um modelo que será utilizado para criar a instância:

```
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Suposição é que a VNet do Azure com a sub-rede corretamente configurada já existe. Se não tiver uma sub-rede configurada corretamente, preparar o ambiente de rede usando separado [geridos de recursos do Azure modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) que podem ser executadas de forma independente ou incluídas neste modelo.

Guarde o conteúdo deste ficheiro como ficheiro. JSON e colocar o caminho de ficheiro no seguinte script do PowerShell para alterar os nomes dos objetos no script:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Assim que o script tiver sido executado com êxito, a Base de Dados SQL pode ser acedida a partir de todos os serviços do Azure e do endereço IP configurado.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
