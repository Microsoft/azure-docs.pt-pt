---
title: Java SDK - Operações do sistema de ficheiros em Data Lake Storage Gen1 - Azure
description: Utilize o Java SDK para Azure Data Lake Storage Gen1 para efetuar operações de sistema de ficheiros em Data Lake Storage Gen1, como criar pastas e carregar e descarregar ficheiros de dados.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6f97443e4bcf6689f0bf49917774f662d5462566
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691779"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Operações do sistema de ficheiros no Azure Data Lake Storage Gen1 utilizando java SDK
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Saiba como utilizar o Azure Data Lake Storage Gen1 Java SDK para realizar operações básicas, tais como criar pastas, carregar e descarregar ficheiros de dados, etc. Para mais informações sobre data Lake Storage Gen1, consulte [Azure Data Lake Storage Gen1](data-lake-store-overview.md).

Você pode aceder aos docs da API Java SDK para data lake storage Gen1 at [Azure Data Lake Storage Gen1 Java API docs](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Pré-requisitos
* Kit de Desenvolvimento do Java (JDK 7 ou superior, com Java versão 1.7 ou superior)
* Conta Gen1 de Armazenamento de Data Lake. Siga as instruções no [Get started com Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md).
* [Maven.](https://maven.apache.org/install.html) Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível compilar sem utilizar um sistema de compilação como Maven ou Gradle, estes sistemas facilitam muito a gestão das dependências.
* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="create-a-java-application"></a>Criar uma aplicação Java
O exemplo de código disponível no [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) explica-lhe o processo de criação de ficheiros no arquivo, de concatenação de ficheiros, de transferência de um ficheiro e de eliminação de alguns ficheiros do arquivo. Esta secção do artigo explica-lhe as partes principais do código.

1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte corte antes da ** \<etiqueta>/projeto:**
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    A primeira dependência é usar o Data Lake Storage`azure-data-lake-store-sdk`Gen1 SDK () do repositório maven. A segunda dependência é para especificar a arquitetura de registo (`slf4j-nop`) a utilizar para esta aplicação. O Data Lake Storage Gen1 SDK utiliza fachada de exploração madeireira [SLF4J,](https://www.slf4j.org/) que permite escolher entre uma série de estruturas de exploração madeireira populares, como Log4j, Java logging, Logback, etc., ou sem registo. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](https://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes declarações de importação à aplicação.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Autenticação

* Para autenticação de utilizador final para a sua aplicação, consulte a [autenticação de utilizador final com data lake storage Gen1 utilizando Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Para autenticação de serviço a serviço para a sua aplicação, consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 usando Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Criar um cliente Gen1 de Armazenamento de Data Lake
A criação de um objeto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) requer que especifique o nome da conta Gen1 de Armazenamento de Data Lake e o fornecedor simbólico que gerou quando autenticou com data Lake Storage Gen1 (ver secção [de autenticação).](#authentication) O nome da conta Data Lake Storage Gen1 tem de ser um nome de domínio totalmente qualificado. Por exemplo, substitua **o FILL-IN-HERE** por algo como **mydatalakestoragegen1.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Os fragmentos de código nas secções seguintes contêm exemplos de algumas operações comuns do sistema de ficheiros. Pode ver os [docs completos de Data Lake Storage Gen1 Java SDK API](https://azure.github.io/azure-data-lake-store-java/javadoc/) do objeto **ADLStoreClient** para ver outras operações.

## <a name="create-a-directory"></a>Criar um diretório

O seguinte corte cria uma estrutura de diretório na raiz da conta Data Lake Storage Gen1 que especificou.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Criar um ficheiro

O fragmento seguinte cria um ficheiro (c.txt) na estrutura de diretórios e escreve alguns dados no ficheiro.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Também pode criar um ficheiro (d.txt) através de matrizes de bytes.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

A definição da função `getSampleContent` utilizada no fragmento anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Acrescentar a um ficheiro

O fragmento seguinte acrescenta conteúdo a um ficheiro existente.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

A definição da função `getSampleContent` utilizada no fragmento anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Ler um ficheiro

O seguinte corte lê o conteúdo de um ficheiro numa conta gen1 de armazenamento de data lake.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Concatenar ficheiros

O seguinte corte concatena dois ficheiros numa conta de Data Lake Storage Gen1. Se for efetuado com êxito, o ficheiro concatenado substitui os dois ficheiros existentes.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro

O seguinte corte renomea um ficheiro numa conta gen1 de armazenamento de data lake.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Obter metadados para um ficheiro

O seguinte fragmento recupera os metadados para um ficheiro numa conta gen1 de armazenamento de data lake.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Definir permissões num ficheiro

O fragmento seguinte define as permissões do ficheiro que criou na secção anterior.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

O fragmento seguinte lista o conteúdo de um diretório, em modo recursivo.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

A definição da função `printDirectoryInfo` utilizada no fragmento anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Eliminar ficheiros e pastas

O seguinte corte elimina os ficheiros e pastas especificados numa conta Gen1 de Armazenamento de Data Lake, recursivamente.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação
1. Para executar a partir de um IDE, localize e prima o botão **Executar**. Para executar a partir do Maven, utilize [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Para produzir um jar autónomo que pode executar a partir da linha de comandos, compile um jar com todas as dependências incluídas com o [plug-in de montagem do Maven](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html). O pom.xml no código fonte de [exemplo no GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tem um exemplo.

## <a name="next-steps"></a>Passos seguintes
* [Explorar JavaDoc para o SDK Java](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)


