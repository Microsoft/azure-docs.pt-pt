---
title: Verificação da consistência dos dados na atividade da cópia
description: Saiba como permitir a verificação da consistência dos dados na atividade de cópia na Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a45c8ce820532d11f18758924dc3399818cb9158
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84610241"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Verificação da consistência dos dados na atividade de cópia (Pré-visualização)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ao mover dados de origem para loja de destino, a atividade de cópia da Azure Data Factory fornece uma opção para que você faça uma verificação adicional de consistência de dados para garantir que os dados não são apenas copiados com sucesso de fonte para loja de destino, mas também verificado como consistente entre a loja de origem e destino. Uma vez encontrados dados inconsistentes durante o movimento de dados, pode abortar a atividade da cópia ou continuar a copiar o resto, permitindo que a definição de tolerância a falhas salte dados inconsistentes. Pode obter os nomes de objetos ignorados, permitindo a definição de registo de sessão na atividade de cópia. 

> [!IMPORTANT]
> Esta funcionalidade está atualmente em pré-visualização com as seguintes limitações em que estamos a trabalhar ativamente:
>- A verificação da consistência dos dados só está disponível em ficheiros binários que copiam entre lojas baseadas em ficheiros com o comportamento 'PreserveHierarchy' na atividade de cópia. Para a cópia de dados tabulares, a verificação da consistência dos dados ainda não está disponível na atividade de cópia.
>- Quando ativa a definição de registo de sessão na atividade de cópia para registar os ficheiros inconsistentes que estão a ser ignorados, a pôr em prática o ficheiro de registo não pode ser 100% garantida se a atividade da cópia falhar.
>- O registo de sessão contém apenas ficheiros inconsistentes, onde os ficheiros copiados com sucesso não são registados até ao momento.

## <a name="supported-data-stores"></a>Arquivos de dados suportados

### <a name="source-data-stores"></a>Lojas de dados de origem

-   [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md)
-   [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2)
-   [Armazenamento de Ficheiros do Azure](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Sistema de Ficheiros](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Lojas de dados de destino

-   [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md)
-   [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2)
-   [Armazenamento de Ficheiros do Azure](connector-azure-file-storage.md)
-   [Sistema de Ficheiros](connector-file-system.md)


## <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição JSON para permitir a verificação da consistência dos dados na Atividade de Cópia: 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
validarDataConsistency | Se for definido como verdadeiro para esta propriedade, a atividade de cópia verificará o tamanho do ficheiro, o último Exemplo de Filme e a caixa de verificação MD5 para cada objeto copiado de fonte para loja de destino para garantir a consistência dos dados entre a loja de origem e destino. Esteja ciente de que o desempenho da cópia será afetado ativando esta opção.  | Verdadeiro<br/>Falso (predefinição) | Não
dataInconsistency | Um dos pares de valores-chave dentro do saco de propriedade skipErrorFile para determinar se deseja saltar os dados inconsistentes.<br/> -Verdade: quer copiar o resto ignorando dados inconsistentes.<br/> - Falso: pretende abortar a atividade de cópia uma vez encontrados dados inconsistentes.<br/>Esteja ciente de que esta propriedade só é válida quando definir validar DataConsistency como True.  | Verdadeiro<br/>Falso (predefinição) | Não
logStorageSettings | Um grupo de propriedades que podem ser especificadas para permitir o registo de sessão para registar objetos ignorados. | | Não
linkedServiceName | O serviço ligado do [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) ou [da Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os ficheiros de registo de sessão. | Os nomes de um ou tipo de `AzureBlobStorage` `AzureBlobFS` serviço ligado, que se refere à instância que utiliza para armazenar os ficheiros de registo. | Não
path | O caminho dos ficheiros de registo. | Especifique o caminho que pretende armazenar os ficheiros de registo. Se não providenciar um caminho, o serviço cria um recipiente para si. | Não

>[!NOTE]
>- A consistência dos dados não é suportada no cenário de cópia de encenação. 
>- Ao copiar ficheiros de, ou para Azure Blob ou Azure Data Lake Storage Gen2, a ADF bloqueia a verificação de verificação de nível MD5 alavancando [Azure Blob API](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy) e [Azure Data Lake Storage Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Se o ContentMD5 em ficheiros existir no Azure Blob ou no Azure Data Lake Storage Gen2 como fontes de dados, a ADF faz verificação de nível de ficheiro MD5 após a leitura dos ficheiros também. Depois de copiar ficheiros para Azure Blob ou Azure Data Lake Storage Gen2 como destino de dados, a ADF escreve ConteúdoMD5 a Azure Blob ou Azure Data Lake Storage Gen2 que podem ser ainda mais consumidos por aplicações a jusante para verificação da consistência de dados.
>- A ADF faz verificação do tamanho do ficheiro ao copiar ficheiros entre quaisquer armazéns.

## <a name="monitoring"></a>Monitorização

### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Após o funcionar da atividade da cópia, pode ver o resultado da verificação da consistência dos dados a partir da saída de cada atividade de cópia executada:

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
Pode ver os detalhes da verificação da consistência dos dados a partir de "dataConsistencyVerification property".

Valor da **VerificaçãoResult:** 
-   **Verificado:** Os seus dados copiados foram verificados para serem consistentes entre a loja de origem e destino. 
-   **Notificável**: Os seus dados copiados não foram verificados para serem consistentes porque não habilitava a validação DataConsistency na atividade de cópia. 
-   **Não suportados**: Os seus dados copiados não foram verificados para serem consistentes porque a verificação da consistência dos dados não é suportada para este par de cópias em particular. 

Valor do **InconsistenteData:** 
-   **Encontrado:** A atividade de cópia da ADF encontrou dados inconsistentes. 
-   **Ignorado:** A atividade de cópia da ADF encontrou e ignorou dados inconsistentes. 
-   **Nenhum**: A atividade de cópia da ADF não encontrou dados inconsistentes. Pode ser porque os seus dados foram verificados para serem consistentes entre a loja de origem e destino ou porque desativou a Validação doDataConsisency na atividade de cópia. 

### <a name="session-log-from-copy-activity"></a>Registo de sessão da atividade de cópia

Se configurar para registar o ficheiro inconsistente, pode encontrar o ficheiro de registo deste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Os ficheiros de registo serão os ficheiros csv. 

O esquema de um ficheiro de registo é o seguinte:

Coluna | Descrição 
-------- | -----------  
Carimbo de data/hora | A marca de tempo quando a ADF ignora os ficheiros inconsistentes.
Nível | O nível de registo deste item. Estará no nível 'Aviso' para o item que mostra o salto do ficheiro.
OperationName | ADF copia comportamento operacional da atividade em cada ficheiro. Será 'FileSkip' para especificar o ficheiro a ser ignorado.
OperaçãoItem | O nome do ficheiro a ser ignorado.
Mensagem | Mais informações para ilustrar por que os ficheiros estão a ser ignorados.

O exemplo de um ficheiro de registo é o seguinte: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
A partir do ficheiro de registo acima, pode ver sample1.csv foi ignorado porque não foi verificado para ser consistente entre a loja de origem e destino. Pode obter mais detalhes sobre o porquê de sample1.csv se tornar inconsistente é porque estava a ser alterada por outras aplicações quando a atividade de cópias ADF está a copiar ao mesmo tempo. 



## <a name="next-steps"></a>Próximos passos
Consulte os outros artigos de Atividade de Cópia:

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Copiar a tolerância à falha da atividade](copy-activity-fault-tolerance.md)


