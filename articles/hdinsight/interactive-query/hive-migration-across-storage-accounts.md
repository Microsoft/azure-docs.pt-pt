---
title: Migração da carga de trabalho da Colmeia para nova conta no Azure Storage
description: Migração da carga de trabalho da Colmeia para nova conta no Azure Storage
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747228"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Migração da carga de trabalho da Colmeia para nova conta no Azure Storage

Aprenda a usar as ações de script para copiar tabelas de Hive através de contas de armazenamento em HDInsight. Isto pode ser útil quando migrar para [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Para copiar manualmente uma tabela individual da Colmeia em HDInsight 4.0, consulte [a exportação/importação da Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Pré-requisitos

* Um novo cluster HDInsight com as seguintes configurações:
  * O seu sistema de ficheiros predefinido está na conta de armazenamento alvo. Consulte [o armazenamento Azure com clusters Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md).
  * A sua versão de cluster deve coincidir com a do cluster de origem.
  * Usa uma nova metástaria de colmeia externa. Ver [Utilizar lojas de metadados externos.](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* Uma conta de armazenamento acessível tanto a clusters originais como a novos. Consulte as contas de armazenamento adicionais aos [tipos e funcionalidades](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) de armazenamento e armazenamento [HDInsight](../hdinsight-hadoop-add-storage.md) para os tipos de armazenamento secundário permitidos.

    Aqui estão algumas opções:
  * Adicione a conta de armazenamento alvo ao cluster original.
  * Adicione a conta de armazenamento original ao novo cluster.
  * Adicione uma conta de armazenamento intermediário aos clusters originais e novos.

## <a name="how-it-works"></a>Como funciona

Vamos executar uma ação de script para exportar tabelas de Colmeia do cluster original para um diretório HDFS especificado. Consulte [a ação do Script para um cluster de execução.](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)

Depois, vamos fazer outra ação de guião sobre o novo cluster para importar as mesas de Colmeia do diretório HDFS.

O script recriará as tabelas para o sistema de ficheiros predefinido do novo cluster. As tabelas autóctones também copiarão os seus dados no armazenamento. As tabelas não-nativas só copiarão por definição. Consulte [os manipuladores de armazenamento hive](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) para obter detalhes sobre tabelas não nativas.

O caminho das mesas externas que não estão no diretório do armazém da Colmeia será preservado. Outras tabelas copiarão para o caminho padrão da Colmeia do cluster alvo. Consulte as propriedades da Colmeia `hive.metastore.warehouse.external.dir` e `hive.metastore.warehouse.dir` .

Os scripts não preservarão permissões de ficheiros personalizados no cluster alvo.

> [!NOTE]
>
> Este guia suporta a cópia de objetos de metadados relacionados com bases de dados, tabelas e divisórias da Hive. Outros objetos de metadados devem ser recriados manualmente.
>
> * Para `Views` , Hive suporta `SHOW VIEWS` o comando a partir de Hive 2.2.0 em HDInsight 4.0. Use `SHOW CREATE TABLE` para definição de vista. Para versões anteriores da Hive, consultar o SQL DB metasteiro para mostrar vistas.
> * Para `Materialized Views` , usar comandos , e `SHOW MATERIALIZED VIEWS` . `DESCRIBE FORMATTED` `CREATE MATERIALIZED VIEW` . Consulte [as vistas materializadas](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) para mais detalhes.
> * Para `Constraints` , suportado a partir de Hive 2.1.0 em HDInsight 4.0, use para `DESCRIBE EXTENDED` listar constrangimentos para uma mesa, e use `ALTER TABLE` para adicionar restrições. Consulte [altere as restrições de mesa](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) para mais detalhes.

## <a name="copy-hive-tables"></a>Copiar mesas de colmeia

1. Aplique a ação de guião "exportação" no cluster original com os seguintes campos.

    Isto irá gerar e executar scripts intermediários da Hive. Pouparão para o `<hdfs-export-path>` especificado.

    Opcionalmente, `--run-script=false` utilize-os para personalizá-los antes de executar manualmente.

    |Propriedade | Valor |
    |---|---|
    |URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Tipo de nó(s)|Head|
    |Parâmetros|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. Após a conclusão bem sucedida da exportação, aplique a ação de "importação" do guião no novo cluster com os seguintes campos.

    |Propriedade | Valor |
    |---|---|
    |URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Tipo de nó(s)|Head|
    |Parâmetros|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Verificação

Descarregue e execute o script como utilizador raiz [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) no nó primário de cada cluster e compare o conteúdo do ficheiro de saída `/tmp/hive_contents.out` . Consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="cleanup-additional-storage-usage"></a>Limpeza de armazenamento adicional

Após a migração de armazenamento estar completa e verificada, pode eliminar os dados na trajetória de exportação de HDFS especificada.

A opção é utilizar o comando HDFS `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>Opção: reduzir o uso adicional de armazenamento

A ação do script de exportação provavelmente duplica o uso do armazenamento devido à Colmeia. No entanto, é possível limitar a utilização adicional do armazenamento migrando manualmente, uma base de dados ou uma tabela de cada vez.

1. Especifique `--run-script=false` para saltar a execução do script gerado da Colmeia. Os scripts de exportação e importação da Colmeia continuariam a ser guardados para a via de exportação.

2. Executar os excertos da base de dados de exportação e importação da Colmeia, base de dados por base de dados ou quadro a mesa, limpando manualmente o caminho de exportação após cada base de dados ou tabela migratória.

## <a name="next-steps"></a>Passos seguintes

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md) (Armazenamento do Azure Data Lake Gen2)
* [Utilizar arquivos de metadados externos](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Tipos e funcionalidades de armazenamento](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Ação do script para um cluster de execução](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
