---
title: Implementar de forma segura o modelo com token SAS
description: Implemente recursos para a Azure com um modelo de Gestor de Recursos Azure que esteja protegido por um token SAS. Mostra Azure PowerShell e Azure CLI.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8b35e82da8ebca98ec9fe1fb7441612bf61fb142
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855663"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Implementar modelo arm privado com token SAS

Quando o seu modelo de Gestor de Recursos Azure (modelo ARM) estiver localizado numa conta de armazenamento, pode restringir o acesso ao modelo para evitar expô-lo publicamente. Acede a um modelo seguro criando um símbolo de assinatura de acesso partilhado (SAS) para o modelo e fornecendo esse token durante a implementação. Este artigo explica como usar a Azure PowerShell ou Azure CLI para implementar um modelo com um token SAS.

> [!IMPORTANT]
> Em vez de fixar o seu modelo com um token SAS, considere usar [as especificações do modelo.](template-specs.md) Com as especificações do modelo, pode partilhar os seus modelos com outros utilizadores da sua organização e gerir o acesso aos modelos através do Azure RBAC.

## <a name="create-storage-account-with-secured-container"></a>Criar conta de armazenamento com recipiente seguro

O seguinte script cria uma conta de armazenamento e um recipiente com acesso público desligado.

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Fornecer ficha SAS durante a implantação

Para implementar um modelo privado numa conta de armazenamento, gere um token SAS e inclua-o no URI para o modelo. Desavenda o prazo de validade para permitir tempo suficiente para completar a implantação.

> [!IMPORTANT]
> A bolha que contém o gabarito está acessível apenas ao proprietário da conta. No entanto, quando se cria um símbolo SAS para a bolha, a bolha é acessível a qualquer pessoa com esse URI. Se outro utilizador intercetar o URI, esse utilizador é capaz de aceder ao modelo. Um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas não deve incluir dados sensíveis como palavras-passe diretamente no modelo.
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

O exemplo a seguir funciona com o ambiente Bash em Cloud Shell. Outros ambientes podem exigir diferentes sintaxe para criar o tempo de validade para o token SAS.

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

Para um exemplo de utilização de um token SAS com modelos ligados, consulte [utilizar modelos ligados com o Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Passos seguintes
* Para uma introdução aos modelos de implantação, consulte [implementar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
* Para definir parâmetros no modelo, consulte [os modelos de autoria](template-syntax.md#parameters).
