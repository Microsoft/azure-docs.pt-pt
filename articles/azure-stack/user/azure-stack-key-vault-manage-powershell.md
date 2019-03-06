---
title: Gerir o Cofre de chaves no Azure Stack com o PowerShell | Documentos da Microsoft
description: Saiba como gerir o Cofre de chaves no Azure Stack com o PowerShell
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: b5c43215ce1800ac162c8b5d19ba4d4c987a3bac
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445811"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Gerir o Cofre de chaves no Azure Stack com o PowerShell

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode gerir o Cofre de chaves no Azure Stack com o PowerShell. Saiba como utilizar cmdlets do PowerShell do Key Vault para:

* Criar um cofre de chaves.
* Store e gerir chaves criptográficas e segredos.
* Autorize os utilizadores ou aplicações para invocar operações no cofre.

>[!NOTE]
>Cmdlets do PowerShell do Cofre de chaves descritos neste artigo são fornecidos no SDK do PowerShell do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de subscrever uma oferta que inclui o serviço Azure Key Vault.
* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).
* [Configurar o ambiente do PowerShell do utilizador do Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Ative a sua subscrição de inquilino para operações do Cofre de chaves

Antes de pode emitir quaisquer operações em relação a um cofre de chaves, terá de se certificar de que a sua subscrição de inquilino está ativada para operações do cofre. Para verificar que as operações do cofre estão ativadas, execute o seguinte comando:

```PowerShell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Saída**

Se a sua subscrição está ativada para operações do cofre, o resultado mostra "RegistrationState" é "registrado" para todos os tipos de recursos de um cofre de chaves.

![Estado de registo do Cofre de chaves](media/azure-stack-key-vault-manage-powershell/image1.png)

Se as operações do Cofre não estiverem ativadas, invoca o seguinte comando para registar o serviço Cofre de chaves na sua subscrição:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Saída**

Se o registo for bem-sucedida, é devolvido o resultado seguinte:

![Registar](media/azure-stack-key-vault-manage-powershell/image2.png) quando invoca os comandos do Cofre de chaves, poderá receber um erro, como "a subscrição não está registada para utilizar o espaço de nomes 'Microsoft. keyvault'." Se obtiver um erro, certifique-se que tiver ativado o fornecedor de recursos do Cofre de chaves ao seguir as instruções mencionadas anteriormente.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Antes de criar um cofre de chaves, crie um grupo de recursos para que todos os recursos relacionados com o Cofre de chaves existem num grupo de recursos. Utilize o seguinte comando para criar um novo grupo de recursos:

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force

```

**Saída**

![Novo grupo de recursos](media/azure-stack-key-vault-manage-powershell/image3.png)

Agora, utilize o **New-AzureRMKeyVault** comando para criar um cofre de chaves no grupo de recursos que criou anteriormente. Este comando lê três parâmetros obrigatórios: nome do grupo de recursos, o nome do Cofre de chaves e localização geográfica.

Execute o seguinte comando para criar um cofre de chaves:

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**Saída**

![Novo cofre de chaves](media/azure-stack-key-vault-manage-powershell/image4.png)

O resultado deste comando mostra as propriedades do Cofre de chaves que criou. Quando uma aplicação acede a este cofre, tem de utilizar o **URI do cofre** propriedade, que é "https://vault01.vault.local.azurestack.external" neste exemplo.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Implantação do Active Directory Federation Services (AD FS)

Numa implementação do AD FS, poderá receber este aviso: "Política de acesso não está definida. Nenhum utilizador ou aplicação tem a permissão de acesso para utilizar este cofre." Para resolver este problema, definir uma política de acesso do cofre com o [Set-AzureRmKeyVaultAccessPolicy](#authorize-an-application-to-use-a-key-or-secret) comando:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Gerir chaves e segredos

Depois de criar um cofre, utilize os seguintes passos para criar e gerir chaves e segredos no cofre.

### <a name="create-a-key"></a>Criar uma chave

Utilize o **Add-AzureKeyVaultKey** comando para criar ou importar uma chave protegida por software num cofre de chaves.

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

O **destino** parâmetro é utilizado para especificar que a chave é protegida de software. Quando a chave é criada com êxito, o comando devolve os detalhes da chave criada.

**Saída**

![Nova chave](media/azure-stack-key-vault-manage-powershell/image5.png)

Agora, pode referenciar a chave criada com o URI. Se criar ou importar uma chave que tenha o mesmo nome que uma chave existente, a chave original é atualizada com os valores especificados na chave do novo. Pode acessar a versão anterior com o URI de específicas da versão da chave. Por exemplo:

* Utilize "https://vault10.vault.local.azurestack.external:443/keys/key01" para obter sempre a versão atual.
* Utilize "https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" para obter esta versão específica.

### <a name="get-a-key"></a>Obter uma chave

Utilize o **Get-AzureKeyVaultKey** comando para ler uma chave e os respetivos detalhes.

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Criar um segredo

Utilize o **Set-AzureKeyVaultSecret** comando para criar ou atualizar um segredo no cofre. Um segredo é criado, se ainda não existir. Se já existir, é criada uma nova versão do segredo do.

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**Saída**

![Criar um segredo](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Obter um segredo

Utilize o **Get-AzureKeyVaultSecret** comando para ler um segredo no Cofre de chaves. Este comando pode devolver todas ou versões específicas de um segredo.

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Depois de criar as chaves e segredos, podem autorizar aplicações externas a utilizá-los.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autorizar uma aplicação a utilizar uma chave ou segredo

Utilize o **Set-AzureRmKeyVaultAccessPolicy** comando para autorizar uma aplicação a aceder a uma chave ou segredo no Cofre de chaves.
No exemplo a seguir, é o nome do cofre *ContosoKeyVault* e a aplicação que pretende autorizar tem um ID de cliente do *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Para autorizar a aplicação, execute o seguinte comando. Opcionalmente, pode especificar a **PermissionsToKeys** parâmetro para definir permissões para um utilizador, aplicação ou um grupo de segurança.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Se pretender autorizar essa mesma aplicação leia os segredos no cofre, execute o seguinte cmdlet:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Passos Seguintes

* [Implementar uma VM com uma palavra-passe armazenada no Cofre de chaves](azure-stack-kv-deploy-vm-with-secret.md)
* [Implementar uma VM com um certificado armazenado no Key Vault](azure-stack-kv-push-secret-into-vm.md)
