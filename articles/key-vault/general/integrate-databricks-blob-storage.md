---
title: Aceder ao Armazenamento de Blobs do Azure com o Azure Databricks e o Azure Key Vault
description: Neste tutorial, você aprenderá a aceder ao Azure Blob Storage da Azure Databricks usando um segredo armazenado em Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: bb574bb3dd000682090c6c3f861e885761753e19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588522"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Tutorial: Access Azure Blob Storage usando Azure Databricks e Azure Key Vault

Neste tutorial, você aprenderá a aceder ao Azure Blob Storage da Azure Databricks usando um segredo armazenado em Azure Key Vault. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento e recipiente blob com Azure CLI
> * Crie um Cofre de Chaves e estabeleça um segredo
> * Crie um espaço de trabalho Azure Databricks e adicione o âmbito secreto do Key Vault
> * Aceda ao seu recipiente blob a partir do espaço de trabalho Azure Databricks

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Antes de iniciar este tutorial, instale o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Criar uma conta de armazenamento e recipiente blob com Azure CLI

Primeiro, terá de criar uma conta de armazenamento para fins gerais para usar bolhas. Se não tiver um [grupo de recursos,](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)crie um antes de executar o comando. O seguinte comando cria e exibe os metadados do recipiente de armazenamento. Copie o **ID.**

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Saída da consola do comando acima. O ID é realçado em verde para o utilizador final ver.](../media/databricks-command-output-1.png)

Antes de poder criar um recipiente para carregar a bolha, terá de atribuir o papel [de Contribuinte de Dados de Armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a si próprio. Para este exemplo, o papel será atribuído à conta de armazenamento que fez anteriormente.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Agora que atribuiu o papel à conta de armazenamento, pode criar um recipiente para a sua bolha.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Uma vez criado o recipiente, pode enviar uma bolha (ficheiro à sua escolha) para esse recipiente. Neste exemplo, é carregado um ficheiro .txt com helloworld.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Enuncie as bolhas no recipiente para verificar se o recipiente o tem.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Saída da consola do comando acima. Exibe o ficheiro que foi guardado no recipiente.](../media/databricks-command-output-2.png)

Obtenha o valor **chave1** do seu recipiente de armazenamento utilizando o seguinte comando. Copie o valor para baixo.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Saída da consola do comando acima. O valor da key1 é destacado numa caixa verde.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Crie um Cofre de Chaves e estabeleça um segredo

Criará um Cofre-Chave usando o seguinte comando. Este comando também mostrará os metadados do Cofre de Chaves. Copie o **ID** e **o vaultUri.**

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![Saída ](../media/databricks-command-output-4.png)
 ![ da consola de imagem do comando acima. O ID e o vaultUri estão ambos em destaque em verde para o utilizador ver.](../media/databricks-command-output-5.png)

Para criar o segredo, use o seguinte comando. Desa cosçar o valor do segredo para o valor **da chave1** da sua conta de armazenamento.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Crie um espaço de trabalho Azure Databricks e adicione o âmbito secreto do Key Vault

Esta secção não pode ser completada através da linha de comando. Siga este [guia.](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope) Terá de aceder ao [portal Azure](https://ms.portal.azure.com/#home) para:

1. Crie o seu recurso Azure Databricks
1. Lance o seu espaço de trabalho
1. Criar um âmbito secreto apoiado por cofres chave

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Aceda ao seu recipiente blob a partir do espaço de trabalho Azure Databricks

Esta secção não pode ser completada através da linha de comando. Siga este [guia.](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks) Você precisará usar o espaço de trabalho Azure Databricks para:

1. Criar um **novo cluster**
1. Criar um **novo caderno**
1. Preencha os campos correspondentes no script Python
1. Executar o roteiro Python

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Passos seguintes

Certifique-se de que o seu Cofre de Chaves é recuperável:
> [!div class="nextstepaction"]
> [Limpar os seus recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)
