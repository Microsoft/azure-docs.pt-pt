---
title: Implementar uma máquina virtual com um certificado armazenado em segurança no Azure Stack | Documentos da Microsoft
description: Saiba como implementar uma máquina virtual e um certificado push à classe com um cofre de chaves no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 52c66dbf5001b3c49ac959276e59d8e794955193
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760126"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Criar uma máquina virtual e instalar um certificado obtido a partir de um cofre de chaves do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como criar uma máquina virtual (VM) do Azure Stack com um certificado do Cofre de chaves instalado.

## <a name="overview"></a>Descrição geral

Os certificados são utilizados em muitos cenários, tais como a autenticação no Active Directory, ou ao encriptar o tráfego da web. Pode armazenar em segurança certificados como segredos num cofre de chave do Azure Stack. As vantagens de utilizar o Cofre de chaves do Azure Stack são:

* Certificados não são expostos num script, histórico de linha de comandos ou modelo.
* O processo de gestão é simplificado.
* Tem controlo das chaves que acedem a certificados.

### <a name="process-description"></a>Descrição de processo

Os passos seguintes descrevem o processo necessário para emitir um certificado para a máquina virtual:

1. Crie um cofre de chave secreta.
2. Atualize o ficheiro azuredeploy.
3. Implemente o modelo.

> [!NOTE]
> Pode utilizar estes passos do Development Kit do Azure Stack ou de um cliente externo se estiver ligado através de VPN.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de subscrever uma oferta que inclui o serviço de Key Vault.
* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).
* [Configurar o ambiente do PowerShell do utilizador do Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Criar um cofre de chave secreta

O script seguinte cria um certificado no formato. pfx, cria um cofre de chaves e armazena o certificado no Cofre de chaves, como um segredo.

> [!IMPORTANT]
> Tem de utilizar o `-EnabledForDeployment` parâmetro ao criar o Cofre de chaves. Esse parâmetro garante que o Cofre de chaves pode ser referenciado a partir de modelos do Azure Resource Manager.

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

Ao executar o script anterior, a saída inclui o URI do segredo. Anote este URI. Precisa fazer referência a ela na [certificado Push para o modelo de Gestor de recursos do Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Transfira o [vm push-certificado windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) pasta de modelos para o seu computador de desenvolvimento. Esta pasta contém os `azuredeploy.json` e `azuredeploy.parameters.json` arquivos, que será necessário nos passos seguintes.

Modificar o `azuredeploy.parameters.json` ficheiro de acordo com os valores de ambiente. Os parâmetros de especial interesse são o nome do cofre, o grupo de recursos do cofre e o segredo do URI (como gerado pelo script anterior). A seção a seguir mostra um exemplo de um ficheiro de parâmetros.

## <a name="update-the-azuredeployparametersjson-file"></a>Atualize o ficheiro azuredeploy.

Atualização do `azuredeploy.parameters.json` de ficheiros com o `vaultName`, URI secreta, `VmName`e outros valores de acordo com o seu ambiente. O ficheiro JSON seguinte mostra um exemplo de ficheiro de parâmetros de modelo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo ao utilizar o seguinte script do PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando o modelo é implementado com êxito, o que resulta na seguinte saída:

![Resultados de implementação do modelo](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

O Azure Stack envia o certificado para a máquina virtual durante a implementação. A localização do certificado depende do sistema de operativo da VM:

* No Windows, o certificado é adicionado para o **LocalMachine** localização, com o arquivo de certificados que o usuário forneceu do certificado.
* No Linux, o certificado é colocado sob o `/var/lib/waagent directory`, com o nome de ficheiro &lt;UppercaseThumbprint&gt;. crt para X509 ficheiro de certificado e &lt;UppercaseThumbprint&gt;.prv para a chave privada.

## <a name="retire-certificates"></a>Remover certificados

Extinguir certificados é parte do processo de gestão do certificado. Não é possível eliminar a versão mais antiga de um certificado, mas pode desativá-lo utilizando o `Set-AzureKeyVaultSecretAttribute` cmdlet.

O exemplo seguinte mostra como desativar um certificado. Utilize os seus próprios valores para o **VaultName**, **nome**, e **versão** parâmetros.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Passos Seguintes

* [Implementar uma VM com uma palavra-passe do Cofre de Chaves](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Permitir que um aplicativo aceder ao Key Vault](azure-stack-key-vault-sample-app.md)
