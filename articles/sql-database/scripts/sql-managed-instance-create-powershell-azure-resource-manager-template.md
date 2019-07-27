---
title: Exemplo do PowerShell – criar uma instância gerenciada no banco de dados SQL do Azure | Microsoft Docs
description: Azure PowerShell script de exemplo para criar uma instância gerenciada no banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 5a60e8efad41c94deeedd545e6e0c1c96ff04e25
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569723"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Usar o PowerShell com Azure Resource Manager modelo para criar uma instância gerenciada no banco de dados SQL do Azure

Instância Gerenciada do Banco de Dados SQL do Azure pode ser criado usando a biblioteca de Azure PowerShell e os modelos de Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá AZ PowerShell 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

Azure PowerShell comandos podem iniciar a implantação usando o modelo de Azure Resource Manager predefinido. As propriedades a seguir podem ser especificadas no modelo:

- Nome da instância
- Nome de usuário e senha do administrador do SQL.
- Tamanho da instância (número de núcleos e tamanho máximo de armazenamento).
- VNet e sub-rede em que a instância será colocada.
- Agrupamento de nível de servidor da instância (versão prévia).

O nome da instância, o nome de usuário do administrador do SQL, a VNet/sub-rede e o agrupamento não podem ser alterados posteriormente. Outras propriedades de instância podem ser alteradas.

## <a name="prerequisites"></a>Pré-requisitos

Este exemplo pressupõe que você [criou um ambiente de rede válido](../sql-database-managed-instance-create-vnet-subnet.md) ou [modificou uma VNet existente](../sql-database-managed-instance-configure-vnet-subnet.md) para sua instância gerenciada. O exemplo usa os cmdlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) , portanto, certifique-se de ter instalado os seguintes módulos do PowerShell:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

O conteúdo a seguir deve ser colocado em um arquivo que representa um modelo que será usado para criar a instância:

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

Suposição que a VNet do Azure com a sub-rede configurada corretamente já exista. Se você não tiver uma sub-rede configurada corretamente, prepare o ambiente de rede usando o [modelo gerenciado de recursos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) separado que pode ser executado de forma independente ou incluído neste modelo.

Salve o conteúdo desse arquivo como arquivo. JSON, coloque o caminho do arquivo no seguinte script do PowerShell e altere os nomes dos objetos no script:

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
