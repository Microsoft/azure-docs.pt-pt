---
title: Tutorial - Implementar um modelo ligado
description: Saiba como implementar um modelo ligado
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589275"
---
# <a name="tutorial-deploy-a-linked-template"></a>Tutorial: Implementar um modelo ligado

Nos [tutoriais anteriores,](./deployment-tutorial-local-template.md)aprendeu a implementar um modelo que é armazenado no seu computador local. Para implementar soluções complexas, você pode quebrar um modelo em muitos modelos, e implementar estes modelos através de um modelo principal. Neste tutorial, você aprende a implementar um modelo principal que contém a referência a um modelo ligado. Quando o modelo principal é implementado, aciona a implementação do modelo ligado. Também aprende a armazenar e a proteger os modelos utilizando o token SAS. Leva cerca de **12 minutos** para ser completado.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o tutorial anterior, mas não é necessário.

## <a name="review-template"></a>Modelo de revisão

Nos tutoriais anteriores, implementa-se um modelo que cria uma conta de armazenamento, um plano de Serviço de Aplicações e uma aplicação web. O modelo utilizado foi:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Criar um modelo ligado

Pode separar o recurso da conta de armazenamento num modelo ligado:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

O modelo a seguir é o modelo principal. O objeto realçado `Microsoft.Resources/deployments` mostra como chamar um modelo ligado. O modelo ligado não pode ser armazenado como um ficheiro local ou um ficheiro que só está disponível na sua rede local. Pode fornecer um valor URI do modelo ligado que inclui HTTP ou HTTPS, ou usar a propriedade _relativaPath_ para implementar um modelo de ligação remota num local relativo ao modelo principal. Uma opção é colocar o modelo principal e o modelo ligado numa conta de armazenamento.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Armazenar o modelo ligado

Tanto o modelo principal como o modelo ligado são armazenados no GitHub:

O seguinte script PowerShell cria uma conta de armazenamento, cria um recipiente e copia os dois modelos de um repositório GitHub para o recipiente. Estes dois modelos são:

- O modelo principal: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- O modelo ligado: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Selecione **Experimente-o** para abrir a Cloud Shell, selecione **Copy** para copiar o script PowerShell e clique com o botão direito para colar o script:

> [!IMPORTANT]
> Os nomes das contas de armazenamento devem ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas. O nome tem de ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com **a loja** anexada, e o nome do projeto deve estar entre 3 e 11 caracteres. Assim, o nome do projeto deve satisfazer os requisitos de nome da conta de armazenamento e tem menos de 11 caracteres.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Implementar o modelo

Para implementar modelos numa conta de armazenamento, gere um token SAS e forneça-o ao parâmetro _-QueryString._ Desavenda o prazo de validade para permitir tempo suficiente para completar a implantação. As bolhas que contêm os modelos estão acessíveis apenas ao proprietário da conta. No entanto, quando se cria um símbolo SAS para uma bolha, a bolha é acessível a qualquer pessoa com esse token SAS. Se outro utilizador intercetar o URI e o token SAS, esse utilizador é capaz de aceder ao modelo. Um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas não deve incluir dados sensíveis como palavras-passe diretamente no modelo.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> No código CLI abaixo, o `date` parâmetro é um argumento `-d` inválido no macOS. Assim, os utilizadores do macOS, para adicionar 2 horas ao tempo atual no terminal no macOS, devem utilizar `-v+2H` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos que implementou eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a implementar um modelo ligado. No próximo tutorial, aprende-se a criar um oleoduto DevOps para implementar um modelo.

> [!div class="nextstepaction"]
> [Criar um pipeline](./deployment-tutorial-pipeline.md)