---
title: Tutorial – usar o Apache HBase no Azure HDInsight
description: Siga este tutorial do Apache HBase para começar a usar o Hadoop no HDInsight. Criar tabelas a partir da shell de HBase e fazer consultas utilizando o Hive.
keywords: hbasecommand, exemplo hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: df216c4d634ac20365cc5a1cc6e26fbd78be7ab9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917391"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Tutorial: Utilizar o Apache HBase no Azure HDInsight

Este tutorial demonstra como criar um cluster do Apache HBase no Azure HDInsight, criar tabelas do HBase e consultar tabelas usando Apache Hive.  Para obter informações gerais do HBase, consulte [visão geral do HBase do HDInsight](./apache-hbase-overview.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar cluster do Apache HBase
> * Criar tabelas do HBase e inserir dados
> * Usar Apache Hive para consultar o Apache HBase
> * Utilizar APIs REST de HBase utilizando Curl
> * Verificar o estado do cluster

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Raso. Os exemplos neste artigo usam o shell bash no Windows 10 para os comandos de rotação. Consulte o [Guia de instalação do subsistema do Windows para Linux para Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter as etapas de instalação.  Outros [shells do UNIX](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos de ondulação, com algumas pequenas modificações, podem funcionar em um prompt de comando do Windows.  Como alternativa, você pode usar o cmdlet do Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Criar cluster do Apache HBase

O procedimento a seguir usa um modelo de Azure Resource Manager para criar um cluster HBase e a conta de armazenamento do Azure padrão dependente. Para compreender os parâmetros utilizados no procedimento e outros métodos de criação do cluster, consulte o artigo [Criar clusters do Hadoop baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Selecione a imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>

2. No painel **Implementação personalizada**, introduza os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscription|Selecione sua assinatura do Azure que é usada para criar o cluster.|
    |Resource group|Crie um grupo de gerenciamento de recursos do Azure ou use um existente.|
    |Location|Especifique o local do grupo de recursos. |
    |clusterName|Insira um nome para o cluster HBase.|
    |Nome e senha de logon do cluster|O nome de início de sessão predefinido é **admin**.|
    |Nome de usuário e senha SSH|O nome de utilizador predefinido é **sshuser**.|

    Os outros parâmetros são opcionais.  

    Cada cluster tem uma dependência de conta do Armazenamento do Azure. Depois de eliminar um cluster, os dados são mantidos na conta do Storage. O nome da conta do Storage predefinida do cluster é o nome do cluster com "store" anexado. É codificado na secção de variáveis de modelo.

3. Selecione **concordo com os termos e condições declarados acima**e, em seguida, selecione **comprar**. A criação de um cluster demora cerca de 20 minutos.

Depois de eliminar um cluster HBase, pode criar outro cluster HBase utilizando o mesmo contentor de blob predefinido. O novo cluster seleciona as tabelas do HBase criadas por si no cluster original. Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Você pode usar o SSH para se conectar a clusters HBase e, em seguida, usar o [shell do Apache HBase](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas HBase, inserir dados e consultar dados.

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![HDInsight HBase tabular data](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

No HBase (uma implementação de [BigTable de nuvem](https://cloud.google.com/bigtable/)), os mesmos dados são semelhantes a:

![HDInsight HBase BigTable data](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Para utilizar a shell de HBase**

1. Use `ssh` o comando para se conectar ao cluster HBase. Edite o comando a seguir `CLUSTERNAME` substituindo pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Use `hbase shell` o comando para iniciar o shell interativo do HBase. Digite o seguinte comando em sua conexão SSH:

    ```bash
    hbase shell
    ```

1. Use `create` o comando para criar uma tabela do HBase com famílias de duas colunas. Os nomes de tabela e coluna diferenciam maiúsculas de minúsculas. Introduza o seguinte comando:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Use `list` o comando para listar todas as tabelas no HBase. Introduza o seguinte comando:

    ```hbase
    list
    ```

1. Use `put` o comando para inserir valores em uma coluna especificada em uma linha especificada em uma tabela específica. Insira os seguintes comandos:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Use `scan` o comando para verificar e retornar `Contacts` os dados da tabela. Introduza o seguinte comando:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Hadoop HBase shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Use `get` o comando para buscar o conteúdo de uma linha. Introduza o seguinte comando:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Você vê resultados semelhantes ao usar o `scan` comando porque há apenas uma linha.

    Para obter mais informações sobre o esquema de tabela do HBase, consulte [introdução ao design de esquema do Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Para obter mais comandos do HBase, consulte [Guia de referência do Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Use `exit` o comando para interromper o shell interativo do HBase. Introduza o seguinte comando:

    ```hbaseshell
    exit
    ```

**Para efetuar o carregamento em massa de dados para a tabela de contactos HBase**

O HBase inclui vários métodos de carregamento dos dados em tabelas.  Para obter mais informações, consulte o artigo [Carregamento em massa](https://hbase.apache.org/book.html#arch.bulk.load).

Um arquivo de dados de exemplo pode ser encontrado em um contêiner de `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`blob público,.  O conteúdo do ficheiro de dados é:

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

Opcionalmente, pode criar um ficheiro de texto e carregar o ficheiro para a sua própria conta de armazenamento. Para obter instruções, consulte [carregar dados para trabalhos de Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

Este procedimento usa a `Contacts` tabela HBase criada no último procedimento.

1. Na conexão SSH aberta, execute o seguinte comando para transformar o arquivo de dados em StoreFiles e armazene em um caminho relativo especificado `Dimporttsv.bulk.output`por.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Execute o seguinte comando para carregar os dados do `/example/data/storeDataFileOutput` na tabela do HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Você pode abrir o Shell do HBase e usar o `scan` comando para listar o conteúdo da tabela.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Usar Apache Hive para consultar o Apache HBase

Você pode consultar dados em tabelas do HBase usando [Apache Hive](https://hive.apache.org/). Nesta secção, irá criar uma tabela de Hive que mapeia para a tabela HBase e utiliza-a para consultar os dados na tabela HBase.

1. Na conexão SSH aberta, use o seguinte comando para iniciar o beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Para obter mais informações sobre o Beeline, veja [Utilizar o Hive com o Hadoop no HDInsight com o Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Execute o script [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) a seguir para criar uma tabela Hive que mapeia para a tabela do HBase. Certifique-se de que você criou a tabela de exemplo referenciada anteriormente neste artigo usando o Shell do HBase antes de executar essa instrução.

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

1. Para sair do beeline, `!exit`use.

1. Para sair da conexão SSH, use `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Utilizar APIs REST de HBase utilizando Curl

A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos com HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Inicie a variável de ambiente para facilitar o uso. Edite os comandos a seguir `MYPASSWORD` substituindo pela senha de logon do cluster. Substitua `MYCLUSTERNAME` pelo nome do cluster HBase. Em seguida, insira os comandos.

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
   * Sm9obiBEb2xl: John giros

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

1. Entre na interface do usuário da Web `https://CLUSTERNAME.azurehdinsight.net` do `CLUSTERNAME` amAmbari em, em que é o nome do cluster do HBase.

1. Selecione **HBase** no menu à esquerda.

1. Selecione **links rápidos** na parte superior da página, aponte para o link do nó active Zookeeper e, em seguida, selecione **HBase Master interface do usuário**.  A IU é aberta noutro separador do browser:

   ![IU HDInsight HBase HMaster](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A IU Principal do HBase contém as seguintes secções:

   - servidores de região
   - principais cópias de segurança
   - tabelas
   - tarefas
   - atributos de software

## <a name="clean-up-resources"></a>Limpar recursos

Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster. Você pode usar o comando `disable 'Contacts'`do HBase. Se você não for continuar a usar este aplicativo, exclua o cluster HBase criado com as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Na caixa de **pesquisa** na parte superior, digite **HDInsight**.
1. Selecione **clusters HDInsight** em **Serviços**.
1. Na lista de clusters HDInsight que aparece, clique em **...** ao lado do cluster que você criou para este tutorial.
1. Clique em **Eliminar**. Clique em **Sim**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu como criar um cluster do Apache HBase e como criar tabelas e exibir os dados nessas tabelas do shell do HBase. Também aprendeu como utilizar uma consulta Hive nos dados de tabelas de HBase e como utilizar as APIs REST C# para criar uma tabela de HBase e recuperar os dados da tabela. Para saber mais, consulte:

> [!div class="nextstepaction"]
> [Visão geral do HBase no HDInsight](./apache-hbase-overview.md)