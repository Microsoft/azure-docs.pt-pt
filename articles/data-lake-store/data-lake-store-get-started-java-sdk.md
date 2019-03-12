---
title: 'Java SDK: Operações de sistema de ficheiros em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft'
description: Utilização do Azure Storage Gen1 Java SDK do Data Lake para executar operações de sistema de ficheiros no Data Lake Storage Gen1, como criar pastas, etc.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: e84e84aac3aca0458dea4f30f6b0e222aafd9d44
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530301"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Operações do sistema de ficheiros em Gen1 de armazenamento do Azure Data Lake com o SDK de Java
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Saiba como utilizar o SDK de Java de geração 1 de armazenamento do Azure Data Lake para executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, etc. Para obter mais informações sobre a geração 1 de armazenamento do Data Lake, veja [Gen1 de armazenamento do Azure Data Lake](data-lake-store-overview.md).

Pode acessar os documentos de API do SDK Java para geração 1 de armazenamento do Data Lake na [documentos da API de Java do Azure Data Lake armazenamento Gen1](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Pré-requisitos
* Kit de Desenvolvimento do Java (JDK 7 ou superior, com Java versão 1.7 ou superior)
* Conta do Data Lake Gen1 de armazenamento. Siga as instruções em [introdução à geração 1 de armazenamento do Azure Data Lake com o portal do Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível compilar sem utilizar um sistema de compilação como Maven ou Gradle, estes sistemas facilitam muito a gestão das dependências.
* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="create-a-java-application"></a>Criar uma aplicação Java
O exemplo de código disponível no [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) explica-lhe o processo de criação de ficheiros no arquivo, de concatenação de ficheiros, de transferência de um ficheiro e de eliminação de alguns ficheiros do arquivo. Esta secção do artigo explica-lhe as partes principais do código.

1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte fragmento antes da etiqueta **\</project>**:
   
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
   
    A primeira dependência é para utilizar o SDK de geração 1 do Data Lake Storage (`azure-data-lake-store-sdk`) do repositório maven. A segunda dependência é para especificar a arquitetura de registo (`slf4j-nop`) a utilizar para esta aplicação. Utiliza o SDK de geração 1 de armazenamento do Data Lake [slf4j](https://www.slf4j.org/) fachada de Registro em log, que permite que escolha a partir de um número de arquiteturas de registo mais populares, como log4j, registo Java, logback, etc., ou nenhum registo. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](https://www.slf4j.org/manual.html#projectDep).

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

## <a name="authentication"></a>Authentication

* Para a autenticação de utilizador final para a sua aplicação, veja [finais autenticação de utilizador com a geração 1 de armazenamento do Data Lake com o Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Para a autenticação de serviço a serviço para a sua aplicação, veja [autenticação do serviço a serviço com geração 1 de armazenamento do Data Lake com o Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Criar um cliente de geração 1 de armazenamento do Data Lake
Criar uma [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) objeto requer que especifique o nome da conta de geração 1 do Data Lake Storage e o fornecedor do token que gerou durante a autenticação com a geração 1 de armazenamento do Data Lake (consulte [autenticação](#authentication) secção). O nome da conta de geração 1 de armazenamento do Data Lake tem de ser um nome de domínio completamente qualificado. Por exemplo, substitua **FILL-IN-HERE** com algo como **mydatalakestoragegen1.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Os fragmentos de código nas secções seguintes contêm exemplos de algumas operações comuns do sistema de ficheiros. Pode ver o completo [documentos da API de SDK do Java do Data Lake armazenamento Gen1](https://azure.github.io/azure-data-lake-store-java/javadoc/) da **ADLStoreClient** objeto para ver outras operações.

## <a name="create-a-directory"></a>Criar um diretório

O fragmento seguinte cria uma estrutura de diretórios na raiz da conta do Data Lake Storage Gen1 que especificou.

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

O fragmento seguinte lê o conteúdo de um ficheiro numa conta de geração 1 de armazenamento do Data Lake.

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

O fragmento seguinte concatena dois ficheiros numa conta de geração 1 de armazenamento do Data Lake. Se for efetuado com êxito, o ficheiro concatenado substitui os dois ficheiros existentes.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro

O fragmento seguinte muda o nome de um ficheiro numa conta de geração 1 de armazenamento do Data Lake.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Obter metadados para um ficheiro

O fragmento seguinte obtém os metadados para um ficheiro numa conta de geração 1 de armazenamento do Data Lake.

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

## <a name="delete-files-and-folders"></a>Elimine ficheiros e pastas

O fragmento seguinte elimina os ficheiros e pastas especificados numa conta do Data Lake Storage Gen1, recursivamente.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação
1. Para executar a partir de um IDE, localize e prima o botão **Executar**. Para executar a partir do Maven, utilize [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Para produzir um jar autónomo que pode executar a partir da linha de comandos, compile um jar com todas as dependências incluídas com o [plug-in de montagem do Maven](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html). O pom. XML no [código de fonte de exemplo no GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tem um exemplo.

## <a name="next-steps"></a>Passos Seguintes
* [Explorar JavaDoc para o SDK Java](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)


