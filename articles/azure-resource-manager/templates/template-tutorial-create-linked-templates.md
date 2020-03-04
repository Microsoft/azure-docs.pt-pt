---
title: Criar modelos ligados
description: Saiba como criar modelos ligados do Azure Resource Manager para criar máquinas virtuais
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dab69c32f7277cd5d746e001b36118e673401bca
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250140"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Tutorial: Criar modelos ligados do Azure Resource Manager

Saiba como criar modelos ligados do Azure Resource Manager. Com os modelos ligados, pode ter um modelo para chamar outro modelo. É ótimo para modelos de modulação. Neste tutorial, você usa o mesmo modelo usado no [Tutorial: Create Azure Resource Manager com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md), que cria uma máquina virtual, uma rede virtual e outros recursos dependentes, incluindo uma conta de armazenamento. Separar a criação de recursos da conta de armazenamento a um modelo ligado.

Chamar um modelo ligado é como fazer uma chamada de função.  Você também aprende como passar os valores do parâmetro para o modelo ligado, e como obter "valores de retorno" do modelo ligado.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Criar o modelo ligado
> * Carregar o modelo ligado
> * Ligar ao modelo ligado
> * Configurar a dependência
> * Implementar o modelo
> * Práticas adicionais

Para mais informações, consulte [os modelos ligados e aninhados de utilização ao utilizar recursos Azure](./linked-templates.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Código de estúdio visual com extensão de ferramentas de gestor de recursos. Consulte [o Código do Estúdio Visual para criar modelos](use-vs-code-to-create-template.md)de Gestor de Recursos Azure .
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, veja [Tutorial: Integrar o Azure Key Vault na implementação de modelos do Resource Manager](./template-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Os Modelos de Início Rápido do Azure são um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Este é o mesmo modelo usado no [Tutorial: Criar modelos de Gestor de Recursos Azure com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md). Guarde duas cópias do mesmo modelo para serem utilizadas como:

* **O modelo principal**: crie todos os recursos exceto a conta de armazenamento.
* **O modelo ligado**: crie a conta de armazenamento.

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.
1. Existem seis recursos definidos pelo modelo:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/networkSecurityGroups`](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     É útil obter alguma compreensão básica do esquema do modelo antes de personalizar o modelo.
1. Selecione **Ficheiro**>**Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome **azuredeploy.json**.
1. Selecione **Ficheiro**>**Guardar Como** para criar outra cópia do ficheiro com o nome **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Criar o modelo ligado

O modelo ligado cria uma conta de armazenamento. O modelo ligado pode ser usado como um modelo autónomo para criar uma conta de armazenamento. Neste tutorial, o modelo ligado leva dois parâmetros, e passa um valor de volta para o modelo principal. Este valor de "retorno" é definido no elemento `outputs`.

1. Abra **o linkedTemplate.json** no Código do Estúdio Visual se o ficheiro não estiver aberto.
1. Efetue as seguintes alterações:

    * Retire todos os parâmetros que não **a localização**.
    * Adicione um parâmetro denominado **storageAccountName**.

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      O nome e a localização da conta de armazenamento são passados do modelo principal para o modelo ligado como parâmetros.

    * Remova o elemento **variável,** e todas as definições variáveis.
    * Remova todos os recursos que não a conta de armazenamento. Deverá remover um total de quatro recursos.
    * Atualizar o valor do elemento **de nome** do recurso da conta de armazenamento para:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Atualize o elemento **outputs**, para que este se assemelhe a:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```

       O **storageUri** é necessário para a definição do recurso de máquina virtual no modelo principal.  Passe o valor de volta ao modelo principal como um valor de resultado.

        Quando tiver terminado, o modelo terá o seguinte aspeto:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2018-11-01",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```

1. Guarde as alterações.

## <a name="upload-the-linked-template"></a>Carregar o modelo ligado

O modelo de principal e o modelo ligado tem de ser acessível a partir de onde executar a implementação. Neste tutorial, utiliza o método de implantação da casca da Nuvem como utilizado no [Tutorial: Create Azure Resource Manager com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md). O modelo principal (azuredeploy.json) é carregado para o shell. O modelo ligado (linkedTemplate.json) tem de ser partilhados em algum lugar de forma segura. O seguinte script PowerShell cria uma conta de Armazenamento Azure, envia o modelo para a conta de Armazenamento e, em seguida, gera um token SAS para conceder acesso limitado ao ficheiro de modelo. Para simplificar o tutorial, o script descarrega um modelo de ligação completo a partir de um repositório GitHub. Se quiser utilizar o modelo linked que criou, pode usar a [casca Cloud](https://shell.azure.com) para carregar o seu modelo de linked e, em seguida, modificar o script para usar o seu próprio modelo ligado.

> [!NOTE]
> O script limita o token SAS para serem utilizados nos oito horas. Se precisar de mais tempo para concluir este tutorial, aumente o tempo de expiração.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
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
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

Write-Host "You need the following values later in the tutorial:"
Write-Host "Resource Group Name: $resourceGroupName"
Write-Host "Linked template URI with SAS token: $templateURI"
Write-Host "Press [ENTER] to continue ..."
```

1. Selecione o botão verde **Try It** para abrir o painel de nuvens Azure.
2. Selecione **Copiar** para copiar o script PowerShell.
3. Clique à direita em qualquer lugar dentro do painel de conchas (a parte azul marinho) e, em seguida, **selecione Pasta**.
4. Tome nota dos dois valores (nome do grupo de recursos e ligado modelo URI) no final do painel de shell. Vai precisar dos valores mais tarde no tutorial.
5. Selecione o modo de **foco de saída** para fechar o painel da concha.

Na prática, gerar um token SAS quando implementar o modelo principal e dar a expiração do token SAS uma janela de menor para que seja mais seguro. Para mais informações, consulte [Fornecer token SAS durante a implementação](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Chamar o modelo ligado

O modelo principal chama-se azuredeploy.json.

1. Abra **azuredeploy.json** no Visual Studio Code se não estiver aberto.
1. Substitua a definição de recursos da conta de armazenamento pelo seguinte snippet json:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":""
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Preste atenção a estes detalhes:

    * Um recurso `Microsoft.Resources/deployments` no modelo principal é utilizado para ligar a outro modelo.
    * O recurso `deployments` tem um nome chamado `linkedTemplate`. Este nome é utilizado para [configurar a dependência](#configure-dependency).
    * Só pode utilizar o modo de implementação [Incremental](./deployment-modes.md) ao chamar modelos ligados.
    * `templateLink/uri` contém o URI do modelo ligado. Atualize o valor para o URI que obtém ao carregar o modelo ligado (aquele com um token SAS).
    * Utilize `parameters` para passar os valores do modelo principal para o modelo ligado.
1. Certifique-se de que atualizou o valor do elemento `uri` ao valor que obteve quando faz o upload do modelo ligado (aquele com um token SAS). Na prática, pretende fornecer o URI com um parâmetro.
1. Guardar o modelo revisado

## <a name="configure-dependency"></a>Configurar a dependência

Recolha do [Tutorial: Criar modelos de Gestor de Recursos Azure com recursos dependentes,](./template-tutorial-create-templates-with-dependent-resources.md)o recurso virtual da máquina depende da conta de armazenamento:

![Diagrama de dependência de modelos do Azure Resource Manager](./media/template-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Uma vez que agora a conta de armazenamento está definida no modelo ligado, tem de atualizar os dois elementos seguintes do recurso `Microsoft.Compute/virtualMachines`.

* Reconfigure o elemento `dependsOn`. A definição da conta de armazenamento é movida para o modelo ligado.
* Reconfigure o elemento `properties/diagnosticsProfile/bootDiagnostics/storageUri`. Em [Criar o modelo ligado](#create-the-linked-template), adicionou um valor de resultado:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```

    O modelo principal requer este valor.

1. Abra azuredeploy.json no Visual Studio Code, se não estiver aberto.
2. Expanda a definição do recurso de máquina virtual e atualize **dependsOn**, conforme mostrado na captura de ecrã seguinte:

    ![Dependência de configuração de modelos ligados do Azure Resource Manager](./media/template-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* é o nome do recurso de implementações.
3. Atualize **propriedades/diagnósticosPerfil/bootDiagnostics/storageUri,** como mostrado na imagem anterior.
4. Guarde o modelo revisado.

## <a name="deploy-the-template"></a>Implementar o modelo

Veja a secção [Implementar o modelo](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para obter o procedimento de implementação. Utilize o mesmo nome de grupo de recursos como a conta de armazenamento para armazenar o modelo ligado. Isso torna mais fácil limpar os recursos na secção seguinte. Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Veja [Pré-requisitos](#prerequisites).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="additional-practice"></a>Prática adicional

Para melhorar o projeto, efetue as seguintes alterações adicionais para o projeto concluído:

1. Modificar o modelo principal (azuredeploy. JSON), para que ele usa o valor URI de modelo ligado através de um parâmetro.
2. Em vez de gerar um token SAS ao carregar o modelo de ligado, gere o token ao implementar o modelo principal. Para mais informações, consulte [Fornecer token SAS durante a implementação](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, modularizado um modelo para um modelo principal e um modelo ligado. Para aprender a utilizar extensões de máquina virtual para efetuar tarefas de implementação de publicação, consulte:

> [!div class="nextstepaction"]
> [Implementar extensões de máquina virtual](./template-tutorial-deploy-vm-extensions.md)
