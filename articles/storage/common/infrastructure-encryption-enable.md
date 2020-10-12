---
title: Criar uma conta de armazenamento com encriptação de infraestrutura habilitada para dupla encriptação de dados
titleSuffix: Azure Storage
description: Os clientes que necessitam de níveis mais elevados de garantia de que os seus dados são seguros também podem permitir uma encriptação AES de 256 bits ao nível da infraestrutura de armazenamento Azure. Quando a encriptação da infraestrutura é ativada, os dados numa conta de armazenamento são encriptados duas vezes com dois algoritmos de encriptação diferentes e duas teclas diferentes.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3164de9c3e44001d58d46eab9f823041b440960b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984137"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Criar uma conta de armazenamento com encriptação de infraestrutura habilitada para dupla encriptação de dados

O Azure Storage encripta automaticamente todos os dados numa conta de armazenamento ao nível do serviço utilizando encriptação AES de 256 bits, uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2. Os clientes que necessitam de níveis mais elevados de garantia de que os seus dados são seguros também podem permitir uma encriptação AES de 256 bits ao nível da infraestrutura de armazenamento Azure. Quando a encriptação da infraestrutura é ativada, os dados numa conta de armazenamento são encriptados duas vezes &mdash; ao nível do serviço e uma ao nível da infraestrutura &mdash; com dois algoritmos de encriptação diferentes e duas teclas diferentes. A dupla encriptação dos dados do Azure Storage protege contra um cenário em que um dos algoritmos ou chaves de encriptação pode estar comprometido. Neste cenário, a camada adicional de encriptação continua a proteger os seus dados.

A encriptação de nível de serviço suporta a utilização de chaves geridas pela Microsoft ou chaves geridas pelo cliente com cofre de chave Azure ou key Vault Managed Hardware Security Model (HSM) (pré-visualização). A encriptação ao nível da infraestrutura baseia-se em chaves geridas pela Microsoft e utiliza sempre uma chave separada. Para obter mais informações sobre a gestão de chaves com encriptação de armazenamento Azure, consulte [Sobre a gestão de chaves de encriptação](storage-service-encryption.md#about-encryption-key-management).

Para encriptar duplamente os seus dados, tem primeiro de criar uma conta de armazenamento configurada para encriptação de infraestruturas. Este artigo descreve como criar uma conta de armazenamento que permite encriptação de infraestrutura.

## <a name="register-to-use-infrastructure-encryption"></a>Registar para utilizar encriptação de infraestrutura

Para criar uma conta de armazenamento que tenha encriptação de infraestrutura ativada, tem primeiro de se registar para utilizar esta funcionalidade com o Azure utilizando o PowerShell ou o Azure CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registar no PowerShell, ligue para o comando [Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Para verificar o estado do seu registo com o PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Após a aprovação do seu registo, deve voltar a registar o fornecedor de recursos de armazenamento Azure. Para voltar a registar o fornecedor de recursos com o PowerShell, ligue para o comando [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registar no Azure CLI, ligue para o comando [de registo de recurso az.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Para verificar o estado da sua inscrição com o Azure CLI, ligue para o comando [de recurso az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Após a aprovação do seu registo, deve voltar a registar o fornecedor de recursos de armazenamento Azure. Para voltar a registar o fornecedor de recursos com o Azure CLI, ligue para o comando [de registo do fornecedor az.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Criar uma conta com encriptação de infraestrutura ativada

Tem de configurar uma conta de armazenamento para utilizar a encriptação da infraestrutura no momento em que criar a conta. A conta de armazenamento deve ser do tipo v2.

A encriptação da infraestrutura não pode ser ativada ou desativada após a criação da conta.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para utilizar o PowerShell para criar uma conta de armazenamento com encriptação de infraestrutura ativada, siga estes passos:

1. No portal Azure, navegue para a página **de contas de Armazenamento.**
1. Escolha o botão **Adicionar** para adicionar uma nova conta de armazenamento v2 para fins gerais.
1. No separador **Advanced,** localize **a encriptação da Infraestrutura** e selecione **Ativado**.
1. Selecione **Review + criar** para terminar a criação da conta de armazenamento.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Screenshot mostrando como ativar encriptação de infraestrutura ao criar conta":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para utilizar o PowerShell para criar uma conta de armazenamento com encriptação de infraestrutura ativada, certifique-se de que instalou o [módulo Az.Storage PowerShell](https://www.powershellgallery.com/packages/Az.Storage), versão 2.2.0 ou posterior. Para obter mais informações, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

Em seguida, crie uma conta de armazenamento v2 para fins gerais, chamando o comando [New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Inclua a `-RequireInfrastructureEncryption` opção de ativar a encriptação da infraestrutura.

O exemplo a seguir mostra como criar uma conta de armazenamento v2 para fins gerais que está configurada para armazenamento geo-redundante de acesso à leitura (RA-GRS) e tem encriptação de infraestrutura ativada para dupla encriptação de dados. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para utilizar o Azure CLI para criar uma conta de armazenamento que tenha encriptação de infraestrutura ativada, certifique-se de que instalou a versão 2.8.0 ou posterior do Azure CLI. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Em seguida, crie uma conta de armazenamento v2 para fins gerais, chamando a [conta de armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando e incluir a `--require-infrastructure-encryption option` encriptação de infraestrutura para ativar.

O exemplo a seguir mostra como criar uma conta de armazenamento v2 para fins gerais que está configurada para armazenamento geo-redundante de acesso à leitura (RA-GRS) e tem encriptação de infraestrutura ativada para dupla encriptação de dados. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Modelo](#tab/template)

O exemplo JSON a seguir cria uma conta de armazenamento v2 para fins gerais que está configurada para armazenamento geo-redundante de acesso à leitura (RA-GRS) e tem encriptação de infraestrutura ativada para dupla encriptação de dados. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Verifique se a encriptação da infraestrutura está ativada

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para verificar se a encriptação da infraestrutura está ativada para uma conta de armazenamento com o portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Em **Definições,** escolha **encriptação**.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Screenshot mostrando como ativar encriptação de infraestrutura ao criar conta":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar se a encriptação da infraestrutura está ativada para uma conta de armazenamento com o PowerShell, ligue para o comando [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Este comando devolve um conjunto de propriedades de conta de armazenamento e seus valores. Recupere o `RequireInfrastructureEncryption` campo dentro da propriedade e verifique se está definido para `Encryption` `True` .

O exemplo a seguir recupera o valor do `RequireInfrastructureEncryption` imóvel. Lembre-se de substituir os valores do espaço reservado nos suportes angulares pelos seus próprios valores:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar se a encriptação da infraestrutura está ativada para uma conta de armazenamento com o Azure CLI, ligue para o comando [da conta de armazenamento AZ.](/cli/azure/storage/account#az-storage-account-show) Este comando devolve um conjunto de propriedades de conta de armazenamento e seus valores. Procure o `requireInfrastructureEncryption` campo dentro da propriedade e verifique se está definido para `encryption` `true` .

O exemplo a seguir recupera o valor do `requireInfrastructureEncryption` imóvel. Lembre-se de substituir os valores do espaço reservado nos suportes angulares pelos seus próprios valores:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](customer-managed-keys-overview.md)