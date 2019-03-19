---
title: Gerir o Azure Data Lake Analytics com Interface de linha de comandos do Azure
description: Este artigo descreve como utilizar a CLI do Azure para gerir tarefas do Data Lake Analytics, origens de dados e os utilizadores.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fa7d46d45c350435c0ffba8f3755ad8bea651c3e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088762"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Gerir o Azure Data Lake Analytics com a Interface de linha de comandos (CLI do Azure)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerir contas do Azure Data Lake Analytics, origens de dados, os utilizadores e tarefas com a CLI do Azure. Para ver tópicos de gestão com outras ferramentas, clique em Selecionar o separador acima.


**Pré-requisitos**

Antes de começar este tutorial, tem de ter os seguintes recursos:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* CLI do Azure. Consulte [instalar e configurar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Transfira e instale o **pré-lançamento** [Ferramentas CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.

* Autenticar com o `az login` de comandos e selecione a subscrição que pretende utilizar. Para mais informações sobre a autenticação através de uma conta escolar ou profissional, consulte [Ligar a uma subscrição do Azure a partir da CLI do Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Agora pode acessar os comandos de Data Lake Analytics e Data Lake Store. Execute o seguinte comando para listar os comandos de Data Lake Store e do Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Gerir contas

Antes de executar as tarefas de Data Lake Analytics, tem de ter uma conta do Data Lake Analytics. Ao contrário do Azure HDInsight, não paga uma conta do Analytics quando não está em execução uma tarefa. Paga apenas o tempo quando está em execução uma tarefa.  Para obter mais informações, consulte [descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Criar contas

Execute o seguinte comando para criar uma conta do Data Lake, 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Contas de atualização

O seguinte comando atualiza as propriedades de uma conta existente do Data Lake Analytics

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Contas de lista

Contas de análise do Data Lake da lista dentro de um grupo de recursos específico

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Obter os detalhes de uma conta

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Eliminar uma conta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Gerir origens de dados

Atualmente, o Data Lake Analytics suporta as seguintes origens de dados de dois:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/common/storage-introduction.md)

Quando cria uma conta do Analytics, tem de designar uma conta de armazenamento do Azure Data Lake para ser a conta de armazenamento predefinida. A conta de armazenamento do Data Lake predefinida é utilizada para armazenar registos de auditoria de metadados e a tarefa de tarefa. Depois de criar uma conta de análise, pode adicionar mais contas de armazenamento do Data Lake e/ou a conta de armazenamento do Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Localizar a conta do Data Lake Store predefinida

Pode ver a conta do Data Lake Store predefinida utilizada ao executar o `az dla account show` comando. Nome da conta predefinida é listado sob a propriedade defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Adicionar mais contas de armazenamento de BLOBs

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> São suportados apenas BLOBs armazenamento nomes abreviados. Não utilize o FQDN, por exemplo "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar mais contas do Data Lake Store

O seguinte comando atualiza a conta do Data Lake Analytics especificada com uma conta do Data Lake Store adicional:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Atualizar a origem de dados existente

Para atualizar uma chave de conta de armazenamento de Blob existente:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Lista de origens de dados:

Para listar as contas do Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Para listar a conta de armazenamento de BLOBs:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Origem de dados de lista do Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Elimine origens de dados:
Para eliminar uma conta do Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Para eliminar uma conta de armazenamento de BLOBs:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Gerir tarefas
Tem de ter uma conta do Data Lake Analytics antes de poder criar uma tarefa.  Para obter mais informações, consulte [contas de gerir o Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Lista de tarefas

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Origem de dados de lista do Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obter os detalhes da tarefa

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Submeter tarefas

> [!NOTE]
> A prioridade de padrão de uma tarefa é 1000 e o grau de padrão de paralelismo para uma tarefa é 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Cancelar tarefas
Utilize o comando de lista para localizar o id da tarefa e, em seguida, utilize Cancelar para cancelar a tarefa.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pipelines e recorrências

**Obter informações sobre pipelines e recorrências**

Utilize os comandos `az dla job pipeline` para ver as informações de pipeline de tarefas previamente submetidas.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Utilize os comandos `az dla job recurrence` para ver as informações de recorrência para tarefas previamente submetidas.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="see-also"></a>Consulte também
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerir o Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

