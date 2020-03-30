---
title: Migrar azure HDInsight 3.6 Cargas de trabalho da Colmeia para HDInsight 4.0
description: Saiba como migrar as cargas de trabalho da Apache Hive em HDInsight 3.6 para HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214649"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar azure HDInsight 3.6 Cargas de trabalho da Colmeia para HDInsight 4.0

Este documento mostra-lhe como migrar as cargas de trabalho da Apache Hive e LLAP em HDInsight 3.6 para HDInsight 4.0. O HDInsight 4.0 fornece funcionalidades mais recentes da Hive e LLAP, tais como vistas materializadas e cache de resultados de consulta. Ao migrar as suas cargas de trabalho para o HDInsight 4.0, pode utilizar muitas funcionalidades mais recentes da Hive 3 que não estão disponíveis no HDInsight 3.6.

Este artigo abrange os seguintes temas:

* Migração de metadados hive para HDInsight 4.0
* Migração segura de tabelas ACID e não ACID
* Preservação das políticas de segurança da Hive através das versões HDInsight
* Execução de consulta e depuração de HDInsight 3.6 para HDInsight 4.0

Uma vantagem da Hive é a capacidade de exportar metadados para uma base de dados externa (referida como a Metaloja da Colmeia). A **Hive Metastore** é responsável pelo armazenamento de estatísticas de tabelas, incluindo a localização do armazenamento de tabelas, nomes de colunas e informações sobre índices de tabela. O esquema de base de dados da metaloja difere entre as versões da Colmeia. A forma recomendada de atualizar a metaloja da Hive com segurança é criar uma cópia e atualizar a cópia em vez do ambiente de produção atual.

## <a name="copy-metastore"></a>Copiar metaloja

HDInsight 3.6 e HDInsight 4.0 requerem diferentes esquemas de metaloja e não podem partilhar uma única metaloja.

### <a name="external-metastore"></a>Metaloja externa

Crie uma nova cópia da sua metaloja externa. Se estiver a utilizar uma metaloja externa, uma das formas seguras e fáceis de fazer uma cópia da metaloja é [restaurar a Base](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) de Dados com um nome diferente utilizando a função de restauro da Base de Dados SQL.  Consulte [Utilize lojas de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre a anexação de uma metaloja externa a um cluster HDInsight.

### <a name="internal-metastore"></a>Metaloja interna

Se estiver a utilizar a metaloja interna, pode utilizar consultas para exportar definições de objetos na metaloja da Hive e importá-las para uma nova base de dados.

Uma vez que este script esteja completo, presume-se que o aglomerado antigo deixará de ser utilizado para aceder a nenhuma das tabelas ou bases de dados referidas no script.

> [!NOTE]
> No caso das tabelas ACID, será criada uma nova cópia dos dados por baixo da tabela.

1. Ligue-se ao cluster HDInsight utilizando um [cliente Secure Shell (SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Ligue-se ao HiveServer2 com o seu [cliente Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) a partir da sua sessão SSH aberta, inserindo o seguinte comando:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Este comando gera um ficheiro chamado **alltables.hql**.

1. Encerre a sessão do SSH. Em seguida, insira um comando scp para baixar **alltables.hql** localmente.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Faça o upload **alltables.hql** para o *novo* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Em seguida, utilize o SSH para se ligar ao *novo* cluster HDInsight. Executar o seguinte código a partir da sessão SSH:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Metaloja de upgrade

Uma vez que a **cópia** da metaloja esteja completa, execute um script de upgrade de esquemas no [Script Action](../hdinsight-hadoop-customize-cluster-linux.md) no cluster HDInsight 3.6 existente para atualizar a nova metaloja para o esquema hive 3. Isto permite que a base de dados seja anexada como metastore HDInsight 4.0.

Utilize os valores na tabela mais abaixo. Substitua-a `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` pelos valores adequados para a metaloja da Colmeia **copiada,** separada por espaços. Não inclua ".database.windows.net" ao especificar o nome do servidor SQL.

|Propriedade | Valor |
|---|---|
|Tipo de script|- Personalizado|
|Nome|Atualização da colmeia|
|Roteiro de bash URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo de nó(s)|Head|
|Parâmetros|SQLSERVERNAMENAME DATABASENAME USERNAME PASSWORD|

> [!Warning]  
> A atualização que converte o esquema de metadados HDInsight 3.6 para o esquema HDInsight 4.0 não pode ser revertida.

Pode verificar a atualização executando a seguinte consulta sql na base de dados:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrar mesas de colmeia para HDInsight 4.0

Depois de completar o conjunto anterior de passos para migrar a Hive Metastore para HDInsight 4.0, as tabelas e bases de `show tables` `show databases` dados registadas na metaloja serão visíveis a partir do cluster HDInsight 4.0 executando ou de dentro do cluster. Consulte a execução da [Consulta através das versões HDInsight](#query-execution-across-hdinsight-versions) para obter informações sobre a execução de consultas em clusters HDInsight 4.0.

Os dados reais das tabelas, no entanto, não são acessíveis até que o cluster tenha acesso às contas de armazenamento necessárias. Para garantir que o seu cluster HDInsight 4.0 pode aceder aos mesmos dados do seu antigo cluster HDInsight 3.6, complete os seguintes passos:

1. Determine a conta de armazenamento Azure da sua tabela ou base de dados.

1. Se o seu cluster HDInsight 4.0 já estiver em execução, prenda a conta de armazenamento Azure ao cluster via Ambari. Se ainda não criou o cluster HDInsight 4.0, certifique-se de que a conta de armazenamento Azure é especificada como a conta primária ou secundária de armazenamento de cluster. Para obter mais informações sobre a adição de contas de armazenamento aos clusters HDInsight, consulte [Adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Implementar novo HDInsight 4.0 e ligar-se à nova metaloja

Depois da atualização do esquema estar concluída, implemente um novo cluster HDInsight 4.0 e ligue a metaloja atualizada. Se já implementou o 4.0, detete-o para que possa ligar-se à metaloja de Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Executar script de migração de esquemas a partir de HDInsight 4.0

As tabelas são tratadas de forma diferente em HDInsight 3.6 e HDInsight 4.0. Por esta razão, não pode partilhar as mesmas tabelas para clusters de diferentes versões. Se pretender utilizar o HDInsight 3.6 ao mesmo tempo que o HDInsight 4.0, deve ter cópias separadas dos dados para cada versão.

A sua carga de trabalho na Colmeia pode incluir uma mistura de tabelas ACID e não-ACID. Uma diferença fundamental entre a Hive no HDInsight 3.6 (Hive 2) e a Hive no HDInsight 4.0 (Hive 3) é a conformidade com o ÁCIDO para as tabelas. No HDInsight 3.6, permitir a conformidade com a Hive ACID requer uma configuração adicional, mas nas tabelas HDInsight 4.0 são compatíveis com ácido por padrão. A única ação necessária antes da migração é executar uma grande compactação contra a tabela ACID no cluster 3.6. A partir da vista da Colmeia ou de Beeline, executar a seguinte consulta:

```sql
alter table myacidtable compact 'major';
```

Esta compactação é necessária porque as tabelas HDInsight 3.6 e HDInsight 4.0 ACID compreendem os deltas ácidode forma diferente. A compactação impõe uma ardósia limpa que garante a consistência. A secção 4 da documentação sobre [migração](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) da Colmeia contém orientações para a compactação a granel das tabelas HDInsight 3.6 ACID.

Depois de completar os passos de migração e compactação da metaloja, pode migrar o armazém real. Depois de completar a migração do armazém da Hive, o armazém HDInsight 4.0 terá as seguintes propriedades:

|3.6 |4,0 |
|---|---|
|Tabelas externas|Tabelas externas|
|Tabelas geridas não transacionais|Tabelas externas|
|Tabelas geridas por transações|Mesas geridas|

Poderá ter de ajustar as propriedades do seu armazém antes de executar a migração. Por exemplo, se espera que alguma tabela seja acedida por terceiros (como um cluster HDInsight 3.6), essa tabela deve ser externa uma vez que a migração esteja concluída. No HDInsight 4.0, todas as tabelas geridas são transacionais. Por isso, as tabelas geridas no HDInsight 4.0 só devem ser acedidas por clusters HDInsight 4.0.

Uma vez que as propriedades da sua mesa são definidas corretamente, execute a ferramenta de migração do armazém hive a partir de um dos headnodos do cluster usando a concha SSH:

1. Ligue-se ao seu headnode de cluster utilizando SSH. Para obter instruções, consulte [Connect to HDInsight utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Abra uma concha de login como o utilizador da Colmeia executando`sudo su - hive`
1. Determine a versão da pilha `ls /usr/hdp`de dados executando . Isto apresentará uma cadeia de versão que deve utilizar no próximo comando.
1. Execute o seguinte comando da concha. Substitua-a `STACK_VERSION` pela cadeia de versão do passo anterior:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Após o completo da ferramenta de migração, o seu armazém da Hive estará pronto para o HDInsight 4.0.

> [!Important]  
> As tabelas geridas no HDInsight 4.0 (incluindo tabelas migradas a partir de 3.6) não devem ser acedidas por outros serviços ou aplicações, incluindo clusters HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Colmeia segura através das versões HDInsight

Desde o HDInsight 3.6, o HDInsight integra-se com o Azure Ative Directory utilizando o Pacote de Segurança Empresarial HDInsight (ESP). A ESP utiliza kerberos e Apache Ranger para gerir as permissões de recursos específicos dentro do cluster. As políticas de Rangers implementadas contra a Hive no HDInsight 3.6 podem ser migradas para o HDInsight 4.0 com os seguintes passos:

1. Navegue para o painel Ranger Service Manager no seu cluster HDInsight 3.6.
2. Navegue para a política chamada **HIVE** e exporte a política para um ficheiro json.
3. Certifique-se de que todos os utilizadores referidos na política exportada existem no novo cluster. Se um utilizador for referido na política json mas não existir no novo cluster, adicione o utilizador ao novo cluster ou remova a referência da política.
4. Navegue para o painel **Ranger Service Manager** no seu cluster HDInsight 4.0.
5. Navegue para a política chamada **HIVE** e importe a política ranger json do passo 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Verifique a compatibilidade e modifique os códigos conforme necessário na aplicação de teste

Ao migrar cargas de trabalho, como programas e consultas existentes, verifique as notas de lançamento e a documentação para alterações e aplique alterações conforme necessário. Se o seu cluster HDInsight 3.6 estiver a utilizar uma metaloja compartilhada de Spark e Hive, é necessária uma configuração adicional utilizando o [Conector hive Warehouse.](./apache-hive-warehouse-connector.md)

## <a name="deploy-new-app-for-production"></a>Implementar nova app para produção

Para mudar para o novo cluster, por exemplo, pode instalar uma nova aplicação de cliente e usá-la como um novo ambiente de produção, ou pode atualizar a sua aplicação de cliente existente e mudar para HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Altere o HDInsight 4.0 para a produção

Se foram criadas diferenças na metaloja durante os testes, terá de atualizar as alterações pouco antes de mudar. Neste caso, pode exportar & importar a metaloja e depois fazer o upgrade novamente.

## <a name="remove-the-old-production"></a>Remover a produção antiga

Uma vez confirmado que o lançamento está completo e totalmente operacional, pode remover a versão 3.6 e a metaloja anterior. Por favor, certifique-se de que tudo é migrado antes de apagar o ambiente.

## <a name="query-execution-across-hdinsight-versions"></a>Execução de consulta através das versões HDInsight

Existem duas formas de executar e depurar consultas de Hive/LLAP dentro de um cluster HDInsight 3.6. A HiveCLI proporciona uma experiência de linha de comando e a vista Tez/Hive proporciona um fluxo de trabalho baseado em GUI.

No HDInsight 4.0, o HiveCLI foi substituído por Beeline. A HiveCLI é um cliente barato para o Hiveserver 1, e a Beeline é um cliente DaJBC que fornece acesso ao Hiveserver 2. A beeline também pode ser usada para se ligar a qualquer outro ponto final compatível com jDBC. A Beeline está disponível fora da caixa no HDInsight 4.0 sem qualquer instalação necessária.

No HDInsight 3.6, o cliente GUI para interagir com o servidor Hive é a Visão da Colmeia Ambari. HDInsight 4.0 não é enviado com Ambari View. Fornecemos uma forma de os nossos clientes utilizarem o Data Analytics Studio (DAS), que não é um serviço HDInsight central. A DAS não é um pacote de hDInsight fora da caixa e não é um pacote oficialmente suportado. No entanto, as DAS podem ser instaladas no cluster utilizando uma ação de [script](../hdinsight-hadoop-customize-cluster-linux.md) da seguinte forma:

|Propriedade | Valor |
|---|---|
|Tipo de script|- Personalizado|
|Nome|DAS|
|Roteiro de bash URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Tipo de nó(s)|Head|

Aguarde 10 a 15 minutos e, `https://CLUSTERNAME.azurehdinsight.net/das/`em seguida, lance o Data Analytics Studio utilizando este URL: .

Pode ser necessária uma atualização da UI Ambari e/ou de um reinício de todos os componentes Ambari antes de aceder às DAS.

Uma vez instalada a DAS, se não vir as consultas que executou no espectador de consultas, faça os seguintes passos:

1. Delineie as configurações para A Colmeia, Tez e DAS, conforme descrito neste guia para resolução de [problemas da instalação das DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Certifique-se de que os seguintes configs de diretório de armazenamento `fs.azure.page.blob.dirs`Azure são blobs page, e que estão listados em:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Reinicie HDFS, Hive, Tez e DAS em ambos os cabeçados.

## <a name="next-steps"></a>Passos seguintes

* [HDInsight 4.0 Anúncio](../hdinsight-version-release.md)
* [HDInsight 4.0 mergulho profundo](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Colmeia 3 Mesas ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
