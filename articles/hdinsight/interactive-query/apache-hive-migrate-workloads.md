---
title: Migrar azure HDInsight 3.6 Cargas de trabalho da Colmeia para HDInsight 4.0
description: Saiba como migrar as cargas de trabalho da Apache Hive em HDInsight 3.6 para HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 14849dd1f68f281009808d1bd1dc1cae62927ab4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594241"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar azure HDInsight 3.6 Cargas de trabalho da Colmeia para HDInsight 4.0

Este documento mostra como migrar as cargas de trabalho da Apache Hive e LLAP em HDInsight 3.6 para HDInsight 4.0. O HDInsight 4.0 fornece funcionalidades mais recentes da Hive e LLAP, tais como vistas materializadas e cache de resultados de consulta. Ao migrar as suas cargas de trabalho para o HDInsight 4.0, pode utilizar muitas funcionalidades mais recentes da Hive 3 que não estão disponíveis no HDInsight 3.6.

Este artigo abrange os seguintes temas:

* Migração de metadados hive para HDInsight 4.0
* Migração segura de tabelas ACID e não ACID
* Preservação das políticas de segurança da Hive através das versões HDInsight
* Execução de consulta e depuração de HDInsight 3.6 para HDInsight 4.0

Uma vantagem da Hive é a capacidade de exportar metadados para uma base de dados externa (referida como a Metaloja da Colmeia). A **Hive Metastore** é responsável pelo armazenamento de estatísticas de tabelas, incluindo a localização do armazenamento de tabelas, nomes de colunas e informações sobre índices de tabela. HDInsight 3.6 e HDInsight 4.0 requerem diferentes esquemas de metaloja e não podem partilhar uma única metaloja. A forma recomendada de atualizar a metaloja da Hive com segurança é atualizar uma cópia em vez da original no ambiente de produção atual. Este documento exige que os clusters originais e novos tenham acesso à mesma Conta de Armazenamento. Por conseguinte, não abrange a migração de dados para outra região.

## <a name="migrate-from-external-metastore"></a>Migrar da metaloja externa

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. Executar uma grande compactação em tabelas ACID em HDInsight 3.6

As tabelas HDInsight 3.6 e HDInsight 4.0 ACID compreendem os deltas ácidos de forma diferente. A única ação necessária antes da migração é executar a compactação "MAJOR" contra cada tabela ACID no cluster 3.6. Consulte o Manual da [Linguagem da Colmeia](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) para obter mais detalhes sobre a compactação.

### <a name="2-copy-sql-database"></a>2. Copiar base de dados SQL
Crie uma nova cópia da sua metaloja externa. Se estiver a utilizar uma metaloja externa, uma das formas seguras e fáceis de fazer uma cópia da metaloja é [restaurar a Base](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) de Dados com um nome diferente utilizando a função de restauro da Base de Dados SQL.  Consulte [Utilize lojas de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre a anexação de uma metaloja externa a um cluster HDInsight.

### <a name="3-upgrade-metastore-schema"></a>3. Atualizar o esquema da metaloja
Uma vez que a **cópia** da metaloja esteja completa, execute um script de upgrade de esquemas no [Script Action](../hdinsight-hadoop-customize-cluster-linux.md) no cluster HDInsight 3.6 existente para atualizar a nova metaloja para o esquema hive 3. (Este passo não requer que a nova metaloja seja ligada a um cluster.) Isto permite que a base de dados seja anexada como metastore HDInsight 4.0.

Utilize os valores na tabela mais abaixo. Substitua-a `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` pelos valores adequados para a **cópia**da metaloja da Hive, separada por espaços. Não inclua ".database.windows.net" ao especificar o nome do servidor SQL.

|Propriedade | Valor |
|---|---|
|Tipo de script|- Personalizado|
|Name|Atualização da colmeia|
|Roteiro de bash URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo de nó(s)|Head|
|Parâmetros|SQLSERVERNAMENAME DATABASENAME USERNAME PASSWORD|

> [!Warning]  
> A atualização que converte o esquema de metadados HDInsight 3.6 para o esquema HDInsight 4.0 não pode ser revertida.

Pode verificar a atualização executando a seguinte consulta sql na base de dados:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. Implementar um novo cluster HDInsight 4.0

1. Especifique a metaloja atualizada como a metaloja hive do novo cluster.

1. Os dados reais das tabelas, no entanto, não são acessíveis até que o cluster tenha acesso às contas de armazenamento necessárias.
Certifique-se de que as Contas de Armazenamento das tabelas da Hive no cluster HDInsight 3.6 são especificadas como as Contas de Armazenamento primárias ou secundárias do novo cluster HDInsight 4.0.
Para obter mais informações sobre a adição de contas de armazenamento aos clusters HDInsight, consulte [Adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. Migração completa com uma ferramenta pós-actualização no HDInsight 4.0

As tabelas geridas devem ser compatíveis com o ÁCIDO no HDInsight 4.0, por defeito. Depois de concluída a migração da metaloja, execute uma ferramenta pós-upgrade para tornar as tabelas geridas anteriormente não acidcompatíveis com o cluster HDInsight 4.0. Esta ferramenta aplicará a seguinte conversão:

|3.6 |4,0 |
|---|---|
|Tabelas externas|Tabelas externas|
|Tabelas geridas não acidificáveis|Tabelas externas com propriedade 'external.table.purpur'='true'|
|Tabelas geridas acid|Tabelas geridas acid|

Execute a ferramenta de pós-actualização da Hive a partir do cluster HDInsight 4.0 utilizando a concha SSH:

1. Ligue-se ao seu headnode de cluster utilizando SSH. Para obter instruções, consulte [Connect to HDInsight utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Abra uma concha de login como o utilizador da Colmeia executando`sudo su - hive`
1. Execute o seguinte comando da concha.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Após o completo da ferramenta, o seu armazém da Hive estará pronto para o HDInsight 4.0.

## <a name="migrate-from-internal-metastore"></a>Migrar da metaloja interna

Se o seu cluster HDInsight 3.6 utilizar uma metaloja interna da Hive, siga os passos abaixo para executar um script, que gera consultas da Hive para exportar definições de objetos a partir da metaloja.

Os clusters HDInsight 3.6 e 4.0 devem utilizar a mesma Conta de Armazenamento.

> [!NOTE]
>
> * No caso das tabelas ACID, será criada uma nova cópia dos dados por baixo da tabela.
>
> * Este guião suporta a migração de bases de dados, mesas e divisórias da Hive, apenas. Espera-se que outros objetos de metadados, como Vistas, UDFs e Restrições de Mesa, sejam copiados manualmente.
>
> * Uma vez que este script esteja completo, presume-se que o aglomerado antigo deixará de ser utilizado para aceder a nenhuma das tabelas ou bases de dados referidas no script.
>
> * Todas as tabelas geridas tornar-se-ão transacionais em HDInsight 4.0. Opcionalmente, mantenha o quadro não transacional exportando os dados para uma tabela externa com a propriedade 'external.table.pur'='true'. Por exemplo,
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

1. A partir da sessão ssh aberta, descarregue o seguinte ficheiro script para gerar um ficheiro chamado **alltables.hql**.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Para um cluster HDInsight regular, sem ESP, basta executar `exporthive_hdi_3_6.sh`.

    * Para um cluster com ESP, kinit e modificar os argumentos a beeline: executar o seguinte, definindo USER e DOMAIN para o utilizador azure AD com permissões completas da Hive.

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

1. Faça o upload **alltables.hql** para o *novo* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Em seguida, utilize o SSH para se ligar ao *novo* cluster HDInsight 4.0. Executar o seguinte código de uma sessão SSH para este cluster:

    Sem ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    Com esp:

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

A ferramenta pós-actualização para migração de metalojaexterna não se aplica aqui, uma vez que as tabelas geridas não acidadas do HDInsight 3.6 convertem-se em tabelas geridas pela ACID em HDInsight 4.0.

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
|Name|DAS|
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
