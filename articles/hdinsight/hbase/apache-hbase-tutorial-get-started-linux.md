---
title: Tutorial - Use Apache HBase em Azure HDInsight
description: Siga este tutorial Apache HBase para começar a usar hadoop no HDInsight. Criar tabelas a partir da shell de HBase e fazer consultas utilizando o Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/14/2020
ms.openlocfilehash: a601d54ebda074a25a988ac2a115f6418dd5c7ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390269"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Tutorial: Use Apache HBase em Azure HDInsight

Este tutorial demonstra como criar um cluster Apache HBase em Azure HDInsight, criar tabelas HBase e mesas de consulta usando a Hive Apache.  Para obter informações gerais do HBase, consulte o artigo [Descrição geral do HBase do HDInsight](./apache-hbase-overview.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar o cluster Apache HBase
> * Criar tabelas HBase e inserir dados
> * Use a Colmeia Apache para consultar Apache HBase
> * Utilizar APIs REST de HBase utilizando Curl
> * Verificar o estado do cluster

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Uma festa. Os exemplos deste artigo usam a concha bash no Windows 10 para os comandos de caracóis. Consulte o Subsistema Windows para obter o Guia de [Instalação do Linux para o Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter as etapas de instalação.  Outras [conchas unix](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos de caracóis, com algumas ligeiras modificações, podem funcionar num pedido de Comando do Windows.  Ou pode utilizar o cmdlet [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod)do Windows PowerShell .

## <a name="create-apache-hbase-cluster"></a>Criar o cluster Apache HBase

O procedimento seguinte utiliza um modelo de Gestor de Recursos Azure para criar um cluster HBase. O modelo também cria a conta de armazenamento de azure padrão dependente. Para compreender os parâmetros utilizados no procedimento e outros métodos de criação do cluster, consulte o artigo [Criar clusters do Hadoop baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Selecione a seguinte imagem para abrir o modelo no portal Azure. O modelo está localizado em [modelos de quickstart Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. A partir do diálogo de **implementação Personalizado,** introduza os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|Selecione a sua subscrição Azure que é usada para criar o cluster.|
    |Grupo de recursos|Crie um grupo de gestão de Recursos Azure ou utilize um existente.|
    |Localização|Especifique a localização do grupo de recursos. |
    |ClusterName|Introduza um nome para o cluster HBase.|
    |Nome e palavra-passe de início de sessão no cluster|O nome de início de sessão predefinido é **admin**.|
    |Nome de utilizador e palavra-passe de SSH|O nome de utilizador predefinido é **sshuser**.|

    Os outros parâmetros são opcionais.  

    Cada cluster tem uma dependência de conta do Armazenamento do Azure. Depois de eliminar um cluster, os dados permanecem na conta de armazenamento. O nome da conta do Storage predefinida do cluster é o nome do cluster com "store" anexado. É codificado na secção de variáveis do modelo.

3. Selecione **Concordo com os termos e condições acima indicados,** e depois selecione **Comprar**. A criação de um cluster demora cerca de 20 minutos.

Depois de eliminar um cluster HBase, pode criar outro cluster HBase utilizando o mesmo contentor de blob predefinido. O novo cluster seleciona as tabelas do HBase criadas por si no cluster original. Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Pode utilizar o SSH para se ligar aos clusters HBase e, em seguida, utilizar [a Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas HBase, inserir dados e consultar dados.

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![Dados tabulares HDInsight Apache HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

Em HBase (uma implementação do [Cloud BigTable),](https://cloud.google.com/bigtable/)os mesmos dados parecem:

![HDInsight Apache HBase BigTable dados](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Para utilizar a shell de HBase**

1. Utilize `ssh` o comando para se ligar ao seu cluster HBase. Edite o comando `CLUSTERNAME` abaixo substituindo pelo nome do seu cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilize `hbase shell` o comando para iniciar a concha interativa HBase. Introduza o seguinte comando na sua ligação SSH:

    ```bash
    hbase shell
    ```

1. Use `create` o comando para criar uma tabela HBase com famílias de duas colunas. Os nomes da mesa e da coluna são sensíveis aos casos. Introduza o seguinte comando:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Utilize `list` o comando para listar todas as tabelas em HBase. Introduza o seguinte comando:

    ```hbase
    list
    ```

1. Utilize `put` o comando para inserir valores numa coluna especificada numa linha especificada numa determinada tabela. Insira os seguintes comandos:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Utilize `scan` o comando para `Contacts` digitalizar e devolver os dados da tabela. Introduza o seguinte comando:

    ```hbase
    scan 'Contacts'
    ```

    ![Concha HDInsight Apache Hadoop HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Use `get` o comando para recolher o conteúdo de uma linha. Introduza o seguinte comando:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Vê-se resultados `scan` semelhantes como usar o comando porque só há uma linha.

    Para obter mais informações sobre o esquema de tabela HBase, consulte [Introdução ao Design De Schema Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Para obter mais comandos HBase, consulte o artigo [Guia de referência Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Utilize `exit` o comando para parar a concha interativa HBase. Introduza o seguinte comando:

    ```hbaseshell
    exit
    ```

**Para efetuar o carregamento em massa de dados para a tabela de contactos HBase**

O HBase inclui vários métodos de carregamento dos dados em tabelas.  Para obter mais informações, consulte o artigo [Carregamento em massa](https://hbase.apache.org/book.html#arch.bulk.load).

Está disponível um ficheiro de dados de exemplo num contentor de blobs público, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  O conteúdo do ficheiro de dados é:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Opcionalmente, pode criar um ficheiro de texto e carregar o ficheiro para a sua própria conta de armazenamento. Para obter as instruções, consulte os dados de [upload para os trabalhos de Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

Este procedimento `Contacts` utiliza a tabela HBase que criou no último procedimento.

1. A partir da sua ligação aberta ssh, execute o seguinte comando para `Dimporttsv.bulk.output`transformar o ficheiro de dados em StoreFiles e armazenar num caminho relativo especificado por .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Executar o seguinte comando para `/example/data/storeDataFileOutput` fazer o upload dos dados da tabela HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Pode abrir a concha HBase `scan` e usar o comando para listar o conteúdo da tabela.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Use a Colmeia Apache para consultar Apache HBase

Pode consultar dados nas tabelas HBase utilizando [a Hive Apache](https://hive.apache.org/). Nesta secção, irá criar uma tabela de Hive que mapeia para a tabela HBase e utiliza-a para consultar os dados na tabela HBase.

1. A partir da sua ligação aberta ssh, utilize o seguinte comando para iniciar a Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Para obter mais informações sobre o Beeline, veja [Utilizar o Hive com o Hadoop no HDInsight com o Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Execute o seguinte script [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) para criar uma tabela Hive que mapeie para a tabela HBase. Certifique-se de que criou a tabela de amostras referenciada anteriormente neste artigo utilizando a concha HBase antes de executar esta declaração.

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

1. Para sair de Beeline, use `!exit`.

1. Para sair da sua ligação ssh, use `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Utilizar APIs REST de HBase utilizando Curl

A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos com HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Definir variável ambiente para facilitar a utilização. Editar os comandos abaixo `MYPASSWORD` substituindo pela senha de login do cluster. Substitua-o `MYCLUSTERNAME` pelo nome do seu cluster HBase. Em seguida, insira os comandos.

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

    A Base64 codifica os valores especificados no interruptor-d. No exemplo:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Pessoal: Nome
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
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Deverá receber uma resposta semelhante à seguinte resposta:
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>Verificar o estado do cluster

O HBase em HDInsight é fornecido com uma interface de utilizador da Web para monitorização de clusters. Utilizando a interface de utilizador da Web, pode pedir estatísticas ou informações sobre regiões.

**Para aceder à IU Principal do HBase**

1. Assine na Ambari Web `https://CLUSTERNAME.azurehdinsight.net` UI onde `CLUSTERNAME` está o nome do seu cluster HBase.

1. Selecione **HBase** a partir do menu esquerdo.

1. Selecione **links rápidos** na parte superior da página, aponte para a ligação de nó do Zookeeper ativa e, em seguida, selecione **HBase Master UI**.  A IU é aberta noutro separador do browser:

   ![HDInsight Apache HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A IU Principal do HBase contém as seguintes secções:

   - servidores de região
   - principais cópias de segurança
   - tabelas
   - tarefas
   - atributos de software

## <a name="clean-up-resources"></a>Limpar recursos

Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster. Pode utilizar o comando `disable 'Contacts'`HBase . Se não vai continuar a utilizar esta aplicação, elimine o cluster HBase que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Na caixa **de pesquisa** na parte superior, digite **HDInsight**.
1. Selecione **clusters HDInsight** em **Serviços**.
1. Na lista de clusters HDInsight que aparece, clique no **...** ao lado do cluster que criou para este tutorial.
1. Clique em **Apagar**. Clique **sim**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um cluster Apache HBase. E como criar tabelas e ver os dados nessas tabelas a partir da concha HBase. Também aprendeu a usar uma consulta da Hive sobre dados em tabelas HBase. E como usar as APIs de REPOUSO HBase C# REST para criar uma tabela HBase e recuperar dados da tabela. Para saber mais, consulte:

> [!div class="nextstepaction"]
> [Visão geral hdInsight HBase](./apache-hbase-overview.md)