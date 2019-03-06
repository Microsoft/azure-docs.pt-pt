---
title: Introdução ao Azure Data Lake Analytics com a CLI do Azure
description: Saiba como utilizar a Interface de linha de comandos do Azure para criar uma conta do Azure Data Lake Analytics e submeter uma tarefa de U-SQL.
ms.service: data-lake-analytics
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 9d9d5a7232529989901709013dcfac12f94afad0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433081"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli"></a>Introdução ao Azure Data Lake Analytics com a CLI do Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Este artigo descreve como utilizar a interface de linha de comandos da CLI do Azure para criar contas de Azure Data Lake Analytics, submeter tarefas de u-SQL e catálogos. A tarefa que lê um ficheiro de valores separados por tabulações (TSV) e converte-o num ficheiro de valores separados por vírgulas (CSV). 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, irá precisar dos seguintes itens:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Este artigo requer que está a executar a CLI do Azure versão 2.0 ou posterior. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 



## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão na subscrição do Azure:

```
azurecli
az login
```

É-lhe pedido para navegar para um URL e introduzir um código de autenticação.  E, em seguida, siga as instruções para introduzir as suas credenciais.

Depois de ter sessão iniciada, o comando de início de sessão lista as suas subscrições.

Para utilizar uma subscrição específica:

```
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Criar conta de Data Lake Analytics
Tem de ter uma conta de Data Lake Analytics antes de poder executar quaisquer tarefas. Para criar uma conta de Data Lake Analytics, tem de especificar os seguintes itens:

* **Grupo de Recursos do Azure**. Uma conta do Data Lake Analytics tem de ser criada dentro de um grupo de recursos do Azure. O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite trabalhar com os recursos na aplicação como um grupo. Pode implementar, atualizar ou eliminar todos os recursos da aplicação numa operação única e coordenada.  

Para listar os grupos de recursos existentes na sua subscrição:

```
az group list
```

Para criar um novo grupo de recursos:

```
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Nome da conta do Data Lake Analytics**. Cada conta do Data Lake Analytics tem um nome.
* **Localização**. Utilize um dos centros de dados do Azure que suportem a Data Lake Analytics.
* **Conta padrão Data Lake Store**: Cada conta do Data Lake Analytics tem uma conta do Data Lake Store predefinida.

Para listar a conta existente do Data Lake Store:

```
az dls account list
```

Para criar uma nova conta do Data Lake Store:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Utilize a seguinte sintaxe para criar uma conta do Data Lake Analytics:

```
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Depois de criar uma conta, pode utilizar os comandos seguintes para listar as contas e mostrar detalhes da conta:

```
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Carregar dados para o Data Lake Store
Neste tutorial, vai processar alguns registos de pesquisa.  O registo de pesquisa pode ser armazenado no Data Lake Store ou no Armazenamento de Blobs do Azure.

O portal do Azure fornece uma interface de utilizador para copiar alguns ficheiros de dados de exemplo para a conta predefinida do Data Lake Store, que incluem um ficheiro de registo de pesquisa. Consulte [Preparar dados de origem](data-lake-analytics-get-started-portal.md) para carregar os dados para a conta de Data Lake Store predefinida.

Para carregar ficheiros através da CLI do Azure, utilize os seguintes comandos:

```
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

A Data Lake Analytics também pode aceder ao armazenamento de Blobs do Azure.  Para carregar dados para o armazenamento de Blobs do Azure, consulte [Utilizar a CLI do Azure com o Armazenamento do Azure](../storage/common/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Submeter tarefas de Data Lake Analytics
As tarefas de Data Lake Analytics são escritas em linguagem U-SQL. Para saber mais sobre U-SQL, consulte [Introdução à linguagem U SQL](data-lake-analytics-u-sql-get-started.md) e [Referência de linguagem U-SQL](https://go.microsoft.com/fwlink/?LinkId=691348).

**Para criar um script de tarefas do Data Lake Analytics**

Crie um ficheiro de texto com o seguinte script de U-SQL e guarde o ficheiro de texto na estação de trabalho:

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Este script U-SQL lê o ficheiro de dados de origem, utilizando **Extractors.Tsv()**, e, em seguida, cria um ficheiro csv, utilizando **Outputters.Csv()**.

Não modifique os dois caminhos, exceto se copiar o ficheiro de origem para uma localização diferente.  O Data Lake Analytics cria a pasta de saída se não existir.

É mais simples utilizar caminhos relativos para ficheiros armazenados em contas do Data Lake Store predefinidas. Também pode utilizar caminhos absolutos.  Por exemplo:

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

Tem de utilizar caminhos absolutos para aceder a ficheiros em contas de Armazenamento ligadas.  A sintaxe para ficheiros armazenados numa Conta de Armazenamento do Azure ligada é:

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> O contentor de Blobs do Azure com blobs públicos não é suportado.      
> O contentor de Blobs do Azure com contentores públicos não é suportado.      
>

**Para submeter tarefas**

Utilize a sintaxe seguinte para submeter uma tarefa.

```
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Por exemplo:

```
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Para listar tarefas e mostrar detalhes da tarefa**

```
azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Para cancelar as tarefas**

```
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Obter resultados de tarefa

Depois de uma tarefa ser concluída, pode utilizar os seguintes comandos para listar os ficheiros de saída e transferir os ficheiros:

```
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

Por exemplo:

```
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Passos Seguintes

* Para ver o documento de referência da CLI do Azure do Data Lake Analytics, consulte [do Data Lake Analytics](/cli/azure/dla).
* Para ver o documento de referência da CLI do Azure do Data Lake Store, consulte [Data Lake Store](/cli/azure/dls).
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
