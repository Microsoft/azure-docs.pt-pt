---
title: 'Criar uma instância (modelo arm & Powershell) '
titleSuffix: Azure SQL Managed Instance
description: Utilize este script de exemplo Azure PowerShell para criar uma instância gerida azure SQL.
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
ms.openlocfilehash: 55b0c8f569a91075d4cd87541af7aeff5da69f9a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053970"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-an-azure-sql-managed-instance"></a>Use powerShell com modelo de Gestor de Recursos Azure para criar uma instância gerida Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

A Instância Gerida azure SQL pode ser criada usando modelos de biblioteca Azure PowerShell e Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer AZ PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar a PowerShell localmente, corra `Connect-AzAccount` para criar uma ligação ao Azure.

Os comandos Azure PowerShell podem começar a ser implantados usando um modelo de Gestor de Recursos Azure predefinido. As seguintes propriedades podem ser especificadas no modelo:

- Nome de instância gerida SQL
- Nome de utilizador e senha do administrador SQL.
- Tamanho da instância (número de núcleos e tamanho máximo de armazenamento).
- VNet e subnet onde a instância será colocada.
- Colagem ao nível do servidor da instância (Pré-visualização).

O nome da instância, nome de utilizador do Administrador SQL, VNet/subnet e colagem não podem ser alterados mais tarde. Outras propriedades podem ser alteradas.

## <a name="prerequisites"></a>Pré-requisitos

Esta amostra pressupõe que [criou um ambiente](../virtual-network-subnet-create-arm-template.md) de rede válido ou [modificou um VNet existente](../vnet-existing-add-subnet.md) para a sua Instância Gerida SQL. Pode preparar o ambiente de rede utilizando um modelo separado de Gestão de [Recursos Azure,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)se necessário. 


A amostra utiliza os cmdlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) e [Get-AzVirtualNetwork,](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) por isso certifique-se de que instalou os seguintes módulos PowerShell:

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

Uma vez que o script esteja concluído, o SQL Managed Instance pode ser acedido a partir de todos os serviços Azure e do endereço IP configurado.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script powerShell de instância gerida sql podem ser encontradas nos [scripts PowerShell geridos pelo Azure SQL](../../database/powershell-script-content-guide.md).
