---
title: Tutorial - Use Apache HBase em Azure HDInsight
description: Siga este tutorial Apache HBase para começar a usar hadoop em HDInsight. Criar tabelas a partir da shell de HBase e fazer consultas utilizando o Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/14/2020
ms.openlocfilehash: 7ce183595ed8e20c4b5cf4afe9ac1174882dc392
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370326"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Tutorial: Use Apache HBase em Azure HDInsight

Este tutorial demonstra como criar um cluster Apache HBase em Azure HDInsight, criar tabelas HBase e tabelas de consultas usando a Colmeia Apache.  Para obter informações gerais do HBase, consulte o artigo [Descrição geral do HBase do HDInsight](./apache-hbase-overview.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar cluster Apache HBase
> * Criar tabelas HBase e inserir dados
> * Use a Colmeia Apache para consultar o Apache HBase
> * Utilizar APIs REST de HBase utilizando Curl
> * Verificar o estado do cluster

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* O Bash. Os exemplos deste artigo utilizam a concha Bash no Windows 10 para os comandos curl. Consulte [o subsistema Windows para o Guia de Instalação do Linux para o Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter etapas de instalação.  Outras [conchas Unix](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos de caracóis, com algumas modificações ligeiras, podem funcionar numa solicitação do Windows Command.  Ou pode utilizar o cmdlet Do Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Criar cluster Apache HBase

O procedimento a seguir utiliza um modelo de Gestor de Recursos Azure para criar um cluster HBase. O modelo também cria a conta de Armazenamento Azure padrão dependente. Para compreender os parâmetros utilizados no procedimento e outros métodos de criação do cluster, consulte o artigo [Criar clusters do Hadoop baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Selecione a seguinte imagem para abrir o modelo no portal Azure. O modelo está localizado em [modelos de arranque rápido Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. A partir do diálogo **de implementação personalizado,** insira os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|Selecione a sua subscrição Azure que é usada para criar o cluster.|
    |Grupo de recursos|Crie um grupo de gestão de recursos Azure ou utilize um existente.|
    |Localização|Especificar a localização do grupo de recursos. |
    |ClusterName|Insira um nome para o cluster HBase.|
    |Nome e palavra-passe de início de sessão no cluster|O nome de início de sessão predefinido é **admin**.|
    |Nome de utilizador e palavra-passe de SSH|O nome de utilizador predefinido é **sshuser**.|

    Os outros parâmetros são opcionais.  

    Cada cluster tem uma dependência de conta do Armazenamento do Azure. Depois de apagar um cluster, os dados ficam na conta de armazenamento. O nome da conta do Storage predefinida do cluster é o nome do cluster com "store" anexado. É codificado na secção de variáveis do modelo.

3. **Selecione Concordo com os termos e condições acima indicados**e, em seguida, selecione **Comprar**. A criação de um cluster demora cerca de 20 minutos.

Depois de eliminar um cluster HBase, pode criar outro cluster HBase utilizando o mesmo contentor de blob predefinido. O novo cluster seleciona as tabelas do HBase criadas por si no cluster original. Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Pode utilizar o SSH para ligar aos clusters HBase e, em seguida, utilizar [a Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas HBase, inserir dados e dados de consulta.

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![Dados tabulares hdInsight Apache HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

Na HBase (uma implementação do [Cloud BigTable),](https://cloud.google.com/bigtable/)os mesmos dados parecem:

![HdInsight Apache HBase BigTable dados](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Para utilizar a shell de HBase**

1. Utilize `ssh` o comando para ligar ao seu cluster HBase. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilize `hbase shell` o comando para iniciar a concha interativa HBase. Introduza o seguinte comando na sua ligação SSH:

    ```bash
    hbase shell
    ```

1. Utilize `create` o comando para criar uma tabela HBase com famílias de duas colunas. Os nomes da tabela e das colunas são sensíveis a maiôs. Introduza o seguinte comando:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Utilize `list` o comando para listar todas as tabelas na Base H. Introduza o seguinte comando:

    ```hbase
    list
    ```

1. Utilize `put` o comando para inserir valores numa coluna especificada numa linha especificada numa tabela específica. Introduza os seguintes comandos:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Utilize `scan` o comando para digitalizar e devolver os dados da `Contacts` tabela. Introduza o seguinte comando:

    ```hbase
    scan 'Contacts'
    ```

    ![HdInsight Apache Hadoop HBase concha](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Use `get` o comando para obter o conteúdo de uma linha. Introduza o seguinte comando:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Vês resultados semelhantes como usar o `scan` comando porque só há uma linha.

    Para obter mais informações sobre o esquema de tabela HBase, consulte [Introdução ao Apache HBase Schema Design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Para obter mais comandos HBase, consulte o artigo [Guia de referência Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Utilize `exit` o comando para parar a concha interativa HBase. Introduza o seguinte comando:

    ```hbaseshell
    exit
    ```

**Para efetuar o carregamento em massa de dados para a tabela de contactos HBase**

O HBase inclui vários métodos de carregamento dos dados em tabelas.  Para obter mais informações, consulte o artigo [Carregamento em massa](https://hbase.apache.org/book.html#arch.bulk.load).

Está disponível um ficheiro de dados de exemplo num contentor de blobs público, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  O conteúdo do ficheiro de dados é:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

Opcionalmente, pode criar um ficheiro de texto e carregar o ficheiro para a sua própria conta de armazenamento. Para obter as instruções, consulte [os dados do Upload para os trabalhos da Apache Hadoop em HDInsight](../hdinsight-upload-data.md).

Este procedimento utiliza a `Contacts` tabela HBase que criou no último procedimento.

1. A partir da sua ligação ssh aberta, executar o seguinte comando para transformar o ficheiro de dados em StoreFiles e armazenar num caminho relativo especificado por `Dimporttsv.bulk.output` .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Executar o seguinte comando para fazer o upload dos dados `/example/data/storeDataFileOutput` para a tabela HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Pode abrir a concha HBase e utilizar o `scan` comando para listar o conteúdo da tabela.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Use a Colmeia Apache para consultar o Apache HBase

Pode consultar dados nas tabelas HBase utilizando [a Colmeia Apache.](https://hive.apache.org/) Nesta secção, irá criar uma tabela de Hive que mapeia para a tabela HBase e utiliza-a para consultar os dados na tabela HBase.

1. A partir da sua ligação ssh aberta, utilize o seguinte comando para iniciar a Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Para obter mais informações sobre o Beeline, veja [Utilizar o Hive com o Hadoop no HDInsight com o Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Execute o seguinte script [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) para criar uma tabela de Colmeia que mapeia para a tabela HBase. Certifique-se de que criou a tabela de amostras referenciada anteriormente neste artigo, utilizando a concha HBase antes de executar esta declaração.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Execute o seguinte script de HiveQL para consultar os dados na tabela HBase:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Para sair da Beeline, use `!exit` .

1. Para sair da sua ligação ssh, use `exit` .

### <a name="separate-hive-and-hbase-clusters"></a>Agrupamentos separados de colmeias e Hbase

A consulta da Hive para aceder aos dados da HBase não precisa de ser executada a partir do cluster HBase. Qualquer cluster que venha com a Colmeia (incluindo Spark, Hadoop, HBase ou Consulta Interativa) pode ser usado para consultar dados da HBase, desde que os seguintes passos estejam concluídos:

1. Ambos os clusters devem ser ligados à mesma Rede Virtual e Sub-rede
2. Cópia `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml` dos headnodes do cluster HBase para os headnodes do cluster hive

### <a name="secure-clusters"></a>Aglomerados Seguros

Os dados da HBase também podem ser consultados a partir da Hive utilizando hbase ativada por ESP: 

1. Ao seguir um padrão de multi-cluster, ambos os agrupamentos devem estar habilitados a E. 
2. Para permitir que a Hive questione os dados da HBase, certifique-se de que o `hive` utilizador tem permissões para aceder aos dados da HBase através do plugin Hbase Apache Ranger
3. Ao utilizar agrupamentos separados, ativados por ESP, o conteúdo dos `/etc/hosts` headnodes do cluster HBase deve ser anexado aos `/etc/hosts` headnodes do cluster Hive. 
> [!NOTE]
> Depois de escalar ambos os aglomerados, `/etc/hosts` deve ser anexado novamente

## <a name="use-hbase-rest-apis-using-curl"></a>Utilizar APIs REST de HBase utilizando Curl

A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos com HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Para ativar as APIs de REPOUSO HBase no cluster HDInsight, adicione o seguinte script de arranque personalizado à secção **De Ação do Script.** Pode adicionar o script de arranque quando criar o cluster ou depois de o cluster ter sido criado. Para **o tipo de nó,** selecione **Servidores de Região** para garantir que o script é executado apenas em Servidores da Região HBase.


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. Definir variável ambiente para facilitar a utilização. Edite os comandos abaixo substituindo pela `MYPASSWORD` palavra-passe de login do cluster. `MYCLUSTERNAME`Substitua-o pelo nome do seu cluster HBase. Então entre nos comandos.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Utilize o seguinte comando para listar as tabelas HBase existentes:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Utilize o seguinte comando para criar uma nova tabela HBase com duas famílias de coluna:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    O esquema é fornecido no formato JSon.
1. Utilize o seguinte comando para inserir alguns dados:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Base64 codificar os valores especificados no interruptor -d. No exemplo:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ=: Pessoal: Nome
   * Sm9obiBEb2xl: João Dinis

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) permite-lhe inserir múltiplos valores (em lote).

1. Utilize o seguinte comando para obter uma linha:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Para mais informações sobre o HBase Rest, veja [Guia de Referência do HBase Apache](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> O thrift não é suportado pelo HBase no HDInsight.
>
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor:
>
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> Deverá receber uma resposta semelhante à seguinte resposta:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>Verificar o estado do cluster

O HBase em HDInsight é fornecido com uma interface de utilizador da Web para monitorização de clusters. Utilizando a interface de utilizador da Web, pode pedir estatísticas ou informações sobre regiões.

**Para aceder à IU Principal do HBase**

1. Inscreva-se na UI web Ambari `https://CLUSTERNAME.azurehdinsight.net` onde está o nome do seu cluster `CLUSTERNAME` HBase.

1. Selecione **HBase** a partir do menu esquerdo.

1. Selecione **links rápidos** no topo da página, aponte para o link de nó do Zookeeper ativo e, em seguida, selecione **HBase Master UI**.  A IU é aberta noutro separador do browser:

   ![HDInsight Apache HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A IU Principal do HBase contém as seguintes secções:

   - servidores de região
   - principais cópias de segurança
   - Tabelas
   - tarefas
   - atributos de software

## <a name="clean-up-resources"></a>Limpar os recursos

Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster. Pode utilizar o comando HBase `disable 'Contacts'` . Se não continuar a utilizar esta aplicação, elimine o cluster HBase que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Na caixa **de pesquisa** no topo, escreva **HDInsight**.
1. Selecione **clusters HDInsight** em **Serviços**.
1. Na lista de clusters HDInsight que aparecem, clique no **...** ao lado do cluster que criou para este tutorial.
1. Clique em **Eliminar**. Clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um cluster Apache HBase. E como criar tabelas e ver os dados nessas tabelas a partir da concha HBase. Também aprendeu a usar uma consulta de Hive em dados em tabelas HBase. E como utilizar as APIs DE REPOUSO HBase C# para criar uma tabela HBase e obter dados da tabela. Para saber mais, veja:

> [!div class="nextstepaction"]
> [Visão geral do HDInsight HBase](./apache-hbase-overview.md)
