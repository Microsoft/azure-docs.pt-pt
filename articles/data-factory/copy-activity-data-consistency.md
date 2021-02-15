---
title: Verificação da consistência dos dados na atividade da cópia
description: Saiba como permitir a verificação da consistência dos dados na atividade de cópia na Azure Data Factory.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: b71657f67c1b9c623d6d48f33b986ac43533cca6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373021"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Verificação da consistência dos dados na atividade da cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ao mover dados de origem para loja de destino, a atividade de cópia da Azure Data Factory fornece uma opção para que você faça uma verificação adicional de consistência de dados para garantir que os dados não são apenas copiados com sucesso de fonte para loja de destino, mas também verificado como consistente entre a loja de origem e destino. Uma vez encontrados ficheiros inconsistentes durante o movimento de dados, pode abortar a atividade da cópia ou continuar a copiar o resto, permitindo que a definição de tolerância a falhas ignore ficheiros inconsistentes. Pode obter os nomes de ficheiros ignorados, permitindo a definição de registo de sessão na atividade de cópia. Pode consultar a [atividade de registo de sessão na cópia](copy-activity-log.md) para mais detalhes.

## <a name="supported-data-stores-and-scenarios"></a>Lojas e cenários de dados suportados

-   A verificação da consistência dos dados é suportada por todos os conectores, exceto FTP, sFTP e HTTP. 
-   A verificação da consistência dos dados não é suportada no cenário de cópia de encenação.
-   Ao copiar ficheiros binários, a verificação da consistência dos dados só está disponível quando o comportamento 'PreserveHierarchy' é definido na atividade de cópia.
-   Ao copiar vários ficheiros binários na atividade de cópia única com verificação de consistência de dados ativada, tem a opção de abortar a atividade da cópia ou continuar a copiar os restantes, permitindo que a definição de tolerância a falhas salte ficheiros inconsistentes. 
-   Ao copiar uma tabela na atividade de cópia única com verificação de consistência de dados ativada, a atividade de cópia falha se o número de linhas lidas a partir da fonte for diferente do número de linhas copiadas para o destino mais o número de linhas incompatíveis que foram ignoradas.


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
validarDataConsistency | Se se definir como verdadeiro para esta propriedade, ao copiar ficheiros binários, a atividade de cópia verificará o tamanho do ficheiro, o último TermodifiedDate e a caixa de verificação MD5 para cada ficheiro binário copiado da fonte para a loja de destino para garantir a consistência dos dados entre a loja de origem e destino. Ao copiar dados tabulares, a atividade de cópia verificará a contagem total de filas após o cumprimento do trabalho para garantir que o número total de linhas lidas a partir da fonte é o mesmo que o número de linhas copiadas para o destino mais o número de linhas incompatíveis que foram ignoradas. Esteja ciente de que o desempenho da cópia será afetado ativando esta opção.  | Verdadeiro<br/>Falso (predefinição) | No
dataInconsistency | Um dos pares de valores-chave dentro do saco de propriedade skipErrorFile para determinar se deseja saltar os ficheiros inconsistentes. <br/> -Verdade: quer copiar o resto ignorando ficheiros inconsistentes.<br/> - Falso: pretende abortar a atividade de cópia uma vez encontrado um ficheiro inconsistente.<br/>Esteja ciente de que esta propriedade só é válida quando estiver a copiar ficheiros binários e definir validar aDataConsistency como True.  | Verdadeiro<br/>Falso (predefinição) | No
logSettings | Um grupo de propriedades que podem ser especificadas para permitir o registo de sessão para registar ficheiros ignorados. | | No
linkedServiceName | O serviço ligado do [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) ou [da Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os ficheiros de registo de sessão. | Os nomes de um ou tipo de `AzureBlobStorage` `AzureBlobFS` serviço ligado, que se refere à instância que utiliza para armazenar os ficheiros de registo. | No
caminho | O caminho dos ficheiros de registo. | Especifique o caminho que pretende armazenar os ficheiros de registo. Se não providenciar um caminho, o serviço cria um recipiente para si. | No

>[!NOTE]
>- Ao copiar ficheiros binários de, ou para Azure Blob ou Azure Data Lake Storage Gen2, a ADF faz o nível de verificação de verificação MD5 alavancando [Azure Blob API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) e [Azure Data Lake Storage Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Se o ContentMD5 em ficheiros existir no Azure Blob ou no Azure Data Lake Storage Gen2 como fontes de dados, a ADF faz verificação de nível de ficheiro MD5 após a leitura dos ficheiros também. Depois de copiar ficheiros para Azure Blob ou Azure Data Lake Storage Gen2 como destino de dados, a ADF escreve ConteúdoMD5 a Azure Blob ou Azure Data Lake Storage Gen2 que podem ser ainda mais consumidos por aplicações a jusante para verificação da consistência de dados.
>- A ADF faz verificação do tamanho do ficheiro ao copiar ficheiros binários entre quaisquer armazéns.

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
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
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
CarimboDeDataEHora | A marca de tempo quando a ADF ignora os ficheiros inconsistentes.
Level | O nível de registo deste item. Estará no nível 'Aviso' para o item que mostra o salto do ficheiro.
OperationName | ADF copia comportamento operacional da atividade em cada ficheiro. Será 'FileSkip' para especificar o ficheiro a ser ignorado.
OperaçãoItem | O nome do ficheiro a ser ignorado.
Mensagem | Mais informações para ilustrar por que os ficheiros estão a ser ignorados.

O exemplo de um ficheiro de registo é o seguinte: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
A partir do ficheiro de registo acima, pode ver sample1.csv foi ignorado porque não foi verificado para ser consistente entre a loja de origem e destino. Pode obter mais detalhes sobre o porquê de sample1.csv se tornar inconsistente é porque estava a ser alterada por outras aplicações quando a atividade de cópias ADF está a copiar ao mesmo tempo. 



## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de Atividade de Cópia:

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Copiar a tolerância à falha da atividade](copy-activity-fault-tolerance.md)