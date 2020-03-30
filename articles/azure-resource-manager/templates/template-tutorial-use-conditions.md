---
title: Use condição em modelos
description: Saiba como implementar recursos do Azure com base em condições. Mostra como implantar um novo recurso ou usar um recurso existente.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f88f141257e8e614f62c7441c313002b5735116d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239199"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Tutorial: Use a condição nos modelos ARM

Aprenda a implementar recursos Azure com base em condições num modelo de Gestor de Recursos Azure (ARM).

No tutorial [Definir a ordem de implementação de recursos](./template-tutorial-create-templates-with-dependent-resources.md), cria uma máquina virtual, uma rede virtual e alguns outros recursos dependentes, incluindo uma conta de armazenamento. Em vez de criar sempre uma nova conta de armazenamento, permite que as pessoas optem entre criar uma nova conta de armazenamento e utilizar uma conta de armazenamento existente. Para alcançar este objetivo, tem de definir um parâmetro adicional. Se o valor do parâmetro for "new" (nova), é criada uma nova conta de armazenamento. Caso contrário, é utilizada uma conta de armazenamento existente com o nome fornecido.

![Diagrama de condição de utilização do gestor de recursos](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início rápido
> * Modificar o modelo
> * Implementar o modelo
> * Limpar recursos

Este tutorial abrange apenas um cenário básico de utilização de condições. Para obter mais informações, consulte:

* [Estrutura de ficheiro de modelo: Condição](conditional-resource-deployment.md).
* [Implante condicionalmente um recurso num modelo ARM](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Função do modelo: Se](./template-functions-logical.md#if).
* [Funções de comparação para modelos ARM](./template-functions-comparison.md)

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [o Use Visual Studio Code para criar modelos ARM](use-vs-code-to-create-template.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para mais informações, consulte [Tutorial: Integre o Cofre chave Azure na implementação](./template-tutorial-use-key-vault.md)do modelo ARM . Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Os modelos Azure QuickStart são um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. A partir do Código do Estúdio Visual, selecione **File**>**Open File**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro.
4. Existem cinco recursos definidos pelo modelo:

   * `Microsoft.Storage/storageAccounts`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     É útil ter alguma compreensão básica do modelo antes de personalizá-lo.
5. Selecione **Guardar Ficheiros**>**Para** guardar uma cópia do ficheiro para o computador local com o nome **azuredeploy.json**.

## <a name="modify-the-template"></a>Modificar o modelo

Introduza duas alterações ao modelo existente:

* Adicionar um parâmetro do nome da conta de armazenamento. Os utilizadores podem especificar um novo nome da conta de armazenamento ou um nome da conta de armazenamento existente.
* Adicionar um novo parâmetro denominado **newOrExisting**. A implementação utiliza este parâmetro para determinar se cria uma nova conta de armazenamento ou se utiliza uma conta de armazenamento existente.

Este é o procedimento para fazer as alterações:

1. Abra **azuredeploy. JSON** no Visual Studio Code.
2. Substitua as três **variáveis ('storageAccountName')** por **parâmetros ('storageAccountName')** em todo o modelo.
3. Remova a definição de variável seguinte:

    ![Diagrama de condição de utilização do gestor de recursos](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. Adicione os dois parâmetros seguintes ao modelo:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    A definição de parâmetros atualizada assemelha-se a:

    ![Condição de utilização do Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Adicione a linha seguinte ao início da definição de conta de armazenamento.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    A condição verifica o valor de um parâmetro denominado **newOrExisting**. Se o valor do parâmetro for **New** (novo), a implementação cria a conta de armazenamento.

    A definição de conta de armazenamento atualizada assemelha-se a:

    ![Condição de utilização do Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Atualize a propriedade **storageUri** da definição de recurso virtual da máquina com o seguinte valor:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Esta alteração é necessária quando utiliza uma conta de armazenamento existente num grupo de recursos diferente.

7. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

Siga as instruções em [Desdobrar o modelo](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para abrir a casca cloud e carregar o modelo revisto e, em seguida, executar o seguinte script PowerShell para implementar o modelo.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> A implementação falhar se **newOrExisting** for **New** (Novo), mas a conta de armazenamento com o nome de conta de armazenamento especificado já existe.

Tente fazer outra implementação com o novo conjunto **OrExisting** para "existente" e especificar uma conta de armazenamento existente. Para criar uma conta do armazenamento antecipadamente, consulte [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Para eliminar o grupo de recursos, selecione **Experimente-o** para abrir a casca cloud. Para colar o script PowerShell, clique na vidraça da direita e, em seguida, **selecione Pasta**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, desenvolveu um modelo que permite que os utilizadores optem entre criar uma conta de armazenamento nova e utilizar uma conta de armazenamento existente. Para saber como obter segredos a partir do Azure Key Vault e utilizá-los como palavras-passe na implementação do modelo, veja:

> [!div class="nextstepaction"]
> [Integrar o Key Vault na implementação do modelo](./template-tutorial-use-key-vault.md)
