---
title: Tolerância a falhas da atividade de cópia no Azure Data Factory
description: Saiba como adicionar tolerância à falha para copiar atividade na Azure Data Factory ignorando os dados incompatíveis.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: a44703aabc35131cf040892999409173638437a7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658767"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolerância a falhas da atividade de cópia no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versão atual](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando copia dados de fonte para loja de destino, a atividade de cópia da Azure Data Factory fornece um certo nível de tolerâncias de falha para evitar interrupções de falhas no meio do movimento de dados. Por exemplo, está a copiar milhões de linhas de fonte para loja de destino, onde foi criada uma chave principal na base de dados de destino, mas a base de dados de origem não tem chaves primárias definidas. Quando por acaso copiar filas duplicadas de origem para destino, irá atingir a falha de violação do PK na base de dados de destino. Neste momento, a atividade de cópia oferece-lhe duas formas de lidar com tais erros: 
- Pode abortar a atividade da cópia assim que se encontrar qualquer falha. 
- Pode continuar a copiar o resto permitindo que a tolerância à falha ignore os dados incompatíveis. Por exemplo, pule a linha duplicada neste caso. Além disso, pode registar os dados ignorados, permitindo o registo de sessão dentro da atividade da cópia. 

## <a name="copying-binary-files"></a>Copiar ficheiros binários 

A ADF suporta os seguintes cenários de tolerância a falhas ao copiar ficheiros binários. Pode optar por abortar a atividade da cópia ou continuar a copiar o resto nos seguintes cenários:

1. Os ficheiros a copiar pela ADF estão a ser eliminados por outras aplicações ao mesmo tempo.
2. Algumas pastas ou ficheiros específicos não permitem o acesso da ADF porque os ACLs desses ficheiros ou pastas requerem um nível de permissão mais elevado do que as informações de ligação configuradas na ADF.
3. Um ou mais ficheiros não são verificados como consistentes entre a loja de origem e destino se permitir a definição de verificação da consistência dos dados na ADF.

### <a name="configuration"></a>Configuração 
Ao copiar ficheiros binários entre lojas de armazenamento, pode ativar a tolerância à falha como seguintes: 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true 
    }, 
    "validateDataConsistency": true, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2", 
            "type": "LinkedServiceReference" 
            }, 
        "path": "sessionlog/" 
     } 
} 
```
Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
skipErrorFile | Um grupo de propriedades para especificar os tipos de falhas que pretende ignorar durante o movimento de dados. | | Não
arquivoDesaparecido | Um dos pares de valor-chave dentro do saco de propriedade skipErrorFile para determinar se pretende ignorar ficheiros, que estão a ser eliminados por outras aplicações quando a ADF está a copiar entretanto. <br/> -Verdade: pretende copiar o resto ignorando os ficheiros que estão a ser apagados por outras aplicações. <br/> - Falso: pretende abortar a atividade da cópia assim que os ficheiros forem apagados da loja de origem no meio do movimento de dados. <br/>Esteja ciente de que esta propriedade está definida como padrão. | Verdadeiro (padrão) <br/>Falso | Não
arquivoForbidden | Um dos pares de valor-chave no saco de propriedade skipErrorFile para determinar se pretende ignorar os ficheiros específicos, quando os ACLs desses ficheiros ou pastas requerem um nível de permissão mais elevado do que a ligação configurada na ADF. <br/> - É verdade: quer copiar o resto ignorando os ficheiros. <br/> - Falso: pretende abortar a atividade da cópia uma vez que tenha o problema da permissão em pastas ou ficheiros. | Verdadeiro <br/>Falso (padrão) | Não
dadosInconsistência | Um dos pares de valor-chave dentro do saco de propriedade skipErrorFile para determinar se pretende ignorar os dados inconsistentes entre a loja de origem e destino. <br/> -Verdade: você quer copiar o resto ignorando dados inconsistentes. <br/> - Falso: pretende abortar a atividade da cópia uma vez encontrados dados inconsistentes. <br/>Esteja ciente de que esta propriedade só é válida quando definir validaçãoDataConsistência como Verdade. | Verdadeiro <br/>Falso (padrão) | Não
logStorageSettings  | Um grupo de propriedades que pode ser especificada quando pretende registar os nomes de objetos ignorados. | &nbsp; | Não
linkedServiceName | O serviço ligado do [Armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties) ou [do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os ficheiros de registo da sessão. | Os nomes de um `AzureBlobStorage` ou tipo de serviço `AzureBlobFS` ligado, que se refere à instância que utiliza para armazenar o ficheiro de registo. | Não
path | O caminho dos ficheiros de registo. | Especifique o caminho que utiliza para armazenar os ficheiros de registo. Se não fornecer um caminho, o serviço cria um recipiente para si. | Não

### <a name="monitoring"></a>Monitorização 

#### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Pode obter o número de ficheiros que são lidos, escritos e ignorados através da saída de cada execução de atividade de cópia. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>Registo de sessão da atividade de cópia

Se configurar para registar os nomes de ficheiros ignorados, pode encontrar o ficheiro de registo deste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Os ficheiros de registo têm de ser os ficheiros CSV. O esquema do ficheiro de registo é o seguinte:

Coluna | Descrição 
-------- | -----------  
Carimbo de data/hora | A marca de tempo quando a ADF ignora o ficheiro.
Nível | O nível de registo deste item. Estará no nível 'Aviso' para o item que mostra o salto de ficheiro.
OperationName | ADF copia comportamento operacional de atividade em cada ficheiro. Será 'FileSkip' especificar o ficheiro a ser ignorado.
OperaçãoItem | Os nomes dos ficheiros a serem ignorados.
Mensagem | Mais informações para ilustrar por que o ficheiro foi ignorado.

O exemplo de um ficheiro de registo é o seguinte: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
A partir do registo acima, pode ver que bigfile.csv foi ignorado devido a outra aplicação apagou este ficheiro quando a ADF estava a copiá-lo. E 3_nopermission.txt foi ignorado porque a ADF não está autorizada a aceder-lhe devido a um problema de permissão.


## <a name="copying-tabular-data"></a>Copiar dados tabulares 

### <a name="supported-scenarios"></a>Cenários suportados
A atividade de cópia suporta três cenários para detetar, saltar e registar dados tabulares incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo da pia**. 

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento blob para uma base de dados SQL com uma definição de esquema que contém três colunas do tipo INT. As linhas de ficheiroCSV que contêm dados numéricos, tais como 123.456.789 são copiadas com sucesso para a loja de sumidouros. No entanto, as linhas que contêm valores não numéricos, como 123.456, o ABC são detetados como incompatíveis e são ignorados.

- **Incompatibilidade no número de colunas entre a fonte e o lavatório**.

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento blob para uma base de dados SQL com uma definição de esquema que contém seis colunas. As linhas de ficheiroCSV que contêm seis colunas são copiadas com sucesso para a pia. As linhas de ficheiro CSV que contêm mais de seis colunas são detetadas como incompatíveis e ignoradas.

- **Violação da chave primária ao escrever para SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Por exemplo: Copiar dados de um servidor SQL para uma base de dados SQL. Uma chave primária é definida na base de dados SQL do lavatório, mas nenhuma chave primária é definida no servidor SQL de origem. As linhas duplicadas existentes na fonte não podem ser copiadas para a pia. Copiar a atividade copia apenas a primeira linha dos dados de origem para o lavatório. As linhas de origem subsequentes que contêm o valor-chave primário duplicado são detetadas como incompatíveis e ignoradas.

>[!NOTE]
>- Para carregar dados no SQL Data Warehouse utilizando o PolyBase, configure as definições de tolerância à falha nativa da PolyBase, especificando as políticas de rejeição através de "[poliBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" na atividade de cópia. Ainda pode ativar o redirecionamento de linhas incompatíveis da PolyBase para Blob ou ADLS tão normal como mostrado abaixo.
>- Esta funcionalidade não se aplica quando a atividade de cópia é configurada para invocar o [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Esta funcionalidade não se aplica quando a atividade da cópia é configurada para invocar um [procedimento armazenado a partir de um lavatório SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

### <a name="configuration"></a>Configuração
O exemplo que se segue fornece uma definição JSON para configurar saltar as linhas incompatíveis na atividade de cópia:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logStorageSettings": { 
    "linkedServiceName": { 
        "referenceName": "ADLSGen2", 
        "type": "LinkedServiceReference" 
        }, 
    "path": "sessionlog/" 
    } 
}, 
```

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
enableSkipIncompatívelRow | Especifica se deve saltar linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>Falso (predefinição) | Não
logStorageSettings | Um grupo de propriedades que pode ser especificada quando pretende registar as linhas incompatíveis. | &nbsp; | Não
linkedServiceName | O serviço ligado do [Armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties) ou [do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar o registo que contém as linhas ignoradas. | Os nomes de um `AzureBlobStorage` ou tipo de serviço `AzureBlobFS` ligado, que se refere à instância que utiliza para armazenar o ficheiro de registo. | Não
path | O caminho dos ficheiros de registo que contém as linhas ignoradas. | Especifique o caminho que pretende utilizar para registar os dados incompatíveis. Se não fornecer um caminho, o serviço cria um recipiente para si. | Não

### <a name="monitor-skipped-rows"></a>Monitor linhas ignoradas
Após a execução da atividade de cópia, pode ver o número de linhas ignoradas na saída da atividade da cópia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Se configurar para registar as linhas incompatíveis, pode encontrar o ficheiro de registo deste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Os ficheiros de registo serão os ficheiros CSV. O esquema do ficheiro de registo é o seguinte:

Coluna | Descrição 
-------- | -----------  
Carimbo de data/hora | A marca de tempo quando a ADF salta as linhas incompatíveis
Nível | O nível de registo deste item. Será no nível 'Aviso' se este item mostrar as linhas ignoradas
OperationName | ADF copia comportamento operacional de atividade em cada linha. Será 'TabularRowSkip' especificar que a linha particular incompatível foi ignorada
OperaçãoItem | As filas ignoradas da loja de dados de origem.
Mensagem | Mais informações para ilustrar por que razão a incompatibilidade desta linha em particular.


Um exemplo do conteúdo do ficheiro de registo é o seguinte:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

A partir do ficheiro de registo de amostras acima, pode ver-se uma linha "data1, data2, data3" foi ignorada devido ao problema de conversão de tipo de fonte para loja de destino. Outra linha "data4, data5, data6" foi ignorada devido a problema de violação de PK de fonte para loja de destino. 


## <a name="copying-tabular-data-legacy"></a>Copiar dados tabulares (legado):

Segue-se a forma de permitir a tolerância à falha apenas para copiar dados tabulares. Se está a criar um novo oleoduto ou atividade, é encorajado a começar [daqui.](#copying-tabular-data)

### <a name="configuration"></a>Configuração
O exemplo que se segue fornece uma definição JSON para configurar saltar as linhas incompatíveis na atividade de cópia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
enableSkipIncompatívelRow | Especifica se deve saltar linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>Falso (predefinição) | Não
redirecionamentoIncompatívelComDefinições RowSettings | Um grupo de propriedades que pode ser especificada quando pretende registar as linhas incompatíveis. | &nbsp; | Não
linkedServiceName | O serviço ligado do [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) para armazenar o registo que contém as linhas ignoradas. | Os nomes de um `AzureStorage` `AzureDataLakeStore` serviço ou tipo ligado, que se refere à instância que pretende utilizar para armazenar o ficheiro de registo. | Não
path | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique o caminho que pretende utilizar para registar os dados incompatíveis. Se não fornecer um caminho, o serviço cria um recipiente para si. | Não

### <a name="monitor-skipped-rows"></a>Monitor linhas ignoradas
Após a execução da atividade de cópia, pode ver o número de linhas ignoradas na saída da atividade da cópia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Se configurar para registar as linhas incompatíveis, pode encontrar o ficheiro de registo neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Os ficheiros de registo só podem ser os ficheiros CSV. Os dados originais que estão a ser ignorados serão registados com vírina como delimitador de coluna, se necessário. Adicionamos mais duas colunas "ErrorCode" e "ErrorMessage" em adicional aos dados originais de origem no ficheiro de registo, onde pode ver a causa principal da incompatibilidade. O ErrorCode e o ErrorMessage serão citados por duas cotações. 

Um exemplo do conteúdo do ficheiro de registo é o seguinte:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)


