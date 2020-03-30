---
title: Consulta Apache Hive através do condutor JDBC - Azure HDInsight
description: Utilize o controlador JDBC a partir de uma aplicação Java para submeter consultas apache hive a Hadoop no HDInsight. Conecte-se programáticamente e do cliente SQuirrel SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7d1a77800093ae01bc4eb1e1269d1e9a60f9ce26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616658"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Query Apache Hive through the JDBC driver in HDInsight (Consultar o Apache Hive através do controlador JDBC no HDInsight)

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Aprenda a usar o condutor JDBC a partir de uma aplicação Java para submeter consultas apache hive a Apache Hadoop em Azure HDInsight. A informação neste documento demonstra como se conectar programáticamente, e do cliente SQuirreL SQL.

Para obter mais informações sobre a Interface Hive JDBC, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de Hadoop HDInsight. Para criar um, consulte Iniciar com [Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Certifique-se de que o serviço HiveServer2 está em execução.
* A [versão Java Developer Kit (JDK) 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) ou superior.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). A SQuirreL é uma aplicação de cliente DaJBC.

## <a name="jdbc-connection-string"></a>Cadeia de ligação JDBC

As ligações JDBC a um cluster HDInsight em Azure são feitas sobre a porta 443, e o tráfego é seguro usando SSL. A porta de entrada pública que os clusters sentam atrás redireciona o tráfego para a porta que o HiveServer2 está realmente a ouvir. A seguinte cadeia de ligação mostra o formato a utilizar para o HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Substitua `CLUSTERNAME` pelo nome do seu cluster do HDInsight.

Ou pode obter a ligação através da **Ambari UI > Hive > Configs > Advanced**.

![Obtenha a cadeia de ligação JDBC através de Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Nome do anfitrião na cadeia de ligação

O nome do hospedeiro 'CLUSTERNAME.azurehdinsight.net' na cadeia de ligação é o mesmo que o URL do cluster. Pode sacá-lo através do portal Azure. 

### <a name="port-in-connection-string"></a>Porta na cadeia de ligação

Só pode utilizar a **porta 443** para se ligar ao cluster de alguns locais fora da rede virtual Azure. O HDInsight é um serviço gerido, o que significa que todas as ligações ao cluster são geridas através de um Gateway seguro. Não é possível ligar diretamente ao HiveServer 2 nas portas 10001 ou 10000 porque estas portas não estão expostas ao exterior. 

## <a name="authentication"></a>Autenticação

Ao estabelecer a ligação, deve utilizar o nome de administrador do cluster HDInsight e a palavra-passe para autenticar o gateway do cluster. Ao ligar-se a clientes JDBC, como o SQuirreL SQL, deve introduzir o nome de administrador e a palavra-passe nas definições do cliente.

A partir de uma aplicação Java, deve utilizar o nome e a senha ao estabelecer uma ligação. Por exemplo, o seguinte código Java abre uma nova ligação utilizando a cadeia de ligação, o nome administrativo e a palavra-passe:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conecte-se com o cliente SQuirreL SQL

SQuirreL SQL é um cliente JDBC que pode ser usado para executar remotamente consultas da Hive com o seu cluster HDInsight. Os seguintes passos assumem que já instalou sQuirreL SQL.

1. Crie um diretório para conter certos ficheiros a serem copiados do seu cluster.

2. No seguinte script, `sshuser` substitua-o pelo nome da conta de utilizador SSH para o cluster.  Substitua-a `CLUSTERNAME` com o nome do cluster HDInsight.  A partir de uma linha de comando, altere o seu diretório de trabalho para o criado no passo anterior e, em seguida, introduza o seguinte comando para copiar ficheiros de um cluster HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Inicie a aplicação SQuirreL SQL. Do esquerdo da janela, selecione **Condutores**.

    ![Condutores abas à esquerda da janela](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. A partir dos ícones na parte superior **+** do diálogo **dos Controladores,** selecione o ícone para criar um controlador.

    ![Ícone de controladores de aplicação SQuirreL SQL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. No diálogo Add Driver, adicione as seguintes informações:

    |Propriedade | Valor |
    |---|---|
    |Nome|Hive|
    |URL de exemplo|jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2|
    |Caminho extra de classe|Utilize o botão **Adicionar** para adicionar todos os ficheiros de frascos descarregados anteriormente.|
    |Nome da classe|org.apache.hive.jdbc.HiveDriver|

   ![adicionar diálogo do condutor com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Selecione **OK** para guardar estas definições.

6. À esquerda da janela SQuirreL SQL, selecione **Aliases**. Em seguida, selecione o **+** ícone para criar um pseudónimo de ligação.

    ![SQuirreL SQL adiciona novo diálogo de pseudónimo](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Utilize os seguintes valores para o diálogo **Add Alias:**

    |Propriedade |Valor |
    |---|---|
    |Nome|Colmeia no HDInsight|
    |Controlador|Utilize a entrega para selecionar o condutor da **Colmeia.**|
    |do IdP|jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2. Substitua **CLUSTERNAME** pelo nome do cluster do HDInsight.|
    |Nome de Utilizador|O nome da conta de login do cluster para o seu cluster HDInsight. O padrão é **administrador.**|
    |Palavra-passe|A palavra-passe para a conta de login do cluster.|

    ![adicionar diálogo pseudónimo com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Utilize o botão **Teste** para verificar se a ligação funciona. Quando ligar a: Aparece a colmeia no diálogo **HDInsight,** selecione **Connect** para realizar o teste. Se o teste for bem sucedido, verá um diálogo **bem sucedido da Ligação.** Se ocorrer um erro, consulte [a resolução de problemas](#troubleshooting).

    Para guardar o pseudónimo de ligação, utilize o botão **Ok** na parte inferior do diálogo **Add Alias.**

8. Do **Connect ao** dropdown na parte superior do SQuirreL SQL, selecione **Hive no HDInsight**. Quando solicitado, selecione **Connect**.

    ![diálogo de ligação com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Uma vez ligado, introduza a seguinte consulta no diálogo de consulta SQL e, em seguida, selecione o ícone **Executar** (uma pessoa em execução). A área de resultados deve mostrar os resultados da consulta.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![diálogo de consulta sql, incluindo resultados](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Conecte-se a partir de uma aplicação java exemplo

Um exemplo de usar um cliente Java para consultar a [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)Hive no HDInsight está disponível em . Siga as instruções no repositório para construir e executar a amostra.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Erro inesperado ocorreu tentando abrir uma ligação SQL

**Sintomas**: Ao ligar-se a um cluster HDInsight que seja da versão 3.3 ou superior, pode receber um erro que ocorreu um erro inesperado. O traço da pilha para este erro começa com as seguintes linhas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: Este erro é causado por um ficheiro de versão mais antiga com um código de câmara.jar incluído com SQuirreL.

**Resolução**: Para corrigir este erro, utilize os seguintes passos:

1. Saia SQuirreL e, em seguida, vá para o diretório onde o `C:\Program Files\squirrel-sql-4.0.0\lib`SQuirreL está instalado no seu sistema, talvez . No diretório SquirreL, `lib` sob o diretório, substitua o existente commons-codec.jar pelo download do cluster HDInsight.

1. Reiniciar o SQuirreL. O erro não deve mais ocorrer ao ligar-se à Hive no HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Ligação desligada por HDInsight

**Sintomas**: Ao tentar descarregar uma enorme quantidade de dados (digamos vários GBs) através do JDBC/ODBC, a ligação é desligada pela HDInsight inesperadamente durante o download. 

**Causa**: Este erro é causado pela limitação dos nós gateway. Ao obter dados da JDBC/ODBC, todos os dados precisam passar pelo nó gateway. No entanto, um portal não foi concebido para descarregar uma enorme quantidade de dados, pelo que a ligação pode ser fechada pelo Gateway se não conseguir lidar com o tráfego.

**Resolução**: Evite utilizar o condutor JDBC/ODBC para descarregar enormes quantidades de dados. Copie os dados diretamente do armazenamento de blob.


## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o JDBC para trabalhar com a Hive, use os seguintes links para explorar outras formas de trabalhar com o Azure HDInsight.

* [Visualizar os dados da Hive Apache com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de Colmeia de Consulta Interativa com Power BI em Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligue o Excel ao HDInsight com o condutor microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligue excel a Apache Hadoop utilizando](apache-hadoop-connect-excel-power-query.md)power query .
* [Ligue-se ao Azure HDInsight e execute consultas apache hive usando ferramentas de data lake para estúdio visual](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta Azure HDInsight para o Código](../hdinsight-for-vscode.md)do Estúdio Visual .
* [Upload data to HDInsight (Carregar dados para o HDInsight)](../hdinsight-upload-data.md)
* [Use a Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [Use o Porco Apache com HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
