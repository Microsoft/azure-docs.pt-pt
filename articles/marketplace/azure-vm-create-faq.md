---
title: Perguntas comuns sobre a VM no Azure Marketplace
description: Questões comuns encontradas ao criar uma máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 2975d1f1558bc7f9e4a12c18882e43a163b97982
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593429"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Perguntas comuns sobre a VM no Azure Marketplace

Estas perguntas frequentes (FAQ) cobrem questões comuns que poderá encontrar ao criar uma oferta de máquina virtual (VM) no Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como posso configurar uma rede privada virtual (VPN) para trabalhar com os meus VMs?

Se estiver a utilizar o modelo de implementação do Gestor de Recursos Azure, tem três opções:

- [Criar um gateway VPN baseado em rotas utilizando o portal Azure](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Criar um gateway VPN baseado em rotas usando Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Criar um gateway VPN baseado em rotas usando CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para executar o software do servidor da Microsoft em VMs baseados em Azure?

Pode encontrar detalhes no [suporte ao software do servidor da Microsoft para máquinas virtuais Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Num VM, como posso gerir a extensão de script personalizado na tarefa de arranque?

Para obter detalhes sobre a utilização da extensão de script personalizada utilizando o módulo Azure PowerShell, os modelos do Gestor de Recursos Azure e os passos de resolução de problemas nos sistemas Windows, consulte [a extensão de script personalizada para windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>As aplicações ou serviços de 32 bits são suportados no Azure Marketplace?

N.º Os sistemas operativos suportados e os serviços standard para os VMs Azure são todos de 64 bits. Embora a maioria dos sistemas operativos de 64 bits suportem versões de 32 bits de aplicações para retrocompatibilidade, usar aplicações de 32 bits como parte da sua solução VM não é suportado e altamente desencorajado. Recrie a sua aplicação como um projeto de 64 bits.

Para obter mais informações, veja estes artigos:

- [Executando aplicações de 32 bits](/windows/desktop/WinProg64/running-32-bit-applications)
- [Suporte para sistemas operativos de 32 bits nas máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: VHD já está registado com repositório de imagem como o recurso

Sempre que tento criar uma imagem dos meus VHDs, recebo o erro "VHD já está registado com repositório de imagem como recurso" em Azure PowerShell. Não criei nenhuma imagem antes nem encontrei nenhuma imagem com este nome em Azure. Como posso resolver isto?

Este problema geralmente aparece se você criou um VM a partir de um VHD que tem um bloqueio nele. Confirme que não há VM atribuído a partir deste VHD e, em seguida, re-tentar a operação. Se esta questão continuar, abra um bilhete de apoio. Consulte [o Suporte para o Centro de Parceiros.](support.md)

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Como posso criar um VM a partir de um vhd generalizado?

### <a name="prepare-an-azure-resource-manager-template"></a>Prepare um modelo de gestor de recursos Azure

Esta secção descreve como criar e implantar uma imagem de máquina virtual (VM) fornecida pelo utilizador. Pode fazê-lo fornecendo imagens VHD do sistema operativo e do disco de dados a partir de um disco rígido virtual implantado no Azure. Estes passos implantam o VM utilizando VHD generalizado.

1. Inicie sessão no portal do Azure.
2. Faça o upload do seu sistema operativo generalizado VHD e VHDs de disco de dados para a sua conta de Armazenamento Azure.
3. Na página inicial, selecione Criar um recurso, procurar por "Implementação do Modelo" e selecione Criar.
4. Escolha Construir o seu próprio modelo no editor.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Mostra a seleção de um modelo":::

5. Cole o modelo JSON seguinte no editor e selecione Save.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Forneça os valores dos parâmetros para as páginas de propriedade personalizadas apresentadas.

 **TABELA 1**

| **ResourceGroupName** | **Nome do grupo de recursos Azure existente. Normalmente, use o mesmo RG que o seu cofre de chaves.** |
| --- | --- |
| ModeloFile | Nome de caminho completo para o ficheiro VHDtoImage.jsligado. |
| utilizadorStorageAccountName | Nome da conta de armazenamento. |
| dnsNameForPublicIP | Nome DNS para o IP público; deve ser minúsculo. |
| subscriptionId | Identificador de assinatura Azure. |
| Localização | Localização geográfica padrão do grupo de recursos. |
| vmName | Nome da máquina virtual. |
| vhdUrl | Endereço web do disco rígido virtual. |
| vmSize | Tamanho da caixa da máquina virtual. |
| publicIPAddressName | Nome do endereço IP público. |
| virtualNetworkName | Nome da rede virtual. |
| nicName | Nome do cartão de interface de rede para a rede virtual. |
| adminUserName | Nome de utilizador da conta do administrador. |
| adminPassword | Senha de administrador. |
|

7. Depois de fornecer estes valores, selecione Comprar.

O Azure vai começar a implantar-se. Cria um novo VM com o VHD especificado não gerido na trajetória de conta de armazenamento especificado. Pode acompanhar o progresso no portal Azure selecionando Máquinas Virtuais no lado esquerdo do portal. Quando o VM for criado, o estado mudará de Iniciar para Executar.

Para a geração 2 VM, utilize este modelo:
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>Implementar um VM Azure usando o PowerShell

Copiar e editar o seguinte script para fornecer valores para as `$storageaccount` `$vhdUrl` variáveis e variáveis. Execute-o para criar um recurso Azure VM a partir do seu VHD generalizado existente.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```

## <a name="how-do-i-test-a-hidden-preview-image"></a>Como posso testar uma imagem de pré-visualização escondida?

Pode implementar imagens de pré-visualização ocultas utilizando modelos de arranque rápido.
Para implementar uma imagem de pré-visualização, 
1. Goto o respetivo modelo de arranque rápido para [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux) ou [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows), selecione "Implementar para Azure". Isto deve levá-lo ao portal Azure.
2. No portal Azure, selecione "Editar modelo".
3. No modelo JSON, procure por imagemReference e atualize o publisherid, offerid, skuid e versão da imagem. Para testar a imagem de pré-visualização, apêndice "-PREVIEW" ao ofendedo.
 ![imagem](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. Clicar em Guardar
5. Preencha o resto dos detalhes. Rever e Criar


## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas da certificação de VM](azure-vm-create-certification-faq.md)
