---
title: Migrar Azure HDInsight 3.6 Cargas de trabalho de colmeia para HDInsight 4.0
description: Saiba como migrar cargas de trabalho da Hive Apache em HDInsight 3.6 a HDInsight 4.0.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: b13e8e088eff95071247a53ad1a4a18879f94053
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742199"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar Azure HDInsight 3.6 Cargas de trabalho de colmeia para HDInsight 4.0

HDInsight 4.0 tem várias vantagens sobre HDInsight 3.6. Aqui está uma [visão geral das novidades em HDInsight 4.0](../hdinsight-version-release.md).

Este artigo abrange etapas para migrar as cargas de trabalho da Colmeia de HDInsight 3.6 a 4.0, incluindo

* Cópia de metástase de colmeia e atualização de esquema
* Migração segura para compatibilidade do ACID
* Preservação das políticas de segurança da Colmeia

Os novos e antigos clusters HDInsight devem ter acesso às mesmas Contas de Armazenamento.

A migração das tabelas da Colmeia para uma nova Conta de Armazenamento tem de ser feita como um passo separado. Consulte [a migração da Colmeia através das Contas de Armazenamento.](./hive-migration-across-storage-accounts.md)

## <a name="steps-to-upgrade"></a>Passos para atualizar

### <a name="1-prepare-the-data"></a>1. Preparar os dados

* HDInsight 3.6 por predefinição não suporta tabelas ACID. No entanto, se as tabelas ACID estiverem presentes, execute a compactação 'MAJOR'. Consulte o [Manual de Idiomas da Colmeia](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) para obter mais informações sobre a compactação.

* Se utilizar [a Azure Data Lake Storage Gen1,](../overview-data-lake-storage-gen1.md)as localizações da mesa de colmeia dependem provavelmente das configurações hdfs do cluster. Execute a seguinte ação de script para tornar estes locais portáteis para outros clusters. Consulte [a ação do Script para um cluster de execução.](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)

    |Propriedade | Valor |
    |---|---|
    |URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Tipo de nó(s)|Head|
    |Parâmetros||

### <a name="2-copy-the-sql-database"></a>2. Copiar a base de dados SQL

* Se o cluster utilizar uma metásta de Colmeia padrão, siga este [guia](./hive-default-metastore-export-import.md) para exportar metadados para uma meta-loja externa. Em seguida, crie uma cópia da metastore externa para atualização.

* Se o cluster utilizar uma metásta de Colmeia externa, crie uma cópia. As opções incluem [exportação/importação](../../azure-sql/database/database-export.md) e [restauro pontual.](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore)

### <a name="3-upgrade-the-metastore-schema"></a>3. Atualize o esquema de metástaria

Este passo utiliza o [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) de HDInsight 4.0 para atualizar o esquema de metástaria.

> [!Warning]
> Este passo não é reversível. Faça isto apenas numa cópia da metásta.

1. Crie um cluster HDInsight 4.0 temporário para aceder à Colmeia 4.0 `schematool` . Pode utilizar a [metásta de Colmeia padrão](../hdinsight-use-external-metadata-stores.md#default-metastore) para este passo.

1. A partir do cluster HDInsight 4.0, execute `schematool` para atualizar a metasstore HDInsight 3.6 do alvo:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Este utilitário utiliza o cliente `beeline` para executar scripts SQL em `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > A sintaxe SQL nestes scripts não é necessariamente compatível com outras ferramentas do cliente. Por exemplo, [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [Editor de Consulta no Portal Azure](../../azure-sql/database/connect-query-portal.md) requerem palavra-chave após cada `GO` comando.
    >
    > Se algum script falhar devido à capacidade de recursos ou intervalos de transação, dimensione a Base de Dados SQL.

1. Verifique a versão final com consulta `select schema_version from dbo.version` .

    A saída deve coincidir com a do seguinte comando de bash do cluster HDInsight 4.0.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Elimine o cluster HDInsight 4.0 temporário.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. Implementar um novo cluster HDInsight 4.0

Crie um novo cluster HDInsight 4.0, [selecionando a metastore da Hive atualizada](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) e as mesmas Contas de Armazenamento.

* O novo cluster não requer ter o mesmo sistema de ficheiros predefinido.

* Se a metassestore contiver tabelas que residem em várias Contas de Armazenamento, é necessário adicionar essas Contas de Armazenamento ao novo cluster para aceder a essas tabelas. Consulte [adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md).

* Se os trabalhos de Colmeia falharem devido à inacessibilidade de armazenamento, verifique se a localização da tabela está numa Conta de Armazenamento adicionada ao cluster.

    Utilize o seguinte comando da Colmeia para identificar a localização da tabela:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. Converter tabelas para a conformidade com o ÁCIDO

As tabelas geridas devem estar em conformidade com o HDInsight 4.0. Corra `strictmanagedmigration` em HDInsight 4.0 para converter todas as tabelas geridas não-ACID para mesas externas com propriedade `'external.table.purge'='true'` . Executar a partir do nome principal:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Colmeia Segura através das versões HDInsight

O HDInsight integra opcionalmente o Azure Ative Directory utilizando o HdInsight Enterprise Security Package (ESP). A ESP usa Kerberos e Apache Ranger para gerir as permissões de recursos específicos dentro do cluster. As políticas de Ranger implementadas contra a Colmeia em HDInsight 3.6 podem ser migradas para HDInsight 4.0 com os seguintes passos:

1. Navegue para o painel ranger Service Manager no seu cluster HDInsight 3.6.
2. Navegue para a política chamada **HIVE** e exporte a política para um ficheiro json.
3. Certifique-se de que todos os utilizadores referidos na política exportada json existem no novo cluster. Se um utilizador for referido na política mas não existir no novo cluster, adicione o utilizador ao novo cluster ou remova a referência da política.
4. Navegue para o painel **ranger Service Manager** no seu cluster HDInsight 4.0.
5. Navegue para a política chamada **HIVE** e importe a política ranger json do passo 2.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>Alterações na Colmeia no HDInsight 4.0 que podem exigir alterações na aplicação

* Consulte a configuração adicional utilizando o [Conector hive Warehouse](./apache-hive-warehouse-connector.md) para partilhar a meta-loja entre faíscas e colmeias para tabelas ACID.

* HDInsight 4.0 utiliza [autorização baseada em armazenamento](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server). Se modificar permissões de ficheiros ou criar pastas como um utilizador diferente do Hive, provavelmente atingirá erros da Hive com base em permissões de armazenamento. Para corrigir, conceder `rw-` acesso ao utilizador. Consulte [o Guia de Permissões HDFS](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` é substituído por `Beeline` .

Consulte o [HDInsight 4.0 Anúncio](../hdinsight-version-release.md) para alterações adicionais.

## <a name="further-reading"></a>Leitura adicional

* [HdInsight 4.0 Anúncio](../hdinsight-version-release.md)
* [HDInsight 4.0 mergulho profundo](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Colmeia 3 Mesas ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
