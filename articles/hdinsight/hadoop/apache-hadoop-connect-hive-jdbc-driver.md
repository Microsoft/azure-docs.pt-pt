---
title: Consulta Apache Hive através do motorista JDBC - Azure HDInsight
description: Utilize o controlador JDBC a partir de uma aplicação Java para submeter consultas de Hive Apache a Hadoop em HDInsight. Ligue programáticamente e a partir do cliente SQuirrel SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 183bc416dde941f11bd94cfcff3bf738b35f876f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207387"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Query Apache Hive through the JDBC driver in HDInsight (Consultar o Apache Hive através do controlador JDBC no HDInsight)

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Aprenda a usar o controlador JDBC a partir de uma aplicação Java. Para submeter consultas apache colmeia a Apache Hadoop em Azure HDInsight. A informação neste documento demonstra como conectar programáticamente, e do cliente SQuirreL SQL.

Para obter mais informações sobre a Interface Hive JDBC, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de Hadoop HDInsight. Para criar um, consulte [Começar com Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Certifique-se de que o serviço HiveServer2 está a funcionar.
* O [Kit de Desenvolvimento java (JDK) versão 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) ou superior.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL é uma aplicação de cliente JDBC.

## <a name="jdbc-connection-string"></a>Cadeia de ligação JDBC

As ligações JDBC a um cluster HDInsight em Azure são feitas sobre a porta 443. O tráfego é assegurado utilizando TLS/SSL. A porta de entrada pública que os aglomerados estão atrás redireciona o tráfego para o porto que a HiveServer2 está realmente a ouvir. A seguinte cadeia de ligação mostra o formato a utilizar para HDInsight:

```http
    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2
```

Substitua `CLUSTERNAME` pelo nome do seu cluster do HDInsight.

Ou você pode obter a ligação através **de Ambari UI > Hive > Configs > Advanced**.

![Obtenha a cadeia de conexão JDBC através de Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Nome do anfitrião na cadeia de conexão

O nome do anfitrião 'CLUSTERNAME.azurehdinsight.net' na cadeia de ligação é o mesmo que o URL do seu cluster. Pode passar pelo portal Azure.

### <a name="port-in-connection-string"></a>Porta na cadeia de ligação

Só pode utilizar a **porta 443** para ligar ao cluster a partir de alguns locais fora da rede virtual Azure. HDInsight é um serviço gerido, o que significa que todas as ligações ao cluster são geridas através de um Gateway seguro. Não é possível ligar-se ao HiveServer 2 diretamente nas portas 10001 ou 10000. Estes portos não estão expostos ao exterior.

## <a name="authentication"></a>Autenticação

Ao estabelecer a ligação, utilize o nome de administração do cluster HDInsight e a palavra-passe para autenticar. A partir de clientes JDBC como SQuirreL SQL, insira o nome de administração e a palavra-passe nas definições do cliente.

A partir de uma aplicação Java, você deve usar o nome e senha ao estabelecer uma ligação. Por exemplo, o seguinte código Java abre uma nova ligação:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conecte-se com o cliente SQuirreL SQL

O SQuirreL SQL é um cliente JDBC que pode ser usado para executar remotamente consultas de Hive com o seu cluster HDInsight. Os seguintes passos pressupõem que já instalou o SQuirreL SQL.

1. Crie um diretório para conter certos ficheiros a serem copiados do seu cluster.

2. No seguinte script, `sshuser` substitua-o pelo nome da conta de utilizador SSH para o cluster.  `CLUSTERNAME`Substitua-o pelo nome do cluster HDInsight.  A partir de uma linha de comando, altere o seu diretório de trabalho para o criado no passo anterior e, em seguida, insira o seguinte comando para copiar ficheiros de um cluster HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Inicie a aplicação SQuirreL SQL. A partir da esquerda da janela, **selecione Drivers**.

    ![Guia de motoristas à esquerda da janela](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. A partir dos ícones no topo do diálogo **Drivers,** selecione o **+** ícone para criar um controlador.

    ![Ícone de controladores de aplicação SQuirreL SQL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. No diálogo Add Driver, adicione as seguintes informações:

    |Propriedade | Valor |
    |---|---|
    |Nome|Hive|
    |EXEMPLO URL|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Caminho extra classe|Utilize o botão **Adicionar** para adicionar todos os ficheiros de frascos descarregados anteriormente.|
    |Nome da classe|org.apache.hive.jdbc.HiveDriver|

   ![adicionar diálogo do condutor com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Selecione **OK** para guardar estas definições.

6. À esquerda da janela SQuirreL SQL, selecione **Aliases**. Em seguida, selecione o **+** ícone para criar um pseudónimo de conexão.

    !['SQuirreL SQL adiciona novo diálogo de pseudónimos'](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Utilize os seguintes valores para o diálogo **Add Alias:**

    |Propriedade |Valor |
    |---|---|
    |Nome|Colmeia em HDInsight|
    |Controlador|Utilize o drop-down para selecionar o condutor **da Colmeia.**|
    |URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Substitua **CLUSTERNAME** pelo nome do cluster do HDInsight.|
    |Nome de Utilizador|O nome da conta de login do cluster para o seu cluster HDInsight. O padrão é **administrador.**|
    |Palavra-passe|A palavra-passe para a conta de login do cluster.|

    ![adicionar diálogo de pseudónimos com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Utilize o botão **Teste** para verificar se a ligação funciona. Quando **ligar a: Hive no diálogo HDInsight** aparece, selecione **Connect** para realizar o teste. Se o teste for bem sucedido, verá um diálogo **de sucesso da Ligação.** Se ocorrer um erro, consulte [a resolução de problemas](#troubleshooting).

    Para guardar o pseudónimo de ligação, utilize o botão **Ok** na parte inferior do diálogo **Add Alias.**

8. Do **Connect ao** dropdown no topo do SQuirreL SQL, selecione **Hive em HDInsight**. Quando solicitado, selecione **Connect**.

    ![diálogo de conexão com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Uma vez ligado, introduza a seguinte consulta no diálogo de consulta SQL e, em seguida, selecione o ícone **'Executar'** (uma pessoa em execução). A área de resultados deve mostrar os resultados da consulta.

    ```hiveql
    select * from hivesampletable limit 10;
    ```

    ![diálogo de consulta sql, incluindo resultados](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Conecte-se a partir de uma aplicação java exemplo

Um exemplo de utilização de um cliente Java para consultar a Hive em HDInsight está disponível em [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) . Siga as instruções do repositório para construir e executar a amostra.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Erro inesperado ocorreu ao tentar abrir uma ligação SQL

**Sintomas**: Ao ligar-se a um cluster HDInsight que é a versão 3.3 ou maior, pode receber um erro que ocorreu. O traço da pilha para este erro começa com as seguintes linhas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: Este erro é causado por uma versão mais antiga do ficheiro commons-codec.jar incluído no SQuirreL.

**Resolução**: Para corrigir este erro, utilize os seguintes passos:

1. Saia da SQuirreL e, em seguida, vá para o diretório onde o SQuirreL está instalado no seu sistema, talvez `C:\Program Files\squirrel-sql-4.0.0\lib` . No diretório SquirreL, sob o `lib` diretório, substitua o co-código-codec.jar existente por aquele descarregado do cluster HDInsight.

1. Reinicie o SQuirrel. O erro não deve continuar a ocorrer quando se liga à Colmeia em HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Ligação desligada por HDInsight

**Sintomas**: Ao tentar descarregar uma enorme quantidade de dados (digamos vários GBs) através de JDBC/ODBC, a ligação é desligada pelo HDInsight inesperadamente durante o download.

**Causa:** Este erro é causado pela limitação dos nós gateway. Ao obter dados do JDBC/ODBC, todos os dados precisam de passar pelo nó Gateway. No entanto, um gateway não foi projetado para descarregar uma grande quantidade de dados, por isso o Gateway pode fechar a ligação se não conseguir lidar com o tráfego.

**Resolução**: Evite utilizar o controlador JDBC/ODBC para descarregar enormes quantidades de dados. Em vez disso, copie os dados diretamente do armazenamento do blob.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o JDBC para trabalhar com a Hive, use os seguintes links para explorar outras formas de trabalhar com a Azure HDInsight.

* [Visualizar os dados da Apache Hive com o Microsoft Power BI em Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de hive de consulta interativa com Power BI em Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Ligue o Excel ao HDInsight com o controlador ODBC da Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligue o Excel ao Apache Hadoop utilizando a Consulta de Potência](apache-hadoop-connect-excel-power-query.md).
* [Use a Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [Use o Porco Apache com HDInsight](../use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
