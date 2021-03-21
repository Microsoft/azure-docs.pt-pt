---
title: Migrar a metásta de Hive padrão para a metásta externa em Azure HDInsight
description: Migrar a metásta de Hive padrão para a metásta externa em Azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 825204fe40125a65e8e6f27c6973417813700a9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746360"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Migrar metasteiro-metadeiro de Colmeia DB para DB de metástaria externa

Este artigo mostra como migrar metadados de uma [metastria padrão DB](../hdinsight-use-external-metadata-stores.md#default-metastore) para Hive para uma base de dados SQL externa em HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Por que migrar para o DB de metastória externa

* A metastinha DB padrão está limitada ao SKU básico e não consegue lidar com cargas de trabalho à escala de produção.

* A metásta externa DB permite ao cliente escalar horizontalmente os recursos de computação da Hive, adicionando novos clusters HDInsight que partilham o mesmo DB metasteiro.

* Para a migração HDInsight 3.6 a 4.0, é obrigatório migrar metadados para a metástaria externa DB antes de atualizar a versão do esquema da Colmeia. Consulte [cargas de trabalho migratórias de HDInsight 3.6 a HDInsight 4.0](./apache-hive-migrate-workloads.md).

Como a metásta padrão DB tem capacidade de computação limitada, recomendamos uma baixa utilização de outros trabalhos no cluster enquanto migram metadados.

Os DBs de origem e alvo devem utilizar a mesma versão HDInsight e as mesmas Contas de Armazenamento. Se atualizar as versões HDInsight de 3.6 a 4.0, complete primeiro os passos deste artigo. Em seguida, siga os passos oficiais de atualização [aqui.](./apache-hive-migrate-workloads.md)

## <a name="prerequisites"></a>Pré-requisitos

Se utilizar [a Azure Data Lake Storage Gen1,](../overview-data-lake-storage-gen1.md)as localizações da mesa de colmeia dependem provavelmente das configurações hdfs do cluster para a Azure Data Lake Storage Gen1. Execute a seguinte ação de script para tornar estes locais portáteis para outros clusters. Consulte [a ação do Script para um cluster de execução.](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)

A ação é semelhante à substituição de symlinks por todos os seus caminhos.

|Propriedade | Valor |
|---|---|
|URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Tipo de nó(s)|Head|
|Parâmetros|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Migrar com exportação/importação usando sqlpackage

Um cluster HDInsight criado apenas após 2020-10-15 suporta a SQL Export/Import para a metastria padrão da Colmeia DB através da utilização `sqlpackage` .

1. Instale [a embalagem de sql para](https://docs.microsoft.com/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) o cluster.

2. Exporte o ficheiro DB de metastagem predefinido para o ficheiro BACPAC executando o seguinte comando.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Guarde o ficheiro BACPAC. Abaixo está uma opção.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Importe o ficheiro BACPAC para uma nova base de dados com etapas listadas [aqui.](../../azure-sql/database/database-import.md)

5. A nova base de dados está pronta para ser [configurada como DB de metástaria externa num novo cluster HDInsight](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Migrar usando o script da Colmeia

Os clusters criados antes de 2020-10-15 não suportam a exportação/importação do DB metasteiro predefinido.

Para estes agrupamentos, siga as [tabelas de copy hive guia através das Contas de Armazenamento,](./hive-migration-across-storage-accounts.md)utilizando um segundo cluster com um [DB de metástaria de colmeia externa](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). O segundo cluster pode usar a mesma conta de armazenamento, mas deve usar um novo sistema de ficheiros predefinido.

### <a name="option-to-shallow-copy"></a>Opção para cópia "rasa"
O consumo de armazenamento duplicaria quando as tabelas fossem "profundas" copiadas utilizando o guia acima. É necessário limpar manualmente os dados no recipiente de armazenamento de origem.
Podemos, em vez disso, copiar as tabelas se não forem transacionais. Todas as tabelas de Hive em HDInsight 3.6 não são transacionais por padrão, mas apenas tabelas externas não são transacionais em HDInsight 4.0. As tabelas transacionais devem ser profundamente copiadas. Siga estes passos para copiar tabelas não transacionais rasas:

1. Execute [o](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) hive-ddls.sh de script no headnode primário do cluster de origem para gerar o DDL para cada mesa de Colmeia.
2. O DDL é escrito para um script local da Colmeia chamado `/tmp/hdi_hive_ddls.hql` . Execute isto no cluster alvo que usa uma metástaria de colmeia externa DB.

## <a name="verify-that-all-hive-tables-are-imported"></a>Verifique se todas as tabelas da Colmeia são importadas

O seguinte comando utiliza uma consulta SQL na metástaria DB para imprimir todas as tabelas da Hive e as suas localizações de dados. Compare as saídas entre clusters novos e antigos para verificar se não faltam mesas na nova metastinha DB.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Leitura adicional

* [Migrar cargas de trabalho de HDInsight 3.6 a 4.0](./apache-hive-migrate-workloads.md)
* [Migração da carga de trabalho da Colmeia através de contas de armazenamento](./hive-migration-across-storage-accounts.md)
* [Conecte-se à Beeline no HDInsight](../hadoop/connect-install-beeline.md)
* [Erro de verificação de erro de resolução de problemas Criar tabela](./interactive-query-troubleshoot-permission-error-create-table.md)
