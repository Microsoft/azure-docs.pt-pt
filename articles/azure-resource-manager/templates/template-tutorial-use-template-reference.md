---
title: Utilizar a referência do modelo
description: Utilize a referência do Gestor de Recursos Azure (modelo ARM) para criar um modelo.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584141"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Tutorial: Utilize a referência do modelo ARM

Saiba como encontrar as informações do esquema do modelo e use as informações para criar modelos de Gestor de Recursos Azure (modelos ARM).

Neste tutorial, você usa um modelo base de Azure Quickstart Templates. Utilizando a documentação de referência do modelo, personaliza o modelo.

![Conta de armazenamento de referência de modelo de gestor de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Compreender o modelo
> * Encontre a referência do modelo
> * Editar o modelo
> * Implementar o modelo

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

[Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/) é um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste início rápido chama-se [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso de conta de Armazenamento do Azure.

1. A partir do Código do Estúdio Visual, selecione Ficheiro Aberto **de**  >  **Ficheiros**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.
1. Selecione   >  **'Guardar ficheiros' para** guardar o ficheiro à medida _queazuredeploy.jsno_ computador local.

## <a name="understand-the-schema"></a>Compreender o esquema

1. Do Código do Estúdio Visual, desabar o modelo até ao nível da raiz. Tem a estrutura mais simples com os seguintes elementos:

    ![Estrutura mais simples do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: especificar a localização do ficheiro de esquema JSON que descreve a versão do idioma do modelo.
    * `contentVersion`: especifique qualquer valor para este elemento documentar alterações significativas no seu modelo.
    * `parameters`: especificar os valores fornecidos quando a implementação é executada para personalizar a implementação de recursos.
    * `variables`: especifique os valores que são usados como fragmentos de JSON no modelo para simplificar as expressões linguísticas do modelo.
    * `resources`: especificar os tipos de recursos que são implantados ou atualizados num grupo de recursos.
    * `outputs`: especificar os valores que são devolvidos após a implantação.

1. `resources`Expandir. Há um `Microsoft.Storage/storageAccounts` recurso definido. O nome SKU usa um valor de parâmetro. O parâmetro chama-se `storageAccountType` .

    ![Definição da conta de armazenamento do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Expanda `parameters` para ver como é `storageAccountType` definido. O parâmetro tem quatro valores permitidos. Encontrará os outros valores permitidos e, em seguida, reverá a definição de parâmetro.

    ![Recursos de conta de armazenamento de modelo de gestor de recursos do gestor de recursos de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Encontre a referência do modelo

1. Navegue pela [referência do modelo Azure](/azure/templates/).
1. Na caixa **de filtro por título,** **introduza as contas de armazenamento** e selecione as **primeiras Contas de Armazenamento** em Referência > **Armazenamento**.

    ![Conta de armazenamento de referência do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Um fornecedor de recursos geralmente tem várias versões API:

    ![Versões de conta de referência de modelo de gestor de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Selecione **Todos os recursos** em **Armazenamento** a partir do painel esquerdo. Esta página lista os tipos de recursos e versões do fornecedor de recursos de armazenamento. Recomenda-se a utilização das versões API mais recentes para os tipos de recursos definidos no seu modelo.

    ![Versões de tipos de conta de referência de modelo de gestor de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Selecione a versão mais recente do `storageAccount` tipo de recurso. A versão mais recente é **2019-06-01** quando este artigo está escrito. Certifique-se de que esta versão corresponde à versão utilizada para o recurso da conta de armazenamento no seu modelo. Se atualizar a versão API, verifique se a definição de recurso corresponde à referência do modelo.

1. Esta página lista os detalhes do tipo de recurso storageAccount. Por exemplo, lista os valores permitidos para o **objeto Sku**. Há mais SKUs do que o que está listado no modelo de arranque rápido que abriu anteriormente. Pode personalizar o modelo de arranque rápido para incluir todos os tipos de armazenamento disponíveis.

    ![Skus de conta de armazenamento de referência de modelo de gestor de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Editar o modelo

A partir do Código do Estúdio Visual, adicione os tipos adicionais de conta de armazenamento, como mostrado na imagem seguinte:

![Recursos de conta de armazenamento de modelo de gestor de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Implementar o modelo

1. Inscreva-se na [Azure Cloud Shell](https://shell.azure.com)

1. Escolha o seu ambiente preferido selecionando **PowerShell** ou **Bash** (para CLI) no canto superior esquerdo.  É necessário reiniciar o Shell quando mudar.

    ![Arquivo de upload do portal Azure Cloud Shell](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecione **Carregar/transferir ficheiros** e, em seguida, selecione **Carregar**. Veja a captura de ecrã anterior. Selecione o ficheiro que guardou na secção anterior. Depois de carregar o ficheiro, pode utilizar o `ls` comando e o comando para verificar se o ficheiro foi carregado com `cat` sucesso.

1. A partir da Cloud Shell, executar os seguintes comandos. Selecione o separador para mostrar o código do PowerShell ou o código da CLI.

   Quando implementar o modelo, especifique o `storageAccountType` parâmetro com um novo valor acrescentado, por exemplo, **Standard_RAGRS**. A implementação falharia se usasse o modelo original de arranque rápido porque **Standard_RAGRS** não era um valor permitido.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
1. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
1. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
1. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar a referência de modelo para personalizar um modelo existente. Para saber mais sobre como criar várias instâncias de contas de armazenamento, veja:

> [!div class="nextstepaction"]
> [Criar múltiplas instâncias](./template-tutorial-create-multiple-instances.md)
