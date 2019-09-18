---
title: Consultar Apache Hive por meio do driver JDBC – Azure HDInsight
description: Use o driver JDBC de um aplicativo Java para enviar Apache Hive consultas ao Hadoop no HDInsight. Conecte-se programaticamente e do cliente SQL SQuirrel.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: hrasheed
ms.openlocfilehash: cd8a6c7e7f5ddf781fcd63f3969eedd8f45424bc
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058623"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Consultar Apache Hive por meio do driver JDBC no HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Saiba como usar o driver JDBC de um aplicativo Java para enviar Apache Hive consultas para Apache Hadoop no Azure HDInsight. As informações neste documento demonstram como se conectar programaticamente e do cliente SQL SQuirreL.

Para obter mais informações sobre a interface JDBC do hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Pré-requisitos

* HDInsight An cluster Hadoop. Para criar um, consulte Introdução [ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* O [Java Developer Kit (JDK) versão 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) ou superior.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL é um aplicativo cliente JDBC.

## <a name="jdbc-connection-string"></a>Cadeia de ligação JDBC

As conexões JDBC a um cluster HDInsight no Azure são feitas pela porta 443 e o tráfego é protegido usando SSL. O gateway público que os clusters ficam atrás redireciona o tráfego para a porta em que HiveServer2 está realmente escutando. A cadeia de conexão a seguir mostra o formato a ser usado para o HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Substitua `CLUSTERNAME` pelo nome do seu cluster do HDInsight.

## <a name="authentication"></a>Authentication

Ao estabelecer a conexão, você deve usar o nome de administrador do cluster HDInsight e a senha para se autenticar no gateway de cluster. Ao se conectar de clientes JDBC, como SQuirreL SQL, você deve inserir o nome do administrador e a senha nas configurações do cliente.

Em um aplicativo Java, você deve usar o nome e a senha ao estabelecer uma conexão. Por exemplo, o código Java a seguir abre uma nova conexão usando a cadeia de conexão, o nome do administrador e a senha:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conectar-se ao cliente do SQuirreL SQL

SQuirreL SQL é um cliente JDBC que pode ser usado para executar remotamente consultas de Hive com seu cluster HDInsight. As etapas a seguir pressupõem que você já instalou o SQuirreL SQL.

1. Crie um diretório para conter determinados arquivos a serem copiados do cluster.

2. No script a seguir, substitua `sshuser` pelo nome da conta de usuário SSH para o cluster.  Substitua `CLUSTERNAME` pelo nome do cluster HDInsight.  Em uma linha de comando, altere seu diretório de trabalho para aquele criado na etapa anterior e, em seguida, digite o seguinte comando para copiar arquivos de um cluster HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Inicie o aplicativo SQuirreL SQL. Na parte esquerda da janela, selecione **drivers**.

    ![Guia Drivers à esquerda da janela](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Nos ícones na parte superior da caixa de diálogo **drivers** , selecione o **+** ícone para criar um driver.

    ![Ícone de drivers de aplicativos do SQL SQuirreL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. Na caixa de diálogo Adicionar Driver, adicione as seguintes informações:

    * **Nome**: Hive
    * **URL de exemplo**:`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Caminho de classe extra**: Use o botão **Adicionar** para adicionar todos os arquivos jar baixados anteriormente
    * **Nome da classe**: org. Apache. Hive. JDBC. HiveDriver

   ![caixa de diálogo Adicionar driver com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Selecione **OK** para salvar essas configurações.

6. À esquerda da janela SQuirreL SQL, selecione **aliases**. Em seguida, **+** selecione o ícone para criar um alias de conexão.

    ![Caixa de diálogo Adicionar novo alias do SQuirreL SQL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Use os valores a seguir para a caixa de diálogo **Adicionar alias** .

    * **Nome**: Hive no HDInsight

    * **Driver**: Use a lista suspensa para selecionar o driver do **Hive**

    * **URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Substitua **CLUSTERNAME** pelo nome do cluster do HDInsight.

    * **Nome de usuário**: O nome da conta de logon do cluster para seu cluster HDInsight. A predefinição é `admin`.

    * **Senha**: A senha da conta de logon do cluster.

   ![caixa de diálogo Adicionar alias com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Use o botão **testar** para verificar se a conexão funciona. Ao **conectar-se a: Hive no HDInsight** diálogo é exibido, selecione **conectar** para executar o teste. Se o teste for bem-sucedido, você verá uma caixa de diálogo **conexão bem-sucedida** . Se ocorrer um erro, consulte [solução de problemas](#troubleshooting).

    Para salvar o alias de conexão, use o botão **OK** na parte inferior da caixa de diálogo **Adicionar alias** .

8. Na lista suspensa **conectar ao** na parte superior do SQuirreL SQL, selecione **Hive no HDInsight**. Quando lhe for pedido, selecione **Connect**.

    ![caixa de diálogo de conexão com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Uma vez conectado, insira a consulta a seguir na caixa de diálogo consulta SQL e, em seguida, selecione o ícone **executar** (uma pessoa em execução). A área de resultados deve mostrar os resultados da consulta.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![caixa de diálogo consulta SQL, incluindo resultados](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Conectar-se de um aplicativo Java de exemplo

Um exemplo de como usar um cliente Java para consultar o hive no HDInsight está [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)disponível em. Siga as instruções no repositório para compilar e executar o exemplo.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ocorreu um erro inesperado ao tentar abrir uma conexão SQL

**Sintomas**: Ao se conectar a um cluster HDInsight que é a versão 3,3 ou superior, você pode receber um erro inesperado. O rastreamento de pilha para esse erro começa com as seguintes linhas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Motivo**: Esse erro é causado por uma versão mais antiga do arquivo Commons-codec. jar incluído com SQuirreL.

**Resolução**: Para corrigir esse erro, use as seguintes etapas:

1. Saia do SQuirreL e vá para o diretório em que o SQuirreL está instalado no sistema. No diretório SquirreL, `lib` no diretório, substitua o Commons-codec. jar existente por um baixado do cluster HDInsight.

2. Reinicie o SQuirreL. O erro não deve ocorrer mais durante a conexão com o hive no HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a usar o JDBC para trabalhar com o Hive, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Visualize Apache Hive dados com o Microsoft Power bi no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados de hive de consulta interativa com Power bi no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conecte o Excel ao HDInsight com o driver ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute Apache Hive consultas usando o data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a ferramenta Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](../hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
