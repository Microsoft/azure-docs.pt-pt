---
title: Condição de uso em modelos
description: Saiba como implementar recursos do Azure com base em condições. Mostra como implantar um novo recurso ou utilizar um recurso existente.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4affc2add2822702c1d5395f81efe01eeedf448b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696028"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Tutorial: Utilizar uma condição nos modelos do ARM

Saiba como implementar recursos Azure com base em condições num modelo de Gestor de Recursos Azure (modelo ARM).

No tutorial [Definir a ordem de implementação de recursos](./template-tutorial-create-templates-with-dependent-resources.md), cria uma máquina virtual, uma rede virtual e alguns outros recursos dependentes, incluindo uma conta de armazenamento. Em vez de criar sempre uma nova conta de armazenamento, permite que as pessoas optem entre criar uma nova conta de armazenamento e utilizar uma conta de armazenamento existente. Para alcançar este objetivo, tem de definir um parâmetro adicional. Se o valor do parâmetro for **novo,** é criada uma nova conta de armazenamento. Caso contrário, é utilizada uma conta de armazenamento existente com o nome fornecido.

![Diagrama de condição do gestor de recursos](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Modificar o modelo
> * Implementar o modelo
> * Limpar os recursos

Este tutorial cobre apenas um cenário básico de utilização de condições. Para obter mais informações, consulte:

* [Estrutura de arquivo de modelo: Condição](conditional-resource-deployment.md).
* [Implementar condicionalmente um recurso num modelo ARM](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy).
* [Função do modelo: Se](./template-functions-logical.md#if).
* [Funções de comparação para modelos ARM](./template-functions-comparison.md)

Para um módulo Microsoft Learn que cubra as condições, consulte [Gerir implementações complexas em nuvem utilizando funcionalidades avançadas do modelo ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integre o cofre da chave azul na implementação do modelo ARM](./template-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Azure Quickstart Templates é um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. A partir do Código do Estúdio Visual, selecione Ficheiro Aberto **de**  >  **Ficheiros**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.
1. Existem seis recursos definidos pelo modelo:

   * [**Microsoft.Storage/storageAcounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    É útil rever a referência do modelo antes de personalizar um modelo.

1. Selecione   >  **'Guardar ficheiros' para** guardar uma cópia do ficheiro no computador local com o nome _azuredeploy.jsligado_.

## <a name="modify-the-template"></a>Modificar o modelo

Introduza duas alterações ao modelo existente:

* Adicionar um parâmetro do nome da conta de armazenamento. Os utilizadores podem especificar um novo nome da conta de armazenamento ou um nome da conta de armazenamento existente.
* Adicione um novo parâmetro chamado `newOrExisting` . A implementação utiliza este parâmetro para determinar se cria uma nova conta de armazenamento ou se utiliza uma conta de armazenamento existente.

Este é o procedimento para fazer as alterações:

1. Abra _azuredeploy. JSON_ no Visual Studio Code.
1. Substitua os três `variables('storageAccountName')` com `parameters('storageAccountName')` todo o modelo.
1. Remova a definição de variável seguinte:

    ![Screenshot que realça as definições variáveis que precisa remover.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Adicione os dois parâmetros seguintes ao início da secção de parâmetros:

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

    Prima Alt+Shift+F para formatar o modelo no Código do Estúdio Visual.

    A definição de parâmetros atualizada assemelha-se a:

    ![Condição de utilização do Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Adicione a linha seguinte ao início da definição de conta de armazenamento.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    A condição verifica o valor do `newOrExisting` parâmetro. Se o valor do parâmetro for **New** (novo), a implementação cria a conta de armazenamento.

    A definição de conta de armazenamento atualizada assemelha-se a:

    ![Screenshot que mostra a definição atualizada da conta de armazenamento.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Atualizar a `storageUri` propriedade da definição de recurso de máquina virtual com o seguinte valor:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Esta alteração é necessária quando utiliza uma conta de armazenamento existente num grupo de recursos diferente.

1. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Inscreva-se na [Azure Cloud Shell](https://shell.azure.com)

1. Escolha o seu ambiente preferido selecionando **PowerShell** ou **Bash** (para CLI) no canto superior esquerdo. É necessário reiniciar o Shell quando mudar.

    ![Arquivo de upload do portal Azure Cloud Shell](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecione **Carregar/transferir ficheiros** e, em seguida, selecione **Carregar**. Veja a captura de ecrã anterior. Selecione o ficheiro que guardou na secção anterior. Depois de carregar o ficheiro, pode utilizar o `ls` comando e o comando para verificar se o ficheiro foi carregado com `cat` sucesso.

1. Executar o seguinte script PowerShell para implementar o modelo.

    > [!IMPORTANT]
    > O nome da conta do Storage tem de ser exclusivo em todo o Azure. O nome deve ter apenas letras ou números minúsculos. Não pode ter mais de 24 caracteres. O nome da conta de armazenamento é o nome do projeto com **a loja** anexada. Certifique-se de que o nome do projeto e o nome da conta de armazenamento gerado satisfazem os requisitos do nome da conta de armazenamento.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > A implementação falha se `newOrExisting` for **nova**, mas a conta de armazenamento com o nome da conta de armazenamento especificado já existe.

Tente fazer outra implantação com `newOrExisting` o conjunto para a conta de armazenamento **existente.** Para criar uma conta do armazenamento antecipadamente, consulte [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Para eliminar o grupo de recursos, selecione **Experimente-o** para abrir o Cloud Shell. Para colar o script PowerShell, clique com o botão direito da vidraça e, em seguida, **selecione Pasta**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, desenvolveu um modelo que permite que os utilizadores optem entre criar uma conta de armazenamento nova e utilizar uma conta de armazenamento existente. Para saber como obter segredos a partir do Azure Key Vault e utilizá-los como palavras-passe na implementação do modelo, veja:

> [!div class="nextstepaction"]
> [Integrar o Key Vault na implementação do modelo](./template-tutorial-use-key-vault.md)
