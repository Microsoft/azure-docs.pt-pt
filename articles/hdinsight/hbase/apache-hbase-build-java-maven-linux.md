---
title: Criar o cliente de Java HBase utilizando o Apache Maven - Azure HDInsight
description: Saiba como utilizar o Apache Maven para criar uma aplicação baseada em Java Apache HBase, em seguida, implementá-la no HBase no HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: fa831ad878d214515849787988ccb32f6c57ce20
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361766"
---
# <a name="build-java-applications-for-apache-hbase"></a>Criar aplicações de Java para o Apache HBase

Saiba como criar uma [Apache HBase](https://hbase.apache.org/) aplicação em Java. Em seguida, utilize a aplicação com o HBase no HDInsight do Azure.

Os passos neste documentam uso [Apache Maven](https://maven.apache.org/) para criar e compilar o projeto. Maven é uma ferramenta de compreensão que permite a criação de software, documentação e relatórios para projetos de Java e o gerenciamento de projetos de software.

> [!NOTE]  
> Os passos neste documento foram testados mais recentemente com o HDInsight 3.6.

> [!IMPORTANT]  
> Os passos neste documento exigem um cluster do HDInsight que utilize o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="requirements"></a>Requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [A plataforma Java JDK](https://aka.ms/azure-jdks) 8 ou posterior.

    > [!NOTE]  
    > HDInsight 3.5 e superior requer Java 8. Versões anteriores do HDInsight requerem Java 7.

* [Apache Maven](https://maven.apache.org/)

* [Um cluster de HDInsight do Azure baseado em Linux com o Apache HBase](apache-hbase-tutorial-get-started-linux.md#create-apache-hbase-cluster)

## <a name="create-the-project"></a>Criar o projeto

1. Na linha de comando no seu ambiente de desenvolvimento, altere os diretórios para a localização onde pretende criar o projeto, por exemplo, `cd code\hbase`.

2. Utilize o **arquétipo** comando, que é instalado com o Maven, para gerar a estrutura do projeto.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]  
    > Se estiver a utilizar o PowerShell, tem de incluir o `-D` parâmetros entre aspas duplas.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Este comando cria um diretório com o mesmo nome que o **artifactID** parâmetro (**hbaseapp** neste exemplo.) Esse diretório contém os seguintes itens:

   * **pom.xml**:  O modelo de objeto de projeto ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contém os detalhes de configuração e informações usados para compilar o projeto.
   * **src**: O diretório que contém o **main/java/com/microsoft/exemplos** directory, onde criar a aplicação.

3. Eliminar o `src/test/java/com/microsoft/examples/apptest.java` ficheiro. Não é utilizado neste exemplo.

## <a name="update-the-project-object-model"></a>Atualizar o modelo de objeto de projeto

1. Editar a `pom.xml` de ficheiros e adicione o seguinte código dentro do `<dependencies>` secção:

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    Esta secção indica que o projeto precisa **hbase-cliente** e **núcleos phoenix** componentes. No momento da compilação, estas dependências são transferidas do repositório Maven padrão. Pode utilizar o [pesquisa de repositório Central Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre esta dependência.

   > [!IMPORTANT]  
   > O número de versão do cliente do hbase tem de corresponder à versão do Apache HBase, que é fornecido com o seu cluster do HDInsight. Utilize a seguinte tabela para encontrar o número de versão correta.

   | Versão de cluster do HDInsight | Versão do Apache HBase para utilizar |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3, 3.4, 3.5 e 3.6 |1.1.2 |

    Para obter mais informações sobre componentes e versões do HDInsight, consulte [quais são os diferentes componentes de Apache Hadoop disponíveis com o HDInsight](../hdinsight-component-versioning.md).

3. Adicione o seguinte código para o **pom** ficheiro. Este texto deve ser dentro de `<project>...</project>` etiquetas no ficheiro, por exemplo, entre `</dependencies>` e `</project>`.

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
                    <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
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
   ```

    Nesta secção configura um recurso (`conf/hbase-site.xml`) que contém informações de configuração para o HBase.

   > [!NOTE]  
   > Também pode definir valores de configuração por meio do código. Veja os comentários no `CreateTable` exemplo.

    Esta secção também configura a [Plug-in de compilador do Apache Maven](https://maven.apache.org/plugins/maven-compiler-plugin/) e [Plug-in do Apache Maven tom](https://maven.apache.org/plugins/maven-shade-plugin/). O compilador Plug-in é usado para compilar a topologia. A Plug-in de tonalidade é utilizada para evitar a duplicação de licença do pacote de JAR que é criado pela Maven. Este plug-in é usado para impedir que um erro de "arquivos de licença duplicados" em tempo de execução no cluster do HDInsight. Utilizar maven-tom-Plug-in com o `ApacheLicenseResourceTransformer` implementação evita que o erro.

    O maven-tom-Plug-in também produz um jar uber que contém todas as dependências exigidas pela aplicação.

4. Guarde o ficheiro `pom.xml`.

5. Criar um diretório chamado `conf` no `hbaseapp` diretório. Este diretório é utilizado para conter as informações de configuração para ligar ao HBase.

6. Utilize o comando seguinte para copiar a configuração do HBase do cluster HBase para o `conf` diretório. Substitua `USERNAME` com o nome do seu início de sessão SSH. Substitua `CLUSTERNAME` com o nome do cluster HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   Para obter mais informações sobre como usar `ssh` e `scp`, consulte [utilizar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Criar a aplicação

1. Vá para o `hbaseapp/src/main/java/com/microsoft/examples` diretório e renomeie o App de ficheiros para `CreateTable.java`.

2. Abra o `CreateTable.java` de ficheiro e substituir o conteúdo existente com o seguinte texto:

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

    Esse código é o **CreateTable** classe, que cria uma tabela denominada **pessoas** e preenchê-lo com alguns usuários predefinidos.

3. Guarde o ficheiro `CreateTable.java`.

4. Na `hbaseapp/src/main/java/com/microsoft/examples` diretório, crie um ficheiro denominado `SearchByEmail.java`. Utilize o seguinte texto como o conteúdo desse ficheiro:

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

    O **SearchByEmail** classe pode ser usada para consultar os linhas por endereço de e-mail. Uma vez que ele utiliza um filtro de expressão regular, pode fornecer uma cadeia de caracteres ou uma expressão regular quando usando a classe.

5. Guarde o ficheiro `SearchByEmail.java`.

6. Na `hbaseapp/src/main/hava/com/microsoft/examples` diretório, crie um ficheiro denominado `DeleteTable.java`. Utilize o seguinte texto como o conteúdo desse ficheiro:

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

    Essa classe limpa as tabelas de HBase criadas neste exemplo, ao desativar e remover a tabela criada pelo `CreateTable` classe.

7. Guarde o ficheiro `DeleteTable.java`.

## <a name="build-and-package-the-application"></a>Criar e empacotar a aplicação

1. Do `hbaseapp` directory, utilize o seguinte comando para criar um ficheiro JAR que contém a aplicação:

    ```bash
    mvn clean package
    ```

    Este comando cria e empacota o aplicativo para um ficheiro. JAR.

2. Quando o comando for concluído, o `hbaseapp/target` diretório contém um arquivo chamado `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > O `hbaseapp-1.0-SNAPSHOT.jar` ficheiro é um jar uber. Ele contém todas as dependências necessárias para executar a aplicação.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Carregar o JAR e executar tarefas (SSH)

Os passos seguintes utilizam `scp` para copiar o JAR para o nó principal primário da sua Apache HBase num cluster do HDInsight. O `ssh` comando, em seguida, é utilizado para ligar ao cluster e execute o exemplo diretamente no nó principal.

1. Para carregar o jar para o cluster, utilize o seguinte comando:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Substitua `USERNAME` com o nome do seu início de sessão SSH. Substitua `CLUSTERNAME` com o nome de cluster do HDInsight.

2. Para ligar ao cluster do HBase, utilize o seguinte comando:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua `USERNAME` o nome do seu início de sessão SSH. Substitua `CLUSTERNAME` com o nome de cluster do HDInsight.

3. Para criar uma tabela de HBase utilizando a aplicação de Java, utilize o seguinte comando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Este comando cria uma tabela do HBase com o nome **pessoas**e a preenche com dados.

4. Para procurar armazenados na tabela de endereços de e-mail, utilize o seguinte comando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Receber os seguintes resultados:

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

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Carregar o JAR e executar tarefas (PowerShell)

Os passos seguintes utilizam o Azure PowerShell para carregar o JAR para o armazenamento predefinido para o seu cluster do Apache HBase. Cmdlets do HDInsight, em seguida, são utilizados para executar os exemplos remotamente.

1. Depois de instalar e configurar o Azure PowerShell, crie um ficheiro denominado `hbase-runner.psm1`. Utilize o seguinte texto como o conteúdo desse ficheiro:

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
   ```

    Este ficheiro contém dois módulos:

   * **Adicionar-HDInsightFile** - utilizado para carregar ficheiros para o cluster
   * **Start-HBaseExample** - utilizado para executar as classes que criou anteriormente

2. Guarde o ficheiro `hbase-runner.psm1`.

3. Abra uma nova janela do PowerShell do Azure, altere os diretórios para o `hbaseapp` directory e, em seguida, execute o seguinte comando:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Altere o caminho para a localização do `hbase-runner.psm1` arquivo criado anteriormente. Este comando regista o módulo com o Azure PowerShell.

4. Utilize o seguinte comando para carregar o `hbaseapp-1.0-SNAPSHOT.jar` ao seu cluster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Substitua `hdinsightclustername` pelo nome do cluster. Quando lhe for pedido, introduza o nome de início de sessão (admin) do cluster e a palavra-passe. O comando carrega o `hbaseapp-1.0-SNAPSHOT.jar` para o `example/jars` localização no armazenamento primário para o seu cluster.

5. Para criar uma tabela com o `hbaseapp`, utilize o seguinte comando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Substitua `hdinsightclustername` pelo nome do cluster. Quando lhe for pedido, introduza o nome de início de sessão (admin) do cluster e a palavra-passe.

    Este comando cria uma tabela chamada **pessoas** no HBase no seu cluster do HDInsight. Este comando não mostra os resultados na janela da consola.

6. Para procurar entradas na tabela, utilize o seguinte comando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Substitua `hdinsightclustername` pelo nome do cluster. Quando lhe for pedido, introduza o nome de início de sessão (admin) do cluster e a palavra-passe.

    Este comando utiliza o `SearchByEmail` classe para pesquisar quaisquer linhas em que o `contactinformation` família de colunas e o `email` coluna, contém a cadeia de caracteres `contoso.com`. Deverá receber os seguintes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Usando **fabrikam.com** para o `-emailRegex` valor devolve os utilizadores que tenham **fabrikam.com** no campo de e-mail. Também pode usar expressões regulares como o termo de pesquisa. Por exemplo, **^ r** devolve endereços que comecem com a letra 'r' de e-mail.

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Não existem resultados ou resultados inesperados ao utilizar o início HBaseExample

Utilize o `-showErr` parâmetro para ver o erro padrão (STDERR) que é produzido durante a execução da tarefa.

## <a name="delete-the-table"></a>Eliminar tabela

Quando tiver terminado com o exemplo, utilize o seguinte para eliminar a **pessoas** tabela usada neste exemplo:

__De um `ssh` sessão__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Do PowerShell do Azure__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>Passos Seguintes

[Saiba como utilizar o SQuirreL SQL com o Apache HBase](apache-hbase-phoenix-squirrel-linux.md)
