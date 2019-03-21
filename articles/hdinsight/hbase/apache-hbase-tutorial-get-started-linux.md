---
title: Introdução ao exemplo HBase no HDInsight - Azure
description: Siga este exemplo de Apache HBase para começar a utilizar o Hadoop no HDInsight. Criar tabelas a partir da shell de HBase e fazer consultas utilizando o Hive.
keywords: hbasecommand, exemplo hbase
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 7f162412a099078302bb348dab9ad3171f9e2913
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199499"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Introdução com um exemplo do Apache HBase no HDInsight

Saiba como criar uma [Apache HBase](https://hbase.apache.org/) cluster no HDInsight, criar tabelas de HBase e consultar tabelas utilizando [Apache Hive](https://hive.apache.org/).  Para obter informações gerais do HBase, consulte o artigo [Descrição geral do HBase do HDInsight][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a experimentar este exemplo do HBase, tem de ter os itens seguintes:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](https://curl.haxx.se/download.html).

## <a name="create-apache-hbase-cluster"></a>Criar cluster do Apache HBase
O procedimento seguinte utiliza um modelo do Azure Resource Manager para criar um cluster do HBase e a conta de Armazenamento do Azure dependente predefinida. Para compreender os parâmetros utilizados no procedimento e outros métodos de criação do cluster, consulte o artigo [Criar clusters do Hadoop baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como utilizar a geração 2 de armazenamento do Data Lake, veja [início rápido: Configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Clique na imagem seguinte para abrir o modelo no portal do Azure. O modelo está localizado em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/).
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. No painel **Implementação personalizada**, introduza os seguintes valores:
   
   * **Subscrição**: Selecione a sua subscrição do Azure que é utilizada para criar o cluster.
   * **Grupo de recursos**: Crie um grupo de gestão de recursos do Azure ou utilize um já existente.
   * **Localização**: Especifique a localização do grupo de recursos. 
   * **ClusterName**: Introduza um nome para o cluster HBase.
   * **Nome de início de sessão e palavra-passe do cluster**: O nome de início de sessão predefinido é **admin**.
   * **Nome de utilizador SSH e palavra-passe**: O nome de utilizador predefinido é **sshuser**.  Pode alterá-lo.
     
     Os outros parâmetros são opcionais.  
     
     Cada cluster tem uma dependência de conta do Armazenamento do Azure. Depois de eliminar um cluster, os dados são mantidos na conta do Storage. O nome da conta do Storage predefinida do cluster é o nome do cluster com "store" anexado. É codificado na secção de variáveis de modelo.
3. Selecione **Concordo com os termos e condições indicados acima** e, em seguida, clique em **Comprar**. A criação de um cluster demora cerca de 20 minutos.

> [!NOTE]  
> Depois de eliminar um cluster HBase, pode criar outro cluster HBase utilizando o mesmo contentor de blob predefinido. O novo cluster seleciona as tabelas do HBase criadas por si no cluster original. Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster.
> 
> 

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados
Pode utilizar o SSH para ligar a HBase clusters e, em seguida, utilizar [Shell do HBase Apache](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas de HBase, inserir dados e consultar dados. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![HDInsight HBase tabular data][img-hbase-sample-data-tabular]

No HBase (uma implementação do [Cloud BigTable](https://cloud.google.com/bigtable/)), os mesmos dados é semelhante a:

![HDInsight HBase BigTable data][img-hbase-sample-data-bigtable]


**Para utilizar a shell de HBase**

1. A partir do SSH, execute o seguinte comando do HBase:
   
    ```bash
    hbase shell
    ```

2. Crie um HBase com duas famílias de coluna:

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. Insira alguns dados:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![HDInsight Hadoop HBase shell][img-hbase-shell]
4. Obter uma única linha
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    Deverá ver os mesmos resultados como quando utiliza o comando de análise porque existe apenas uma linha.
   
    Para obter mais informações sobre o esquema de tabela do HBase, consulte [introdução ao Design do esquema HBase Apache][hbase-schema]. Para obter mais comandos HBase, consulte o artigo [Guia de referência Apache HBase][hbase-quick-start].
5. Sair da shell
   
    ```hbaseshell
    exit
    ```

**Para efetuar o carregamento em massa de dados para a tabela de contactos HBase**

O HBase inclui vários métodos de carregamento dos dados em tabelas.  Para obter mais informações, consulte o artigo [Carregamento em massa](https://hbase.apache.org/book.html#arch.bulk.load).

Um ficheiro de dados de exemplo pode ser encontrado no contentor de blob público, *wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt*.  O conteúdo do ficheiro de dados é:

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

Opcionalmente, pode criar um ficheiro de texto e carregar o ficheiro para a sua própria conta de armazenamento. Para obter instruções, consulte [carregar dados para as tarefas do Apache Hadoop no HDInsight][hdinsight-upload-data].

> [!NOTE]  
> Este procedimento utiliza a tabela de contactos HBase que criou no último procedimento.

1. No SSH, execute o seguinte comando para transformar o ficheiro de dados em StoreFiles e armazená-lo num caminho relativo especificado por Dimporttsv.bulk.output.  Se estiver na Shell de HBase, utilize o comando exit para sair.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Execute o seguinte comando para carregar os dados do /example/data/storeDataFileOutput para a tabela HBase:
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Pode abrir a shell do HBase e utilizar o comando de análise para listar o conteúdo da tabela.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Utilizar o Apache Hive para consultar o Apache HBase

Pode consultar dados nas tabelas de HBase utilizando [Apache Hive](https://hive.apache.org/). Nesta secção, irá criar uma tabela de Hive que mapeia para a tabela HBase e utiliza-a para consultar os dados na tabela HBase.

1. Abrir **PuTTY** e ligar ao cluster.  Consulte as instruções no procedimento anterior.
2. A partir da sessão SSH, utilize o comando seguinte para iniciar o Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Para obter mais informações sobre o Beeline, veja [Utilizar o Hive com o Hadoop no HDInsight com o Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
       
3. Execute o seguinte [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) script para criar uma tabela do Hive que mapeia para a tabela HBase. Certifique-se de que criou a tabela de exemplo referida anteriormente neste tutorial, utilizando a shell de HBase antes de executar esta instrução.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. Execute o seguinte script de HiveQL para consultar os dados na tabela HBase:

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>Utilizar APIs REST de HBase utilizando Curl

A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos com HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Utilize o seguinte comando para listar as tabelas HBase existentes:

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

1. Utilize o seguinte comando para criar uma nova tabela HBase com duas famílias de coluna:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    O esquema é fornecido no formato JSon.
1. Utilize o seguinte comando para inserir alguns dados:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
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
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
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

1. Inicie sessão na IU da Web Ambari em https://&lt;Clustername >. azurehdinsight.net.
2. Clique em **HBase** no menu à esquerda.
3. Clique em **Ligações rápidas** no topo da página, aponte para a ligação de nó ativa do Zookeeper e, em seguida, clique em **IU Principal do HBase**.  A IU é aberta noutro separador do browser:

   ![IU HDInsight HBase HMaster](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A IU Principal do HBase contém as seguintes secções:

   - servidores de região
   - principais cópias de segurança
   - tabelas
   - tarefas
   - atributos de software

## <a name="delete-the-cluster"></a>Eliminar o cluster
Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a criar um cluster do Apache HBase e como criar tabelas e ver os dados nessas tabelas a partir da shell de HBase. Também aprendeu como utilizar uma consulta Hive nos dados de tabelas de HBase e como utilizar as APIs REST C# para criar uma tabela de HBase e recuperar os dados da tabela.

Para saber mais, consulte:

* [Descrição geral do HBase do HDInsight][hdinsight-hbase-overview]: O Apache HBase é uma base de dados para os NoSQL de código-fonte aberto, Apache, que se baseada no Apache Hadoop que fornece acesso aleatório e consistência segura para grandes quantidades de dados não estruturados e semi-estruturados.

[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: https://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: https://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
