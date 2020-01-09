---
title: Proteger artefatos em modelos
description: Saiba como proteger os artefactos utilizados nos seus modelos do Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 42f11ecb8e7c7ae47b5a5ab5ff4f946833945aa1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472619"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Proteger artefactos em implementações de modelo do Azure Resource Manager

Saiba como proteger os artefatos usados em seus modelos de Azure Resource Manager usando uma conta de armazenamento do Azure com SAS (assinaturas de acesso compartilhado). Artefatos de implementação são todos os ficheiros, além do ficheiro de modelo principal, que são necessárias para concluir uma implementação. Por exemplo, no [tutorial: importar arquivos BACPAC do SQL com modelos Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md), o modelo principal cria uma instância do banco de dados SQL do Azure. Ele também chama um arquivo BACPAC para criar tabelas e inserir dados. O arquivo BACPAC é um artefato e é armazenado em uma conta de armazenamento do Azure. Uma chave de conta de armazenamento foi usada para acessar o artefato. 

Neste tutorial, vai utilizar SAS a conceder acesso limitado para o ficheiro BACPAC em sua própria conta de armazenamento do Azure. Para obter mais informações sobre SAS, consulte [Using partilhado assinaturas de acesso (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Para saber como proteger um modelo vinculado, consulte [tutorial: criar modelos de Azure Resource Manager vinculados](./template-tutorial-create-linked-templates.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Prepare um arquivo BACPAC.
> * Abra um modelo existente.
> * Edite o modelo.
> * Implante o modelo.
> * Verificar a implementação.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com uma extensão de ferramentas do Resource Manager. Consulte [usar Visual Studio Code para criar modelos de Azure Resource Manager](./use-vs-code-to-create-template.md).
* Revisão [Tutorial: ficheiros BACPAC do SQL de importação com modelos Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md). O modelo utilizado neste tutorial é a desenvolveu esse tutorial. Uma ligação de transferência do modelo concluída é fornecida neste artigo.
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador do SQL Server. Aqui está um exemplo que você pode usar para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, veja [Tutorial: Integrar o Azure Key Vault na implementação de modelos do Resource Manager](./template-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um ficheiro BACPAC

Nesta seção, você prepara o arquivo BACPAC para que o arquivo seja acessível com segurança ao implantar o modelo do Resource Manager. Existem cinco procedimentos nesta secção:

* Transfira o ficheiro BACPAC.
* Criar uma conta de Armazenamento do Azure.
* Crie um contêiner de BLOB da conta de armazenamento.
* Carregue o ficheiro BACPAC para o contentor.
* Obter o token SAS do ficheiro BACPAC.

1. Selecione **Experimente** para abrir o Cloud Shell. Em seguida, Cole o seguinte script do PowerShell na janela do Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

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

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Anote a URL do arquivo BACPAC e o token SAS. Você precisará desses valores ao implantar o modelo.

## <a name="open-an-existing-template"></a>Abra um modelo existente

Nesta sessão, você modifica o modelo criado no [tutorial: importar arquivos SQL BACPAC com modelos de Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md) para chamar o arquivo bacpac com um token SAS. O modelo desenvolvido no tutorial de extensão do SQL é compartilhado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. No Visual Studio Code, selecione **Ficheiro** > **Abrir Ficheiro**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.

    Há quatro recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        É útil obter alguma compreensão básica do modelo antes de personalizá-lo.
1. Selecione **Ficheiro** > **Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome *azuredeploy.json*.

## <a name="edit-the-template"></a>Editar o modelo

1. Substitua a definição do parâmetro storageAccountKey pela definição de parâmetro a seguir: 

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Parâmetros de artefactos de seguro de tutorial do Resource Manager](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Atualize o valor dos três elementos a seguir do recurso de extensão SQL:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

O modelo completo assemelha-se a:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Implementar o modelo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Consulte a seção [implantar o modelo](./template-tutorial-create-multiple-instances.md#deploy-the-template) para o procedimento de implantação. Em vez disso, use o seguinte script de implantação do PowerShell.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Utilize uma palavra-passe gerada. Veja [Pré-requisitos](#prerequisites).
Para obter os valores de _artifactsLocation, _artifactsLocationSasToken e bacpacFileName, consulte [preparar um arquivo BACPAC](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Verificar a implementação

No portal, selecione a base de dados SQL a partir do grupo de recursos recentemente implementado. Selecione **Editor de consultas (pré-visualização)** e, em seguida, introduza as credenciais de administrador. Você verá duas tabelas importadas para o banco de dados.

![Editor de consultas (pré-visualização)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos. Você verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você implantou um SQL Server e um banco de dados SQL e importou um arquivo BACPAC usando um token SAS. Para saber como criar um pipeline do Azure para desenvolver e implantar os modelos do Resource Manager continuamente, consulte:

> [!div class="nextstepaction"]
> [Integração contínua com o pipeline do Azure](./template-tutorial-use-azure-pipelines.md)
