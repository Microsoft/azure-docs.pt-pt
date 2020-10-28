---
title: Criar uma instância gerida (modelo ARM & PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Utilize este script exemplo Azure PowerShell para criar uma instância gerida.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 7c364fe7fa0ac4dd70a01a75478289ea861dee7f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790836"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Use o PowerShell com um modelo de Gestor de Recursos Azure para criar uma instância gerida

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Pode criar uma instância gerida utilizando os modelos da biblioteca Azure PowerShell e do Gestor de Recursos Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a Azure PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, corra `Connect-AzAccount` para criar uma ligação ao Azure.

Os comandos Azure PowerShell podem iniciar a implementação utilizando um modelo predefinido do Gestor de Recursos Azure. As seguintes propriedades podem ser especificadas no modelo:

- Nome da instância gerida
- Nome de utilizador e senha de administrador SQL.
- Tamanho do caso (número de núcleos e tamanho máximo de armazenamento).
- VNet e sub-rede onde o caso será colocado.
- Colagem ao nível do servidor da instância (pré-visualização).

O nome de exemplo, nome de utilizador do administrador SQL, VNet/subnet e colagem não podem ser alterados mais tarde. Outras situações podem ser alteradas.

## <a name="prerequisites"></a>Pré-requisitos

Esta amostra pressupõe que [criou um ambiente de rede válido](../virtual-network-subnet-create-arm-template.md) ou [modificou um VNet existente](../vnet-existing-add-subnet.md) para a sua instância gerida. Pode preparar o ambiente de rede utilizando um modelo separado [do Gestor de Recursos Azure,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)se necessário. 


A amostra utiliza os cmdlets [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [Get-AzVirtualNetwork,](/powershell/module/az.network/get-azvirtualnetwork)por isso certifique-se de que instalou os seguintes módulos PowerShell:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager


Guarde o seguinte script num ficheiro .json e note a localização do ficheiro: 

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

Atualize o seguinte script PowerShell com o caminho de ficheiro correto para o ficheiro .json que guardou anteriormente e altere os nomes dos objetos no script:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
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

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Uma vez que o script esteja concluído, a instância gerida pode ser acedida a partir de todos os serviços Azure e do endereço IP configurado.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure PowerShell, consulte [a documentação da Azure PowerShell](/powershell/azure/).

As amostras adicionais de script powerShell para Azure SQL Managed Instance podem ser encontradas em [scripts powerShell de instância gerida Azure SQL](../../database/powershell-script-content-guide.md).