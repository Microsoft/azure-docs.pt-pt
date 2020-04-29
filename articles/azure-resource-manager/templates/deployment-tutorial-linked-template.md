---
title: Tutorial - Implementar um modelo ligado
description: Saiba como implementar um modelo ligado
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 177a994450b6ffe5489a8c95c3b484521fd9b77b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80672925"
---
# <a name="tutorial-deploy-a-linked-template"></a>Tutorial: Implementar um modelo ligado

Nos [tutoriais anteriores,](./deployment-tutorial-local-template.md)aprendeu a implementar um modelo que está armazenado no seu computador local. Para implementar soluções complexas, pode quebrar um modelo em muitos modelos e implementar estes modelos através de um modelo principal. Neste tutorial, você aprende a implementar um modelo principal que contém a referência a um modelo ligado. Quando o modelo principal é implantado, despoleta a implantação do modelo ligado. Também aprende a armazenar e a proteger o modelo ligado utilizando token SAS. Leva cerca de **12 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o tutorial anterior, mas não é necessário.

## <a name="review-template"></a>Modelo de revisão

Nos tutoriais anteriores, você implementa um modelo que cria uma conta de armazenamento, plano de Serviço de Aplicações e aplicação web. O modelo utilizado foi:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Criar um modelo ligado

Pode separar o recurso da conta de armazenamento num modelo ligado:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

O seguinte modelo é o modelo principal.  O objeto de **Microsoft.Resources/implementações** destacado mostra como chamar um modelo ligado. O modelo ligado não pode ser armazenado como um ficheiro local ou um ficheiro que só está disponível na sua rede local. Só pode fornecer um valor URI que inclua *http* ou *https*. O Gestor de Recursos deve ter acesso ao modelo. Uma opção é colocar o seu modelo ligado numa conta de armazenamento e usar o URI para esse item. O URI é passado para o modelo usando um parâmetro. Consulte a definição de parâmetro realçado.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Guarde uma cópia do modelo principal para o seu computador local com a extensão .json, por exemplo, azuredeploy.json. Não precisa guardar uma cópia do modelo ligado.  O modelo ligado será copiado de um repositório GitHub para uma conta de armazenamento.

## <a name="store-the-linked-template"></a>Armazenar o modelo ligado

O seguinte script PowerShell cria uma conta de armazenamento, cria um recipiente e copia o modelo ligado de um repositório GitHub para o recipiente. Uma cópia do modelo ligado é armazenada no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Selecione **Tente-o** para abrir a Cloud Shell, selecione **Copy** para copiar o script PowerShell e clique no painel da concha para colar o script:

> [!IMPORTANT]
> Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e utilizar números e letras minúsculas apenas. O nome deve ser único. No modelo, o nome da conta de armazenamento é o nome do projeto com "loja" anexado, e o nome do projeto deve estar entre 3 e 11 caracteres. Assim, o nome do projeto deve satisfazer os requisitos de nome da conta de armazenamento e tem menos de 11 caracteres.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

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

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Implementar o modelo

Para implantar um modelo privado numa conta de armazenamento, gere uma ficha SAS e inclua-a no URI para o modelo. Detete o tempo de validade para permitir tempo suficiente para completar a implantação. A bolha que contém o modelo é acessível apenas ao proprietário da conta. No entanto, quando se cria um símbolo SAS para a bolha, a bolha é acessível a qualquer pessoa com esse URI. Se outro utilizador intercetar o URI, esse utilizador poderá aceder ao modelo. Um token SAS é uma boa forma de limitar o acesso aos seus modelos, mas não deve incluir dados sensíveis como palavras-passe diretamente no modelo.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> No código CLI do Azure abaixo, o parâmetro-d data seria um argumento inválido no macOS. Assim, os utilizadores de macOS, para adicionar 2 horas ao tempo atual no terminal no macOS, deve utilizar -v+2H.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que implementou ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a implementar um modelo ligado. No próximo tutorial, aprende-se a criar um oleoduto DevOp para implementar um modelo.

> [!div class="nextstepaction"]
> [Criar um pipeline](./deployment-tutorial-pipeline.md)
