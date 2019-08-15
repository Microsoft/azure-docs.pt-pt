---
title: Implantar Azure Resource Manager modelo com segurança com o token SAS
description: Implante recursos no Azure com um modelo de Azure Resource Manager que é protegido por um token SAS. Mostra Azure PowerShell e CLI do Azure.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: tomfitz
ms.openlocfilehash: f396618350e4f4a9be09db421d073aec6ba52b65
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036959"
---
# <a name="deploy-private-resource-manager-template-with-sas-token"></a>Implementar um modelo privado do Resource Manager com o token SAS

Quando o modelo está localizado em uma conta de armazenamento, você pode restringir o acesso ao modelo para evitar expô-lo publicamente. Você acessa um modelo protegido criando um token de SAS (assinatura de acesso compartilhado) para o modelo e fornecendo esse token durante a implantação. Este artigo explica como usar Azure PowerShell ou CLI do Azure para implantar um modelo com um token SAS.

## <a name="create-storage-account-with-secured-container"></a>Criar conta de armazenamento com contêiner protegido

O script a seguir cria uma conta de armazenamento e um contêiner com acesso público desativado.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Carregar modelo para conta de armazenamento

Agora, você está pronto para carregar seu modelo na conta de armazenamento. Forneça o caminho para o modelo que você deseja usar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Fornecer token SAS durante a implantação

Para implantar um modelo privado em uma conta de armazenamento, gere um token SAS e inclua-o no URI para o modelo. Defina o tempo de expiração para permitir tempo suficiente para concluir a implantação.

> [!IMPORTANT]
> O blob que contém o modelo é acessível apenas ao proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob pode ser acessado por qualquer pessoa com esse URI. Se outro usuário interceptar o URI, esse usuário poderá acessar o modelo. Um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.
>

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Para obter um exemplo de como usar um token SAS com modelos vinculados, consulte [usando modelos vinculados com Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à implantação de modelos, consulte [implantar recursos com modelos e Azure PowerShell do Resource Manager](resource-group-template-deploy.md).
* Para obter um script de exemplo completo que implanta um modelo, consulte [implantar script de modelo do Resource Manager](resource-manager-samples-powershell-deploy.md)
* Para definir parâmetros no modelo, consulte [criando modelos](resource-group-authoring-templates.md#parameters).
