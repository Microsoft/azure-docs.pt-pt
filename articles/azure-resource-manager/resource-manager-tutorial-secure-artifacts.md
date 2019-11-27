---
title: Proteger artefatos em modelos
description: Saiba como proteger os artefactos utilizados nos seus modelos do Azure Resource Manager.
author: mumian
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b37f7e284b655a362c5a4231a7c1da3719762644
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326429"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Proteger artefactos em implementações de modelo do Azure Resource Manager

Saiba como proteger os artefactos utilizados nos seus modelos do Azure Resource Manager com a conta de armazenamento do Azure com assinaturas de acesso partilhado (SAS). Artefatos de implementação são todos os ficheiros, além do ficheiro de modelo principal, que são necessárias para concluir uma implementação. Por exemplo, no [tutorial: importar arquivos BACPAC do SQL com modelos Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), o modelo principal cria um banco de dados SQL do Azure; Ele também chama um arquivo BACPAC para criar tabelas e inserir dados. Ficheiro BACPAC é um artefato. O artefacto é armazenado numa conta de armazenamento do Azure com o acesso público. Neste tutorial, vai utilizar SAS a conceder acesso limitado para o ficheiro BACPAC em sua própria conta de armazenamento do Azure. Para obter mais informações sobre SAS, consulte [usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Para saber como proteger o modelo vinculado, consulte [tutorial: criar modelos de Azure Resource Manager vinculados](./resource-manager-tutorial-create-linked-templates.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um ficheiro BACPAC
> * Abra um modelo existente
> * Editar o modelo
> * Implementar o modelo
> * Verificar a implementação

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com a extensão de ferramentas do Resource Manager. Consulte [usar Visual Studio Code para criar modelos de Azure Resource Manager](./resource-manager-tools-vs-code.md).
* Leia o [tutorial: importar arquivos BACPAC do SQL com modelos de Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). O modelo utilizado neste tutorial é a desenvolveu esse tutorial. Uma ligação de transferência do modelo concluída é fornecida neste artigo.
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador do SQL Server. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, veja [Tutorial: Integrar o Azure Key Vault na implementação de modelos do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um ficheiro BACPAC

Nesta secção, prepare o ficheiro BACPAC para que o ficheiro está acessível em segurança quando implementar o modelo do Resource Manager. Existem cinco procedimentos nesta secção:

* Transfira o ficheiro BACPAC.
* Criar uma conta de Armazenamento do Azure.
* Crie um contentor de Blob da conta de armazenamento.
* Carregue o ficheiro BACPAC para o contentor.
* Obter o token SAS do ficheiro BACPAC.

Para automatizar essas etapas usando um script do PowerShell, consulte o script em [carregar o modelo vinculado](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Transfira o ficheiro BACPAC

Baixe o [arquivo BACPAC](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac)e salve o arquivo em seu computador local com o mesmo nome, **SQLDatabaseExtension. BACPAC**.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Selecione a imagem seguinte para abrir um modelo do Resource Manager no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Introduza as seguintes propriedades:

    * **Subscrição**: selecione a sua subscrição do Azure.
    * **Grupo de recursos**: selecione **criar novo** e dê a ele um nome. Um grupo de recursos é um contentor para recursos do Azure para o efeito de gestão. Neste tutorial, pode utilizar o mesmo grupo de recursos para a conta de armazenamento e a base de dados do SQL do Azure. Anote este nome de grupo de recursos, irá precisar dele quando criar a base de dados do SQL do Azure mais tarde nos tutoriais.
    * **Local**: selecione uma região. Por exemplo, **EUA Central**.
    * **Tipo de conta de armazenamento**: Use o valor padrão, que é **Standard_LRS**.
    * **Local**: Use o valor padrão, que é **[resourcegroup (). Location]** . Isso significa que usar a localização do grupo de recursos para a conta de armazenamento.
    * **Eu concordo com os termos e condições iniciados acima**: (selecionado)
3. Selecione **Comprar**.
4. Selecione o ícone de notificação (o ícone de sino) no canto superior direito do portal para ver o estado de implementação.

    ![Painel de notificações do portal tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Depois que a conta de armazenamento for implantada com êxito, selecione **ir para o grupo de recursos** no painel de notificação para abrir o grupo de recursos.

### <a name="create-a-blob-container"></a>Criar um contentor de BLOBs

Um contentor de BLOBs é necessária para poder carregar todos os ficheiros.

1. Selecione a conta de armazenamento para abri-la. Deverá ver apenas uma conta de armazenamento listada no grupo de recursos. O nome da sua conta de armazenamento é diferente daquele mostrado na captura de ecrã seguinte.

    ![Conta de armazenamento de tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Selecione o bloco **BLOBs** .

    ![Blobs de tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Selecione **+ contêiner** na parte superior para criar um novo contêiner.
4. Introduza os seguintes valores:

    * **Nome**: insira **sqlbacpac**.
    * **Nível de acesso público**: Use o valor padrão, **privado (sem acesso anônimo)** .
5. Selecione **OK**.
6. Selecione **sqlbacpac** para abrir o contêiner recém-criado.

### <a name="upload-the-bacpac-file-to-the-container"></a>Carregue o ficheiro BACPAC para o contentor

1. Selecione **Upload**.
2. Introduza os seguintes valores:

    * **Arquivos**: seguindo as instruções para selecionar o arquivo BACPAC que você baixou anteriormente. O nome padrão é **SQLDatabaseExtension. bacpac**.
    * **Tipo de autenticação**: selecione **SAS**.  *SAS* é o valor padrão.
3. Selecione **Upload**.  Depois do ficheiro é carregado com êxito, o nome do ficheiro deve ser listado no contentor.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />gerar um token SAS

1. Clique com o botão direito do mouse em **SQLDatabaseExtension. bacpac** do contêiner e selecione **gerar SAS**.
2. Introduza os seguintes valores:

    * **Permissão**: Use o padrão, **ler**.
    * **Data/hora de início e expiração**: o valor padrão fornece oito horas para usar o token SAS. Se precisar de mais tempo para concluir este tutorial, atualize a **expiração**.
    * **Endereços IP permitidos**: Deixe esse campo em branco.
    * **Protocolos permitidos**: Use o valor padrão: **https**.
    * **Chave de assinatura**: Use o valor padrão: **chave 1**.
3. Selecione **gerar token SAS de BLOB e URL**.
4. Faça uma cópia da **URL de SAS do blob**. No meio da URL está o nome do arquivo **SQLDatabaseExtension. bacpac**.  O nome de ficheiro divide o URL em três partes:

   - **Localização do artefato**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Certifique-se de que a localização termina com uma "/".
   - **Nome do arquivo BACPAC**: SQLDatabaseExtension. BACPAC.
   - **Token SAS de localização do artefato**: Verifique se o token precede com um "?."

     Você precisa desses três valores em [implantar o modelo](#deploy-the-template).

## <a name="open-an-existing-template"></a>Abra um modelo existente

Nesta sessão, você modifica o modelo criado no [tutorial: importar arquivos SQL BACPAC com modelos de Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) para chamar o arquivo bacpac com um token SAS.  O modelo desenvolvido no tutorial de extensão do SQL é compartilhado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.

    Existem cinco recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     É útil ter alguma compreensão básica do modelo antes de personalizá-lo.
4. Selecione **Ficheiro**>**Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome **azuredeploy.json**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione os seguintes parâmetros adicionais:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Parâmetros de artefactos de seguro de tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Atualize o valor dos dois seguintes elementos:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Implementar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Veja a secção [Implementar o modelo](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) para obter o procedimento de implementação. Em alternativa, utilize o seguinte script de implementação do PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Utilize uma palavra-passe gerada. Veja [Pré-requisitos](#prerequisites).
Para obter os valores de _artifactsLocation, _artifactsLocationSasToken e bacpacFileName, consulte [gerar um token SAS](#generate-a-sas-token).

## <a name="verify-the-deployment"></a>Verificar a implementação

No portal, selecione a base de dados SQL a partir do grupo de recursos recentemente implementado. Selecione **Editor de consultas (pré-visualização)** e, em seguida, introduza as credenciais de administrador. Deverá ver duas tabelas importadas para a base de dados:

![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um servidor de SQL, uma base de dados SQL e importar um ficheiro BACPAC com o SAS token. Para saber como criar um pipeline do Azure para desenvolver e implantar os modelos do Resource Manager continuamente, consulte

> [!div class="nextstepaction"]
> [Integração contínua com o pipeline do Azure](./resource-manager-tutorial-use-azure-pipelines.md)
