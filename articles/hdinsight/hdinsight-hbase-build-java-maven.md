---
title: Criar uma aplicação de Java HBase para HDInsight do Azure baseado no Windows
description: Saiba como utilizar o Apache Maven para criar uma aplicação baseada em Java Apache HBase, em seguida, implementá-la a um cluster do HDInsight do Azure com base em Windows.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: c0ecfd3f148cecae713740ef37d4fe7a2e2f184f
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361817"
---
# <a name="use-apache-maven-to-build-java-applications-that-use-apache-hbase-with-windows-based-hdinsight-apache-hadoop"></a>Utilizar o Apache Maven para construir aplicações Java que utilizam o HBase do Apache com o HDInsight baseado em Windows (Apache Hadoop)
Saiba como criar e compilar um [Apache HBase](https://hbase.apache.org/) aplicação em Java utilizando o Apache Maven. Em seguida, utilize a aplicação com o Azure HDInsight (Apache Hadoop).

[Apache Maven](https://maven.apache.org/) é uma ferramenta de gestão e a compreensão de projeto de software que permite a criação de software, documentação e relatórios para projetos de Java. Neste artigo, saiba como usá-lo para criar uma aplicação básica de Java que cria, consultas e elimina uma tabela de HBase num cluster HDInsight do Azure.

> [!IMPORTANT]  
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Windows. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="requirements"></a>Requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* [A plataforma Java JDK](https://aka.ms/azure-jdks) 7 ou posterior
* [Apache Maven](https://maven.apache.org/)
* Um cluster do HDInsight baseado em Windows com o HBase

    > [!NOTE]  
    > Os passos neste documento foram testados com versões de cluster do HDInsight 3.2 e 3.3. Os valores padrão fornecidos nos exemplos destinam-se um cluster do HDInsight 3.3.

## <a name="create-the-project"></a>Criar o projeto
1. Na linha de comando no seu ambiente de desenvolvimento, altere os diretórios para a localização onde pretende criar o projeto, por exemplo, `cd code\hdinsight`.
2. Utilize o **arquétipo** comando, que é instalado com o Maven, para gerar a estrutura do projeto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Este comando cria um diretório no local atual, com o nome especificado pelos **artifactID** parâmetro (**hbaseapp** neste exemplo.) Esse diretório contém os seguintes itens:

   * **pom.xml**:  O modelo de objeto de projeto ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contém os detalhes de configuração e informações usados para compilar o projeto.
   * **src**: O diretório que contém o **main\java\com\microsoft\examples** directory, onde irá criar a aplicação.
3. Eliminar a **src\test\java\com\microsoft\examples\apptest.java** porque não está a ser utilizado neste exemplo de ficheiro.

## <a name="update-the-project-object-model"></a>Atualizar o modelo de objeto de projeto
1. Editar a **pom. XML** do ficheiro e adicione o seguinte código dentro do `<dependencies>` secção:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Esta seção demonstra Maven que o projeto requer **hbase-cliente** versão **1.1.2**. No momento da compilação, esta dependência é transferida a partir do repositório de Maven padrão. Pode utilizar o [pesquisa de repositório Central do Apache Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre esta dependência.

   > [!IMPORTANT]  
   > O número de versão tem de corresponder à versão do HBase que é fornecido com o seu cluster do HDInsight. Utilize a seguinte tabela para encontrar o número de versão correta.
   >
   >

   | Versão de cluster do HDInsight | Versão do HBase para utilizar |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    Para obter mais informações sobre componentes e versões do HDInsight, consulte [quais são os diferentes componentes de Apache Hadoop disponíveis com o HDInsight](hdinsight-component-versioning.md).
2. Se estiver a utilizar um cluster do HDInsight 3.3, também tem de adicionar o seguinte para o `<dependencies>` secção:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    Esta dependência carregará os componentes de phoenix-core, que são utilizados pela versão de Hbase 1.1.x.
3. Adicione o seguinte código para o **pom** ficheiro. Esta secção tem de estar dentro de `<project>...</project>` etiquetas no ficheiro, por exemplo, entre `</dependencies>` e `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
              </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                  </transformers>
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

    O `<resources>` secção configura um recurso (**conf\hbase site**) que contém informações de configuração para o HBase.

   > [!NOTE]  
   > Também pode definir valores de configuração por meio do código. Veja os comentários no **CreateTable** exemplo que se segue para saber como fazê-lo.
   >
   >

    Isso `<plugins>` secção configura o [Plug-in de compilador do Apache Maven](https://maven.apache.org/plugins/maven-compiler-plugin/) e [Plug-in do Apache Maven tom](https://maven.apache.org/plugins/maven-shade-plugin/). O compilador Plug-in é usado para compilar a topologia. A Plug-in de tonalidade é utilizada para evitar a duplicação de licença do pacote de JAR que é criado pela Maven. O motivo pelo qual que ele é usado é que os ficheiros de licença duplicados causarem um erro em tempo de execução no cluster do HDInsight. Utilizar maven-tom-Plug-in com o `ApacheLicenseResourceTransformer` implementação impede este erro.

    O maven-tom-Plug-in também produz um uber jar (ou fat jar) que contém todas as dependências exigidas pela aplicação.
4. Guarde o ficheiro **pom.xml**.
5. Criar um novo diretório com o nome **conf** no **hbaseapp** diretório. Na **conf** diretório, crie um ficheiro denominado **hbase-site**. Utilize o seguinte como conteúdo do ficheiro:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     https://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Este ficheiro será utilizado para carregar a configuração do HBase para um cluster do HDInsight.

   > [!NOTE]  
   > Este é um ficheiro de um mínimo de site de hbase e contém as definições de mínimo bare para o cluster do HDInsight.

6. Guardar a **hbase-site** ficheiro.

## <a name="create-the-application"></a>Criar a aplicação
1. Vá para o **hbaseapp\src\main\java\com\microsoft\examples** diretório e renomeie o App de ficheiros para **CreateTable.java**.
2. Abra o **CreateTable.java** de ficheiro e substituir o conteúdo existente com o código a seguir:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Este é o **CreateTable** classe, que irá criar uma tabela chamada **pessoas** e preenchê-lo com alguns usuários predefinidos.
3. Guardar a **CreateTable.java** ficheiro.
4. Na **hbaseapp\src\main\java\com\microsoft\examples** diretório, crie um novo ficheiro designado **SearchByEmail.java**. Utilize o seguinte código como o conteúdo deste ficheiro:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    O **SearchByEmail** classe pode ser usada para consultar os linhas por endereço de e-mail. Uma vez que ele utiliza um filtro de expressão regular, pode fornecer uma cadeia de caracteres ou uma expressão regular quando usando a classe.
5. Guardar a **SearchByEmail.java** ficheiro.
6. Na **hbaseapp\src\main\hava\com\microsoft\examples** diretório, crie um novo ficheiro designado **DeleteTable.java**. Utilize o seguinte código como o conteúdo deste ficheiro:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Essa classe é para a limpeza neste exemplo, ao desativar e remover a tabela criada pela **CreateTable** classe.
7. Guardar a **DeleteTable.java** ficheiro.

## <a name="build-and-package-the-application"></a>Criar e empacotar a aplicação
1. Abra uma linha de comandos e altere os diretórios para o **hbaseapp** diretório.
2. Utilize o seguinte comando para criar um ficheiro JAR que contém a aplicação:

        mvn clean package

    Isto limpa quaisquer artefactos de compilação anterior, transfere quaisquer dependências que ainda não tiver sido instaladas, em seguida, cria e empacota o aplicativo.
3. Quando o comando for concluído, o **hbaseapp\target** diretório contém um arquivo chamado **hbaseapp-1.0-SNAPSHOT.jar**.

   > [!NOTE]  
   > O **hbaseapp-1.0-SNAPSHOT.jar** ficheiro é um uber jar (por vezes denominado um jar fat,), que contém todas as dependências necessárias para executar a aplicação.

## <a name="upload-the-jar-file-and-start-a-job"></a>Carregar o ficheiro JAR e iniciar uma tarefa
Existem várias formas para carregar um ficheiro ao seu cluster do HDInsight, conforme descrito em [carregar dados para as tarefas do Apache Hadoop no HDInsight](hdinsight-upload-data.md). Os passos seguintes utilizam o Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Depois de instalar e configurar o Azure PowerShell, crie um novo ficheiro designado **hbase-runner.psm1**. Utilize o seguinte como o conteúdo deste ficheiro:

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"

        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"

        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>

        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,

        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,

        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,

        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get the login for the HDInsight cluster
        $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

        # The JAR
        $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

        # The job definition
        $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex

        # Get the job output
        $job = Start-AzHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds
        }

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>

        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,

                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,

                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,

                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )

            Set-StrictMode -Version 3

            # Is the Azure module installed?
            FindAzure

            # Get authentication for the cluster
            $creds=Get-Credential

            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }

            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName

            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }

        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Connect-AzAccount cmdlet to login to your subscription."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}

            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey

            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Este ficheiro contém dois módulos:

   * **Adicionar-HDInsightFile** - utilizado para carregar ficheiros para o HDInsight
   * **Start-HBaseExample** - utilizado para executar as classes que criou anteriormente
2. Guardar a **hbase-runner.psm1** ficheiro.
3. Abra uma nova janela do PowerShell do Azure, altere os diretórios para o **hbaseapp** directory e, em seguida, execute o seguinte comando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Altere o caminho para a localização do **hbase-runner.psm1** arquivo criado anteriormente. O módulo é registada para esta sessão do Azure PowerShell.
4. Utilize o seguinte comando para carregar os **hbaseapp-1.0-SNAPSHOT.jar** ao seu cluster do HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Substitua **hdinsightclustername** com o nome do cluster do HDInsight. O comando carrega o **hbaseapp-1.0-SNAPSHOT.jar** para o **exemplo/jars** localização no armazenamento primário para o seu cluster do HDInsight.
5. Depois dos ficheiros são carregados, utilize o seguinte código para criar uma tabela com o **hbaseapp**:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Substitua **hdinsightclustername** com o nome do cluster do HDInsight.

    Este comando cria uma nova tabela chamada **pessoas** no seu cluster do HDInsight. Este comando não mostra os resultados na janela da consola.
6. Para procurar entradas na tabela, utilize o seguinte comando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Substitua **hdinsightclustername** com o nome do cluster do HDInsight.

    Este comando utiliza o **SearchByEmail** classe para pesquisar quaisquer linhas em que o **contactinformation que era** família de colunas e o **e-mail** coluna, contém a cadeia de caracteres **contoso.com**. Deverá receber os seguintes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Usando **fabrikam.com** para o `-emailRegex` valor devolve os utilizadores que tenham **fabrikam.com** no campo de e-mail. Uma vez que esta pesquisa é implementada com um filtro com base em expressão regular, também pode introduzir as expressões regulares, como **^ r**, as entradas de devolve em que o e-mail começa com a letra 'r'.

## <a name="delete-the-table"></a>Eliminar tabela
Quando tiver terminado com o exemplo, utilize o seguinte comando da sessão do PowerShell do Azure para eliminar a **pessoas** tabela usada neste exemplo:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Substitua **hdinsightclustername** com o nome do cluster do HDInsight.

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Não existem resultados ou resultados inesperados ao utilizar o início HBaseExample
Utilize o `-showErr` parâmetro para ver o erro padrão (STDERR) que é produzido durante a execução da tarefa.
