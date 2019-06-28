---
title: Tutorial – utilizar o Apache HBase no HDInsight do Azure
description: Siga este tutorial do Apache HBase para começar a utilizar o hadoop no HDInsight. Criar tabelas a partir da shell de HBase e fazer consultas utilizando o Hive.
keywords: hbasecommand, exemplo hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: 48b02a042b55af9ff65f57220f7a64c9cbde8848
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445550"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Tutorial: Utilizar o Apache HBase no HDInsight do Azure

Este tutorial demonstra como criar um cluster do Apache HBase no HDInsight do Azure, criar tabelas de HBase e consultar tabelas utilizando o Apache Hive.  Para obter informações gerais do HBase, consulte [descrição geral do HBase do HDInsight](./apache-hbase-overview.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar cluster do Apache HBase
> * Criar tabelas de HBase e inserir dados
> * Utilizar o Apache Hive para consultar o Apache HBase
> * Utilizar APIs REST de HBase utilizando Curl
> * Verificar o estado do cluster

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Os exemplos neste artigo utilizam o shell de Bash no Windows 10 para os comandos de curl. Ver [subsistema Windows para o guia de instalação de Linux para Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter os passos de instalação.  Outros [shells do Unix](https://www.gnu.org/software/bash/) funcionará bem.  Os exemplos de curl, com algumas pequenas modificações, podem trabalhar num prompt de comando do Windows.  Em alternativa, pode utilizar o cmdlet do Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Criar cluster do Apache HBase

O procedimento seguinte utiliza um modelo Azure Resource Manager para criar um cluster do HBase e o padrão de dependente de conta de armazenamento do Azure. Para compreender os parâmetros utilizados no procedimento e outros métodos de criação do cluster, consulte o artigo [Criar clusters do Hadoop baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Selecione a imagem seguinte para abrir o modelo no portal do Azure. O modelo está localizado em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. No painel **Implementação personalizada**, introduza os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|Selecione a sua subscrição do Azure que é utilizada para criar o cluster.|
    |Grupo de recursos|Crie um grupo de gestão de recursos do Azure ou utilize um já existente.|
    |Location|Especifique a localização do grupo de recursos. |
    |ClusterName|Introduza um nome para o cluster HBase.|
    |Nome de início de sessão do cluster e a palavra-passe|O nome de início de sessão predefinido é **admin**.|
    |Nome de utilizador SSH e palavra-passe|O nome de utilizador predefinido é **sshuser**.|

    Os outros parâmetros são opcionais.  

    Cada cluster tem uma dependência de conta do Armazenamento do Azure. Depois de eliminar um cluster, os dados são mantidos na conta do Storage. O nome da conta do Storage predefinida do cluster é o nome do cluster com "store" anexado. É codificado na secção de variáveis de modelo.

3. Selecione **concordo com os termos e condições indicados acima**e, em seguida, selecione **Compra**. A criação de um cluster demora cerca de 20 minutos.

Depois de eliminar um cluster HBase, pode criar outro cluster HBase utilizando o mesmo contentor de blob predefinido. O novo cluster seleciona as tabelas do HBase criadas por si no cluster original. Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Pode utilizar o SSH para ligar a HBase clusters e, em seguida, utilizar [Shell do HBase Apache](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas de HBase, inserir dados e consultar dados.

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![HDInsight HBase tabular data](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

No HBase (uma implementação do [Cloud BigTable](https://cloud.google.com/bigtable/)), os mesmos dados é semelhante a:

![HDInsight HBase BigTable data](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Para utilizar a shell de HBase**

1. Utilize `ssh` comando para ligar ao cluster do HBase. Editar o comando abaixo, substituindo `CLUSTERNAME` com o nome do cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilize `hbase shell` comando para iniciar o shell interativo do HBase. Introduza o seguinte comando na sua ligação de SSH:

    ```bash
    hbase shell
    ```

1. Utilize `create` comando para criar uma tabela de HBase com duas famílias de coluna. Os nomes de tabela e coluna diferenciam maiúsculas de minúsculas. Introduza o seguinte comando:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Utilize `list` command para listar todas as tabelas no HBase. Introduza o seguinte comando:

    ```hbase
    list
    ```

1. Utilize `put` comando para inserir valores numa coluna especificada numa linha especificada numa tabela específica. Introduza os seguintes comandos:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Uso `scan` comando para analisar e retornar o `Contacts` dados da tabela. Introduza o seguinte comando:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Hadoop HBase shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Utilize `get` comando para obter o conteúdo de uma linha. Introduza o seguinte comando:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Ver resultados semelhantes como utilizar o `scan` porque existe apenas uma linha de comando.

    Para obter mais informações sobre o esquema de tabela do HBase, consulte [introdução ao Design do esquema HBase Apache](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Para obter mais comandos HBase, consulte [guia de referência Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Utilize `exit` comando para parar o shell interativo do HBase. Introduza o seguinte comando:

    ```hbaseshell
    exit
    ```

**Para efetuar o carregamento em massa de dados para a tabela de contactos HBase**

O HBase inclui vários métodos de carregamento dos dados em tabelas.  Para obter mais informações, consulte o artigo [Carregamento em massa](https://hbase.apache.org/book.html#arch.bulk.load).

Um ficheiro de dados de exemplo pode ser encontrado no contentor de blob público, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  O conteúdo do ficheiro de dados é:

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

Opcionalmente, pode criar um ficheiro de texto e carregar o ficheiro para a sua própria conta de armazenamento. Para obter instruções, consulte [carregar dados para as tarefas do Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

Este procedimento utiliza a `Contacts` tabelas de HBase que criou no último procedimento.

1. Pelos seus aberto ssh ligação, execute o seguinte comando para transformar os dados de ficheiros para StoreFiles e o armazenamos num caminho relativo especificado por `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Execute o seguinte comando para carregar os dados do `/example/data/storeDataFileOutput` para a tabela HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Pode abrir a shell de HBase e utilizar o `scan` comando para listar o conteúdo da tabela.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Utilizar o Apache Hive para consultar o Apache HBase

Pode consultar dados nas tabelas de HBase utilizando [Apache Hive](https://hive.apache.org/). Nesta secção, irá criar uma tabela de Hive que mapeia para a tabela HBase e utiliza-a para consultar os dados na tabela HBase.

1. Do seu abrir a ligação ssh, utilize o seguinte comando para iniciar o Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Para obter mais informações sobre o Beeline, veja [Utilizar o Hive com o Hadoop no HDInsight com o Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Execute o seguinte [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) script para criar uma tabela do Hive que mapeia para a tabela HBase. Certifique-se de que criou a tabela de exemplo referida anteriormente neste artigo, utilizando a shell de HBase antes de executar esta instrução.

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

1. Para sair do Beeline, utilize `!exit`.

1. Para sair do seus ssh ligação, utilize `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Utilizar APIs REST de HBase utilizando Curl

A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos com HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Inicie a variável de ambiente para facilidade de utilização. Editar os comandos abaixo, substituindo `MYPASSWORD` com a palavra-passe de início de sessão do cluster. Substitua `MYCLUSTERNAME` com o nome do cluster do HBase. Em seguida, introduza os comandos.

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

    Terá de codificar em base64 os valores especificados no comutador -d. No exemplo:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Pessoal: nome
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

1. Inicie sessão na IU da Web do Ambari em `https://CLUSTERNAME.azurehdinsight.net` onde `CLUSTERNAME` é o nome do cluster do HBase.

1. Selecione **HBase** no menu à esquerda.

1. Selecione **ligações rápidas** na parte superior da página, aponte para a ligação de nó ativa do Zookeeper e, em seguida, selecione **IU principal do HBase**.  A IU é aberta noutro separador do browser:

   ![IU HDInsight HBase HMaster](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A IU Principal do HBase contém as seguintes secções:

   - servidores de região
   - principais cópias de segurança
   - tabelas
   - tarefas
   - atributos de software

## <a name="clean-up-resources"></a>Limpar recursos

Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster. Pode usar o comando de HBase `disable 'Contacts'`. Se não pretender continuar a utilizar esta aplicação, elimine o cluster do HBase que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Na **pesquisa** caixa na parte superior, tipo **HDInsight**.
1. Selecione **clusters do HDInsight** sob **serviços**.
1. Na lista de clusters do HDInsight que aparece, clique o **...**  junto do cluster que criou para este tutorial.
1. Clique em **Eliminar**. Clique em **Sim**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a criar um cluster do Apache HBase e como criar tabelas e ver os dados nessas tabelas a partir da shell de HBase. Também aprendeu como utilizar uma consulta Hive nos dados de tabelas de HBase e como utilizar as APIs REST C# para criar uma tabela de HBase e recuperar os dados da tabela. Para saber mais, consulte:

> [!div class="nextstepaction"]
> [Descrição geral do HBase do HDInsight](./apache-hbase-overview.md)