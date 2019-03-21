---
title: Criar modelos ligados do Azure Resource Manager | Microsoft Docs
description: Saiba como criar modelos ligados do Azure Resource Manager para criar máquinas virtuais
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/18/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 25dda12ca33165cfc64ffd949a2068acb5150b84
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097154"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Tutorial: Criar modelos do Azure Resource Manager ligados

Saiba como criar modelos ligados do Azure Resource Manager. Com os modelos ligados, pode ter um modelo para chamar outro modelo. É ótimo para modelos de modulação. Neste tutorial, vai utilizar o mesmo modelo utilizado [Tutorial: Criar modelos Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md), que cria uma máquina virtual, uma rede virtual e outros recursos dependentes, incluindo uma conta de armazenamento. Separar a criação de recursos da conta de armazenamento a um modelo ligado.

A chamada de um modelo ligado é como fazer uma chamada de função.  Também aprenderá como passar valores de parâmetros ao modelo de ligado e como obter "valores de retorno" do modelo ligado.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início rápido
> * Criar o modelo ligado
> * Carregar o modelo ligado
> * Ligar ao modelo ligado
> * Configurar a dependência
> * Implementar o modelo
> * Práticas adicionais

Para obter mais informações, consulte [utilização ligados e aninhados modelos durante a implantação de recursos do Azure](./resource-group-linked-templates.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* [Visual Studio Code](https://code.visualstudio.com/) com [extensão Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Os Modelos de Início Rápido do Azure são um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Este é o mesmo modelo usado no [Tutorial: Criar modelos Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Guarde duas cópias do mesmo modelo para serem utilizadas como:

* **O modelo principal**: crie todos os recursos exceto a conta de armazenamento.
* **O modelo ligado**: crie a conta de armazenamento.

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.
4. Existem cinco recursos definidos pelo modelo:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     É útil obter algumas noções básicas sobre o esquema de modelo antes de personalizar o modelo.
5. Selecione **Ficheiro**>**Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome **azuredeploy.json**.
6. Selecione **Ficheiro**>**Guardar Como** para criar outra cópia do ficheiro com o nome **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Criar o modelo ligado

O modelo ligado cria uma conta de armazenamento. O modelo ligado pode ser utilizado como um modelo autónomo para criar uma conta de armazenamento. Neste tutorial, o modelo ligado usa dois parâmetros e transmite um valor para o modelo principal. Este valor de "return" está definido no `outputs` elemento.

1. Open **linkedTemplate.json** no Visual Studio Code, se não é possível abrir o ficheiro.
2. Efetue as seguintes alterações:

    * Remover todos os parâmetros que **localização**.
    * Adicione um parâmetro denominado **storageAccountName**. 
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        O nome da conta de armazenamento e a localização são transferidos do modelo principal para o modelo ligado como parâmetros.
        
    * Remover os **variáveis** elemento e todas as definições de variável.
    * Remova todos os recursos que não seja a conta de armazenamento. Deverá remover um total de quatro recursos.
    * Atualize o valor do **nome** elemento do recurso de conta de armazenamento para:

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
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
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
3. Guarde as alterações.

## <a name="upload-the-linked-template"></a>Carregar o modelo ligado

O modelo de principal e o modelo ligado tem de ser acessível a partir de onde executar a implementação. Neste tutorial, vai utilizar o método de implementação do Cloud shell como que utilizou no [Tutorial: Criar modelos Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md). O modelo principal (azuredeploy.json) é carregado para o shell. O modelo ligado (linkedTemplate.json) tem de ser partilhados em algum lugar de forma segura. O seguinte script do PowerShell cria uma conta de armazenamento do Azure, carrega o modelo para a conta de armazenamento e, em seguida, gera um token SAS para conceder acesso limitado para o ficheiro de modelo. Para simplificar o tutorial, o script transfere um modelo ligado concluído de um local compartilhado. Se pretender utilizar o modelo ligado que criou, pode utilizar o [Cloud shell](https://shell.azure.com) carregue o modelo ligado e, em seguida, modificar o script a utilizar o seu próprio modelo ligado.

> [!NOTE]
> O script limita o token SAS para serem utilizados nos oito horas. Se precisar de mais tempo para concluir este tutorial, aumente o tempo de expiração.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
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

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Selecione o **experimentar** botão verde para abrir o painel do Azure cloud shell.
2. Selecione **cópia** para copiar o script do PowerShell.
3. Faça duplo clique em qualquer lugar dentro do painel de shell (a parte navy azul) e, em seguida, selecione **colar**.
4. Tome nota dos dois valores (nome do grupo de recursos e ligado modelo URI) no final do painel de shell. Vai precisar dos valores mais tarde no tutorial.
5. Selecione **sair do modo de foco** para fechar o painel de shell.

Na prática, gerar um token SAS quando implementar o modelo principal e dar a expiração do token SAS uma janela de menor para que seja mais seguro. Para obter mais informações, consulte [token de SAS de fornecer durante a implementação](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Chamar o modelo ligado

O modelo principal chama-se azuredeploy.json.

1. Open **azuredeploy. JSON** no Visual Studio Code, se não estiver aberto.
2. Elimine a definição de recurso de conta de armazenamento a partir do modelo:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. Adicione o seguinte fragmento json ao local em que tinha a definição de conta de armazenamento:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
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
4. Certifique-se de atualizar o valor da `uri` elemento para o valor de obteve ao carregar o modelo ligado (aquele com um token SAS). Na prática, pretende fornecer o URI com um parâmetro.
5. Guardar o modelo revisado

## <a name="configure-dependency"></a>Configurar a dependência

Lembre-se pelo [Tutorial: Criar modelos Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md), o recurso de máquina virtual depende da conta de armazenamento:

![Diagrama de dependência de modelos do Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Uma vez que agora a conta de armazenamento está definida no modelo ligado, tem de atualizar os dois elementos seguintes do recurso `Microsoft.Compute/virtualMachines`.

* Reconfigure o elemento `dependOn`. A definição da conta de armazenamento é movida para o modelo ligado.
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

    ![Dependência de configuração de modelos ligados do Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* é o nome do recurso de implementações.  
3. Atualizar **propriedades/diagnosticsProfile/bootDiagnostics/storageUri** conforme mostrado na captura de ecrã anterior.
4. Guarde o modelo revisado.

## <a name="deploy-the-template"></a>Implementar o modelo

Veja a secção [Implementar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para obter o procedimento de implementação. Utilize o mesmo nome de grupo de recursos como a conta de armazenamento para armazenar o modelo ligado. Isso torna mais fácil limpar os recursos na secção seguinte. Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Veja [Pré-requisitos](#prerequisites).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="additional-practice"></a>Prática adicional

Para melhorar o projeto, efetue as seguintes alterações adicionais para o projeto concluído:

1. Modificar o modelo principal (azuredeploy. JSON), para que ele usa o valor URI de modelo ligado através de um parâmetro.
2. Em vez de gerar um token SAS ao carregar o modelo de ligado, gere o token ao implementar o modelo principal. Para obter mais informações, consulte [token de SAS de fornecer durante a implementação](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, modularizado um modelo para um modelo principal e um modelo ligado. Para aprender a utilizar extensões de máquina virtual para efetuar tarefas de implementação de publicação, consulte:

> [!div class="nextstepaction"]
> [Implementar extensões de máquina virtual](./deployment-manager-tutorial.md)
