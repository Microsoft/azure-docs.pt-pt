---
title: Encriptação do serviço de armazenamento do Azure com chaves geridas pelo cliente no Azure Key Vault | Documentos da Microsoft
description: Utilize a funcionalidade de encriptação de serviço de armazenamento do Azure para encriptar o armazenamento de Blobs do Azure e ficheiros do Azure no lado do serviço ao armazenar os dados e decifrá-la ao obter os dados a utilizar chaves geridas pelo cliente.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: 56cf7f19ef3a3cebf705beceadf8f02681b2e2af
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730193"
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault

Microsoft Azure está empenhada em ajudar a proteger e salvaguardar os seus dados para satisfazer os seus compromissos de conformidade e segurança organizacionais. Uma forma que a plataforma de armazenamento do Azure protege os seus dados é através do Storage Service Encryption (SSE), que encripta os dados ao escrevê-lo para o armazenamento e desencripta os dados quando recuperá-lo. A encriptação e desencriptação é automático, transparente e utiliza a 256 bits [encriptação AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard), um bloco mais cifras disponíveis.

Pode utilizar chaves de encriptação gerida pela Microsoft e o SSE ou pode usar suas próprias chaves de encriptação. Este artigo descreve como utilizar as suas próprias chaves de encriptação. Para obter mais informações sobre como utilizar chaves geridas pela Microsoft, ou sobre o SSE em geral, consulte [encriptação do serviço de armazenamento para dados Inativos](storage-service-encryption.md).

O SSE para o armazenamento de Blobs do Azure e ficheiros do Azure estão integrados no Azure Key Vault, para que possa utilizar um cofre de chaves para gerir as chaves de encriptação. Pode criar suas próprias chaves de encriptação e armazená-las num cofre de chaves, ou pode usar APIs do Azure Key Vault para gerar chaves de encriptação. Com o Azure Key Vault, pode gerir e controlar as suas chaves e também a utilização da chave de auditoria.

> [!Note]  
> Utilizar chaves geridas pelo cliente de encriptação do serviço de armazenamento não está disponível para [Managed Disks do Azure](../../virtual-machines/windows/managed-disks-overview.md). [O Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md) utiliza a norma da indústria [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer uma solução de encriptação, integrado com o Cofre de chaves.

Porquê criar suas próprias chaves? Chaves personalizadas dão-lhe mais flexibilidade, para que pode criar, girar, desativar e definir controlos de acesso. Chaves personalizadas também permitem-lhe auditar as chaves de encriptação utilizadas para proteger os seus dados.

## <a name="get-started-with-customer-managed-keys"></a>Começar com chaves geridas pelo cliente

Para utilizar chaves geridas pelo cliente e o SSE, pode criar um novo cofre de chaves e chave ou pode usar um cofre de chaves existente e a chave. A conta de armazenamento e o Cofre de chaves têm de estar na mesma região, mas podem estar em diferentes subscrições.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="step-1-create-a-storage-account"></a>Passo 1: Criar uma conta de armazenamento

Primeiro, crie uma conta de armazenamento, se ainda não tiver um. Para obter mais informações, veja [Criar uma conta de armazenamento](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Passo 2: Ativar o SSE para o armazenamento de BLOBs e ficheiros

Para ativar o SSE com chaves geridas pelo cliente, os dois recursos de proteção de chaves, eliminação de forma recuperável e fazer limpar, também devem ser ativados no Azure Key Vault. Estas definições Certifique-se que as chaves não podem ser acidental ou intencionalmente eliminadas. O período de retenção máximo de chaves está definido para 90 dias, proteger os usuários contra atores maliciosos ou ataques de ransomware.

Se quiser habilitar programaticamente a chaves geridas pelo cliente para o SSE, pode utilizar o [API de REST de fornecedor de recursos de armazenamento do Azure](https://docs.microsoft.com/rest/api/storagerp), o [biblioteca do cliente de fornecedor de recursos do armazenamento para .NET](https://docs.microsoft.com/dotnet/api), [ O Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), ou o [CLI do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Para utilizar chaves geridas pelo cliente e o SSE, tem de atribuir uma identidade de conta de armazenamento para a conta de armazenamento. Pode definir a identidade, executando o seguinte comando do PowerShell ou CLI do Azure:

```powershell
Set-AzStorageAccount -ResourceGroupName $resourceGroup -Name $accountName -AssignIdentity
```

```azurecli-interactive
az storage account update \
    --name <account_name> \
    --resource-group <resource_group> \
    --assign-identity
```

Se não tiver um cofre de chaves, pode criá-la a partir do portal, Powershell ou CLI:

```powershell
New-AzKeyVault -Name <vault_name> -ResourceGroupName <resource_group> -Location <location>
```

```azurecli-interactive
az keyvault create -n <vault_name> -g <resource_group> -l <region> --enable-soft-delete --enable-purge-protection
```

Se estiver a utilizar o Cofre de chaves existente, tem de ativar a eliminação de forma recuperável e fazer não remover o seu Cofre, executando os seguintes comandos do PowerShell ou CLI do Azure:

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName $vaultName).ResourceId).Properties `
    | Add-Member -MemberType NoteProperty -Name enableSoftDelete -Value 'True'

Set-AzResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName $vaultName).ResourceId).Properties `
    | Add-Member -MemberType NoteProperty -Name enablePurgeProtection -Value 'True'

Set-AzResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Passo 3: Ativar a encriptação com chaves geridas pelo cliente

Por predefinição, o SSE usa chaves geridas pela Microsoft. Pode ativar o SSE com chaves geridas pelo cliente para a conta de armazenamento utilizando o [portal do Azure](https://portal.azure.com/). Sobre o **definições** painel para a conta de armazenamento, clique em **encriptação**. Selecione o **sua própria chave** opção, conforme mostrado na figura a seguir.

![Opção de encriptação do portal de captura de ecrã de com](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Passo 4: Selecione a sua chave

Pode especificar a chave como um URI ou ao selecionar a chave a partir de um cofre de chaves.

#### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como um URI

Para especificar a chave de um URI, siga estes passos:

1. Escolha o **URI da chave de Enter** opção.
2. Na **URI da chave** campo, especifique o URI.

   ![Portal captura de ecrã que mostra a encriptação com introduza a opção de chave de uri](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

#### <a name="specify-a-key-from-a-key-vault"></a>Especifique uma chave de um cofre de chaves

Tem de ter um cofre de chaves e uma chave no Cofre de chaves. Para especificar a chave de um cofre de chaves, siga estes passos:

1. Escolha o **selecione do Key Vault** opção.
2. Escolha o Cofre de chaves que contém a chave que pretende utilizar.
3. Escolha a chave de Cofre de chaves.

   ![Utilizam o portal encriptações de captura de ecrã com sua própria opção de chave](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Se a conta de armazenamento não tem acesso ao Cofre de chaves, pode executar o comando do PowerShell do Azure mostrado na imagem seguinte para conceder acesso.

![Captura de ecrã Portal, com acesso negado para o key vault](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Também pode conceder acesso através do portal do Azure ao navegar para o Cofre de chaves do Azure no portal do Azure e conceder acesso à conta de armazenamento.

Pode associar a chave acima com uma conta de armazenamento existente com os seguintes comandos do PowerShell:

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

```azurecli-interactive
kv_uri=$(az keyvault show -n <vault_name> -g <resource_group> --query properties.vaultUri -o tsv)
key_version=$(az keyvault key list-versions -n <key_name> --vault-name <vault_name> --query [].kid -o tsv | cut -d '/' -f 6)
az storage account update -n <account_name> -g <resource_group> --encryption-key-name <key_name> --encryption-key-version $key_version --encryption-key-source Microsoft.Keyvault --encryption-key-vault $kv_uri 
```


### <a name="step-5-copy-data-to-storage-account"></a>Passo 5: Copiar dados para a conta de armazenamento

Para transferir dados para a sua nova conta de armazenamento para que este está encriptado. Para obter mais informações, consulte [FAQ sobre a encriptação do serviço de armazenamento](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Passo 6: Consultar o estado dos dados encriptados

Consulte o estado dos dados encriptados.

## <a name="faq-for-sse-with-customer-managed-keys"></a>FAQ sobre a SSE com chaves cliente gerido

**Estou usando o armazenamento Premium; Pode utilizar chaves geridas pelo cliente e o SSE?**  
Sim, o SSE com chaves geridas pela Microsoft e gerida pelo cliente é suportada no armazenamento Standard e o armazenamento Premium.

**É possível criar novas contas de armazenamento e o SSE com chaves geridas pelo cliente ativadas com o Azure PowerShell e CLI do Azure?**  
Sim.

**Quanto mais armazenamento do Azure custo se eu utilizar chaves geridas pelo cliente e o SSE?**  
Existe um custo associado para utilizar o Azure Key Vault. Para obter mais detalhes, visite [preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). Não existe nenhum custo adicional para o SSE, que está ativado para todas as contas de armazenamento.

**Encriptação do serviço de armazenamento está disponível em Managed Disks do Azure?**  
Encriptação do serviço de armazenamento está disponível para Managed Disks do Azure com chaves geridas pela Microsoft, mas não com o cliente de chaves geridas. Em lugar de Managed Disks suporte SSE com chaves geridas pelo cliente, é recomendável [do Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), que usa o padrão da indústria [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)no Linux para fornecer encriptação, integrado com o Cofre de chaves.

**Como é diferente do Azure Disk Encryption Storage Service Encryption?**  
O Azure Disk Encryption fornece integração entre soluções baseadas no sistema operacional como o BitLocker e o DM-Crypt e o Cofre de chaves do Azure. Encriptação do serviço de armazenamento fornece encriptação nativamente na camada de plataforma de armazenamento do Azure, abaixo da máquina virtual.

**Pode revogar o acesso às chaves de encriptação?**
Sim, pode revogar o acesso em qualquer altura. Existem várias formas de revogar o acesso para as suas chaves. Consulte a [PowerShell do Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/) e [CLI do Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) para obter mais detalhes. A revogar o acesso com eficiência irá bloquear o acesso a todos os blobs na conta de armazenamento como a chave de encriptação da conta não está acessível pelo armazenamento do Azure.

**Posso criar uma conta de armazenamento e a chave numa região diferente?**  
Não, a conta de armazenamento e o Azure Key Vault e a chave tem de estar na mesma região.

**Posso habilitar chaves geridas pelo cliente para o SSE ao criar a conta de armazenamento?**  
Não. Quando cria pela primeira vez que a conta de armazenamento, apenas as chaves geridas pela Microsoft estão disponíveis para o SSE. Para utilizar chaves geridas pelo cliente, tem de atualizar as propriedades da conta de armazenamento. Pode utilizar o REST ou uma das bibliotecas de cliente do armazenamento para atualizar programaticamente a sua conta de armazenamento ou atualizar as propriedades da conta de armazenamento no portal do Azure depois de criar a conta.

**Pode desativar a encriptação durante a utilização de chaves geridas pelo cliente e o SSE?**  
Não, não é possível desativar a encriptação. A encriptação está ativada por predefinição para o armazenamento de Blobs do Azure, ficheiros do Azure, filas do Azure e armazenamento de tabelas do Azure. Opcionalmente, pode mudar da utilização de chaves geridas pela Microsoft para utilizar chaves geridas pelo cliente e vice-versa.

**O SSE está ativado ao criar uma nova conta de armazenamento?**  
A SSE está ativada para todas as contas de armazenamento e para armazenamento de Blobs do Azure, o ficheiros do Azure, o armazenamento de filas do Azure e o armazenamento de tabelas do Azure.

**Não é possível ativar o SSE com chaves geridas pelo cliente na minha conta de armazenamento.**  
É uma conta de armazenamento do Azure Resource Manager? Contas de armazenamento clássicas não são suportadas com chaves geridas pelo cliente. SSE com chaves geridas pelo cliente só pode ser ativado em contas de armazenamento do Resource Manager.

**O que é a eliminação de forma recuperável e fazer não remover? É necessário ativar esta definição para utilizar o SSE com chaves geridas pelo cliente?**  
Eliminação de forma recuperável e fazer não remover tem de ser ativado para utilizar o SSE com chaves geridas pelo cliente. Estas definições Certifique-se de que a chave não é acidental ou intencionalmente eliminada. O período de retenção máximo de chaves está definido para 90 dias, proteger os usuários contra ataques de ransomware e de atores maliciosos. Não é possível desativar esta definição.

**O SSE com chaves geridas pelo cliente apenas é permitido em regiões específicas?**  
O SSE com chaves geridas pelo cliente está disponível em todas as regiões para o armazenamento de Blobs do Azure e ficheiros do Azure.

**Como posso contactar alguém se eu tiver quaisquer problemas ou pretender fornecer comentários?**  
Contacte [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) para quaisquer problemas relacionados com a encriptação do serviço de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre o conjunto abrangente de segurança a recursos que ajudam os programadores criar aplicações seguras, consulte a [guia de segurança de armazenamento](storage-security-guide.md).
- Para obter informações gerais sobre o Azure Key Vault, consulte [o que é o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
- Para começar a trabalhar no Azure Key Vault, consulte [introdução ao Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
