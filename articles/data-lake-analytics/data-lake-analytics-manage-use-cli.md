---
title: Gerir o Azure Data Lake Analytics usando o Azure CLI
description: Este artigo descreve como usar o Azure CLI para gerir empregos de Data Lake Analytics, fontes de dados, & utilizadores.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 69a48952ef273acb8cf7eb0ec5968e12b962b622
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79454368"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Gerir o Azure Data Lake Analytics utilizando a interface de linha de comando Azure (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerir contas azure Data Lake Analytics, fontes de dados, utilizadores e empregos usando o Azure CLI. Para ver tópicos de gestão utilizando outras ferramentas, clique no separador selecione acima.


**Pré-requisitos**

Antes de iniciar este tutorial, deve ter os seguintes recursos:

* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Consulte [instalar e configurar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Transfira e instale o **pré-lançamento** [Ferramentas CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.

* Autenticar utilizando o `az login` comando e selecionar a subscrição que pretende utilizar. Para mais informações sobre a autenticação através de uma conta escolar ou profissional, consulte [Ligar a uma subscrição do Azure a partir da CLI do Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Agora pode aceder aos comandos data Lake Analytics e Data Lake Store. Executar o seguinte comando para listar os comandos data Lake Store e Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Gerir contas

Antes de executar quaisquer trabalhos de Data Lake Analytics, você deve ter uma conta Data Lake Analytics. Ao contrário do Azure HDInsight, não se paga uma conta Analytics quando não está a gerir um emprego. Só se paga o tempo em que está a gerir um emprego.  Para mais informações, consulte a [visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Criar contas

Executar o seguinte comando para criar uma conta data lake, 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Atualizar contas

O comando seguinte atualiza as propriedades de uma conta de Análise de Data Lake existente

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Contas de lista

Lista de contas do Lago de Dados Analytics dentro de um grupo de recursos específicos

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Obtenha detalhes de uma conta

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Eliminar uma conta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Gerir origens de dados

Data Lake Analytics suporta atualmente as seguintes duas fontes de dados:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento Azure](../storage/common/storage-introduction.md)

Quando criar uma conta Analytics, deve designar uma conta de Armazenamento de Lagos De Dados Azure como a conta de armazenamento padrão. A conta de armazenamento padrão data Lake é usada para armazenar metadados de trabalho e registos de auditoria de emprego. Depois de ter criado uma conta Analytics, pode adicionar contas adicionais de Armazenamento de Data Lake e/ou conta de Armazenamento Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Encontre a conta padrão data lake store

Pode visualizar a conta padrão data `az dla account show` lake store utilizada executando o comando. O nome da conta predefinida está listado na propriedade defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Adicione contas adicionais de armazenamento Blob

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Apenas os nomes curtos de armazenamento blob são suportados. Não utilize o FQDN, por exemplo, "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar contas adicionais da Data Lake Store

O comando seguinte atualiza a conta de Data Lake Analytics especificada com uma conta adicional data Lake Store:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Atualizar fonte de dados existente

Para atualizar uma chave de conta de armazenamento Blob existente:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Listar fontes de dados:

Para listar as contas da Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Para listar a conta de armazenamento Blob:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Data Lake Analytics lista fonte de dados](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Eliminar fontes de dados:
Para eliminar uma conta data lake store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Para eliminar uma conta de armazenamento Blob:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Gerir tarefas
Você deve ter uma conta Data Lake Analytics antes de criar um emprego.  Para mais informações, consulte [gerir as contas do Lago de Dados Analytics](#manage-accounts).

### <a name="list-jobs"></a>Lista de empregos

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics lista fonte de dados](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obter detalhes do trabalho

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Submeter postos de trabalho

> [!NOTE]
> A prioridade padrão de um trabalho é 1000, e o grau padrão de paralelismo para um trabalho é 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Cancelar empregos
Use o comando da lista para encontrar a identidade de trabalho e, em seguida, use cancelar para cancelar o trabalho.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pipelines e recorrências

**Obter informações sobre pipelines e recorrências**

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

## <a name="see-also"></a>Consulte também
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerir o Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

