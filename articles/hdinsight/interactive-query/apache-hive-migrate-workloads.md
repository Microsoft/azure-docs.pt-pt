---
title: Migrar Azure HDInsight 3.6 Cargas de trabalho de colmeia para HDInsight 4.0
description: Saiba como migrar cargas de trabalho da Hive Apache em HDInsight 3.6 a HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: bcc0faa8fdbd61ab3e3e0886256f7c796e5a98e2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011511"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar Azure HDInsight 3.6 Cargas de trabalho de colmeia para HDInsight 4.0

Este documento mostra como migrar cargas de trabalho apaches e LLAP em HDInsight 3.6 a HDInsight 4.0. HDInsight 4.0 fornece funcionalidades mais recentes de Hive e LLAP, tais como vistas materializadas e caching de resultados de consulta. Quando migrar as suas cargas de trabalho para HDInsight 4.0, pode utilizar muitas funcionalidades mais recentes da Hive 3 que não estão disponíveis no HDInsight 3.6.

Este artigo abrange os seguintes temas:

* Migração de metadados da Colmeia para HDInsight 4.0
* Migração segura de tabelas ACID e não-ACID
* Preservação das políticas de segurança da Hive através das versões HDInsight
* Execução de consultas e depuração de HDInsight 3.6 a HDInsight 4.0

Uma das vantagens da Colmeia é a capacidade de exportar metadados para uma base de dados externa (referida como a Metástore da Colmeia). A **Hive Metastore** é responsável pelo armazenamento de estatísticas de tabelas, incluindo a localização de armazenamento de mesas, nomes de colunas e informações sobre índices de tabela. HDInsight 3.6 e HDInsight 4.0 requerem esquemas de metástamas diferentes e não podem partilhar uma única meta-loja. A forma recomendada de atualizar a metástore da Colmeia com segurança é atualizar uma cópia em vez da original no ambiente de produção atual. Este documento requer que os clusters originais e novos tenham acesso à mesma Conta de Armazenamento. Por conseguinte, não abrange a migração de dados para outra região.

## <a name="migrate-from-external-metastore"></a>Migrar da metastore externa

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. Executar uma grande compactação em tabelas ACID em HDInsight 3.6

As tabelas HDInsight 3.6 e HDInsight 4.0 ACID compreendem os deltas acid de forma diferente. A única ação necessária antes da migração é executar a compactação 'MAJOR' contra cada tabela ACID no cluster 3.6. Consulte o [Manual de Idiomas da Colmeia](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) para obter mais informações sobre a compactação.

### <a name="2-copy-sql-database"></a>2. Copiar base de dados SQL
Crie uma nova cópia da sua metástase externa. Se estiver a utilizar uma metástamo externa, uma das formas seguras e fáceis de fazer uma cópia da metastore é restaurar a base de [dados](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) com um nome diferente utilizando a `RESTORE` função.  Consulte [as lojas de metadados externos em Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre a fixação de uma metástore externa a um cluster HDInsight.

### <a name="3-upgrade-metastore-schema"></a>3. Atualizar o esquema da metástaria
Assim que a **cópia** da metástore estiver concluída, execute um script de atualização de esquemas em [Script Action](../hdinsight-hadoop-customize-cluster-linux.md) no cluster HDInsight 3.6 existente para atualizar a nova metastore para o esquema da Hive 3. (Este passo não requer que a nova meta-loja seja ligada a um cluster.) Isto permite que a base de dados seja anexada como metásta de HDInsight 4.0.

Utilize os valores na tabela mais abaixo. `SQLSERVERNAME DATABASENAME USERNAME PASSWORD`Substitua-os pelos valores adequados para a **cópia** da metástasia da Colmeia, separada por espaços. Não inclua ".database.windows.net" ao especificar o nome do servidor SQL.

|Propriedade | Valor |
|---|---|
|Tipo de script|- Personalizado|
|Nome|Upgrade de colmeia|
|URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo de nó(s)|Head|
|Parâmetros|SQLSERVERNAME PALAVRA-PASSE DE NOME DE UTILIZADOR|

> [!Warning]  
> A atualização que converte o esquema de metadados HDInsight 3.6 para o esquema HDInsight 4.0 não pode ser revertida.

Pode verificar a atualização executando a seguinte consulta sql com a base de dados:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. Implementar um novo cluster HDInsight 4.0

1. Especifique a metastore atualizada como a metásta de Hive do novo cluster.

1. Os dados reais das tabelas, no entanto, não são acessíveis até que o cluster tenha acesso às contas de armazenamento necessárias.
Certifique-se de que as contas de armazenamento das tabelas de colmeias no cluster HDInsight 3.6 são especificadas como as contas de armazenamento primária ou secundária do novo cluster HDInsight 4.0.
Para obter mais informações sobre a adição de contas de armazenamento a clusters HDInsight, consulte [Adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. Migração completa com uma ferramenta pós-upgrade em HDInsight 4.0

As tabelas geridas devem ser compatíveis com o ACID em HDInsight 4.0, por padrão. Uma vez concluída a migração de metastões, execute uma ferramenta pós-actualização para tornar as tabelas geridas anteriormente não-ACID compatíveis com o cluster HDInsight 4.0. Esta ferramenta aplicará a seguinte conversão:

|3.6 |4.0 |
|---|---|
|Tabelas externas|Tabelas externas|
|Tabelas geridas não-ACID|Tabelas externas com propriedade 'external.table.purpur'='true'|
|Tabelas geridas por ACID|Tabelas geridas por ACID|

Execute a ferramenta de pós-upgrade da Colmeia a partir do cluster HDInsight 4.0 utilizando a concha SSH:

1. Ligue-se ao seu headnode de cluster utilizando SSH. Para obter instruções, consulte [Connect to HDInsight usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Abra uma concha de login como utilizador da Colmeia executando `sudo su - hive`
1. Execute o seguinte comando a partir da concha.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Depois de concluída a ferramenta, o seu armazém da Hive estará pronto para o HDInsight 4.0.

## <a name="migrate-from-internal-metastore"></a>Migrar da metastore interna

Se o seu cluster HDInsight 3.6 utilizar uma metástore de Colmeia interna, siga os passos abaixo para executar um script, que gera consultas de Hive para exportar definições de objetos a partir da metastore.

Os clusters HDInsight 3.6 e 4.0 devem utilizar a mesma Conta de Armazenamento.

> [!NOTE]
>
> * No caso das tabelas ACID, será criada uma nova cópia dos dados por baixo da tabela.
>
> * Este script suporta a migração de bases de dados, tabelas e divisórias da Colmeia, apenas. Espera-se que outros objetos de metadados, como Views, UDFs e Restrições de Tabela, sejam copiados manualmente.
>
> * Uma vez que este script esteja concluído, presume-se que o cluster antigo deixará de ser utilizado para aceder a qualquer uma das tabelas ou bases de dados referidas no script.
>
> * Todas as tabelas geridas tornar-se-ão transacionais em HDInsight 4.0. Opcionalmente, mantenha o quadro não transacional exportando os dados para uma tabela externa com a propriedade 'external.table.purpur'='true'. Por exemplo,
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Ligue-se ao cluster HDInsight 3.6 utilizando um [cliente Secure Shell (SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. A partir da sessão SSH aberta, descarregue o seguinte ficheiro script para gerar um ficheiro chamado **Alltables.hql**.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Para um cluster HDInsight regular, sem ESP, basta executar `exporthive_hdi_3_6.sh` .

    * Para um cluster com ESP, kinit e modificar os argumentos para beeline: executar o seguinte, definindo USER e DOMAIN para utilizador Azure AD com permissões completas de Colmeia.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Encerre a sessão do SSH. Em seguida, insira um comando scp para baixar **alltables.hql** localmente.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Faça o upload **de alltables.hql** para o *novo* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Em seguida, utilize o SSH para ligar ao *novo* cluster HDInsight 4.0. Executar o seguinte código de uma sessão de SSH para este cluster:

    Sem ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    Com ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

A ferramenta de pós-actualização para a migração de metalojas externas não se aplica aqui, uma vez que as tabelas geridas não-ACID a partir de HDInsight 3.6 convertem-se em tabelas geridas por ACID em HDInsight 4.0.

> [!Important]  
> As tabelas geridas em HDInsight 4.0 (incluindo tabelas migradas de 3.6) não devem ser acedidas por outros serviços ou aplicações, incluindo clusters HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Colmeia Segura através das versões HDInsight

Desde o HDInsight 3.6, o HDInsight integra-se com o Azure Ative Directory utilizando o HdInsight Enterprise Security Package (ESP). A ESP usa Kerberos e Apache Ranger para gerir as permissões de recursos específicos dentro do cluster. As políticas de Ranger implementadas contra a Colmeia em HDInsight 3.6 podem ser migradas para HDInsight 4.0 com os seguintes passos:

1. Navegue para o painel ranger Service Manager no seu cluster HDInsight 3.6.
2. Navegue para a política chamada **HIVE** e exporte a política para um ficheiro json.
3. Certifique-se de que todos os utilizadores referidos na política exportada json existem no novo cluster. Se um utilizador for referido na política mas não existir no novo cluster, adicione o utilizador ao novo cluster ou remova a referência da política.
4. Navegue para o painel **ranger Service Manager** no seu cluster HDInsight 4.0.
5. Navegue para a política chamada **HIVE** e importe a política ranger json do passo 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Verifique a compatibilidade e modifique os códigos conforme necessário na aplicação de teste

Ao migrar cargas de trabalho, tais como programas e consultas existentes, verifique as notas de lançamento e documentação para alterações e aplique as alterações conforme necessário. Se o seu cluster HDInsight 3.6 estiver a utilizar uma metastore de faíscas e colmeias partilhadas, é necessária uma configuração adicional utilizando o [Conector do Armazém da Colmeia.](./apache-hive-warehouse-connector.md)

## <a name="deploy-new-app-for-production"></a>Implementar nova app para produção

Para mudar para o novo cluster, por exemplo, pode instalar uma nova aplicação de cliente e usá-la como um novo ambiente de produção, ou pode atualizar a sua aplicação de cliente existente e mudar para HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Switch HDInsight 4.0 para a produção

Se as diferenças foram criadas na meta-loja durante os testes, terá de atualizar as alterações pouco antes de mudar. Neste caso, você pode exportar & importar a meta-loja e, em seguida, atualizar novamente.

## <a name="remove-the-old-production"></a>Remova a produção antiga

Depois de ter confirmado que o lançamento está completo e totalmente operacional, pode remover a versão 3.6 e a metásta. Por favor, certifique-se de que tudo é migrado antes de apagar o ambiente.

## <a name="query-execution-across-hdinsight-versions"></a>Execução de consultas em versões HDInsight

Existem duas formas de executar e depurar consultas de Hive/LLAP dentro de um cluster HDInsight 3.6. A HiveCLI proporciona uma experiência de linha de comando e a [vista Tez/Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) proporciona um fluxo de trabalho baseado em GUI.

Em HDInsight 4.0, HiveCLI foi substituído por Beeline. A vista Tez/Hive proporciona um fluxo de trabalho baseado em GUI. A HiveCLI é cliente da Hiveserver 1, e a Beeline é um cliente JDBC que dá acesso à Hiveserver 2. Beeline também pode ser usado para ligar a qualquer outro ponto final de base de dados compatível com JDBC. A Beeline está disponível fora de caixa no HDInsight 4.0 sem necessidade de qualquer instalação.

## <a name="next-steps"></a>Passos seguintes

* [HdInsight 4.0 Anúncio](../hdinsight-version-release.md)
* [HDInsight 4.0 mergulho profundo](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Colmeia 3 Mesas ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)