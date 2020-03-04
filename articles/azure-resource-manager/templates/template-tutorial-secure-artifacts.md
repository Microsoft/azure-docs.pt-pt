---
title: Artefactos seguros em modelos
description: Saiba como proteger os artefactos utilizados nos seus modelos do Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7069ff363cf274ba855efc9b598d8d01e64e18d1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250117"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Proteger artefactos em implementações de modelo do Azure Resource Manager

Saiba como proteger os artefactos utilizados nos seus modelos de Gestor de Recursos Azure utilizando uma conta de Armazenamento Azure com assinaturas de acesso partilhado (SAS). Artefatos de implementação são todos os ficheiros, além do ficheiro de modelo principal, que são necessárias para concluir uma implementação. Por exemplo, no [Tutorial: Importar ficheiros BACPAC com modelos](./template-tutorial-deploy-sql-extensions-bacpac.md)de Gestor de Recursos Azure, o modelo principal cria uma instância de base de dados Azure SQL. Também chama um ficheiro BACPAC para criar tabelas e inserir dados. O ficheiro BACPAC é um artefacto e está armazenado numa conta de Armazenamento Azure. Uma chave de conta de armazenamento foi usada para aceder ao artefacto. 

Neste tutorial, vai utilizar SAS a conceder acesso limitado para o ficheiro BACPAC em sua própria conta de armazenamento do Azure. Para obter mais informações sobre o SAS, consulte [A utilização de assinaturas de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Para aprender a garantir um modelo ligado, consulte [Tutorial: Crie modelos ligados do Gestor](./template-tutorial-create-linked-templates.md)de Recursos Azure .

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Prepare um ficheiro BACPAC.
> * Abra um modelo existente.
> * Editar o modelo.
> * Desdobrar o modelo.
> * Verificar a implementação.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Código de estúdio visual com uma extensão de ferramentas de gestor de recursos. Consulte [o Código do Estúdio Visual para criar modelos](./use-vs-code-to-create-template.md)de Gestor de Recursos Azure .
* Review [Tutorial: Import SQL BACPAC ficheiros com modelos de Gestor de Recursos Azure](./template-tutorial-deploy-sql-extensions-bacpac.md). O modelo utilizado neste tutorial é a desenvolveu esse tutorial. Uma ligação de transferência do modelo concluída é fornecida neste artigo.
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador do SQL Server. Aqui está uma amostra que pode usar para gerar uma senha:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, veja [Tutorial: Integrar o Azure Key Vault na implementação de modelos do Resource Manager](./template-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um ficheiro BACPAC

Nesta secção, prepara o ficheiro BACPAC de modo a que o ficheiro esteja acessível de forma segura quando implementar o modelo de Gestor de Recursos. Existem cinco procedimentos nesta secção:

* Transfira o ficheiro BACPAC.
* Criar uma conta de Armazenamento do Azure.
* Crie um recipiente de depósito.
* Carregue o ficheiro BACPAC para o contentor.
* Obter o token SAS do ficheiro BACPAC.

1. Selecione **Tente** abrir a casca da nuvem. Em seguida, colar o seguinte script PowerShell na janela da concha.

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

1. Escreva o URL do ficheiro BACPAC e o token SAS. Precisa destes valores quando implementa o modelo.

## <a name="open-an-existing-template"></a>Abra um modelo existente

Nesta sessão, modifica o modelo que criou no [Tutorial: Importar ficheiros BACPAC com modelos](./template-tutorial-deploy-sql-extensions-bacpac.md) do Gestor de Recursos Azure para chamar o ficheiro BACPAC com um token SAS. O modelo desenvolvido no tutorial de extensão SQL é partilhado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. No Visual Studio Code, selecione **Ficheiro** > **Abrir Ficheiro**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.

    Existem quatro recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        É útil obter alguma compreensão básica do modelo antes de personalizá-lo.
1. Selecione **Ficheiro** > **Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome *azuredeploy.json*.

## <a name="edit-the-template"></a>Editar o modelo

1. Substitua a definição do parâmetro StorageAccountKey pela seguinte definição de parâmetro: 

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Parâmetros de artefactos de seguro de tutorial do Resource Manager](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Atualizar o valor dos três elementos seguintes do recurso de extensão SQL:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

O modelo completo assemelha-se a:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Implementar o modelo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Consulte a secção [de modelo sintetização](./template-tutorial-create-multiple-instances.md#deploy-the-template) para o procedimento de implantação. Utilize o seguinte script de implementação PowerShell.

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
Para os valores de _artifactsLocation, _artifactsLocationSasToken e bacpacFileName, consulte [Prepare um ficheiro BACPAC](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Verificar a implementação

No portal, selecione a base de dados SQL a partir do grupo de recursos recentemente implementado. Selecione **Editor de consultas (pré-visualização)** e, em seguida, introduza as credenciais de administrador. Verá duas mesas importadas na base de dados.

![Editor de consulta (pré-visualização)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal Azure, selecione **Grupo Recurso** a partir do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos. Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um servidor SQL e uma base de dados SQL e importou um ficheiro BACPAC utilizando um token SAS. Para aprender a criar um oleoduto Azure para desenvolver e implementar continuamente modelos de Gestor de Recursos, consulte:

> [!div class="nextstepaction"]
> [Integração contínua com o Pipeline Azure](./template-tutorial-use-azure-pipelines.md)
