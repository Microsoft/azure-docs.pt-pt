---
title: Use a Apache Maven para construir um cliente Java HBase para o Azure HDInsight
description: Aprenda a usar o Apache Maven para construir uma aplicação Apache HBase baseada em Java e, em seguida, implemente-a para HBase no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/24/2019
ms.openlocfilehash: 3e9b23ce450e45dfedcee8b20e09b1c2b52b6e68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75495793"
---
# <a name="build-java-applications-for-apache-hbase"></a>Construir aplicações Java para Apache HBase

Saiba como criar uma aplicação [Apache HBase](https://hbase.apache.org/) em Java. Em seguida, utilize a aplicação com HBase no Azure HDInsight.

Os passos neste documento utilizam [a Apache Maven](https://maven.apache.org/) para criar e construir o projeto. A Maven é uma ferramenta de gestão e compreensão de projetos de software que lhe permite construir software, documentação e relatórios para projetos java.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache HBase no HDInsight. Ver [Começar com Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado](https://maven.apache.org/install.html) de acordo com Apache.  Maven é um sistema de construção de projetos para projetos Java.

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Se utilizar o PowerShell, necessitará do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

* Um editor de texto. Este artigo utiliza o Microsoft Notepad.

## <a name="test-environment"></a>Ambiente de teste

O ambiente utilizado para este artigo era um computador que executava o Windows 10.  Os comandos foram executados num pedido de comando, e os vários ficheiros foram editados com o Bloco de Notas. Modifique em conformidade para o seu ambiente.

A partir de um pedido de comando, introduza os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Insira o seguinte comando para criar um projeto Maven chamado **hbaseapp:**

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Este comando cria um `hbaseapp` diretório nomeado no local atual, que contém um projeto básico de Maven. O segundo comando muda o `hbaseapp`diretório de trabalho para . O terceiro comando cria um `conf`novo diretório, que será usado mais tarde. O `hbaseapp` diretório contém os seguintes itens:

    * `pom.xml`: O Modelo de Objetos de Projeto[(POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)contém informações e detalhes de configuração utilizados para construir o projeto.
    * `src\main\java\com\microsoft\examples`: Contém o seu código de candidatura.
    * `src\test\java\com\microsoft\examples`: Contém testes para a sua aplicação.

2. Remova o código de exemplo gerado. Elimine os ficheiros `AppTest.java`de `App.java` teste e aplicação gerados e introduzindo os comandos abaixo:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Atualizar o Modelo de Objeto de Projeto

Para obter uma referência completa do ficheiro https://maven.apache.org/pom.htmlpom.xml, consulte .  Abra `pom.xml` entrando no comando abaixo:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Adicionar dependências

Em, `pom.xml`adicione o seguinte `<dependencies>` texto na secção:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Esta secção indica que o projeto necessita de componentes **hbase-cliente e** **phoenix-core.** No momento da compilação, estas dependências são descarregadas do repositório padrão maven. Você pode usar a Pesquisa De [Repositório Central Maven](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) para saber mais sobre esta dependência.

> [!IMPORTANT]  
> O número de versão do hbase-cliente deve corresponder à versão do Apache HBase que é fornecida com o seu cluster HDInsight. Utilize a tabela seguinte para encontrar o número de versão correta.

| Versão de cluster HDInsight | Versão Apache HBase para usar |
| --- | --- |
| 3.6 | 1.1.2 |
| 4,0 | 2.0.0 |

Para obter mais informações sobre as versões e componentes hDInsight, consulte [quais são os diferentes componentes Apache Hadoop disponíveis com hDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Configuração de construção

Os plug-ins Maven permitem personalizar as fases de construção do projeto. Esta secção é usada para adicionar plug-ins, recursos e outras opções de configuração de construção.

Adicione o seguinte `pom.xml` código ao ficheiro e, em seguida, guarde e feche o ficheiro. Este texto deve `<project>...</project>` estar dentro das etiquetas do `</dependencies>` `</project>`ficheiro, por exemplo, entre e .

```xml
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
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
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
```

Esta secção configura um`conf/hbase-site.xml`recurso que contém informações de configuração para HBase.

> [!NOTE]  
> Também pode definir valores de configuração via código. Veja os comentários `CreateTable` no exemplo.

Esta secção também configura o [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) e [Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/). O plug-in do compilador é usado para compilar a topologia. O plug-in de sombra é usado para evitar a duplicação de licenças no pacote JAR que é construído pela Maven. Este plugin é utilizado para evitar um erro de "duplicado ficheiros de licença" no tempo de execução no cluster HDInsight. A utilização de maven-shade-plugin com a `ApacheLicenseResourceTransformer` implementação evita o erro.

O plugin de sombra maven também produz um jarro uber que contém todas as dependências exigidas pela aplicação.

### <a name="download-the-hbase-sitexml"></a>Descarregue o hbase-site.xml

Utilize o seguinte comando para copiar a configuração `conf` HBase do cluster HBase para o diretório. Substitua-o `CLUSTERNAME` pelo nome do cluster HDInsight e introduza o comando:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Criar a aplicação

### <a name="implement-a-createtable-class"></a>Implementar uma classe CreateTable

Introduza o comando abaixo para `CreateTable.java`criar e abra um novo ficheiro . Selecione **Sim** na solicitação para criar um novo ficheiro.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Em seguida, copie e cole o código java abaixo no novo ficheiro. Em seguida, feche o arquivo.

```java
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
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

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
```

Este código `CreateTable` é a classe, `people` que cria uma tabela com o nome e povoa-o com alguns utilizadores predefinidos.

### <a name="implement-a-searchbyemail-class"></a>Implementar uma classe SearchByEmail

Introduza o comando abaixo para `SearchByEmail.java`criar e abra um novo ficheiro . Selecione **Sim** na solicitação para criar um novo ficheiro.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Em seguida, copie e cole o código java abaixo no novo ficheiro. Em seguida, feche o arquivo.

```java
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

    // Create a regex filter
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
```

A `SearchByEmail` aula pode ser usada para consultar filas por endereço de e-mail. Como utiliza um filtro de expressão regular, pode fornecer uma corda ou uma expressão regular ao utilizar a classe.

### <a name="implement-a-deletetable-class"></a>Implementar uma classe DeleteTable

Introduza o comando abaixo para `DeleteTable.java`criar e abra um novo ficheiro . Selecione **Sim** na solicitação para criar um novo ficheiro.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Em seguida, copie e cole o código java abaixo no novo ficheiro. Em seguida, feche o arquivo.

```java
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
```

A `DeleteTable` classe limpa as tabelas HBase criadas neste exemplo, desativando e largando a tabela criada pela `CreateTable` classe.

## <a name="build-and-package-the-application"></a>Construir e embalar a aplicação

1. A `hbaseapp` partir do diretório, utilize o seguinte comando para construir um ficheiro JAR que contenha a aplicação:

    ```cmd
    mvn clean package
    ```

    Este comando constrói e embala a aplicação num ficheiro .jar.

2. Quando o comando estiver `hbaseapp/target` concluído, o `hbaseapp-1.0-SNAPSHOT.jar`diretório contém um ficheiro chamado .

   > [!NOTE]  
   > O `hbaseapp-1.0-SNAPSHOT.jar` ficheiro é um frasco de uber. Contém todas as dependências necessárias para executar a aplicação.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Faça upload do JAR e gere postos de trabalho (SSH)

Os seguintes `scp` passos são utilizados para copiar o JAR para o nó principal da cabeça do seu Apache HBase no cluster HDInsight. O `ssh` comando é então utilizado para ligar ao cluster e executar o exemplo diretamente no nó da cabeça.

1. Faça upload do jarro para o aglomerado. Substitua-o `CLUSTERNAME` pelo nome do cluster HDInsight e introduza o seguinte comando:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Ligue-se ao cluster HBase. Substitua-o `CLUSTERNAME` pelo nome do cluster HDInsight e introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Para criar uma tabela HBase utilizando a aplicação Java, utilize o seguinte comando na sua ligação aberta ssh:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Este comando cria uma tabela HBase chamada **pessoas**, e povoa-a com dados.

4. Para procurar endereços de e-mail armazenados na tabela, utilize o seguinte comando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Recebe os seguintes resultados:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Para eliminar a tabela, utilize o seguinte comando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Faça upload do JAR e faça trabalhos (PowerShell)

Os seguintes passos utilizam o módulo Azure PowerShell [AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) para carregar o JAR para o armazenamento predefinido para o seu cluster Apache HBase. Os cmdlets HDInsight são então utilizados para executar os exemplos remotamente.

1. Depois de instalar e configurar o módulo `hbase-runner.psm1`AZ, crie um ficheiro nomeado . Utilize o seguinte texto como o conteúdo desse ficheiro:

   ```powershell
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
            Connect-AzAccount
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
   ```

    Este ficheiro contém dois módulos:

   * **Add-HDInsightFile** - usado para carregar ficheiros para o cluster
   * **Start-HBaseExample** - usado para executar as classes criadas anteriormente

2. Guarde `hbase-runner.psm1` o `hbaseapp` ficheiro no diretório.

3. Registe os módulos com o Azure PowerShell. Abra uma nova janela Azure PowerShell e `CLUSTERNAME` edite o comando abaixo substituindo pelo nome do seu cluster. Em seguida, introduza os seguintes comandos:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Utilize o seguinte comando `hbaseapp-1.0-SNAPSHOT.jar` para fazer o upload do para o seu cluster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Quando solicitado, introduza o nome de login do cluster (administrador) e a palavra-passe. O comando envia `hbaseapp-1.0-SNAPSHOT.jar` o `example/jars` local para o local no armazenamento primário para o seu cluster.

5. Para criar uma `hbaseapp`tabela utilizando o seguinte comando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Quando solicitado, introduza o nome de login do cluster (administrador) e a palavra-passe.

    Este comando cria uma tabela chamada **pessoas** em HBase no seu cluster HDInsight. Este comando não mostra qualquer saída na janela da consola.

6. Para procurar entradas na tabela, utilize o seguinte comando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Quando solicitado, introduza o nome de login do cluster (administrador) e a palavra-passe.

    Este comando `SearchByEmail` utiliza a classe para procurar `contactinformation` quaisquer `email` linhas onde a `contoso.com`família da coluna e a coluna contenham a corda . Deve receber os seguintes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Utilizar **fabrikam.com** `-emailRegex` para o valor devolve os utilizadores que **fabrikam.com** no campo de email. Também pode utilizar expressões regulares como termo de pesquisa. Por exemplo, **^r** devolve endereços de e-mail que começam com a letra 'r'.

7. Para eliminar a tabela, utilize o seguinte comando:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Não há resultados ou resultados inesperados ao utilizar o Start-HBaseExample

Utilize `-showErr` o parâmetro para visualizar o erro padrão (STDERR) que é produzido durante o funcionamento do trabalho.

## <a name="next-steps"></a>Passos seguintes

[Saiba como usar o SQLLine com Apache HBase](apache-hbase-query-with-phoenix.md)
