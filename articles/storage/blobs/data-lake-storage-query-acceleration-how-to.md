---
title: Filtrar dados utilizando a aceleração da consulta de armazenamento de dados do Azure (pré-visualização) [ Microsoft Docs
description: Utilize a aceleração da consulta (pré-visualização) para recuperar um subconjunto de dados da sua conta de armazenamento.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: b71d4e0eab0c1c1a7cfaf6e5bf9169776308fd3b
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583856"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrar dados utilizando a aceleração da consulta de armazenamento do lago de dados do Azure (pré-visualização)

Este artigo mostra-lhe como utilizar a aceleração da consulta (pré-visualização) para recuperar um subconjunto de dados da sua conta de armazenamento. 

A aceleração da consulta (pré-visualização) é uma nova capacidade para o Armazenamento de Lagos de Dados Azure que permite que aplicações e quadros de análise otimizem drasticamente o processamento de dados, recuperando apenas os dados que necessitam para realizar uma determinada operação. Para saber mais, consulte a Aceleração da Consulta de Armazenamento do [Lago de Dados azure (pré-visualização)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> A funcionalidade de aceleração da consulta está em pré-visualização pública, e está disponível nas regiões centrais do Canadá e da França Central. Para rever as limitações, consulte o artigo sobre [questões conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Pré-requisitos

### <a name="net"></a>[.NET](#tab/dotnet)

- Para aceder ao Armazenamento Azure, necessitará de uma subscrição Azure. Se ainda não tem uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento **v2 de uso geral.** ver [Criar uma conta de armazenamento.](../common/storage-quickstart-create-account.md)

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Para aceder ao Armazenamento Azure, necessitará de uma subscrição Azure. Se ainda não tem uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento **v2 de uso geral.** ver [Criar uma conta de armazenamento.](../common/storage-quickstart-create-account.md)

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) versão 8 ou superior.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > Este artigo assume que criou um projeto Java usando Apache Maven. Para um exemplo de como criar um projeto utilizando Apache Maven, consulte a [Configuração](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Instalar pacotes 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Descarregue os pacotes de aceleração de consultas. Pode obter um ficheiro .zip comprimido que contenha estes [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)pacotes utilizando este link: . 

2. Extraio o conteúdo deste ficheiro para o seu diretório de projeto.

3. Abra o seu ficheiro de projeto *(.csproj)* num editor de \<\> texto e adicione estas referências de pacote dentro do elemento Project.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Restaure os pacotes SDK de pré-visualização. Este comando de exemplo restaura as embalagens `dotnet restore` SDK de pré-visualização utilizando o comando. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Restaurar todas as outras dependências do repositório público NuGet.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Crie diretório na raiz do seu projeto. O diretório raiz é o diretório que contém o ficheiro **pom.xml.**

   > [!NOTE]
   > Os exemplos deste artigo assumem que o nome do diretório é **lib**.

2. Descarregue os pacotes de aceleração de consultas. Pode obter um ficheiro .zip comprimido que contenha estes [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)pacotes utilizando este link: . 

3. Extraios os ficheiros neste ficheiro .zip para o diretório que criaste. No nosso exemplo, esse diretório chama-se **lib.** 

4. Abra o ficheiro *pom.xml* no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Adicionar declarações


### <a name="net"></a>[.NET](#tab/dotnet)

Adicione `using` estas declarações ao topo do seu ficheiro de código.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

A aceleração da consulta recupera os dados formatados CSV e Json. Por isso, certifique-se de adicionar declarações usando quaisquer bibliotecas de análise CSV ou Json que escolha utilizar. Os exemplos que aparecem neste artigo analisam um ficheiro CSV utilizando a biblioteca [CsvHelper](https://www.nuget.org/packages/CsvHelper/) que está disponível no NuGet. Portanto, adicionaríamos estas `using` declarações ao topo do ficheiro de código.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Para compilar exemplos apresentados neste artigo, também `using` terá de adicionar estas declarações.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Adicione `import` estas declarações ao topo do seu ficheiro de código.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Recuperar dados utilizando um filtro

Pode utilizar o SQL para especificar os predicados do filtro de linha e as projeções da coluna num pedido de aceleração de consulta. O código seguinte consulta um ficheiro CSV no armazenamento e devolve todas `Hemingway, Ernest`as linhas de dados onde a terceira coluna corresponde ao valor . 

- Na consulta SQL, a `BlobStorage` palavra-chave é usada para denotar o ficheiro que está a ser consultado.

- As referências da `_N` coluna são especificadas como onde está `_1`a primeira coluna . Se o ficheiro de origem contiver uma linha de cabeçalho, então pode referir-se às colunas pelo nome especificado na linha do cabeçalho. 

### <a name="net"></a>[.NET](#tab/dotnet)

O método `BlobQuickQueryClient.QueryAsync` de asincronização envia a consulta para a API de aceleração da consulta, e depois transmite os resultados de volta para a aplicação como um objeto [Stream.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

O `BlobQuickQueryClient.openInputStream()` método envia a consulta para a API de aceleração da consulta `InputStream` e, em seguida, transmite os resultados de volta para a aplicação como um objeto que pode ser lido como qualquer outro objeto InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Recuperar colunas específicas

Pode examinar os seus resultados num subconjunto de colunas. Assim recupera-se apenas as colunas necessárias para realizar um determinado cálculo. Isto melhora o desempenho da aplicação e reduz o custo porque menos dados são transferidos para a rede. 

Este código recupera `PublicationYear` apenas a coluna para todos os livros do conjunto de dados. Também utiliza a informação da linha do cabeçalho no ficheiro fonte para colunas de referência na consulta.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

O código seguinte combina filtragem de linhas e projeções de colunas na mesma consulta. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Passos seguintes

- [Formulário de inscrição de aceleração de consulta](https://aka.ms/adls/qa-preview-signup)    
- [Aceleração da consulta de armazenamento do lago Azure Data (pré-visualização)](data-lake-storage-query-acceleration.md)
- [Referência linguística SQL de aceleração de consulta (pré-visualização)](query-acceleration-sql-reference.md)
