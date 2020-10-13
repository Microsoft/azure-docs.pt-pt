---
title: Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight
description: Aprenda a usar a Azure Data Lake Storage Gen2 com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 4ef53b2249f8ce57255c13126c9310f1c889d64f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855060"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) é um serviço de armazenamento em nuvem dedicado à análise de big data, construído no [armazenamento Azure Blob.](../storage/blobs/storage-blobs-introduction.md) Data Lake Storage Gen2 combina as capacidades de armazenamento de Azure Blob e Azure Data Lake Storage Gen1. O serviço resultante oferece funcionalidades da Azure Data Lake Storage Gen1, incluindo: semântica do sistema de ficheiros, segurança ao nível do diretório e nível de ficheiro, e adaptabilidade. Juntamente com o armazenamento de baixo custo, tiered, alta disponibilidade e capacidades de recuperação de desastres a partir do armazenamento Azure Blob.

Para uma comparação completa das opções de criação de cluster usando data lake storage gen2, consulte [opções de armazenamento compare para uso com clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade de Gen2 de armazenamento de data lake

Data Lake Storage Gen2 está disponível como uma opção de armazenamento para quase todos os tipos de cluster Azure HDInsight como uma conta de armazenamento padrão e uma conta de armazenamento adicional. HBase, no entanto, pode ter apenas uma conta com Data Lake Storage Gen2.

> [!Note]  
> Depois de selecionar data lake storage gen2 como o seu **tipo de armazenamento primário,** não pode selecionar um Data Lake Storage Gen1 como armazenamento adicional.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Criar clusters HDInsight usando data lake storage gen2

Utilize os seguintes links para instruções detalhadas sobre como criar clusters HDInsight com acesso à Data Lake Storage Gen2.

* [Usando o Portal](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Utilizar a CLI do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* O PowerShell não é atualmente suportado para criar um cluster HDInsight com a Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controlo de acesso para Data Lake Storage Gen2 em HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Que tipo de permissões suportam o Data Lake Storage Gen2?

Data Lake Storage Gen2 utiliza um modelo de controlo de acesso que suporta tanto o controlo de acesso baseado em funções (RBAC) como as listas de controlo de acesso semelhantes a POSIX (ACLs). Data Lake Storage Gen1 suporta listas de controlo de acesso apenas para controlar o acesso aos dados.

O RBAC utiliza atribuições de funções para aplicar eficazmente conjuntos de permissões aos utilizadores, grupos e princípios de serviço para recursos Azure. Tipicamente, esses recursos Azure estão limitados a recursos de alto nível (por exemplo, contas de armazenamento Azure Blob). Para o armazenamento de Azure Blob, e também data lake storage gen2, este mecanismo foi estendido ao recurso do sistema de ficheiros.

Para obter mais informações sobre permissões de ficheiros com o RBAC, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Para obter mais informações sobre permissões de ficheiros com ACLs, consulte [as listas de controlo do Access em ficheiros e diretórios.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Como controlo o acesso aos meus dados na Data Lake Storage Gen2?

A capacidade do seu cluster HDInsight de aceder a ficheiros na Data Lake Storage Gen2 é controlada através de identidades geridas. Uma identidade gerida é uma identidade registada no Azure Ative Directory (Azure AD) cujas credenciais são geridas pela Azure. Com identidades geridas, não precisa de registar os principais serviços em Azure AD. Ou manter credenciais como certificados.

Os serviços Azure têm dois tipos de identidades geridas: atribuídas pelo sistema e atribuídas pelo utilizador. O HDInsight utiliza identidades geridas atribuídas pelo utilizador para aceder ao Data Lake Storage Gen2. A `user-assigned managed identity` é criado como um recurso autónomo Azure. Através de um processo de criação, o Azure cria uma identidade no inquilino do Azure AD no qual a subscrição que está a ser utilizada confia. Depois de criada, a identidade pode ser atribuída a uma ou mais instâncias do serviço do Azure.

O ciclo de vida das identidades atribuídas pelo utilizador é gerido separadamente do ciclo de vida das instâncias do serviço do Azure ao qual estão atribuídas. Para obter mais informações sobre identidades geridas, veja [quais são as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Como posso definir permissões para os utilizadores de Azure AD consultarem dados na Data Lake Storage Gen2 utilizando a Hive ou outros serviços?

Para definir permissões para os utilizadores consultarem os dados, utilize os grupos de segurança Azure AD como o principal designado em ACLs. Não atribua diretamente permissões de acesso a ficheiros a utilizadores individuais ou a princípios de serviço. Com grupos de segurança AZure AD para controlar o fluxo de permissões, pode adicionar e remover utilizadores ou principais de serviço sem reaplicar ACLs a toda uma estrutura de diretório. Basta adicionar ou remover os utilizadores do grupo de segurança Azure AD apropriado. Os ACLs não são herdados, por isso a recandidúsci dos ACLs requer a atualização do ACL em todos os ficheiros e subdiretivos.

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Existem várias formas de aceder aos ficheiros em Data Lake Storage Gen2 a partir de um cluster HDInsight.

* **Utilizar o nome completamente qualificado**. Com esta abordagem, fornece o caminho completo para o ficheiro ao qual pretende aceder.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Utilizando o formato de caminho abreviado**. Com esta abordagem, você substitui o caminho até a raiz do cluster com:

    ```
    abfs:///<file.path>/
    ```

* **Utilizar o caminho relativo**. Com esta abordagem, fornece apenas o caminho relativo para o ficheiro ao qual pretende aceder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Os exemplos baseiam-se numa [ligação ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) ao nó da cabeça do cluster. Os exemplos utilizam os três esquemas URI. Substituir `CONTAINERNAME` e `STORAGEACCOUNT` pelos valores relevantes

#### <a name="a-few-hdfs-commands"></a>Alguns comandos hdfs

1. Crie um arquivo no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento de clusters.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copie os dados do armazenamento local para o armazenamento de clusters.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Listar conteúdos de diretório no armazenamento de clusters.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Criação de uma mesa de colmeia

Três localizações de ficheiros são mostradas para fins ilustrativos. Para execução real, utilize apenas uma das `LOCATION` entradas.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Passos seguintes

* [Integração Azure HDInsight com pré-visualização da Data Lake Storage Gen2 - ACL e atualização de segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Tutorial: Extrair, transformar e carregar dados usando Consulta Interativa em Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)