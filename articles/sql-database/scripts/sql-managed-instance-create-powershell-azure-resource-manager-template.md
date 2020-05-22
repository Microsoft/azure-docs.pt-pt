---
title: Exemplo de modelo - crie uma instância gerida na Base de Dados Azure SQL
description: Utilize este script de exemplo Azure PowerShell para criar uma instância gerida na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: eccc3dcb2731183719833ae99f475eb27e6a05c1
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774511"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Use powerShell com modelo de Gestor de Recursos Azure para criar uma instância gerida na Base de Dados Azure SQL

A base de dados Azure SQL Managed Instance pode ser criada usando modelos de biblioteca Azure PowerShell e Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer AZ PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

Os comandos Azure PowerShell podem começar a ser implantados utilizando o modelo de Gestor de Recursos Azure predefinido. As seguintes propriedades podem ser especificadas no modelo:

- Nome da instância
- Nome de utilizador e senha do administrador SQL.
- Tamanho da instância (número de núcleos e tamanho máximo de armazenamento).
- VNet e subnet onde a instância será colocada.
- Colagem ao nível do servidor da instância (Pré-visualização).

O nome da instância, nome de utilizador do Administrador SQL, VNet/subnet e colagem não podem ser alterados mais tarde. Outras propriedades podem ser alteradas.

## <a name="prerequisites"></a>Pré-requisitos

Esta amostra pressupõe que [criou um ambiente](../sql-database-managed-instance-create-vnet-subnet.md) de rede válido ou [vNet existente modificado](../sql-database-managed-instance-configure-vnet-subnet.md) para a sua Instância Gerida. A amostra utiliza os cmdlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) e [Get-AzVirtualNetwork,](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) por isso certifique-se de que instalou os seguintes módulos PowerShell:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

O seguinte conteúdo deve ser colocado num ficheiro que represente um modelo que será utilizado para criar a instância:

```json
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
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
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

Supõe-se que o Azure VNet com a subnet devidamente configurada já existe. Se não tiver uma sub-rede devidamente configurada, prepare o ambiente de rede utilizando um modelo separado de Gestão de [Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) que possa ser executado independentemente ou incluído neste modelo.

Guarde o conteúdo deste ficheiro como ficheiro .json, coloque o caminho do ficheiro no seguinte script PowerShell e altere os nomes dos objetos no script:

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

Uma vez que o script tenha sido executado com sucesso, a Base de Dados SQL pode ser acedida a partir de todos os serviços Azure e do endereço IP configurado.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
