---
title: Criar modelo - Código de Estúdio Visual
description: Utilize o Visual Studio Code e a extensão Azure Resource Manager Tools para trabalhar em modelos do Resource Manager.
author: mumian
ms.date: 04/13/2020
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 96e57146fb6bb17cbb8bb5975371e07b66f3ec8b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255095"
---
# <a name="quickstart-create-arm-templates-by-using-visual-studio-code"></a>Quickstart: Criar modelos ARM usando o Código de Estúdio Visual

Saiba como usar o código do Estúdio Visual e a extensão de Ferramentas de Gestor de Recursos Azure para criar e editar modelos de Gestor de Recursos Azure (ARM). Pode criar modelos ARM no Código do Estúdio Visual sem a extensão, mas a extensão fornece opções autocompletas que simplificam o desenvolvimento do modelo. Para compreender os conceitos associados à implementação e gestão das suas soluções Azure, consulte a visão geral da [implementação](overview.md)do modelo .

Neste arranque rápido, você implementa uma conta de armazenamento:

![Modelo de gestor de recursos quickstart diagrama de código de estúdio visual](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

- [Código de estúdio visual.](https://code.visualstudio.com/)
- Extensão das Ferramentas do Azure Resource Manager. Para instalar, siga estes passos:

    1. Abra o Visual Studio Code.
    2. Prima **CTRL+SHIFT+X** para abrir o painel Extensões
    3. Procure **Azure Resource Manager Tools** e, em seguida, selecione **Instalar**.
    4. Para concluir a instalação da extensão, selecione **Recarregar**.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Em vez de criar um modelo de raiz, pode abrir um modelo dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Os modelos Azure Quickstart é um repositório para modelos ARM.

O modelo utilizado neste início rápido chama-se [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso de conta de Armazenamento do Azure.

1. A partir do Código do Estúdio Visual, selecione **File**>**Open File**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro.
4. Selecione **File**>**Save As** para guardar o ficheiro como **azuredeploy.json** para o seu computador local.

## <a name="edit-the-template"></a>Editar o modelo

Para experimentar como editar um modelo usando o Código do `outputs` Estúdio Visual, adicione mais um elemento na secção para mostrar o URI de armazenamento.

1. Adicione mais um resultado ao modelo exportado:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Quando concluir, o ecrã de resultado terá este aspeto:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Se copiou e colou o código dentro do Código do Estúdio Visual, tente reescrever o elemento de **valor** para experimentar a capacidade IntelliSense da extensão ferramentas de gestor de recursos.

    ![IntelliSense Visual Studio Code de modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Selecione**Guardar** **ficheiros**>para guardar o ficheiro.

## <a name="deploy-the-template"></a>Implementar o modelo

Existem muitos métodos para implementar modelos. A Azure Cloud Shell é usada neste arranque rápido. A Cloud Shell suporta tanto o Azure CLI como o Azure PowerShell. Utilize o seletor de separadores para escolher entre CLI e PowerShell.

1. Inscreva-se na [Casca de Nuvem Azure](https://shell.azure.com)

2. Escolha o seu ambiente preferido selecionando **powerShell** ou **Bash**(CLI) no canto superior esquerdo.  É necessário reiniciar o Shell quando mudar.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Azure portal Cloud Shell CLI](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure portal Cloud Shell PowerShell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Selecione **Carregar/transferir ficheiros** e, em seguida, selecione **Carregar**.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Ficheiro de upload do portal Azure Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Ficheiro de upload do portal Azure Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Selecione o ficheiro que guardou na secção anterior. O nome predefinido é **azuredeploy.json**. O ficheiro do modelo deve ser acessível a partir da concha.

    Pode utilizar opcionalmente o comando **ls** e o comando do **gato** para verificar se o ficheiro é carregado com sucesso.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Ficheiro da lista cloud shell do portal Azure](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Ficheiro da lista cloud shell do portal Azure](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. A partir da Cloud Shell, execute os seguintes comandos. Selecione o separador para mostrar o código do PowerShell ou o código da CLI. Forneça um nome de projeto, que é usado para gerar um nome de grupo de recursos.  O nome do grupo de recursos é o nome do projeto com **rg** anexado.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" &&
    echo "Press [ENTER] to continue ..."
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

    Atualize o nome de ficheiro do modelo, se guardar o ficheiro com um nome diferente de **azuredeploy.json**.

    A captura de ecrã seguinte mostra uma implementação de exemplo:

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Modelo de implantação da Cloud Shell do portal Azure](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Modelo de implantação da Cloud Shell do portal Azure](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    O nome da conta de armazenamento e o URL de armazenamento na secção de resultados são destacados na captura de ecrã. Precisa do nome da conta de armazenamento no próximo passo.

5. Execute o seguinte comando do PowerShell ou da CLI para listar a conta de armazenamento acabada de criar:

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName &&
    echo "Press [ENTER] to continue ..."
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

Para saber mais sobre como utilizar contas de armazenamento do Azure, veja [Início Rápido: Carregar, transferir e listar blobs através do portal do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos. O nome do grupo de recursos é o nome do projeto com **rg** anexado. Verá um recurso de conta de armazenamento no grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

O foco principal deste início rápido é usar o Visual Studio Code para editar um modelo existente a partir de modelos de Início Rápido do Azure. Também aprendeu a implementar o modelo usando cli ou PowerShell da Casca de Nuvem Azure. Os modelos de Início Rápido do Azure poderão não fornecer-lhe tudo o que precisa. Para saber mais sobre o desenvolvimento do modelo, consulte a nossa nova série tutorial de principiante:

> [!div class="nextstepaction"]
> [Tutoriais para principiantes](./template-tutorial-create-first-template.md)
