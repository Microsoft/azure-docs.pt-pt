---
title: Java definidas pelo utilizador função (UDF com o Apache Hive no HDInsight - Azure)
description: Saiba como criar uma com base em Java definidas pelo utilizador função (UDF) que funciona com o Apache Hive. Neste exemplo UDF converte uma tabela de cadeias de caracteres de texto em minúsculas.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 94e9a70707472eb94109ebcc404fd7a1a3074135
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575751"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Utilizar um Java UDF com o Apache Hive no HDInsight

Saiba como criar uma com base em Java definidas pelo utilizador função (UDF) que funciona com o Apache Hive. O Java UDF neste exemplo converte uma tabela de cadeias de caracteres de texto em minúsculas carateres.

## <a name="requirements"></a>Requisitos

* Um cluster do HDInsight 

    > [!IMPORTANT]
    > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

    A maioria dos passos neste documento funcionam em ambos os clusters baseados em Windows e Linux. No entanto, os passos utilizados para carregar a UDF compilada para o cluster e executá-la são específicos para clusters baseados em Linux. São fornecidas hiperligações para informações que podem ser utilizadas com clusters baseados em Windows.

* [Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/) 8 ou posterior (ou um equivalente, como OpenJDK)

* [Apache Maven](https://maven.apache.org/)

* Um editor de texto ou o Java IDE

    > [!IMPORTANT]
    > Se criar os ficheiros de Python num cliente Windows, tem de utilizar um editor que utiliza LF como um fim da linha. Se não tiver a certeza se o seu editor utiliza LF ou CRLF, consulte a secção de resolução de problemas para obter os passos sobre a remover o caráter de CR.

## <a name="create-an-example-java-udf"></a>Criar um exemplo de Java UDF 

1. A partir de uma linha de comandos, utilize o seguinte para criar um novo projeto Maven:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Se estiver a utilizar o PowerShell, tem de colocar os parâmetros entre aspas. Por exemplo, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Este comando cria um diretório chamado **exampleudf**, que contém o projeto Maven.

2. Depois do projeto ter sido criado, eliminar a **exampleudf/src/teste** diretório que foi criado como parte do projeto.

3. Abra o **exampleudf/pom.xml**e substituir o existente `<dependencies>` entrada com o seguinte XML:

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
                <version>2.3</version>
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

4. Mudar o nome **exampleudf/src/main/java/com/microsoft/examples/App.java** ao **ExampleUDF.java**e, em seguida, abra o ficheiro no seu editor.

5. Substitua os conteúdos do **ExampleUDF.java** de ficheiros com o seguinte, em seguida, guarde o ficheiro.

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

1. Utilize o seguinte comando para compilar e empacotar a UDF:

    ```bash
    mvn compile package
    ```

    Este comando cria e empacota a UDF no `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` ficheiro.

2. Utilize o `scp` comandos para copiar o ficheiro para o cluster do HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight.net
    ```

    Substitua `myuser` com a conta de utilizador SSH para o seu cluster. Substitua `mycluster` com o nome do cluster. Se utilizou uma palavra-passe para proteger a conta SSH, lhe for pedido para introduzir a palavra-passe. Se utilizou um certificado, poderá ter de utilizar o `-i` parâmetro para especificar o ficheiro de chave privada.

3. Ligar ao cluster através de SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. A partir da sessão SSH, copie o ficheiro jar para o armazenamento do HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Utilizar a UDF do Hive

1. Utilize o seguinte para iniciar o cliente de Beeline da sessão SSH.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Este comando assume que utilizou a predefinição **administrador** para a conta de início de sessão para o seu cluster.

2. Assim que chegam ao `jdbc:hive2://localhost:10001/>` , introduza o seguinte para adicionar a UDF Hive e expô-la como uma função.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Este exemplo assume que o armazenamento do Azure é armazenamento predefinido para o cluster. Se o cluster utiliza a geração 2 de armazenamento do Data Lake em vez disso, altere a `wasb:///` valor a `abfs:///`. Se o cluster utiliza Gen1 de armazenamento do Data Lake, altere a `wasb:///` valor a `adl:///`.

3. Utilize a UDF para converter valores obtidos a partir de uma tabela de cadeias de caracteres em minúsculas.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Esta consulta seleciona a plataforma do dispositivo (Android, Windows, iOS, etc.) da tabela, converter a cadeia de caracteres para o caso mais baixo e, em seguida, exibi-los. A saída será semelhante ao seguinte texto:

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Passos Seguintes

Para outras formas de trabalhar com o Hive, consulte [utilizar o Apache Hive com HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre as funções de Hive User-Defined, consulte [operadores do Hive do Apache e funções definidas pelo utilizador](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) secção do wiki do Hive em apache.org.
