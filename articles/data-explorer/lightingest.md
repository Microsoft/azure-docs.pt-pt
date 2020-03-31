---
title: LightIngest é um utilitário de linha de comando para ingestão no Azure Data Explorer.
description: Saiba mais sobre lightIngest, um utilitário de linha de comando para ingestão de dados ad-hoc no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 99517e45892cd7a6167ae83ff3058edae1377b10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109566"
---
# <a name="install-and-use-lightingest"></a>Instale e use o LightIngest

LightIngest é um utilitário de linha de comando para ingestão de dados ad-hoc no Azure Data Explorer.
O utilitário pode retirar dados de origem de uma pasta local ou de um recipiente de armazenamento de blob Azure.

## <a name="prerequisites"></a>Pré-requisitos

* LightIngest - descarregue-o como parte do [pacote Microsoft.Azure.Kusto.Tools NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)
* WinRAR - descarregue-o a partir de [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Instalar o LightIngest

1. Navegue para o local no seu computador onde descarregou LightIngest. 
1. Utilizando o WinRAR, extraa o diretório de *ferramentas* para o seu computador.

## <a name="run-lightingest"></a>Executar LightIngest

1. Navegue para o diretório de *ferramentas* extraídas no seu computador.
1. Elimine as informações de localização existentes da barra de localização.
    
      ![Eliminar informações de localização](media/lightingest/lightingest-location-bar.png)

1. Introduza `cmd` e prima **Enter**.
1. No pedido de `LightIngest.exe` comando, entre seguido pelo argumento da linha de comando relevante.

    > [!Tip]
    > Para obter uma lista de argumentos de `LightIngest.exe /help`linha de comando suportados, insira .
    >
    >![Ajuda da linha de comando](media/lightingest/lightingest-cmd-line-help.png)

1. Introduza `LightIngest` a cadeia de ligação ao cluster Azure Data Explorer que irá gerir a ingestão.
    Encerre a cadeia de ligação em citações duplas e siga a especificação das cordas de [ligação Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto).

    Por exemplo:
    ```
    LightIngest "Data Source=https://{Cluster name and region}.kusto.windows.net;AAD Federated Security=True"  -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* O método recomendado `LightIngest` é trabalhar com o ponto `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`final da ingestão em . Desta forma, o serviço Azure Data Explorer pode gerir a carga de ingestão, e você pode facilmente recuperar de erros transitórios. No entanto, também `LightIngest` pode configurar para trabalhar`https://{yourClusterNameAndRegion}.kusto.windows.net`diretamente com o ponto final do motor ( ).
* Para um ótimo desempenho de ingestão, é importante que a `LightIngest` LightIngest conheça o tamanho dos dados brutos e, por isso, irá estimar o tamanho não comprimido dos ficheiros locais. No `LightIngest` entanto, pode não ser capaz de estimar corretamente o tamanho bruto das bolhas comprimidos sem primeiro descarregá-las. Por isso, ao ingerir bolhas comprimidos, `rawSizeBytes` coloque a propriedade nos metadados blob para o tamanho dos dados não comprimidos em bytes.

## <a name="general-command-line-arguments"></a>Argumentos gerais da linha de comando

|Nome do argumento         |Nome abreviado   |Tipo    |Obrigatório |Descrição                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |string  |Obrigatório |Cadeia de ligação do explorador de [dados Azure](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto) especificando o ponto final de Kusto que irá lidar com a ingestão. Deve ser incluído em orçamentos duplos |
|-base de dados             |-db          |string  |Opcional  |Nome da base de dados target Azure Data Explorer |
|-mesa                |             |string  |Obrigatório |Nome da tabela target Azure Data Explorer |
|-fonteCaminho           |-fonte      |string  |Obrigatório |Caminho para ficheiros de origem ou uri raiz do recipiente de bolhas. Se os dados estiverem em blocos, deve conter a chave da conta de armazenamento ou o SAS. Recomendado para encerrar em cotações duplas |
|-prefixo               |             |string  |Opcional  |Quando os dados de origem para ingerir residem no armazenamento de bolhas, este prefixo URL é partilhado por todas as bolhas, excluindo o nome do recipiente. <br>Por exemplo, se os `MyContainer/Dir1/Dir2`dados estiverem dentro, então o prefixo deve ser `Dir1/Dir2`. É recomendado o encerramento em orçamentos duplos |
|-padrão              |             |string  |Opcional  |Padrão pelo qual os ficheiros/bolhas de origem são colhidos. Suporta wildcards. Por exemplo, `"*.csv"`. Recomendado para encerrar em cotações duplas |
|-zipPattern           |             |string  |Opcional  |Expressão regular a utilizar ao selecionar quais ficheiros num arquivo ZIP para ingerir.<br>Todos os outros ficheiros no arquivo serão ignorados. Por exemplo, `"*.csv"`. É recomendado rodeá-lo em citações duplas |
|-formato               |-f           |string  |Opcional  |Formato de dados de origem. Deve ser um dos [formatos suportados](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#supported-data-formats) |
|-ingestionMappingPath |-mappingPath |string  |Opcional  |Caminho para o ficheiro de mapeamento de colunas de ingestão (obrigatório para os formatos Json e Avro). Ver [mapeamento de dados](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-ingestionMappingRef  |-mapeamentoRef  |string  |Opcional  |Nome de um mapeamento de coluna de ingestão pré-criado (obrigatório para os formatos Json e Avro). Ver [mapeamento de dados](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-criaçãoTimePattern  |             |string  |Opcional  |Quando definido, é utilizado para extrair a propriedade CreationTime do caminho do ficheiro ou blob. Ver [usar o argumento CreationTimePattern](#using-creationtimepattern-argument) |
|-ignorarFirstRow       |-ignorarPrimeiro |bool    |Opcional  |Se definido, o primeiro registo de cada ficheiro/bolha é ignorado (por exemplo, se os dados de origem tiver cabeçalhos) |
|-tag                  |             |string  |Opcional  |[Etiquetas](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) para associar aos dados ingeridos. Múltiplas ocorrências são permitidas |
|-dontWait             |             |bool    |Opcional  |Se for definido como "verdadeiro", não aguarda a conclusão da ingestão. Útil ao ingerir grandes quantidades de ficheiros/bolhas |

### <a name="using-creationtimepattern-argument"></a>Usando o argumento CreationTimePattern

O `-creationTimePattern` argumento extrai a propriedade CreationTime do caminho do arquivo ou blob. O padrão não precisa de refletir todo o caminho do item, apenas a secção que encerra o carimbo temporal que pretende utilizar.
O valor do argumento deve conter três secções:
* Teste constante imediatamente anterior à marca de tempo, incluído em cotações únicas
* O formato timestamp, na notação padrão [.NET DateTime](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Texto constante imediatamente a seguir à marca de tempo Por exemplo, se os nomes blob terminarem com "valores históricos19840101.parquet" (a marca de tempo `-creationTimePattern` é de quatro dígitos para o ano, dois dígitos para o mês e dois dígitos para o dia do mês), o valor correspondente para o argumento é 'valores históricos'yyymMdd'.parquet'.

### <a name="command-line-arguments-for-advanced-scenarios"></a>Argumentos de linha de comando para cenários avançados

|Nome do argumento         |Nome abreviado   |Tipo    |Obrigatório |Descrição                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-compressão          |-cr          |double  |Opcional  |Dica de relação de compressão. Útil ao ingerir ficheiros/bolhas comprimidos para ajudar o Azure Data Explorer a avaliar o tamanho dos dados brutos. Calculado como tamanho original dividido pelo tamanho comprimido |
|-limite                |-l           |número inteiro |Opcional  |Se definido, limita a ingestão aos primeiros ficheiros N |
|-listaApenas             |-lista        |bool    |Opcional  |Se definido, apenas exibe os itens que teriam sido selecionados para ingestão| 
|-ingestTimeout        |             |número inteiro |Opcional  |Tempo de tempo em minutos para todas as operações de ingerir conclusão. Incumprimentos para`60`|
|-forceSync            |             |bool    |Opcional  |Se definido, força a ingestão sincronizada. Incumprimentos para`false` |
|-dataBatchSize        |             |número inteiro |Opcional  |Define o limite de tamanho total (MB, descomprimido) de cada operação ingerir |
|-filesInBatch         |             |número inteiro |Opcional  |Define o limite de contagem de ficheiros/bolha de cada operação ingerir |
|-devTracing           |-vestígios       |string  |Opcional  |Se definidos, os registos de diagnóstico são escritos a um diretório local (por predefinição, `RollingLogs` no diretório atual, ou podem ser modificados definindo o valor do interruptor) |

## <a name="blob-metadata-properties"></a>Propriedades dos metadados blob
Quando utilizado com bolhas Azure, `LightIngest` utilizará certas propriedades de metadados blob para aumentar o processo de ingestão.

|Propriedade de metadados                            | Utilização                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Se definido, será interpretado como o tamanho de dados não comprimidos                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Interpretado como carimbo de tempo UTC. Se definido, será usado para anular o tempo de criação em Kusto. Útil para cenários de enchimento de costas |

## <a name="usage-examples"></a>Exemplos de utilização

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Ingerir bolhas utilizando uma chave de conta de armazenamento ou um token SAS

* Ingerir 10 bolhas sob conta `ACCOUNT`de armazenamento `DIR`especificada, em pasta, debaixo do recipiente, `CONT`e combinando o padrão`*.csv.gz`
* Destino é `DB`base `TABLE`de dados, tabela, e o mapeamento `MAPPING` de ingestão é pré-criado no destino
* A ferramenta vai esperar até que as operações de ingerir completem
* Note as diferentes opções para especificar a base de dados-alvo e a chave da conta de armazenamento vs. Token SAS

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Ingerir todas as bolhas num recipiente, sem incluir as linhas de cabeçalho

* Ingerir todas as bolhas sob `ACCOUNT`conta de `DIR1/DIR2`armazenamento `CONT`especificada, em pasta, debaixo do recipiente, e combinando o padrão`*.csv.gz`
* Destino é `DB`base `TABLE`de dados, tabela, e o mapeamento `MAPPING` de ingestão é pré-criado no destino
* As bolhas de origem contêm linha de cabeçalho, por isso a ferramenta é instruída a deixar cair o primeiro registo de cada bolha
* A ferramenta publicará os dados para ingestão e não esperará que as operações ingestão completem

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Ingerir todos os ficheiros JSON de um caminho

* Ingerir todos os `PATH`ficheiros no caminho, combinando com o padrão`*.json`
* Destino é `DB`base `TABLE`de dados, tabela, e o mapeamento de ingestão é definido em arquivo local`MAPPING_FILE_PATH`
* A ferramenta publicará os dados para ingestão e não esperará que as operações ingestão completem

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Ingerir ficheiros e escrever ficheiros de rastreio de diagnóstico

* Ingerir todos os `PATH`ficheiros no caminho, combinando com o padrão`*.json`
* Destino é `DB`base `TABLE`de dados, tabela, e o mapeamento de ingestão é definido em arquivo local`MAPPING_FILE_PATH`
* A ferramenta publicará os dados para ingestão e não esperará que as operações ingestão completem
* Os ficheiros de rastreio de diagnóstico serão escritos localmente sob pasta`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Changelog
|Versão        |Alterações                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Argumento `-zipPattern` adicionado</li><li>Argumento `-listOnly` adicionado</li><li>O resumo dos argumentos é apresentado antes do início da execução</li><li>CriaçãoTempo é lido a partir de propriedades de metadados `-creationTimePattern` blob ou a partir de blob ou nome de arquivo, de acordo com o argumento</li></ul>|
