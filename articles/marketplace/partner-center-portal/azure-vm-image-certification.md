---
title: Certificação de máquina virtual Azure - Azure Marketplace
description: Aprenda a testar e submeter uma oferta virtual de máquina no mercado comercial.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bd7e40855f30612b90cf28365c0b1410cd3e3d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731132"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Certificação de imagem da máquina virtual Azure (VM)

> [!NOTE]
> Estamos a mover a gestão das suas ofertas Azure VM do Cloud Partner Portal para partner Center. Até que as suas ofertas sejam migradas, por favor continue a seguir as instruções em [Create certificados para azure key vault](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) no Cloud Partner Portal para gerir as suas ofertas.

Este artigo descreve como testar e submeter uma imagem de máquina virtual (VM) no mercado comercial para garantir que cumpre os mais recentes requisitos de publicação do Azure Marketplace.

Complete estes passos antes de submeter a sua oferta vm:

1. Criar e implementar certificados.
2. Implante um VM Azure utilizando a sua imagem generalizada.
3. Executar validações.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Criar e implementar certificados para o Cofre chave Azure

Esta secção descreve como criar e implementar os certificados auto-assinados necessários para configurar a conectividade Windows Remote Management (WinRM) a uma máquina virtual hospedada pelo Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Criar certificados para o Cofre chave Azure

Este processo consiste em três etapas:

1. Crie o certificado de segurança.
2. Crie o Cofre chave Azure para armazenar o certificado.
3. Guarde os certificados para o cofre da chave.

Pode utilizar um novo ou um grupo de recursos Azure existente para este trabalho.

#### <a name="create-the-security-certificate"></a>Criar o certificado de segurança

Editar e executar o seguinte script Azure PowerShell para criar o ficheiro de certificado (.pfx) numa pasta local. Substitua os valores pelos parâmetros apresentados na tabela a seguir.

| **Parâmetro** | **Descrição** |
| --- | --- |
| $certroopath | Pasta local para guardar o ficheiro .pfx para. |
| $location | Uma das localizações geográficas padrão do Azure. |
| $vmName | Nome da máquina virtual Azure planeada. |
| $certname | Nome do certificado; deve corresponder ao nome de domínio totalmente qualificado do VM planeado. |
| $certpassword | A palavra-passe para os certificados deve coincidir com a palavra-passe utilizada para o VM planeado. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
    $location = "westus"

    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"

    # Certification name - should match with FQDN of Windows Azure creating VM
    $certname = "$vmName.$location.cloudapp.azure.com"

    # Certification password - should be match with password of Windows Azure creating VM
    $certpassword = "SecretPassword@123"

    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb

```

> [!TIP]
> Mantenha aberta e em execução a mesma sessão de consola Azure PowerShell durante estes passos para manter os valores dos vários parâmetros.

> [!WARNING]
> Se guardar este script, guarde-o apenas num local seguro porque contém informações de segurança (uma palavra-passe).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Crie o cofre chave Azure para armazenar o certificado

Copie o conteúdo do modelo abaixo para um ficheiro na sua máquina local. No exemplo abaixo, este `C:\certLocation\keyvault.json`recurso é).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Editar e executar o seguinte script Azure PowerShell para criar um Cofre chave Azure e o grupo de recursos associados. Substitua os valores pelos parâmetros apresentados no quadro seguinte

| **Parâmetro** | **Descrição** |
| --- | --- |
| $postfix | Cadeia numérica aleatória ligada aos identificadores de implantação. |
| $rgName | Nome do grupo de recursos Azure (RG) para criar. |
| $location | Uma das localizações geográficas padrão do Azure. |
| $kvTemplateJson | Caminho de arquivo (keyvault.json) contendo modelo de Gestor de Recursos para cofre chave. |
| $kvname | Nome do novo cofre chave.|
|   |   |

```PowerShell
    # Creating Key vault in resource group

    # "Random" number for deployment identifiers
    $postfix = "0101048"

    # Resource group name
    $rgName = "TestRG$postfix"

    # Location of Resource Group
    $location = "westus"

    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"

    # Key vault name
    $kvname = "isvkv$postfix"

    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host

        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }

        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message
      Break
      }

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message
        Break
        }

    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all

```

#### <a name="store-the-certificates-to-the-key-vault"></a>Guarde os certificados para o cofre chave

Guarde os certificados contidos no ficheiro .pfx para o novo cofre de chaves utilizando este script:

```PowerShell
     $fileName =$certroopath+"\$certname"+".pfx"

     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Implante um VM Azure utilizando a sua imagem generalizada

Esta secção descreve como implantar uma imagem VHD generalizada para criar um novo recurso Azure VM. Para este processo, usaremos o modelo de Gestor de Recursos Azure fornecido e o script Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Prepare um modelo de Gestor de Recursos Azure

Copie o seguinte modelo de Gestor de Recursos Azure para a implementação de VHD para um ficheiro local chamado VHDtoImage.json. O próximo script solicitará a localização na máquina local para usar este JSON.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
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
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
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
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
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
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
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
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Editar este ficheiro para fornecer valores para estes parâmetros:

| **Parâmetro** | **Descrição** |
| --- | --- |
| ResourceGroupName | Nome de grupo de recursos Azure existente. Normalmente, use o mesmo RG que o seu cofre chave. |
| ModeloFile | Nome completo do caminho para o ficheiro VHDtoImage.json. |
| userStorageAccountName | Nome da conta de armazenamento. |
| sNameForPublicIP | Nome DNS para o IP público; deve ser minúscula. |
| subscriptionId | Identificador de assinatura Azure. |
| Localização | Localização geográfica Standard Azure do grupo de recursos. |
| vmName | Nome da máquina virtual. |
| nome do cofre | Nome do cofre da chave. |
| vaultResourceGroup | Grupo de recursos do cofre chave. |
| certificadoUrl | Endereço web (URL) do certificado, incluindo versão armazenada no `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`cofre da chave, por exemplo: . |
| vhdUrl | Endereço web do disco rígido virtual. |
| vmSize | Tamanho da caixa de máquinavirtual. |
| nome de endereço ipaddress público | Nome do endereço IP público. |
| virtualNetworkName | Nome da rede virtual. |
| nicName | Nome do cartão de interface de rede para a rede virtual. |
| adminUserName | Nome de utilizador da conta de administrador. |
| adminPassword | Senha de administrador. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Implementar um VM Azure

Copiar e editar o seguinte script `$storageaccount` `$vhdUrl` para fornecer valores para as e variáveis. Execute-o para criar um recurso Azure VM a partir do vHD generalizado existente.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Executar validações

Existem duas formas de executar validações na imagem implementada:

- Utilize ferramenta de teste de certificação para certificação Azure
- Use a API auto-teste

### <a name="download-and-run-the-certification-test-tool"></a>Descarregue e execute a ferramenta de teste de certificação

A ferramenta de teste de certificação da Azure Certified funciona numa máquina local do Windows, mas testa um Windows ou VM Linux baseados em Azure. Certifica que a imagem VM do utilizador pode ser utilizada com o Microsoft Azure e que foram cumpridas as orientações e requisitos em torno da preparação do seu VHD. A saída da ferramenta é um relatório de compatibilidade que irá enviar para o portal Partner Center para solicitar a certificação VM.

1. Descarregue e instale a mais recente ferramenta de teste de [certificação para a Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Abra a ferramenta de certificação e, em seguida, selecione **Iniciar Novo Teste**.
3. A partir do ecrã de informações de **teste,** introduza um nome de **teste** para o ensaio.
4. Selecione a **Plataforma** para o seu VM, seja windows server ou Linux. A escolha da sua plataforma afeta as opções restantes.
5. Se o seu VM estiver a utilizar este serviço de base de dados, selecione o Teste para a caixa de verificação de base de **dados Azure SQL.**

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Ligue a ferramenta de certificação a uma imagem VM

A ferramenta liga-se a VMs baseados no Windows com o [Azure PowerShell](https://docs.microsoft.com/powershell/) e liga-se aos VMs Linux através [de SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Ligue a ferramenta de certificação a uma imagem De Linux VM

1. Selecione o modo de **autenticação SSH:** Autenticação de palavra-passe ou autenticação de ficheiros chave.
2. Se utilizar a autenticação baseada em palavra-passe, introduza valores para o **nome DNS VM,** nome do **utilizador**, e **palavra-passe**. Também pode alterar o número de **porta SSH** predefinido.

    ![Ferramenta de teste certificada Azure, autenticação de senha de Imagem VM Linux](media/avm-cert2.png)

3. Se utilizar a autenticação baseada em ficheiros chave, introduza valores para o **Nome DNS VM,** **nome do utilizador**e localização da chave **privada.** Também pode incluir uma **palavra-passe** ou alterar o número de **porta SSH** padrão.

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ligue a ferramenta de certificação a uma imagem VM baseada no Windows**

1. Introduza o **nome DNS VM** totalmente qualificado (por exemplo, MyVMName.Cloudapp.net).
2. Introduza valores para o Nome de **Utilizador** e **palavra-passe**.

    ![Ferramenta de teste certificada Azure, autenticação de senha de imagem VM baseada no Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Realizar um teste de certificação

Depois de ter dado os valores do parâmetro para a sua imagem VM na ferramenta de certificação, selecione **Test Connection** para criar uma ligação válida ao seu VM. Depois de verificada uma ligação, selecione **Next** para iniciar o teste. Quando o teste estiver concluído, os resultados dos testes são mostrados numa tabela. A coluna 'Status' mostra (Passar/Falhar/Aviso) para cada teste. Se algum dos testes falhar, a sua imagem _não_ está certificada. Neste caso, reveja os requisitos e as mensagens de falha, efaça as alterações sugeridas e faça o teste novamente.

Após o teste automatizado, forneça informações adicionais sobre a sua imagem VM nos dois separadores do ecrã do **Questionário,** **Avaliação Geral** e **Personalização kernel,** e, em seguida, selecione **Next**.

O último ecrã permite-lhe fornecer mais informações, como informações de acesso SSH para uma imagem De VM Linux, e uma explicação para quaisquer avaliações falhadas se estiver à procura de exceções.

Por fim, selecione **Generate Report** para descarregar os resultados dos testes e registar ficheiros para os casos de teste executados, juntamente com as suas respostas ao questionário. Guarde os resultados no mesmo recipiente que os seus VHDs.

## <a name="next-step"></a>Passo seguinte

- [Gerar um identificador de recursos uniformes (URI) para cada VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)
