---
title: Migrar cargas de trabalho do hive do Azure HDInsight 3,6 para o HDInsight 4,0
description: Saiba como migrar cargas de trabalho Apache Hive no HDInsight 3,6 para o HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 9f49a9224ed123b76f4d300c27a8dd5822e50ea3
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706024"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar cargas de trabalho do hive do Azure HDInsight 3,6 para o HDInsight 4,0

Este documento mostra como migrar cargas de trabalho Apache Hive e LLAP no HDInsight 3,6 para o HDInsight 4,0. O HDInsight 4,0 fornece recursos mais recentes de Hive e LLAP, como exibições materializadas e cache de resultados de consultas. Ao migrar suas cargas de trabalho para o HDInsight 4,0, você pode usar muitos recursos mais recentes do hive 3 que não estão disponíveis no HDInsight 3,6.

Este artigo aborda os seguintes assuntos:

* Migração de metadados do hive para o HDInsight 4,0
* Migração segura de tabelas ACID e não ACID
* Preservação de políticas de segurança do hive em versões do HDInsight
* Execução de consulta e depuração do HDInsight 3,6 para o HDInsight 4,0

Uma vantagem do hive é a capacidade de exportar metadados para um banco de dados externo (chamado de metastore do hive). O **metastore do hive** é responsável por armazenar estatísticas de tabela, incluindo o local de armazenamento de tabela, nomes de coluna e informações de índice de tabela. O esquema de banco de dados do metastore difere entre as versões do hive. A maneira recomendada para atualizar o metastore do Hive com segurança é criar uma cópia e atualizar a cópia em vez do ambiente de produção atual.

## <a name="copy-metastore"></a>Copiar metastore

O HDInsight 3,6 e o HDInsight 4,0 exigem esquemas de metastore diferentes e não podem compartilhar um único metastore.

### <a name="external-metastore"></a>Metastore externo

Crie uma nova cópia do seu metastore externo. Se você estiver usando um metastore externo, uma das maneiras seguras e fáceis de fazer uma cópia do metastore é [restaurar o banco de dados](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) com um nome diferente usando a função de restauração do banco de dados SQL.  Consulte [usar repositórios de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre como anexar um metastore externo a um cluster HDInsight.

### <a name="internal-metastore"></a>Metastore interno

Se você estiver usando o metastore interno, poderá usar consultas para exportar definições de objeto no metastore do Hive e importá-las para um novo banco de dados.

1. Conecte-se ao cluster HDInsight usando um [cliente Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Conecte-se ao HiveServer2 com o [cliente do beeline](../hadoop/apache-hadoop-use-hive-beeline.md) de sua sessão SSH aberta inserindo o seguinte comando:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; do echo "create database $d; use $d;" >> alltables.sql; for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"` ; do ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

    Esse comando gera um arquivo chamado de **TableName. SQL**. Como o banco de dados padrão não pode ser excluído/recriado, remova `create database default;` instrução em **TableName. SQL**.

1. Saia da sessão SSH. Em seguida, insira um comando scp para baixar o **autotables. SQL** localmente.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.sql c:/hdi
    ```

1. Carregue o **autotables. SQL** para o *novo* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Em seguida, use o SSH para se conectar ao *novo* cluster HDInsight. Execute o seguinte código da sessão SSH:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.sql
    ```

## <a name="upgrade-metastore"></a>Atualizar metastore

Depois que a **cópia** do metastore for concluída, execute um script de atualização de esquema na [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) no cluster existente do HDInsight 3,6 para atualizar o novo metastore para o esquema do hive 3. Isso permite que o banco de dados seja anexado como um metastore do HDInsight 4,0.

Use os valores na tabela abaixo. Substitua `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` pelos valores apropriados para os metastore do Hive **copiados** , separados por espaços. Não inclua ". database.windows.net" ao especificar o nome do SQL Server.

|Propriedade | Valor |
|---|---|
|Tipo de script|-Personalizado|
|Nome|Atualização do hive|
|URI do script bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo (s) de nó|Head|
|Parâmetros|SENHA DE NOME DE USUÁRIO DATABASENAME SQLSERVERNAME|

> [!Warning]  
> A atualização que converte o esquema de metadados do HDInsight 3,6 para o esquema do HDInsight 4,0 não pode ser revertida.

Você pode verificar a atualização executando a seguinte consulta SQL no banco de dados:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrar tabelas do hive para o HDInsight 4,0

Depois de concluir o conjunto anterior de etapas para migrar o metastore do hive para o HDInsight 4,0, as tabelas e os bancos de dados registrados no metastore estarão visíveis no cluster HDInsight 4,0 executando `show tables` ou `show databases` de dentro do cluster. Confira a [execução da consulta em versões do hdinsight](#query-execution-across-hdinsight-versions) para obter informações sobre a execução da consulta em clusters do hdinsight 4,0.

No entanto, os dados reais das tabelas não ficam acessíveis até que o cluster tenha acesso às contas de armazenamento necessárias. Para garantir que seu cluster HDInsight 4,0 possa acessar os mesmos dados que o cluster HDInsight 3,6 antigo, conclua as seguintes etapas:

1. Determine a conta de armazenamento do Azure da tabela ou do banco de dados.

1. Se o cluster HDInsight 4,0 já estiver em execução, anexe a conta de armazenamento do Azure ao cluster via Ambari. Se você ainda não criou o cluster HDInsight 4,0, verifique se a conta de armazenamento do Azure está especificada como a conta de armazenamento de cluster primária ou secundária. Para obter mais informações sobre como adicionar contas de armazenamento a clusters HDInsight, consulte [Adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Implantar novo HDInsight 4,0 e conectar-se ao novo metastore

Após a conclusão da atualização do esquema, implante um novo cluster do HDInsight 4,0 e conecte o metastore atualizado. Se você já tiver implantado o 4,0, defina-o para que você possa se conectar ao metastore do Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Executar script de migração de esquema do HDInsight 4,0

As tabelas são tratadas de forma diferente no HDInsight 3,6 e no HDInsight 4,0. Por esse motivo, você não pode compartilhar as mesmas tabelas para clusters de versões diferentes. Se você quiser usar o HDInsight 3,6 ao mesmo tempo que o HDInsight 4,0, deverá ter cópias separadas dos dados para cada versão.

A carga de trabalho do hive pode incluir uma mistura de tabelas ACID e não ACID. Uma das principais diferenças entre o hive no HDInsight 3,6 (Hive 2) e o hive no HDInsight 4,0 (Hive 3) é a conformidade com ACID para tabelas. No HDInsight 3,6, a habilitação do hive ACID-Compliance requer configuração adicional, mas nas tabelas do HDInsight 4,0 estão em conformidade com ACID por padrão. A única ação necessária antes da migração é executar uma compactação principal em relação à tabela ACID no cluster 3,6. Na exibição do hive ou em beeline, execute a seguinte consulta:

```sql
alter table myacidtable compact 'major';
```

Essa compactação é necessária porque as tabelas ACID do HDInsight 3,6 e do HDInsight 4,0 compreendem os deltas ACID de forma diferente. A compactação impõe um Slate limpo que garante a consistência. A seção 4 da [documentação de migração do hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contém diretrizes para compactação em massa de tabelas Acid 3,6 do HDInsight.

Depois de concluir as etapas de migração e compactação do metastore, você pode migrar o depósito real. Depois de concluir a migração do depósito do hive, o depósito 4,0 do HDInsight terá as seguintes propriedades:

|3,6 |4,0 |
|---|---|
|Tabelas externas|Tabelas externas|
|Tabelas gerenciadas não transacionais|Tabelas externas|
|Tabelas gerenciadas transacionais|Tabelas gerenciadas|

Talvez seja necessário ajustar as propriedades do seu depósito antes de executar a migração. Por exemplo, se você espera que alguma tabela seja acessada por terceiros (como um cluster HDInsight 3,6), essa tabela deverá ser externa quando a migração for concluída. No HDInsight 4,0, todas as tabelas gerenciadas são transacionais. Portanto, as tabelas gerenciadas no HDInsight 4,0 só devem ser acessadas por clusters HDInsight 4,0.

Depois que as propriedades da tabela forem definidas corretamente, execute a ferramenta de migração de depósito do hive de um dos cabeçalho de cluster usando o Shell SSH:

1. Conecte-se ao seu cabeçalho de cluster usando SSH. Para obter instruções, consulte [conectar-se ao HDInsight usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Abra um shell de logon como o usuário do hive executando `sudo su - hive`
1. Determine a versão da pilha de plataformas de dados executando `ls /usr/hdp`. Isso exibirá uma cadeia de caracteres de versão que você deve usar no próximo comando.
1. Execute o comando a seguir no Shell. Substitua `STACK_VERSION` pela cadeia de caracteres da versão da etapa anterior:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Após a conclusão da ferramenta de migração, o seu depósito do hive estará pronto para o HDInsight 4,0.

> [!Important]  
> Tabelas gerenciadas no HDInsight 4,0 (incluindo tabelas migradas de 3,6) não devem ser acessadas por outros serviços ou aplicativos, incluindo clusters HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Proteger o hive em versões do HDInsight

Desde o HDInsight 3,6, o HDInsight integra-se com Azure Active Directory usando o Enterprise Security Package do HDInsight (ESP). O ESP usa o Kerberos e o Apache Ranger para gerenciar as permissões de recursos específicos no cluster. As políticas de Ranger implantadas no hive no HDInsight 3,6 podem ser migradas para o HDInsight 4,0 com as seguintes etapas:

1. Navegue até o painel de Service Manager do Ranger no cluster do HDInsight 3,6.
2. Navegue até a política chamada **Hive** e exporte a política para um arquivo JSON.
3. Certifique-se de que todos os usuários referenciados no JSON da política exportada existam no novo cluster. Se um usuário for referenciado na política JSON, mas não existir no novo cluster, adicione o usuário ao novo cluster ou remova a referência da política.
4. Navegue até o painel de **Service Manager do Ranger** no cluster do HDInsight 4,0.
5. Navegue até a política chamada **Hive** e importe a política do Ranger JSON da etapa 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Verificar a compatibilidade e modificar os códigos conforme necessário no aplicativo de teste

Ao migrar cargas de trabalho, como programas e consultas existentes, verifique as notas de versão e a documentação de alterações e aplique as alterações conforme necessário. Se o seu cluster HDInsight 3,6 estiver usando um Spark e metastore do Hive compartilhados, será necessária uma [configuração adicional usando o conector do depósito do hive](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Implantar novo aplicativo para produção

Para alternar para o novo cluster, por exemplo, você pode instalar um novo aplicativo cliente e usá-lo como um novo ambiente de produção, ou pode atualizar seu aplicativo cliente existente e alternar para o HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Alternar o HDInsight 4,0 para a produção

Se as diferenças tiverem sido criadas no metastore durante o teste, você precisará atualizar as alterações logo antes de alternar. Nesse caso, você pode exportar & importar o metastore e, em seguida, atualizar novamente.

## <a name="remove-the-old-production"></a>Remover a produção antiga

Depois de confirmar que a versão está completa e totalmente operacional, você pode remover a versão 3,6 e o metastore anterior. Certifique-se de que tudo seja migrado antes de excluir o ambiente.

## <a name="query-execution-across-hdinsight-versions"></a>Execução de consulta em versões do HDInsight

Há duas maneiras de executar e depurar consultas Hive/LLAP em um cluster HDInsight 3,6. O HiveCLI fornece uma experiência de linha de comando e o modo de exibição tez/Hive fornece um fluxo de trabalho baseado em GUI.

No HDInsight 4,0, o HiveCLI foi substituído por Beeline. HiveCLI é um cliente Thrift para Hiveserver 1 e beeline é um cliente JDBC que fornece acesso ao Hiveserver 2. Beeline também pode ser usado para se conectar a qualquer outro ponto de extremidade de banco de dados compatível com JDBC. O beeline está disponível de fora da caixa no HDInsight 4,0 sem nenhuma instalação necessária.

No HDInsight 3,6, o cliente de GUI para interagir com o servidor do hive é a exibição do hive do Ambari. O HDInsight 4,0 não é fornecido com a exibição Ambari. Fornecemos uma maneira para que nossos clientes usem o DAS (data Analytics Studio), que não é um serviço principal do HDInsight. O DAS não é fornecido com clusters HDInsight prontos para uso e não é um pacote oficialmente suportado. No entanto, o DAS pode ser instalado no cluster usando uma [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) da seguinte maneira:

|Propriedade | Valor |
|---|---|
|Tipo de script|-Personalizado|
|Nome|Attached|
|URI do script bash|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Tipo (s) de nó|Head|

Aguarde de 5 a 10 minutos e, em seguida, inicie o data Analytics Studio usando esta URL: `https://CLUSTERNAME.azurehdinsight.net/das/`.

Quando o DAS estiver instalado, se você não vir as consultas que você executou no Visualizador de consultas, execute as seguintes etapas:

1. Defina as configurações de Hive, tez e DAS, conforme descrito neste [guia para solução de problemas de instalação do das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Verifique se as seguintes configurações de diretório do armazenamento do Azure são blobs de página e se estão listadas em `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Reinicie HDFS, Hive, tez e DAS em ambos os cabeçalho.

## <a name="next-steps"></a>Passos seguintes

* [Comunicado do HDInsight 4,0](../hdinsight-version-release.md)
* [Aprofundamento no HDInsight 4,0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3-tabelas ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
