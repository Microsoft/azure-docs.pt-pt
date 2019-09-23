---
title: UDF (função definida pelo usuário) Java com Apache Hive no HDInsight – Azure
description: Saiba como criar uma UDF (função definida pelo usuário) baseada em Java que funcione com Apache Hive. Este exemplo UDF converte uma tabela de cadeias de caracteres de texto em minúsculas.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 43208636fb275c38573f820ef8245d7652b4aa86
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181179"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Usar um UDF Java com Apache Hive no HDInsight

Saiba como criar uma UDF (função definida pelo usuário) baseada em Java que funcione com Apache Hive. O Java UDF neste exemplo converte uma tabela de cadeias de texto em caracteres com todos os minúsculos.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks)
* O [Apache Maven](https://maven.apache.org/download.cgi) foi [instalado](https://maven.apache.org/install.html) corretamente de acordo com o Apache.  O Maven é um sistema de compilação de projeto para projetos Java.
* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário de clusters. Isso seria wasb://para o armazenamento do Azure, abfs://para Azure Data Lake Storage Gen2 ou adl://para Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure, `wasbs://`o URI será.  Consulte também a [transferência segura](../../storage/common/storage-require-secure-transfer.md).

* Um editor de texto ou Java IDE

    > [!IMPORTANT]  
    > Se você criar os arquivos do Python em um cliente Windows, deverá usar um editor que usa LF como uma terminação de linha. Se você não tiver certeza se o editor usa LF ou CRLF, consulte a seção [solução de problemas](#troubleshooting) para ver as etapas de remoção do caractere CR.

## <a name="test-environment"></a>Ambiente de teste
O ambiente usado para este artigo foi um computador que executa o Windows 10.  Os comandos foram executados em um prompt de comando e os vários arquivos foram editados com o bloco de notas. Modifique de acordo com o seu ambiente.

Em um prompt de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Criar um exemplo de UDF Java

1. Crie um novo projeto Maven inserindo o seguinte comando:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Este comando cria um diretório chamado `exampleudf`, que contém o projeto Maven.

2. Depois que o projeto tiver sido criado, exclua o `exampleudf/src/test` diretório que foi criado como parte do projeto digitando o seguinte comando:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Abra `pom.xml` inserindo o comando a seguir:

    ```cmd
    notepad pom.xml
    ```

    Em seguida, substitua `<dependencies>` a entrada existente pelo seguinte XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Essas entradas especificam a versão do Hadoop e do hive incluídos no HDInsight 3,6. Você pode encontrar informações sobre as versões do Hadoop e do hive fornecidos com o HDInsight no documento [controle de versão de componente do hdinsight](../hdinsight-component-versioning.md) .

    Adicione uma `<build>` seção antes da `</project>` linha no final do arquivo. Esta seção deve conter o seguinte XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Essas entradas definem como compilar o projeto. Especificamente, a versão do Java que o projeto usa e como criar um uberjar para implantação no cluster.

    Salve o arquivo depois que as alterações forem feitas.

4. Digite o comando a seguir para criar e abrir um novo `ExampleUDF.java`arquivo:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Em seguida, copie e cole o código Java abaixo no novo arquivo. Em seguida, feche o arquivo.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Esse código implementa um UDF que aceita um valor de cadeia de caracteres e retorna uma versão em minúsculas da cadeia de caracteres.

## <a name="build-and-install-the-udf"></a>Criar e instalar o UDF

Nos comandos abaixo, substitua `sshuser` pelo nome de usuário real, se for diferente. Substituir `mycluster` pelo nome real do cluster.

1. Compile e empacote o UDF digitando o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Esse comando cria e empacota o UDF no `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` arquivo.

2. Use o `scp` comando para copiar o arquivo para o cluster HDInsight digitando o seguinte comando:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Conecte-se ao cluster usando SSH digitando o seguinte comando:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Na sessão SSH aberta, copie o arquivo JAR para o armazenamento do HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Usar o UDF do hive

1. Inicie o cliente beeline da sessão SSH digitando o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Esse comando pressupõe que você usou o padrão de **admin** para a conta de logon do cluster.

2. Depois de chegar ao `jdbc:hive2://localhost:10001/>` prompt, digite o seguinte para adicionar o UDF ao hive e expô-lo como uma função.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Use o UDF para converter valores recuperados de uma tabela em cadeias de caracteres em letras minúsculas.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Essa consulta seleciona o estado da tabela, converte a cadeia de caracteres em letras minúsculas e, em seguida, exibe-as junto com o nome não modificado. A saída é semelhante ao seguinte texto:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Resolução de problemas

Ao executar o trabalho do hive, você pode encontrar um erro semelhante ao seguinte texto:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Esse problema pode ser causado pelas terminações de linha no arquivo Python. Muitos editores do Windows usam o padrão CRLF como terminação de linha, mas os aplicativos Linux geralmente esperam LF.

Você pode usar as seguintes instruções do PowerShell para remover os caracteres CR antes de carregar o arquivo para o HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Passos seguintes

Para outras maneiras de trabalhar com o Hive, consulte [usar o Apache Hive com o HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre funções definidas pelo usuário do hive, consulte a seção [operadores de Apache Hive e funções definidas pelo usuário](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) do wiki do hive em Apache.org.
