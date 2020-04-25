---
title: Implantação automática de VM com configuração de aplicação Azure quickstart
description: Este quickstart demonstra como usar o módulo PowerShell Azure e os modelos do Gestor de Recursos Azure para implementar uma loja de configuração de aplicações Azure. Em seguida, utilize os valores na loja para implantar um VM.
author: lisaguthrie
ms.author: lcozzens
ms.date: 04/14/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 96d09de73e8b904a8e26eb4f365d34fab1401203
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137557"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Quickstart: Implementação automática de VM com configuração de aplicativos e modelo de gestor de recursos

O módulo Azure PowerShell é utilizado para criar e gerir recursos Azure utilizando cmdlets ou scripts PowerShell. Este quickstart mostra-lhe como usar modelos Azure PowerShell e Azure Resource Manager para implementar uma loja de configuração de aplicações Azure. Em seguida, aprende-se a usar os valores-chave da loja para implementar um VM.

Utiliza o modelo pré-requisito para criar uma loja de configuração de aplicações e, em seguida, adicione valores-chave na loja utilizando o portal Azure ou o Azure CLI. O modelo primário refere as configurações de valor-chave existentes a partir de uma loja de configuração existente. Os valores recuperados são usados para definir propriedades dos recursos criados pelo modelo, como um VM neste exemplo.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)

* Este arranque rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada no computador local. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e insira as suas credenciais Azure no navegador pop-up:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Se tiver mais de uma subscrição, selecione a subscrição que deseja utilizar para este arranque rápido executando os seguintes cmdlets. Não se esqueça `<your subscription name>` de substituir pelo nome da sua subscrição:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos Azure com [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Implementar uma loja de configuração de aplicações Azure

Antes de poder aplicar valores-chave ao VM, deve ter uma loja de configuração de aplicações Azure existente. Esta secção detalha como implementar uma loja de configuração de aplicações Azure usando um modelo de Gestor de Recursos Azure. Se já tem uma loja de cartões de aplicação, pode passar para a próxima secção deste artigo. 

1. Copie e cole o seguinte código json num novo ficheiro chamado *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Copie e cole o seguinte código json num novo ficheiro chamado *prereq.azuredeploy.parmeters.json*. Substitua o **GET-UNIQUE** por um nome único para a sua Loja de Configuração.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. Na janela PowerShell, execute o seguinte comando para implantar a loja de configuração de aplicações Azure. Não se esqueça de substituir o nome do grupo de recursos, o caminho do ficheiro do modelo e o caminho do ficheiro do parâmetro do modelo.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" `
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Adicione os valores-chave da configuração VM

Pode criar uma loja de configuração de aplicações utilizando um modelo de Gestor de Recursos Azure, mas precisa adicionar valores-chave utilizando o portal Azure ou o Azure CLI. Neste arranque rápido, adicione valores-chave utilizando o portal Azure.

1. Assim que a implementação estiver concluída, navegue para a recém-criada loja de configuração de aplicações no [portal Azure](https://portal.azure.com).

1. Selecione **Definições** > **De acesso .** Tome nota da cadeia de ligação principal de leitura. Mais tarde, utilizará esta cadeia de ligação para configurar a sua aplicação para comunicar com a loja de configuração de aplicações que criou.

1. Selecione Configuração **Explorer** > **Criar** para adicionar os seguintes pares de valor-chave:

   |Chave|Valor|
   |-|-|
   |windowsOsVersão|Centro de Dados 2019-Datacenter|
   |diskSizeGB|1023|
  
   Introduza *o modelo* para **Etiquetar,** mas mantenha o **Tipo de Conteúdo** vazio.

## <a name="deploy-vm-using-stored-key-values"></a>Implementar VM utilizando valores-chave armazenados

Agora que adicionou valores-chave à loja, está pronto para implementar um VM usando um modelo de Gestor de Recursos Azure. O modelo refere as teclas **windowsOsVersion** e **diskSizeGB** que criou.

> [!WARNING]
> Os modelos ARM não podem fazer referência a chaves numa loja de configuração de aplicações que tenha link privado ativado.

1. Copie e cole o seguinte código json num novo ficheiro chamado *azuredeploy.json,* ou descarregue o ficheiro a partir dos [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Copie e cole o seguinte código json num novo ficheiro chamado *azuredeploy.parameters.json,* ou descarregue o ficheiro a partir dos [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Substitua os valores do parâmetro no modelo com os seguintes valores:

   |Parâmetro|Valor|
   |-|-|
   |adminPassword|Uma senha de administrador para o VM.|
   |appConfigStoreName|O nome da sua loja de configuração de aplicações Azure.|
   |appConfigStoreResourceGroup|O grupo de recursos que contém a sua loja de configuração de aplicações.|
   |vmSkuKey|*windowsOSVersão*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Um nome de utilizador administrador para o VM.|
   |storageAccountName|Um nome único para uma conta de armazenamento associada ao VM.|
   |domínioNameLabel|Um nome de domínio único.|

1. Na janela PowerShell, execute o seguinte comando para implantar o VM. Não se esqueça de substituir o nome do grupo de recursos, o caminho do ficheiro do modelo e o caminho do ficheiro do parâmetro do modelo.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>"
       -TemplateFile "<path to azuredeploy.json>" `
       -TemplateParameterFile "<path to azuredeploy.parameters.json>"
   ```

Parabéns! Implementou um VM utilizando configurações armazenadas na Configuração de Aplicações Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a loja de configuração de aplicações, vM e todos os recursos relacionados. Se planeia utilizar a loja de configuração de aplicações ou VM no futuro, pode não aapagar. Se não vai continuar a usar este trabalho, elimine todos os recursos criados por este quickstart executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, implementou um VM utilizando um modelo de Gestor de Recursos Azure e valores-chave da Configuração de Aplicações Azure.

Para aprender sobre a criação de outras aplicações com a Configuração de Aplicações Azure, continue para o seguinte artigo:

> [!div class="nextstepaction"]
> [Quickstart: Crie uma app core ASP.NET com configuração de app Azure](quickstart-aspnet-core-app.md)
