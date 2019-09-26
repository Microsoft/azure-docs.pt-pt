---
title: Gerenciar Azure Data Lake Analytics usando CLI do Azure
description: Este artigo descreve como usar o CLI do Azure para gerenciar Data Lake Analytics trabalhos, fontes de dados & usuários.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: d66926d8ba87096537800d22a9c116b7b10d23cf
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309744"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Gerenciar Azure Data Lake Analytics usando a CLI (interface de linha de comando) do Azure

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas de Azure Data Lake Analytics, fontes de dados, usuários e trabalhos usando o CLI do Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique na guia selecionar acima.


**Pré-requisitos**

Antes de iniciar este tutorial, você deve ter os seguintes recursos:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* CLI do Azure. Consulte [instalar e configurar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Transfira e instale o **pré-lançamento** [Ferramentas CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.

* Autentique usando o `az login` comando e selecione a assinatura que você deseja usar. Para mais informações sobre a autenticação através de uma conta escolar ou profissional, consulte [Ligar a uma subscrição do Azure a partir da CLI do Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Agora você pode acessar os comandos Data Lake Analytics e Data Lake Store. Execute o seguinte comando para listar os comandos de Data Lake Store e Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Gerir contas

Antes de executar qualquer trabalho de Data Lake Analytics, você deve ter uma conta de Data Lake Analytics. Ao contrário do Azure HDInsight, você não paga por uma conta de análise quando não está executando um trabalho. Você paga apenas pelo tempo em que está executando um trabalho.  Para obter mais informações, consulte [Azure data Lake Analytics visão geral](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Criar contas

Execute o comando a seguir para criar uma conta de Data Lake, 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Atualizar contas

O comando a seguir atualiza as propriedades de uma conta existente do Data Lake Analytics

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Listar contas

Listar contas Data Lake Analytics dentro de um grupo de recursos específico

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Obter detalhes de uma conta

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Excluir uma conta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Gerenciar fontes de dados

O Data Lake Analytics atualmente dá suporte às duas fontes de dados a seguir:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/common/storage-introduction.md)

Ao criar uma conta de análise, você deve designar uma conta de Azure Data Lake Storage para ser a conta de armazenamento padrão. A conta de armazenamento de Data Lake padrão é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Depois de criar uma conta de análise, você pode adicionar mais contas de Data Lake Storage e/ou conta de armazenamento do Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Localizar a conta de Data Lake Store padrão

Você pode exibir a conta de data Lake Store padrão usada ao executar `az dla account show` o comando. O nome da conta padrão é listado na propriedade defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Adicionar mais contas de armazenamento de BLOBs

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Somente nomes curtos de armazenamento de BLOBs têm suporte. Não use o FQDN, por exemplo, "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar contas de Data Lake Store adicionais

O comando a seguir atualiza a conta de Data Lake Analytics especificada com uma conta de Data Lake Store adicional:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Atualizar fonte de dados existente

Para atualizar uma chave de conta de armazenamento de BLOBs existente:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Listar fontes de dados:

Para listar as contas de Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Para listar a conta de armazenamento de BLOBs:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Fonte de dados da lista de Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Excluir fontes de dados:
Para excluir uma conta de Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Para excluir uma conta de armazenamento de BLOBs:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Gerir tarefas
Você deve ter uma conta de Data Lake Analytics antes de poder criar um trabalho.  Para obter mais informações, consulte [gerenciar contas de data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Listar trabalhos

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Fonte de dados da lista de Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obter detalhes do trabalho

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Enviar trabalhos

> [!NOTE]
> A prioridade padrão de um trabalho é 1000, e o grau padrão de paralelismo para um trabalho é 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Cancelar tarefas
Use o comando list para localizar a ID do trabalho e, em seguida, use Cancel para cancelar o trabalho.

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

