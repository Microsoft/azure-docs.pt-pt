---
title: Java definidas pelo utilizador função (UDF com o Apache Hive no HDInsight - Azure)
description: Saiba como criar uma com base em Java definidas pelo utilizador função (UDF) que funciona com o Apache Hive. Neste exemplo UDF converte uma tabela de cadeias de caracteres de texto em minúsculas.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 24c2e8b9600b3d622d3d6b42b3bc3615a87ff853
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686632"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Utilizar um Java UDF com o Apache Hive no HDInsight

Saiba como criar uma com base em Java definidas pelo utilizador função (UDF) que funciona com o Apache Hive. O Java UDF neste exemplo converte uma tabela de cadeias de caracteres de texto em minúsculas carateres.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Hadoop no HDInsight. Ver [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Kit de desenvolvimento Java (JDK) versão 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) , de acordo com o Apache.  A maven é um projeto de criar o sistema de projetos de Java.
* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para seu armazenamento primário de clusters. Isto poderá ser wasb: / / para o armazenamento do Azure, abfs: / / para a geração 2 de armazenamento do Azure Data Lake ou adl: / / para a geração 1 de armazenamento do Azure Data Lake. Se a transferência segura é ativada para o armazenamento do Azure ou de geração 2 de armazenamento do Data Lake, o URI seria wasbs: / / ou abfss: / /, respectivamente Consulte também [transferência segura](../../storage/common/storage-require-secure-transfer.md).

* Um editor de texto ou o Java IDE

    > [!IMPORTANT]  
    > Se criar os ficheiros de Python num cliente Windows, tem de utilizar um editor que utiliza LF como um fim da linha. Se não tiver a certeza se o seu editor utiliza LF ou CRLF, consulte a [resolução de problemas](#troubleshooting) secção para obter passos sobre como remover o caráter de CR.

## <a name="test-environment"></a>Ambiente de teste
O ambiente utilizado para este artigo foi um computador com Windows 10.  Os comandos foram executados numa linha de comandos e os diversos arquivos foram editados com o bloco de notas. Modificar em conformidade para o seu ambiente.

Num prompt de comando, introduza os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Criar um exemplo de Java UDF

1. Crie um novo projeto Maven ao introduzir o seguinte comando:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Este comando cria um diretório chamado `exampleudf`, que contém o projeto Maven.

2. Depois do projeto ter sido criado, eliminar o `exampleudf/src/test` diretório que foi criado como parte do projeto, introduzindo o seguinte comando:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Abra `pom.xml` introduzindo o comando abaixo:

    ```cmd
    notepad pom.xml
    ```

    Em seguida, substitua o existente `<dependencies>` entrada com o seguinte XML:

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

    Estas entradas de especificar a versão do Hadoop e ao Hive incluído com o HDInsight 3.6. Pode encontrar informações sobre as versões do Hadoop e ao Hive fornecido com o HDInsight a partir da [controlo de versões de componente de HDInsight](../hdinsight-component-versioning.md) documento.

    Adicionar uma `<build>` secção antes do `</project>` linha no final do ficheiro. Esta secção deve conter o seguinte XML:

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

    Estas entradas definem como compilar o projeto. Especificamente, a versão do Java que utiliza o projeto e como criar um uberjar para a implementação para o cluster.

    Guarde o ficheiro depois das alterações foram feitas.

4. Introduza o comando abaixo para criar e abrir um novo ficheiro `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Em seguida, copie e cole o código de java abaixo para o novo ficheiro. Em seguida, feche o ficheiro.

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

    Esse código implementa uma UDF que aceita um valor de cadeia de caracteres e retorna uma versão em minúsculas da cadeia de caracteres.

## <a name="build-and-install-the-udf"></a>Compilar e instalar a UDF

Os comandos abaixo, substitua `sshuser` com o nome de utilizador real, se for diferente. Substitua `mycluster` com o nome de cluster real.

1. Compilar e empacotar a UDF, introduzindo o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Este comando cria e empacota a UDF no `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` ficheiro.

2. Utilize o `scp` comandos para copiar o ficheiro para o cluster do HDInsight, introduzindo o seguinte comando:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Ligar ao cluster através de SSH ao introduzir o seguinte comando:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Abrir sessão de SSH, copie o ficheiro jar para o armazenamento do HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Utilizar a UDF do Hive

1. Inicie o cliente de Beeline da sessão SSH, introduzindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Este comando assume que utilizou a predefinição **administrador** para a conta de início de sessão para o seu cluster.

2. Assim que chegam ao `jdbc:hive2://localhost:10001/>` , introduza o seguinte para adicionar a UDF Hive e expô-la como uma função.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Utilize a UDF para converter valores obtidos a partir de uma tabela de cadeias de caracteres em minúsculas.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Esta consulta seleciona o estado da tabela, converter a cadeia de caracteres para o caso e, em seguida, exibi-los junto com o nome sem modificações. A saída será semelhante ao seguinte texto:

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

Durante a execução da tarefa do hive, pode encontrar um erro semelhante ao seguinte texto:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Esse problema pode dever-se se a fins de linha no ficheiro de Python. Muitos padrão de editores de Windows para utilizar CRLF como o fim da linha, mas as aplicações do Linux normalmente esperam LF.

Pode utilizar as seguintes declarações de PowerShell para remover os carateres de CR antes de carregar o ficheiro para o HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Passos Seguintes

Para outras formas de trabalhar com o Hive, consulte [utilizar o Apache Hive com HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre as funções de Hive User-Defined, consulte [operadores do Hive do Apache e funções definidas pelo utilizador](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) secção do wiki do Hive em apache.org.
