---
title: Filtrar os dados utilizando a aceleração da consulta de armazenamento do lago de dados Azure | Microsoft Docs
description: Utilize a aceleração da consulta para recuperar um subconjunto de dados da sua conta de armazenamento.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a925d3f55395d094c7f19f65de4b72fd20a11a41
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213679"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrar dados utilizando a aceleração da consulta de armazenamento do lago de dados Azure

Este artigo mostra-lhe como utilizar a aceleração da consulta para recuperar um subconjunto de dados da sua conta de armazenamento. 

A aceleração de consultas permite que aplicações e quadros de análise otimizem dramaticamente o processamento de dados, recuperando apenas os dados que necessitam para realizar uma determinada operação. Para saber mais, consulte [a aceleração da consulta de armazenamento do lago de dados Azure.](data-lake-storage-query-acceleration.md)

## <a name="prerequisites"></a>Pré-requisitos

- Para aceder ao Azure Storage, precisará de uma subscrição do Azure. Se ainda não tiver uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento **v2 para fins gerais.** ver [Criar uma conta de armazenamento](../common/storage-account-create.md).

- Escolha um separador para visualizar quaisquer pré-requisitos específicos da SDK.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Não aplicável

  ### <a name="net"></a>[.NET](#tab/dotnet)

  O [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/) versão 8 ou superior

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > Este artigo pressupõe que criou um projeto Java usando Apache Maven. Para um exemplo de como criar um projeto utilizando o Apache Maven, consulte [Configuração](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 ou maior.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Não existem pré-requisitos adicionais necessários para utilizar o Node.js SDK.

---

## <a name="enable-query-acceleration"></a>Permitir aceleração de consultas

Para utilizar a aceleração da consulta, tem de registar a função de aceleração de consulta com a sua subscrição. Uma vez verificada a funcionalidade, tem de registar o fornecedor de recursos Azure Storage. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Passo 1: Registar a função de aceleração de consulta

Para utilizar a aceleração da consulta, tem primeiro de registar a função de aceleração de consulta com a sua subscrição. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Abra uma janela de comando Windows PowerShell.

1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estale a sua subscrição ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

3. Registe a função de aceleração de consulta utilizando o comando [Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md), ou se [instalou](/cli/azure/install-azure-cli) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estale a sua subscrição ativa para a subscrição da conta de armazenamento.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

3. Registe a função de aceleração de consulta utilizando o comando [de registo de funcionalidades az.](/cli/azure/feature#az-feature-register)

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Passo 2: Verifique se a funcionalidade está registada

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar se o registo está completo, utilize o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar se o registo está completo, utilize o comando [de recurso az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Passo 3: Registar o fornecedor de recursos de armazenamento Azure

Após a aprovação do seu registo, deve voltar a registar o fornecedor de recursos de armazenamento Azure. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para registar o fornecedor de recursos, utilize o comando [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para registar o fornecedor de recursos, utilize o comando [de registo do fornecedor az.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Configurar o ambiente

### <a name="step-1-install-packages"></a>Passo 1: Instalar pacotes 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Instale a versão do módulo Az 4.6.0 ou superior.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Para atualizar a partir de uma versão mais antiga de Az, executar o seguinte comando:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Abra uma solicitação de comando e altere o diretório `cd` () na pasta do projeto Por exemplo:

   ```console
   cd myProject
   ```

2. Instale a `12.5.0-preview.6` versão ou posteriormente da biblioteca do cliente de armazenamento Azure Blob para o pacote .NET utilizando o `dotnet add package` comando. 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. Os exemplos que aparecem neste artigo analisam um ficheiro CSV utilizando a biblioteca [CsvHelper.](https://www.nuget.org/packages/CsvHelper/) Para utilizar esta biblioteca, utilize o seguinte comando.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Abra o arquivo *pom.xml* do seu projeto num editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Instale a biblioteca de clientes Azure Data Lake Storage para Python utilizando [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Instale a biblioteca do cliente do Data Lake para o JavaScript abrindo uma janela de terminal e, em seguida, digitando o seguinte comando.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Passo 2: Adicionar declarações

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Não aplicável

#### <a name="net"></a>[.NET](#tab/dotnet)

Adicione estas `using` declarações ao topo do seu ficheiro de código.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

A aceleração da consulta recupera dados formatados de CSV e Json. Por isso, certifique-se de adicionar declarações para quaisquer bibliotecas de análise CSV ou Json que você escolher usar. Os exemplos que aparecem neste artigo analisam um ficheiro CSV utilizando a biblioteca [CsvHelper](https://www.nuget.org/packages/CsvHelper/) que está disponível no NuGet. Portanto, adicionamos estas `using` declarações ao topo do ficheiro de código.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Para compilar exemplos apresentados neste artigo, também terá de adicionar estas `using` declarações.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Adicione estas `import` declarações ao topo do seu ficheiro de código.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Adicione estas declarações de importação ao topo do seu ficheiro de código.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Inclua o `storage-blob` módulo colocando esta declaração na parte superior do seu ficheiro de código. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

A aceleração da consulta recupera dados formatados de CSV e Json. Por isso, certifique-se de adicionar declarações para quaisquer módulos de análise CSV ou Json que escolha utilizar. Os exemplos que aparecem neste artigo analisam um ficheiro CSV utilizando o módulo [fast-csv.](https://www.npmjs.com/package/fast-csv) Portanto, adicionamos esta declaração ao topo do ficheiro de código.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Recuperar dados utilizando um filtro

Pode utilizar o SQL para especificar os predicados do filtro de linha e as projeções das colunas num pedido de aceleração de consulta. O código que se segue consulta um ficheiro CSV no armazenamento e devolve todas as linhas de dados onde a terceira coluna corresponde ao valor `Hemingway, Ernest` . 

- Na consulta SQL, a palavra-chave `BlobStorage` é usada para denotar o ficheiro que está a ser consultado.

- As referências de coluna são especificadas como `_N` onde está a primeira coluna `_1` . Se o ficheiro de origem contiver uma linha de cabeçalho, então pode consultar as colunas pelo nome especificado na linha do cabeçalho. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

O método async `BlobQuickQueryClient.QueryAsync` envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta para a aplicação como um objeto [Stream.](/dotnet/api/system.io.stream)

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

O método `BlobQuickQueryClient.openInputStream()` envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta para a aplicação como um objeto que pode ser lido como qualquer outro objeto `InputStream` InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Este exemplo envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta. O `blob` objeto transmitido para a `queryHemingway` função de ajudante é do tipo [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient). Para saber mais sobre como obter um objeto [BlockBlobClient,](/javascript/api/@azure/storage-blob/blockblobclient) consulte [Quickstart: Gerencie as bolhas com JavaScript v12 SDK em Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Recuperar colunas específicas

Pode ver os seus resultados num subconjunto de colunas. Assim, só recuperas as colunas necessárias para fazer um determinado cálculo. Isto melhora o desempenho da aplicação e reduz o custo porque menos dados são transferidos ao longo da rede. 

Este código recupera apenas a `BibNum` coluna para todos os livros do conjunto de dados. Também utiliza as informações da linha do cabeçalho no ficheiro de origem para colunas de referência na consulta.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

O código a seguir combina a filtragem de linha e as projeções das colunas na mesma consulta. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Passos seguintes

- [Aceleração da consulta de armazenamento do lago de dados Azure](data-lake-storage-query-acceleration.md)
- [Referência linguística SQL de aceleração de consulta](query-acceleration-sql-reference.md)
