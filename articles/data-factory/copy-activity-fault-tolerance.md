---
title: Tolerância a falhas da atividade de cópia no Azure Data Factory
description: Saiba como adicionar tolerância a falhas à atividade de cópia na Azure Data Factory ignorando os dados incompatíveis.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fb6beb776f5a553e85f690d49e3433f93b9ee16
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809546"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolerância a falhas da atividade de cópia no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versão atual](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ao copiar dados de fonte para loja de destino, a atividade de cópia da Azure Data Factory fornece determinados níveis de tolerâncias de falhas para evitar a interrupção de falhas no meio do movimento de dados. Por exemplo, está a copiar milhões de linhas da fonte para a loja de destino, onde foi criada uma chave primária na base de dados de destino, mas a base de dados de origem não tem nenhuma chave primária definida. Quando por acaso copiar linhas duplicadas de origem para o destino, você vai atingir a falha de violação de PK na base de dados de destino. Neste momento, a atividade de cópia oferece-lhe duas formas de lidar com tais erros: 
- Pode abortar a atividade da cópia assim que se encontrar qualquer falha. 
- Pode continuar a copiar o resto, permitindo que a tolerância à falha ignore os dados incompatíveis. Por exemplo, ignore a linha duplicada neste caso. Além disso, pode registar os dados ignorados, permitindo o registo de sessão dentro da atividade de cópia. Pode consultar a [atividade de registo de sessão na cópia](copy-activity-log.md) para mais detalhes.

## <a name="copying-binary-files"></a>Copiar ficheiros binários 

A ADF suporta os seguintes cenários de tolerância a falhas ao copiar ficheiros binários. Pode optar por abortar a atividade da cópia ou continuar a copiar o resto nos seguintes cenários:

1. Os ficheiros a copiar pela ADF estão a ser eliminados por outras aplicações ao mesmo tempo.
2. Algumas pastas ou ficheiros específicos não permitem o acesso da ADF porque os ACLs desses ficheiros ou pastas requerem um nível de permissão mais elevado do que a informação de ligação configurada no ADF.
3. Um ou mais ficheiros não são verificados para serem consistentes entre a loja de origem e destino se permitir a definição de verificação da consistência dos dados em ADF.

### <a name="configuration"></a>Configuração 
Ao copiar ficheiros binários entre armazéns, pode ativar a tolerância à falha como seguintes: 

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
        "dataInconsistency": true,
        "invalidFileName": true     
    }, 
    "validateDataConsistency": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```
Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
skipErrorFile | Um grupo de propriedades para especificar os tipos de falhas que pretende ignorar durante o movimento de dados. | | No
arquivoSSing | Um dos pares de valores-chave dentro do saco de propriedade skipErrorFile para determinar se deseja saltar ficheiros, que estão a ser eliminados por outras aplicações quando a ADF está a copiar entretanto. <br/> -Verdade: pretende copiar o resto ignorando os ficheiros que são eliminados por outras aplicações. <br/> - Falso: pretende abortar a atividade da cópia assim que quaisquer ficheiros forem eliminados da loja de origem no meio do movimento de dados. <br/>Esteja ciente de que esta propriedade está definida como padrão. | Verdadeiro(padrão) <br/>Falso | No
arquivoS Proibido | Um dos pares de valores-chave dentro do saco de propriedade skipErrorFile para determinar se deseja saltar os ficheiros específicos, quando os ACLs desses ficheiros ou pastas requerem um nível de permissão mais elevado do que a ligação configurada em ADF. <br/> -Verdade: quer copiar o resto ignorando os ficheiros. <br/> - Falso: pretende abortar a atividade de cópia uma vez que recebe o problema de permissão em pastas ou ficheiros. | Verdadeiro <br/>Falso(padrão) | No
dataInconsistency | Um dos pares de valores-chave dentro do saco de propriedade skipErrorFile para determinar se deseja ignorar os dados inconsistentes entre a loja de origem e destino. <br/> -Verdade: quer copiar o resto ignorando dados inconsistentes. <br/> - Falso: pretende abortar a atividade de cópia uma vez encontrados dados inconsistentes. <br/>Esteja ciente de que esta propriedade só é válida quando definir validar DataConsistency como True. | Verdadeiro <br/>Falso(padrão) | No
invalidArname | Um dos pares de valores-chave dentro do saco de propriedade skipErrorFile para determinar se deseja saltar os ficheiros específicos, quando os nomes dos ficheiros são inválidos para a loja de destino. <br/> -Verdade: pretende copiar o resto ignorando os ficheiros com nomes de ficheiros inválidos. <br/> - Falso: pretende abortar a atividade de cópia uma vez que os ficheiros tenham nomes de ficheiros inválidos. <br/>Esteja ciente de que esta propriedade funciona ao copiar ficheiros binários de qualquer loja de armazenamento para ADLS Gen2 ou copiar ficheiros binários da AWS S3 para qualquer loja de armazenamento apenas. | Verdadeiro <br/>Falso(padrão) | No
logSettings  | Um grupo de propriedades que podem ser especificadas quando pretende registar os nomes dos objetos ignorados. | &nbsp; | No
linkedServiceName | O serviço ligado do [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) ou [da Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os ficheiros de registo de sessão. | Os nomes de um `AzureBlobStorage` serviço ligado ou `AzureBlobFS` tipo, que se refere à instância que utiliza para armazenar o ficheiro de registo. | No
caminho | O caminho dos ficheiros de registo. | Especifique o caminho que utiliza para armazenar os ficheiros de registo. Se não providenciar um caminho, o serviço cria um recipiente para si. | No

> [!NOTE]
> Os seguintes são os pré-requisitos para permitir a tolerância a falhas na atividade de cópias ao copiar ficheiros binários.
> Para saltar ficheiros específicos quando estão a ser eliminados da loja de origem:
> - O conjunto de dados de origem e o conjunto de dados do lavatório têm de ser um formato binário e o tipo de compressão não pode ser especificado. 
> - Os tipos de loja de dados suportados são o armazenamento Azure Blob, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, File System, FTP, SFTP, Amazon S3, Google Cloud Storage e HDFS.
> - Só se especificar vários ficheiros no conjunto de dados de origem, que podem ser uma pasta, wildcard ou uma lista de ficheiros, a atividade de cópia pode ignorar os ficheiros de erro específicos. Se um único ficheiro for especificado no conjunto de dados de origem para ser copiado para o destino, a atividade da cópia falhará se ocorrer algum erro.
>
> Para saltar ficheiros específicos quando o seu acesso está proibido de ser armazenado:
> - O conjunto de dados de origem e o conjunto de dados do lavatório têm de ser um formato binário e o tipo de compressão não pode ser especificado. 
> - Os tipos de loja de dados suportados são o armazenamento Azure Blob, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, SFTP, Amazon S3 e HDFS.
> - Só se especificar vários ficheiros no conjunto de dados de origem, que podem ser uma pasta, wildcard ou uma lista de ficheiros, a atividade de cópia pode ignorar os ficheiros de erro específicos. Se um único ficheiro for especificado no conjunto de dados de origem para ser copiado para o destino, a atividade da cópia falhará se ocorrer algum erro.
>
> Para saltar ficheiros específicos quando são verificados como inconsistentes entre a loja de origem e destino:
> - Pode obter mais detalhes do data consistência doc [aqui.](./copy-activity-data-consistency.md)

### <a name="monitoring"></a>Monitorização 

#### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Pode obter o número de ficheiros que estão a ser lidos, escritos e ignorados através da saída de cada atividade de cópia executada. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>Registo de sessão da atividade de cópia

Se configurar para registar os nomes de ficheiros ignorados, pode encontrar o ficheiro de registo deste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Os ficheiros de registo têm de ser os ficheiros csv. O esquema do ficheiro de registo é o seguinte:

Coluna | Descrição 
-------- | -----------  
CarimboDeDataEHora | A hora de tempo quando a ADF ignora o ficheiro.
Level | O nível de registo deste item. Estará no nível 'Aviso' para o item que mostra o salto do ficheiro.
OperationName | ADF copia comportamento operacional da atividade em cada ficheiro. Será 'FileSkip' para especificar o ficheiro a ser ignorado.
OperaçãoItem | Os nomes dos ficheiros a serem ignorados.
Mensagem | Mais informações para ilustrar o porquê do ficheiro ser ignorado.

O exemplo de um ficheiro de registo é o seguinte: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
A partir do registo acima, pode ver bigfile.csv foi ignorado devido a outra aplicação eliminada este ficheiro quando a ADF o estava a copiar. E 3_nopermission.txt foi ignorado porque a ADF não está autorizada a aceder-lhe devido a um problema de permissão.


## <a name="copying-tabular-data"></a>Cópia de dados tabulares 

### <a name="supported-scenarios"></a>Cenários suportados
A atividade de cópia suporta três cenários para detetar, saltar e registar dados tabulares incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo da pia**. 

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento Blob para uma base de dados SQL com uma definição de esquema que contém três colunas do tipo INT. As linhas de ficheiros CSV que contêm dados numéricos, tais como 123.456.789 são copiadas com sucesso para a loja de pias. No entanto, as linhas que contêm valores não numéricos, como 123.456, são detetadas como incompatíveis e são ignoradas.

- **Desajuste no número de colunas entre a fonte e a pia**.

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento Blob para uma base de dados SQL com uma definição de esquema que contém seis colunas. As linhas de ficheiro CSV que contêm seis colunas são copiadas com sucesso para a loja de pias. As linhas de ficheiro CSV que contenham mais de seis colunas são detetadas como incompatíveis e são ignoradas.

- **Violação de chave primária ao escrever para SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Por exemplo: Copiar dados de um servidor SQL para uma base de dados SQL. Uma chave primária é definida na base de dados SQL do lavatório, mas nenhuma chave primária é definida no servidor SQL de origem. As linhas duplicadas existentes na fonte não podem ser copiadas para a pia. Copiar a atividade copia apenas a primeira linha dos dados de origem na pia. As linhas de origem subsequentes que contêm o valor da chave primária duplicada são detetadas como incompatíveis e ignoradas.

>[!NOTE]
>- Para carregar dados no Azure Synapse Analytics utilizando o PolyBase, configurar as definições de tolerância à falha nativa da PolyBase especificando políticas de rejeição através de "[poliBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" na atividade de cópia. Pode ainda ativar a reorientação das linhas incompatíveis da PolyBase para Blob ou ADLS normalmente, como mostrado abaixo.
>- Esta funcionalidade não se aplica quando a atividade de cópia é configurada para invocar a [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Esta função não se aplica quando a atividade da cópia é configurada para invocar um [procedimento armazenado a partir de um lavatório SQL](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink).

### <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição JSON para configurar saltar as linhas incompatíveis na atividade de cópia:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    } 
}, 
```

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Especifica se deve saltar linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>Falso (predefinição) | No
logSettings | Um grupo de propriedades que podem ser especificadas quando pretende registar as linhas incompatíveis. | &nbsp; | No
linkedServiceName | O serviço ligado do [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar o registo que contém as linhas ignoradas. | Os nomes de um `AzureBlobStorage` serviço ligado ou `AzureBlobFS` tipo, que se refere à instância que utiliza para armazenar o ficheiro de registo. | No
caminho | O caminho dos ficheiros de registo que contém as linhas ignoradas. | Especifique o caminho que pretende utilizar para registar os dados incompatíveis. Se não providenciar um caminho, o serviço cria um recipiente para si. | No

### <a name="monitor-skipped-rows"></a>Monitor saltou linhas
Após o funcionar da atividade da cópia, pode ver o número de linhas ignoradas na saída da atividade da cópia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Se configurar para registar as linhas incompatíveis, pode encontrar o ficheiro de registo deste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Os ficheiros de registo serão os ficheiros csv. O esquema do ficheiro de registo é o seguinte:

Coluna | Descrição 
-------- | -----------  
CarimboDeDataEHora | A hora de se pular quando a ADF salta as linhas incompatíveis
Level | O nível de registo deste item. Estará no nível 'Aviso' se este item mostrar as linhas ignoradas
OperationName | ADF copia comportamento operacional da atividade em cada linha. Será 'TabularRowSkip' especificar que a linha incompatível particular foi ignorada
OperaçãoItem | As filas ignoradas da loja de dados de origem.
Mensagem | Mais informações para ilustrar por que razão a incompatibilidade desta linha em particular.


Um exemplo do conteúdo do ficheiro de registo é o seguinte:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

A partir do ficheiro de registo de amostras acima, pode ver uma linha "data1, dados2, dados3" foi ignorada devido a um problema de conversão de tipo de fonte para loja de destino. Outra linha "data4, data5, data6" foi ignorada devido a um problema de violação de PK de fonte para loja de destino. 


## <a name="copying-tabular-data-legacy"></a>Copiar dados tabulares (legado):

Segue-se a forma de permitir a tolerância à falha apenas para copiar dados tabulares. Se estiver a criar um novo oleoduto ou atividade, é encorajado a começar a partir [daqui.](#copying-tabular-data)

### <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição JSON para configurar saltar as linhas incompatíveis na atividade de cópia:

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
enableSkipIncompatibleRow | Especifica se deve saltar linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>Falso (predefinição) | No
redireccionamentosIncompatíveis | Um grupo de propriedades que podem ser especificadas quando pretende registar as linhas incompatíveis. | &nbsp; | No
linkedServiceName | O serviço ligado da [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) ou [da Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) para armazenar o registo que contém as linhas ignoradas. | Os nomes de um `AzureStorage` `AzureDataLakeStore` serviço ou tipo ligado, que se refere à instância que pretende utilizar para armazenar o ficheiro de registo. | No
caminho | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique o caminho que pretende utilizar para registar os dados incompatíveis. Se não providenciar um caminho, o serviço cria um recipiente para si. | No

### <a name="monitor-skipped-rows"></a>Monitor saltou linhas
Após o funcionar da atividade da cópia, pode ver o número de linhas ignoradas na saída da atividade da cópia:

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

Os ficheiros de registo só podem ser os ficheiros csv. Os dados originais que estão a ser ignorados serão registados com vírgula como limagem de coluna, se necessário. Adicionamos mais duas colunas "ErrorCode" e "ErrorMessage" adicionais aos dados originais de origem no ficheiro de registo, onde é possível ver a causa principal da incompatibilidade. O ErrorCode e o ErrorMessage serão citados por cotações duplas. 

Um exemplo do conteúdo do ficheiro de registo é o seguinte:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)