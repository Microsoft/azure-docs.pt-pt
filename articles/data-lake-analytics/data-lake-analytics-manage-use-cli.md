---
title: Gerir a Azure Data Lake Analytics usando O Azure CLI
description: Este artigo descreve como usar o CLI Azure para gerir os empregos do Data Lake Analytics, fontes de dados, & utilizadores.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 01/29/2018
ms.openlocfilehash: 19b471d85a52fe38b72ad55847d022fb56b3c4f0
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220929"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Gerir a Azure Data Lake Analytics utilizando a Interface de linha de comando Azure (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerir as contas Azure Data Lake Analytics, fontes de dados, utilizadores e empregos usando o Azure CLI. Para ver tópicos de gestão utilizando outras ferramentas, clique no separador acima.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deve ter os seguintes recursos:

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- CLI do Azure. Consulte [instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli).

  - Transfira e instale o **pré-lançamento** [Ferramentas CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.

- Autenticar utilizando o `az login` comando e selecionar a subscrição que pretende utilizar. Para mais informações sobre a autenticação através de uma conta escolar ou profissional, consulte [Ligar a uma subscrição do Azure a partir da CLI do Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Agora pode aceder aos comandos Data Lake Analytics e Data Lake Store. Executar o seguinte comando para listar os comandos Data Lake Store e Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Gerir contas

Antes de executar quaisquer trabalhos de Data Lake Analytics, você deve ter uma conta Data Lake Analytics. Ao contrário do Azure HDInsight, não se paga uma conta Analytics quando não está a gerir um emprego. Só se paga pelo tempo em que está a gerir um emprego.  Para mais informações, consulte [a Visão Geral do Azure Data Lake Analytics](data-lake-analytics-overview.md).

### <a name="create-accounts"></a>Criar contas

Executar o seguinte comando para criar uma conta Data Lake,

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Atualizar contas

O seguinte comando atualiza as propriedades de uma conta de análise do Data Lake existente

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Contas de lista

Lista de dados contas do Lago Analytics dentro de um grupo de recursos específico

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Obter detalhes de uma conta

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Eliminar uma conta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Gerir origens de dados

Data Lake Analytics suporta atualmente as duas fontes de dados seguintes:

- [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
- [Armazenamento do Azure](../storage/common/storage-introduction.md)

Quando criar uma conta Analytics, tem de designar uma conta de Armazenamento de Data Lake Azure para ser a conta de armazenamento predefinida. A conta de armazenamento padrão data lake é usada para armazenar metadados de trabalho e registos de auditoria de trabalho. Depois de ter criado uma conta Analytics, pode adicionar contas adicionais de Armazenamento de Data Lake e/ou conta de Armazenamento Azure.

### <a name="find-the-default-data-lake-store-account"></a>Encontre a conta padrão data lake store

Pode ver a conta padrão da Data Lake Store utilizada executando o `az dla account show` comando. O nome da conta predefinido está listado na propriedade padrãoDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Adicionar contas adicionais de armazenamento Blob

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Apenas os nomes curtos de armazenamento Blob são suportados. Não utilize fQDN, por exemplo , "myblob.blob.core.windows.net".
>

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar contas adicionais data lake store

O seguinte comando atualiza a conta data lake analytics especificada com uma conta adicional data lake store:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Atualizar a fonte de dados existente

Para atualizar uma chave de conta de armazenamento Blob existente:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Listar fontes de dados

Para listar as contas da Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Para listar a conta de armazenamento Blob:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Screenshot que mostra Azure C L I com as informações "dataLakeStoreAccounts:" realçadas.](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Eliminar fontes de dados

Para eliminar uma conta data lake store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Para eliminar uma conta de armazenamento Blob:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Gerir tarefas

Deve ter uma conta data lake analytics antes de poder criar um emprego.  Para obter mais informações, consulte [as contas Do Lago de Dados Analíticos.](#manage-accounts)

### <a name="list-jobs"></a>Lista de empregos

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics lista fonte de dados](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obter detalhes de emprego

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Apresentar postos de trabalho

> [!NOTE]
> A prioridade padrão de um trabalho é 1000, e o grau de paralelismo padrão para um trabalho é 1.
>
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Cancelar empregos

Use o comando da lista para encontrar a identificação do trabalho e, em seguida, use o cancelamento para cancelar o trabalho.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pipelines e recorrências

### <a name="get-information-about-pipelines-and-recurrences"></a>Obter informações sobre pipelines e recorrências

Utilize os comandos `az dla job pipeline` para ver as informações de pipeline de tarefas previamente submetidas.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Utilize os comandos `az dla job recurrence` para ver as informações de recorrência para tarefas previamente submetidas.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
- [Gerir a Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
