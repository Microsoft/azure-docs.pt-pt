---
title: Consulte o Apache Hive através do controlador JDBC - Azure HDInsight
description: Utilize o controlador JDBC a partir de uma aplicação de Java para submeter consultas do Apache Hive para o Hadoop no HDInsight. Ligar através de programação e do cliente SQuirrel SQL.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: hrasheed
ms.openlocfilehash: dc088d3a6baa4198a1d5cb566b968c631ae5f2c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113820"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Consulte o Apache Hive através do controlador JDBC no HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Saiba como utilizar o controlador JDBC a partir de uma aplicação de Java para submeter consultas do Apache Hive, Apache hadoop no HDInsight do Azure. As informações neste documento demonstra como estabelecer ligação através de programação e do cliente SQuirreL SQL.

Para obter mais informações sobre a Interface de JDBC do Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de Hadoop do HDInsight. Para criar um, veja [introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* O [Kit de desenvolvimento Java (JDK) versão 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) ou superior.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL é um aplicativo de cliente do JDBC.


## <a name="jdbc-connection-string"></a>Cadeia de ligação JDBC

Ligações de JDBC, para um cluster do HDInsight no Azure são efetuadas através da porta 443, e o tráfego é protegido por SSL. O gateway público que os clusters ficam por trás redireciona o tráfego para a porta que HiveServer2, na verdade, está a escutar. A seguinte cadeia de ligação mostra o formato a utilizar para o HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Substitua `CLUSTERNAME` pelo nome do seu cluster do HDInsight.

## <a name="authentication"></a>Authentication

Ao estabelecer a ligação, tem de utilizar o nome de administrador de cluster do HDInsight e a palavra-passe para autenticar para o gateway do cluster. Ao ligar a partir de clientes JDBC, como o SQuirreL SQL, tem de introduzir o nome de administrador e a palavra-passe nas definições do cliente.

A partir de uma aplicação de Java, tem de utilizar o nome e a palavra-passe ao estabelecer uma ligação. Por exemplo, o seguinte código de Java abre uma nova conexão usando a cadeia de ligação, o nome de administrador e a palavra-passe:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conecte-se o cliente de SQuirreL SQL

SQuirreL SQL é um cliente JDBC, que pode ser utilizado para executar remotamente as consultas do Hive com o seu cluster do HDInsight. Os passos seguintes partem do princípio de que já instalou o SQuirreL SQL.

1. Crie um diretório que contém a determinados ficheiros ser copiados do seu cluster.

2. O script seguinte, substitua `sshuser` com o nome de conta de utilizador SSH para o cluster.  Substitua `CLUSTERNAME` com o nome de cluster do HDInsight.  Numa linha de comandos, introduza o seguinte comando para copiar ficheiros de um cluster do HDInsight:

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-codec*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    ```

3. Inicie o aplicativo SQuirreL SQL. No lado esquerdo da janela, selecione **Drivers**.

    ![Separador de Drivers no lado esquerdo da janela](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Partir dos ícones na parte superior a **Drivers** caixa de diálogo, selecione a **+** ícone para criar um controlador.

    ![Ícones de controladores](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. Na caixa de diálogo Adicionar controlador, adicione as seguintes informações:

    * **Nome**: Hive
    * **URL de exemplo**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Caminho de classe extra**: Utilize o botão Adicionar para adicionar todas ficheiros jar transferida anteriormente
    * **Nome de classe**: org.apache.hive.jdbc.HiveDriver

   ![adicionar a caixa de diálogo de driver](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Selecione **OK** para salvar essas configurações.

6. No lado esquerdo da janela SQuirreL SQL, selecione **Aliases**. Em seguida, selecione o **+** ícone para criar um alias de ligação.

    ![Adicionar novo alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Utilize os seguintes valores para o **adicionar Alias** caixa de diálogo.

    * **Nome**: Hive no HDInsight

    * **Driver**: Utilize a lista pendente para selecionar o **Hive** driver

    * **URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Substitua **CLUSTERNAME** pelo nome do cluster do HDInsight.

    * **Nome de utilizador**: O cluster início de sessão nome da conta para o seu cluster do HDInsight. A predefinição é `admin`.

    * **palavra-passe**: A palavra-passe para a conta de início de sessão do cluster.

   ![adicionar a caixa de diálogo de alias](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Utilize o **teste** botão para verificar se a ligação funciona. Quando **ligar a: Hive no HDInsight** for apresentada a caixa de diálogo, selecione **Connect** para executar o teste. Se o teste for bem-sucedido, verá uma **ligação estabelecida com êxito** caixa de diálogo. Se ocorrer um erro, consulte [resolução de problemas](#troubleshooting).

    Para guardar o alias de ligação, utilize o **Ok** na parte inferior do **adicionar Alias** caixa de diálogo.

8. Do **ligue-se ao** menu pendente na parte superior do SQuirreL SQL, selecione **Hive no HDInsight**. Quando lhe for pedido, selecione **Connect**.

    ![caixa de diálogo de ligação](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Depois de ligado, introduza a seguinte consulta para a caixa de diálogo de consulta SQL e, em seguida, selecione o **executar** ícone (uma pessoa em execução). A área de resultados deve mostrar os resultados da consulta.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![diálogo de consulta de SQL, incluindo os resultados](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Ligar a partir de um aplicação Java de exemplo

Um exemplo do uso de um cliente de Java a consulta do Hive no HDInsight está disponível em [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Siga as instruções no repositório para compilar e executar o exemplo.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ocorreu erro inesperado ao tentar abrir uma ligação de SQL

**Os sintomas**: Ao ligar a um cluster do HDInsight versão 3.3 ou superior, poderá receber um erro que ocorreu um erro inesperado. O rastreio de pilha para este erro começa com as seguintes linhas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Motivo**: Este erro é causado por um ficheiro de commons codec.jar versão mais antigo acompanha o SQuirreL.

**Resolução**: Para corrigir este erro, utilize os seguintes passos:

1. Saia SQuirreL e, em seguida, avance para o diretório onde o SQuirreL está instalado no seu sistema. No diretório SquirreL, sob o `lib` diretório, substitua o codec.jar commons existente com a um transferido a partir do cluster do HDInsight.

2. Reinicie o SQuirreL. Já não deve ocorrer o erro ao ligar ao Hive no HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu a utilizar o JDBC para trabalhar com o Hive, utilize as seguintes ligações para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Visualize os dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de consulta interativa do Hive com o Power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Utilizar Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Ligar o Excel ao HDInsight com o controlador Microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligar o Excel para Apache Hadoop com o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ligar ao Azure HDInsight e executar consultas do Apache Hive com o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](../hdinsight-upload-data.md)
* [Utilizar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
