---
title: Condição de utilização dos modelos do Azure Resource Manager | Microsoft Docs
description: Saiba como implementar recursos do Azure com base em condições.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 023be5d0a5320163b3eabfe80f35894763ab89fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099615"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Tutorial: Condição de utilização em modelos do Azure Resource Manager

Saiba como implementar recursos do Azure com base em condições.

No tutorial [Definir a ordem de implementação de recursos](./resource-manager-tutorial-create-templates-with-dependent-resources.md), cria uma máquina virtual, uma rede virtual e alguns outros recursos dependentes, incluindo uma conta de armazenamento. Em vez de criar sempre uma nova conta de armazenamento, permite que as pessoas optem entre criar uma nova conta de armazenamento e utilizar uma conta de armazenamento existente. Para alcançar este objetivo, tem de definir um parâmetro adicional. Se o valor do parâmetro for "new" (nova), é criada uma nova conta de armazenamento.

![Diagrama de condição de utilização de modelo do Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início rápido
> * Modificar o modelo
> * Implementar o modelo
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* [Visual Studio Code](https://code.visualstudio.com/) com [extensão Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Os Modelos de Início Rápido do Azure são um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.
4. Existem cinco recursos definidos pelo modelo:

   * `Microsoft.Storage/storageAccounts`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     É útil ter alguma compreensão básica do modelo antes de personalizá-lo.
5. Selecione **Ficheiro**>**Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome **azuredeploy.json**.

## <a name="modify-the-template"></a>Modificar o modelo

Introduza duas alterações ao modelo existente:

* Adicionar um parâmetro do nome da conta de armazenamento. Os utilizadores podem especificar um novo nome da conta de armazenamento ou um nome da conta de armazenamento existente.
* Adicionar um novo parâmetro denominado **newOrExisting**. A implementação utiliza este parâmetro para determinar onde criar uma conta de armazenamento nova ou para utilizar uma conta de armazenamento existente.

Este é o procedimento para fazer as alterações:

1. Abra **azuredeploy. JSON** no Visual Studio Code.
2. Substitua as **variáveis ('storageAccountName')** pelos **parâmetros ('storageAccountName')** em todo o modelo.  Existem três aspetos de **variáveis ('storageAccountName')**.
3. Remova a definição de variável seguinte:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
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

    ![Condição de utilização do Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Adicione a linha seguinte ao início da definição de conta de armazenamento.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    A condição verifica o valor de um parâmetro denominado **newOrExisting**. Se o valor do parâmetro for **New** (novo), a implementação cria a conta de armazenamento.

    A definição de conta de armazenamento atualizada assemelha-se a:

    ![Condição de utilização do Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Atualize **storageUri** com o seguinte valor:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Esta alteração é necessária quando utiliza uma conta de armazenamento existente num grupo de recursos diferente.

7. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Siga as instruções em [Implementar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para implementar o modelo.

Quando implementa o modelo com o Azure PowerShell, tem de especificar um parâmetro adicional. Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Veja [Pré-requisitos](#prerequisites).

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

Tente fazer outra implementação com **newOrExisting** definido como "existing" (existente) e especifique uma conta de armazenamento existente. Para criar uma conta do armazenamento antecipadamente, consulte [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, desenvolveu um modelo que permite que os utilizadores optem entre criar uma conta de armazenamento nova e utilizar uma conta de armazenamento existente. Para saber como obter segredos a partir do Azure Key Vault e utilizá-los como palavras-passe na implementação do modelo, veja:

> [!div class="nextstepaction"]
> [Integrar o Key Vault na implementação do modelo](./resource-manager-tutorial-use-key-vault.md)
