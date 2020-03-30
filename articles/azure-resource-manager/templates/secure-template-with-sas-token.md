---
title: Implementar de forma segura o modelo com token SAS
description: Desloque recursos para o Azure com um modelo de Gestor de Recursos Azure que é protegido por um token SAS. Mostra Azure PowerShell e Azure CLI.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156400"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Implementar modelo de ARM privado com token SAS

Quando o seu modelo De gestor de recursos Azure (ARM) estiver localizado numa conta de armazenamento, pode restringir o acesso ao modelo para evitar expô-lo publicamente. Você acede a um modelo seguro criando um símbolo de assinatura de acesso partilhado (SAS) para o modelo, e fornecendo esse símbolo durante a implementação. Este artigo explica como usar o Azure PowerShell ou o Azure CLI para implementar um modelo com um token SAS.

## <a name="create-storage-account-with-secured-container"></a>Criar conta de armazenamento com recipiente seguro

O seguinte guião cria uma conta de armazenamento e um contentor com acesso público desligado.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

## <a name="upload-template-to-storage-account"></a>Modelo de upload para conta de armazenamento

Agora, está pronto para enviar o seu modelo para a conta de armazenamento. Forneça o caminho para o modelo que pretende utilizar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Fornecer token SAS durante a implantação

Para implantar um modelo privado numa conta de armazenamento, gere uma ficha SAS e inclua-a no URI para o modelo. Detete o tempo de validade para permitir tempo suficiente para completar a implantação.

> [!IMPORTANT]
> A bolha que contém o modelo é acessível apenas ao proprietário da conta. No entanto, quando se cria um símbolo SAS para a bolha, a bolha é acessível a qualquer pessoa com esse URI. Se outro utilizador intercetar o URI, esse utilizador poderá aceder ao modelo. Um token SAS é uma boa forma de limitar o acesso aos seus modelos, mas não deve incluir dados sensíveis como palavras-passe diretamente no modelo.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Para um exemplo de utilização de um símbolo SAS com modelos ligados, consulte [Utilizar modelos ligados com](linked-templates.md)o Gestor de Recursos Azure .


## <a name="next-steps"></a>Passos seguintes
* Para uma introdução aos modelos de implantação, consulte [os recursos de implantação com modelos ARM e Azure PowerShell](deploy-powershell.md).
* Para definir parâmetros no modelo, consulte [os modelos de autor](template-syntax.md#parameters).
