---
title: Verificação da consistência dos dados na atividade de cópia
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
ms.openlocfilehash: a386c7d44cf5ba7eda895006cda7ce1fa9b798ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664978"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Verificação da consistência dos dados na atividade da cópia (Pré-visualização)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ao mover dados de fonte para loja de destino, a atividade de cópia da Azure Data Factory fornece uma opção para que você faça uma verificação adicional de consistência de dados para garantir que os dados não só são copiados com sucesso de fonte para loja de destino, como também verificado sê-lo consistente entre fonte e loja de destino. Uma vez encontrados dados inconsistentes durante o movimento de dados, pode abortar a atividade da cópia ou continuar a copiar o resto, permitindo que a regulação da tolerância à falha ignore dados inconsistentes. Pode obter os nomes de objetos ignorados, permitindo a definição de registo de sessão na atividade da cópia. 

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização com as seguintes limitações em que estamos a trabalhar ativamente:
>- A verificação da consistência dos dados só está disponível em ficheiros binários que copiam entre lojas baseadas em ficheiros com comportamento 'PreserveHierarchy' na atividade de cópia. Para copiar dados tabular, a verificação da consistência dos dados ainda não está disponível na atividade de cópia.
>- Quando ativa a definição de registo de sessão na atividade da cópia para registar os ficheiros inconsistentes que estão a ser ignorados, a completude do ficheiro de registo não pode ser 100% garantida se a atividade da cópia falhar.
>- O registo da sessão contém apenas ficheiros inconsistentes, onde os ficheiros copiados com sucesso não estão registados até agora.

## <a name="supported-data-stores"></a>Arquivos de dados suportados

### <a name="source-data-stores"></a>Lojas de dados de origem

-   [Armazenamento Azure Blob](connector-azure-blob-storage.md)
-   [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2)
-   [Armazenamento de Ficheiros do Azure](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Sistema de Ficheiros](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Lojas de dados de destino

-   [Armazenamento Azure Blob](connector-azure-blob-storage.md)
-   [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2)
-   [Armazenamento de Ficheiros do Azure](connector-azure-file-storage.md)
-   [Sistema de Ficheiros](connector-file-system.md)


## <a name="configuration"></a>Configuração
O exemplo que se segue fornece uma definição JSON para permitir a verificação da consistência dos dados na Atividade de Cópia: 

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
validandoAConsistência data | Se você definir verdadeiro para esta propriedade, a atividade de cópia verificará o tamanho do ficheiro, o último Datato Modificado e o controlo MD5 para cada objeto copiado de fonte para loja de destino para garantir a consistência dos dados entre a fonte e a loja de destino. Esteja ciente de que o desempenho da cópia será afetado através da ativação desta opção.  | Verdadeiro<br/>Falso (predefinição) | Não
dadosInconsistência | Um dos pares de valor-chave dentro do saco de propriedade skipErrorFile para determinar se pretende ignorar os dados inconsistentes.<br/> -Verdade: você quer copiar o resto ignorando dados inconsistentes.<br/> - Falso: pretende abortar a atividade da cópia uma vez encontrados dados inconsistentes.<br/>Esteja ciente de que esta propriedade só é válida quando definir validaçãoDataConsistência como Verdade.  | Verdadeiro<br/>Falso (predefinição) | Não
logStorageSettings | Um grupo de propriedades que pode ser especificada para permitir o registo de sessão para registar objetos ignorados. | | Não
linkedServiceName | O serviço ligado do [Armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties) ou [do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os ficheiros de registo da sessão. | Os nomes de um `AzureBlobStorage` ou tipos de serviço `AzureBlobFS` ligado, que se refere à instância que utiliza para armazenar os ficheiros de registo. | Não
path | O caminho dos ficheiros de registo. | Especifique o caminho que pretende armazenar os ficheiros de registo. Se não fornecer um caminho, o serviço cria um recipiente para si. | Não

>[!NOTE]
>- A consistência dos dados não é suportada no cenário de cópia de encenação. 
>- Ao copiar ficheiros binários de qualquer loja de armazenamento para O Armazenamento De Azure Blob ou Azure Data Lake Storage Gen2, a atividade de cópia faz o tamanho do ficheiro e verifica a verificação de verificação de md5 para garantir a consistência dos dados entre as lojas de origem e destino. 
>- Ao copiar ficheiros binários de qualquer loja de armazenamento para qualquer loja de armazenamento que não seja o Armazenamento De Azure Blob ou o Azure Data Lake Storage Gen2, a atividade de cópia faz verificação do tamanho do ficheiro para garantir a consistência dos dados entre a loja de origem e destino.


## <a name="monitoring"></a>Monitorização

### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Após a execução da atividade da cópia, pode ver o resultado da verificação da consistência dos dados a partir da saída de cada execução de cada atividade de cópia:

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
Pode ver os detalhes da verificação da consistência dos dados a partir de "dataConsistênciaPropriedade Verificação".

Valor da **VerificaçãoResultado:** 
-   **Verificado**: Os seus dados copiados foram verificados como consistentes entre a loja de origem e destino. 
-   **NotVerified**: Os seus dados copiados não foram verificados como consistentes porque não ativou a validaçãoDataConsistência na atividade da cópia. 
-   **Não suportado**: Os seus dados copiados não foram verificados como consistentes porque a verificação da consistência dos dados não é suportada para este par de cópias em particular. 

Valor dos **Dados Inconsistentes:** 
-   **Encontrado**: A atividade de cópia ADF encontrou dados inconsistentes. 
-   **Ignorado**: A atividade de cópia ADF encontrou e ignorou dados inconsistentes. 
-   **Nenhuma**: A atividade de cópia ADF não encontrou dados inconsistentes. Pode ser porque os seus dados foram verificados como consistentes entre a loja de origem e destino ou porque desativou a Consistência do Datana na atividade da cópia. 

### <a name="session-log-from-copy-activity"></a>Registo de sessão da atividade de cópia

Se configurar para registar o ficheiro inconsistente, pode encontrar o ficheiro de registo deste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Os ficheiros de registo serão os ficheiros CSV. 

O esquema de um ficheiro de registo é o seguinte:

Coluna | Descrição 
-------- | -----------  
Carimbo de data/hora | A marca de tempo quando a ADF ignora os ficheiros inconsistentes.
Nível | O nível de registo deste item. Estará no nível 'Aviso' para o item que mostra o salto de ficheiro.
OperationName | ADF copia comportamento operacional de atividade em cada ficheiro. Será 'FileSkip' especificar o ficheiro a ser ignorado.
OperaçãoItem | O nome do ficheiro a ser ignorado.
Mensagem | Mais informações para ilustrar por que os ficheiros são ignorados.

O exemplo de um ficheiro de registo é o seguinte: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
A partir do ficheiro de registo acima, pode ver que a amostra1.csv foi ignorada porque não foi verificado como consistente entre a loja de origem e destino. Você pode obter mais detalhes sobre por que a amostra1.csv torna-se inconsistente é porque estava sendo alterada por outras aplicações quando a atividade de cópia ADF está copiando ao mesmo tempo. 



## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos da Atividade cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Copiar tolerância à falha da atividade](copy-activity-fault-tolerance.md)


