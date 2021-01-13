---
title: Tutorial - Implementar um modelo ligado
description: Saiba como implementar um modelo ligado
ms.date: 01/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4ec49fad35e958f010461abf2ee0e3dab8077d55
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134199"
---
# <a name="tutorial-deploy-a-linked-template"></a>Tutorial: Implementar um modelo ligado

Nos [tutoriais anteriores,](./deployment-tutorial-local-template.md)aprendeu a implementar um modelo que é armazenado no seu computador local. Para implementar soluções complexas, você pode quebrar um modelo em muitos modelos, e implementar estes modelos através de um modelo principal. Neste tutorial, você aprende a implementar um modelo principal que contém a referência a um modelo ligado. Quando o modelo principal é implementado, aciona a implementação do modelo ligado. Também aprende a armazenar e a proteger o modelo ligado utilizando o token SAS. Leva cerca de **12 minutos** para ser completado.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o tutorial anterior, mas não é necessário.

## <a name="review-template"></a>Modelo de revisão

Nos tutoriais anteriores, implementa-se um modelo que cria uma conta de armazenamento, um plano de Serviço de Aplicações e uma aplicação web. O modelo utilizado foi:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Criar um modelo ligado

Pode separar o recurso da conta de armazenamento num modelo ligado:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

O modelo a seguir é o modelo principal. O objeto realçado `Microsoft.Resources/deployments` mostra como chamar um modelo ligado. O modelo ligado não pode ser armazenado como um ficheiro local ou um ficheiro que só está disponível na sua rede local. Só é possível fornecer um valor URI que inclua HTTP ou HTTPS. O Gestor de Recursos deve ser capaz de aceder ao modelo. Uma opção é colocar o seu modelo ligado numa conta de armazenamento e usar o URI para esse item. O URI é passado para o modelo usando um parâmetro. Consulte a definição de parâmetro realçado.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Guarde uma cópia do modelo principal para o seu computador local com a extensão _.json,_ por exemplo, _azuredeploy.jsligado_. Não precisa de guardar uma cópia do modelo ligado. O modelo ligado será copiado de um repositório GitHub para uma conta de armazenamento.

## <a name="store-the-linked-template"></a>Armazenar o modelo ligado

O seguinte script PowerShell cria uma conta de armazenamento, cria um recipiente e copia o modelo ligado de um repositório GitHub para o recipiente. Uma cópia do modelo ligado é armazenada no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Selecione **Experimente-o** para abrir a Cloud Shell, selecione **Copy** para copiar o script PowerShell e clique com o botão direito para colar o script:

> [!IMPORTANT]
> Os nomes das contas de armazenamento devem ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas. O nome tem de ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com **a loja** anexada, e o nome do projeto deve estar entre 3 e 11 caracteres. Assim, o nome do projeto deve satisfazer os requisitos de nome da conta de armazenamento e tem menos de 11 caracteres.

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

Para implementar um modelo privado numa conta de armazenamento, gere um token SAS e inclua-o no URI para o modelo. Desavenda o prazo de validade para permitir tempo suficiente para completar a implantação. A bolha que contém o gabarito está acessível apenas ao proprietário da conta. No entanto, quando se cria um símbolo SAS para a bolha, a bolha é acessível a qualquer pessoa com esse URI. Se outro utilizador intercetar o URI, esse utilizador é capaz de aceder ao modelo. Um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas não deve incluir dados sensíveis como palavras-passe diretamente no modelo.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> No código CLI abaixo, o `date` parâmetro é um argumento `-d` inválido no macOS. Assim, os utilizadores do macOS, para adicionar 2 horas ao tempo atual no terminal no macOS, devem utilizar `-v+2H` .

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

Limpe os recursos que implementou eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a implementar um modelo ligado. No próximo tutorial, aprende-se a criar um oleoduto DevOps para implementar um modelo.

> [!div class="nextstepaction"]
> [Criar um pipeline](./deployment-tutorial-pipeline.md)
