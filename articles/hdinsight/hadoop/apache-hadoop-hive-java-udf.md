---
title: Função definida pelo utilizador java (UDF) com Apache Hive Azure HDInsight
description: Saiba como criar uma função definida pelo utilizador java (UDF) que funciona com a Apache Hive. Este exemplo UDF converte uma tabela de cordas de texto para minúsculas.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946567"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Use um Java UDF com Colmeia Apache em HDInsight

Saiba como criar uma função definida pelo utilizador java (UDF) que funciona com a Apache Hive. O Java UDF neste exemplo converte uma tabela de cordas de texto para caracteres minúsculos.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado hadoop em HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado de](https://maven.apache.org/install.html) acordo com Apache.  Maven é um sistema de construção de projetos para projetos java.
* O [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário dos seus clusters. Esta seria wasb:// para o Azure Storage, abfs:// para a Azure Data Lake Storage Gen2 ou adl:// para a Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Azure, o URI será `wasbs://` .  Consulte também, [transferência segura.](../../storage/common/storage-require-secure-transfer.md)

* Um editor de texto ou Java IDE

    > [!IMPORTANT]  
    > Se criar os ficheiros Python num cliente Windows, deve utilizar um editor que utilize o LF como final de linha. Se não tiver a certeza se o seu editor utiliza LF ou CRLF, consulte a secção [de resolução de problemas](#troubleshooting) para obter passos na remoção do carácter CR.

## <a name="test-environment"></a>Ambiente de teste

O ambiente utilizado para este artigo era um computador que executava o Windows 10.  Os comandos foram executados num pedido de comando, e os vários ficheiros foram editados com Bloco de Notas. Modifique em conformidade para o seu ambiente.

A partir de um pedido de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Criar um exemplo Java UDF

1. Criar um novo projeto Maven introduzindo o seguinte comando:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Este comando cria um diretório chamado `exampleudf` , que contém o projeto Maven.

2. Uma vez criado o projeto, elimine o `exampleudf/src/test` diretório que foi criado como parte do projeto, introduzindo o seguinte comando:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Abra `pom.xml` introduzindo o comando abaixo:

    ```cmd
    notepad pom.xml
    ```

    Em seguida, substitua a entrada existente `<dependencies>` pelo seguinte XML:

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

    Estas entradas especificam a versão de Hadoop e Hive incluída com HDInsight 3.6. Pode encontrar informações sobre as versões de Hadoop e Hive fornecidas com HDInsight a partir do documento [de versão componente HDInsight.](../hdinsight-component-versioning.md)

    Adicione uma `<build>` secção antes da linha no final do `</project>` ficheiro. Esta secção deve conter o seguinte XML:

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

    Estas entradas definem como construir o projeto. Especificamente, a versão de Java que o projeto usa e como construir um uberjar para implantação no cluster.

    Guarde o ficheiro uma vez que as alterações tenham sido efetuadas.

4. Introduza o comando abaixo para criar e abrir um novo `ExampleUDF.java` ficheiro:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Em seguida, copie e cole o código java abaixo no novo ficheiro. Em seguida, feche o arquivo.

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

    Este código implementa um UDF que aceita um valor de cadeia, e devolve uma versão minúscula da cadeia.

## <a name="build-and-install-the-udf"></a>Construir e instalar o UDF

Nos comandos abaixo, `sshuser` substitua-o pelo nome de utilizador real se for diferente. `mycluster`Substitua-o pelo nome do cluster real.

1. Compilar e embalar o UDF introduzindo o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Este comando constrói e embala o UDF no `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` ficheiro.

2. Utilize o `scp` comando para copiar o ficheiro para o cluster HDInsight, introduzindo o seguinte comando:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Ligue ao cluster utilizando o SSH introduzindo o seguinte comando:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. A partir da sessão SSH aberta, copie o ficheiro do frasco para o armazenamento HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Use o UDF da Colmeia

1. Inicie o cliente Beeline a partir da sessão SSH, introduzindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Este comando pressupõe que utilizou o padrão de **administração** para a conta de login do seu cluster.

2. Assim que chegar ao `jdbc:hive2://localhost:10001/>` pedido, insira o seguinte para adicionar o UDF à Colmeia e exponha-o como uma função.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Utilize o UDF para converter valores recuperados de uma tabela para cadeias minúsculas.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Esta consulta seleciona o estado da tabela, converte a corda para minúsculas e, em seguida, mostra-as juntamente com o nome não modificado. A saída parece semelhante ao seguinte texto:

    ```output
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
    ```

## <a name="troubleshooting"></a>Resolução de problemas

Ao executar a função de colmeia, poderá deparar-se com um erro semelhante ao seguinte texto:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Este problema pode ser causado pelas terminações de linha no ficheiro Python. Muitos editores do Windows padrão para usar CRLF como o fim da linha, mas as aplicações Linux geralmente esperam LF.

Pode utilizar as seguintes declarações do PowerShell para remover os caracteres CR antes de enviar o ficheiro para HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Passos seguintes

Para outras formas de trabalhar com a Colmeia, consulte [Use Apache Hive com HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre as Funções User-Defined da Colmeia, consulte [os Operadores de Colmeia Apache e a](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) secção User-Defined Funções da Colmeia wiki em apache.org.