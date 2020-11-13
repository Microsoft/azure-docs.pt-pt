---
title: Sessão iniciar sessão na atividade de cópia
description: Saiba como ativar o início de sessão na atividade de cópia do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: e56a840da07a2f6e966867699506f0122a0e7956
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593660"
---
#  <a name="session-log-in-copy-activity"></a>Sessão iniciar sessão na atividade de cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pode registar os nomes de ficheiros copiados na atividade de cópias, o que pode ajudá-lo a garantir que os dados não são apenas copiados com sucesso da fonte para a loja de destino, como também consistente entre a loja de origem e destino, revendo os ficheiros copiados em registos de sessão de funções de cópia.  

Quando ativa a definição de tolerância a falhas na atividade de cópia para saltar dados defeituosos, os ficheiros ignorados e as linhas ignoradas também podem ser registados.  Pode obter mais detalhes da tolerância a [falhas na atividade de cópia.](copy-activity-fault-tolerance.md) 

## <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição JSON para permitir o registo de sessão na Atividade de Cópia: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
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

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | Quando o definir como verdadeiro, terá a oportunidade de registar ficheiros copiados, ficheiros ignorados ou linhas ignoradas.  | Verdadeiro<br/>Falso (predefinição) | Não
logLevel | "Info" registará todos os ficheiros copiados, ficheiros ignorados e linhas ignoradas. "Aviso" registará ficheiros ignorados e apenas saltará linhas.  | Informações<br/>Aviso (predefinição) | Não
enableReliableLogging | Quando for verdade, a atividade de cópia em modo fiável irá descarregar os registos imediatamente assim que cada ficheiro for copiado para o destino.  Quando estiver a copiar grandes quantidades de ficheiros com modo de registo fiável ativado na atividade de cópia, deve esperar que o resultado da cópia seja impactado, uma vez que são necessárias operações de dupla escrita para cada cópia de ficheiros. Um pedido é para a loja de destino e outro pedido é para a loja de armazenamento de registos.  A atividade de cópia no melhor modo de esforço irá lavar os registos com um lote de registos dentro de um período de tempo, onde a produção de cópia será muito menos impactada. A completude e a atualidade da exploração madeireira não estão garantidas neste modo, uma vez que existem algumas possibilidades de que o último lote de eventos de registo não tenha sido lavado no ficheiro de registo quando a atividade da cópia falhou. Neste momento, verá que alguns ficheiros copiados para o destino não estão registados.  | Verdadeiro<br/>Falso (predefinição) | Não
logLocationSettings | Um grupo de propriedades que podem ser usadas para especificar a localização para armazenar os registos de sessão. | | Não
linkedServiceName | O serviço ligado do [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) ou [da Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os ficheiros de registo de sessão. | Os nomes de um ou tipo de `AzureBlobStorage` `AzureBlobFS` serviço ligado, que se refere à instância que utiliza para armazenar os ficheiros de registo. | Não
caminho | O caminho dos ficheiros de registo. | Especifique o caminho que pretende armazenar os ficheiros de registo. Se não providenciar um caminho, o serviço cria um recipiente para si. | Não


## <a name="monitoring"></a>Monitorização

### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Depois de a atividade da cópia funcionar completamente, pode ver o caminho dos ficheiros de registo a partir da saída de cada atividade de cópia executada. Pode encontrar os ficheiros de registo do caminho: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Os ficheiros de registo serão os ficheiros csv. 

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

### <a name="the-schema-of-the-log-file"></a>O esquema do ficheiro de registo

Segue-se o esquema de um ficheiro de registo.

Coluna | Descrição 
-------- | -----------  
Timestamp | A hora da hora quando a ADF lê, escreve ou salta o objeto.
Nível | O nível de registo deste item. Pode ser 'Aviso' ou "Informação".
OperationName | ADF copia comportamento operacional da atividade em cada objeto. Pode ser 'FileRead', 'FileWrite', 'FileSkip', ou 'TabularRowSkip'.
OperaçãoItem | Os nomes dos ficheiros ou as filas ignoradas.
Mensagem | Mais informações para mostrar se o ficheiro foi lido a partir da loja de origem ou escrito na loja de destino. Também pode ser por isso que o ficheiro ou linhas foi ignorado.

Segue-se um exemplo de um ficheiro de registo. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
A partir do ficheiro de registo acima, pode ver sample1.csv foi ignorado porque não foi verificado para ser consistente entre a loja de origem e destino. Pode obter mais detalhes sobre o porquê de sample1.csv se tornar inconsistente é porque estava a ser alterada por outras aplicações quando a atividade de cópias ADF está a copiar ao mesmo tempo. Também pode ver sample2.csv foi copiado com sucesso de fonte para loja de destino.

Pode utilizar vários motores de análise para analisar os ficheiros de registo.  Existem alguns exemplos abaixo para usar a consulta SQL para analisar o ficheiro de registo importando o ficheiro de registo csv para a base de dados SQL onde o nome da tabela pode ser SessionLogDemo.  

-   Dê-me a lista de ficheiros copiados. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Dê-me a lista de ficheiros copiado dentro de um determinado intervalo de tempo. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Dê-me um ficheiro particular com o seu tempo copiado e metadados. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Dê-me uma lista de ficheiros com os metadados deles copiados dentro de um intervalo de tempo. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Dê-me a lista de ficheiros ignorados. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Dá-me a razão pela qual um ficheiro em particular saltou. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Dê-me a lista de ficheiros ignorados pela mesma razão: "o ficheiro blob não existe". 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Dê-me o nome do ficheiro que requer mais tempo para copiar.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de Atividade de Cópia:

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Copiar a tolerância à falha da atividade](copy-activity-fault-tolerance.md)
- [Copiar consistência dos dados da atividade](copy-activity-data-consistency.md)