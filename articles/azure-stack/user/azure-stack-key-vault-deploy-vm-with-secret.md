---
title: Implementar uma VM com a palavra-passe armazenada com segurança no Azure Stack | Documentos da Microsoft
description: Saiba como implementar uma VM com uma palavra-passe armazenada no Cofre de chaves do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 30ebccd6bee26900ac01f7f4d17993e24c9db745
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779254"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Criar uma máquina virtual utilizando uma palavra-passe segura armazenada no Cofre de chaves do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo explica implementar uma máquina de virtual do Windows Server com uma palavra-passe armazenada no Cofre de chaves do Azure Stack. Utilizar uma palavra-passe do Cofre de chaves é mais seguro do que passando uma palavra-passe de texto sem formatação.

## <a name="overview"></a>Descrição geral

Pode armazenar valores como uma palavra-passe como um segredo num cofre de chaves do Azure Stack. Depois de criar um segredo, fazer referência a ele em modelos do Azure Resource Manager. Utilizar segredos com o Resource Manager fornece as seguintes vantagens:

* Não tem de introduzir manualmente segredo sempre que implantar um recurso.
* Pode especificar quais os utilizadores ou principais de serviço podem aceder a um segredo.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de subscrever uma oferta que inclui o serviço de Key Vault.
* [Instale o PowerShell para o Azure Stack.](azure-stack-powershell-install.md)
* [Configure o ambiente do PowerShell.](azure-stack-powershell-configure-user.md)

Os passos seguintes descrevem o processo necessário para criar uma máquina virtual ao obter a palavra-passe armazenada no Cofre de chaves:

1. Crie um cofre de chave secreta.
2. Atualize o ficheiro azuredeploy.
3. Implemente o modelo.

> [!NOTE]  
> Pode utilizar estes passos do Development Kit do Azure Stack ou de um cliente externo se estiver ligado através de VPN.

## <a name="create-a-key-vault-secret"></a>Criar um cofre de chave secreta

O script seguinte cria um cofre de chaves e armazena uma palavra-passe no Cofre de chaves, como um segredo. Utilize o `-EnabledForDeployment` parâmetro ao criar o Cofre de chaves. Este parâmetro certifica-se de que o Cofre de chaves pode ser referenciado a partir de modelos do Azure Resource Manager.

```PowerShell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Ao executar o script anterior, a saída inclui o URI do segredo. Anote este URI. Precisa fazer referência a ela na [máquina virtual de implementar o Windows com a palavra-passe no modelo de Cofre de chaves](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Transfira o [101-vm--palavra-passe segura](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) pasta no computador de desenvolvimento. Esta pasta contém os `azuredeploy.json` e `azuredeploy.parameters.json` arquivos, que será necessário nos passos seguintes.

Modificar o `azuredeploy.parameters.json` ficheiro de acordo com os valores de ambiente. Os parâmetros de especial interesse são o nome do cofre, o grupo de recursos do cofre e o segredo do URI (como gerado pelo script anterior). O arquivo a seguir é um exemplo de um ficheiro de parâmetros:

## <a name="update-the-azuredeployparametersjson-file"></a>Atualize o ficheiro azuredeploy.

Atualize o ficheiro azuredeploy com o URI do Cofre de chaves, secretName, adminUsername dos valores da máquina virtual, de acordo com o seu ambiente. O ficheiro JSON seguinte mostra um exemplo de ficheiro de parâmetros de modelo:

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Implementação de modelos

Agora, implemente o modelo ao utilizar o seguinte script do PowerShell:

```PowerShell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando o modelo é implementado com êxito, o que resulta na seguinte saída:

![Saída de implementação](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Passos Seguintes

* [Implemente uma aplicação de exemplo com o Key Vault](azure-stack-key-vault-sample-app.md)
* [Implementar uma VM com um certificado do Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
