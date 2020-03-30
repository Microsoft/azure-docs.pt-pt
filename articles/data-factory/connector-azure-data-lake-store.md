---
title: Copiar dados de ou para Azure Data Lake Storage Gen1
description: Saiba como copiar dados de lojas de dados de origem suportada para a Azure Data Lake Store, ou da Data Lake Store para lojas de sink suportadas, utilizando data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: a8ba8b212a504a8f8e4e29fbd50126189998e81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065472"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiar dados de ou para azure Data Lake Storage Gen1 usando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

Este artigo descreve como copiar dados de e para o Azure Data Lake Storage Gen1. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Data Lake Storage Gen1 é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md) 
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, com este conector pode:

- Copiar ficheiros utilizando um dos seguintes métodos de autenticação: diretor de serviço ou identidades geridas para os recursos Azure.
- Copiar ficheiros como é ou analisar ou gerar ficheiros com os [formatos de ficheiros suportados e os códigos](supported-file-formats-and-compression-codecs.md)de compressão .
- [Preservar os ACLs](#preserve-acls-to-data-lake-storage-gen2) ao copiar em Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Se copiar dados utilizando o tempo de execução de integração auto-hospedado, `<ADLS account name>.azuredatalakestore.net` `login.microsoftonline.com/<tenant>/oauth2/token` configure a firewall corporativa para permitir o tráfego de saída de e para a porta 443. Este último é o Serviço de Token de Segurança Azure que o tempo de integração precisa de comunicar para obter o sinal de acesso.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para uma análise de como utilizar o conector Azure Data Lake Store, consulte [os dados de carga na Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem informações sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas da Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Azure Data Lake Store:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A `type` propriedade deve ser definida para **AzureDataLakeStore**. | Sim |
| dataLakeStoreUri | Informações sobre a conta Azure Data Lake Store. Esta informação tem um dos `https://[accountname].azuredatalakestore.net/webhdfs/v1` `adl://[accountname].azuredatalakestore.net/`seguintes formatos: ou . | Sim |
| subscriptionId | O ID de subscrição Azure a que pertence a conta Data Lake Store. | Obrigatório para afundar |
| resourceGroupName | O nome do grupo de recursos Azure a que pertence a conta Data Lake Store. | Obrigatório para afundar |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o tempo de execução da integração Azure ou um tempo de execução de integração auto-hospedado se a sua loja de dados estiver localizada numa rede privada. Se esta propriedade não for especificada, o tempo de execução de integração do Azure padrão é usado. |Não |

### <a name="use-service-principal-authentication"></a>Utilizar a autenticação principal do serviço

Para utilizar a autenticação principal do serviço, siga estes passos.

1. Registe uma entidade de aplicação no Azure Ative Directory e conceda-lhe acesso à Data Lake Store. Para obter passos detalhados, consulte a [autenticação serviço-a-serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:

    - ID da aplicação
    - Chave de aplicação
    - ID do inquilino

2. Conceda a permissão adequada ao diretor de serviço. Veja exemplos de como a permissão funciona em Data Lake Storage Gen1 a partir do controlo de [acesso em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como fonte**: No > **Acesso**ao Explorador de **Dados,** conceda pelo menos a permissão **de executar** todas as pastas a montante, incluindo a raiz, juntamente com a permissão **de leitura** para os ficheiros copiarem. Pode optar por adicionar a **Esta pasta e a todas as crianças** para recursiva, e adicionar como **permissão de acesso e entrada de permissão por defeito**. Não há requisito no controlo de acesso ao nível da conta (IAM).
    - **Como pia**: No > **acesso**do explorador de **dados,** conceda pelo menos a permissão **de executar** todas as pastas a montante, incluindo a raiz, juntamente com a permissão **de Escrita** para a pasta do lavatório. Pode optar por adicionar a **Esta pasta e a todas as crianças** para recursiva, e adicionar como **permissão de acesso e entrada de permissão por defeito**. Se utilizar um tempo de execução de integração Azure para copiar (tanto a fonte como a pia estão na nuvem), no IAM, conceda pelo menos o papel **de Leitor** de forma a permitir que a Data Factory detete a região para data lake store. Se quiser evitar este papel IAM, crie explicitamente um tempo de execução de [integração Azure](create-azure-integration-runtime.md#create-azure-ir) com a localização da Data Lake Store. Por exemplo, se a sua Data Lake Store estiver na Europa Ocidental, crie um tempo de execução de integração Azure com localização definida para "Europa Ocidental". Associe-os no serviço ligado à Data Lake Store, como mostra o seguinte exemplo.

As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| serviçoPrincipalId | Especifique a identificação do cliente do pedido. | Sim |
| serviçoPrincipalKey | Especifique a chave da aplicação. Marque este `SecureString` campo como um para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique as informações do arrendatário, tais como nome de domínio ou identificação do inquilino, segundo a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Utilize identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos Azure,](data-factory-service-identity.md)que representa esta fábrica de dados específica. Pode utilizar diretamente esta identidade gerida para autenticação data Lake Store, semelhante à utilização do seu próprio diretor de serviço. Permite que esta fábrica designada aceda e copie dados de ou para data lake store.

Para utilizar identidades geridas para autenticação de recursos Azure, siga estes passos.

1. [Recuperar a informação](data-factory-service-identity.md#retrieve-managed-identity) de identidade gerida pela fábrica de dados copiando o valor do "ID de aplicação de identidade de serviço" gerado juntamente com a sua fábrica.

2. Conceda o acesso de identidade gerido à Data Lake Store. Veja exemplos de como a permissão funciona em Data Lake Storage Gen1 a partir do controlo de [acesso em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como fonte**: No > **Acesso**ao Explorador de **Dados,** conceda pelo menos a permissão **de executar** todas as pastas a montante, incluindo a raiz, juntamente com a permissão **de leitura** para os ficheiros copiarem. Pode optar por adicionar a **Esta pasta e a todas as crianças** para recursiva, e adicionar como **permissão de acesso e entrada de permissão por defeito**. Não há requisito no controlo de acesso ao nível da conta (IAM).
    - **Como pia**: No > **acesso**do explorador de **dados,** conceda pelo menos a permissão **de executar** todas as pastas a montante, incluindo a raiz, juntamente com a permissão **de Escrita** para a pasta do lavatório. Pode optar por adicionar a **Esta pasta e a todas as crianças** para recursiva, e adicionar como **permissão de acesso e entrada de permissão por defeito**. Se utilizar um tempo de execução de integração Azure para copiar (tanto a fonte como a pia estão na nuvem), no IAM, conceda pelo menos o papel **de Leitor** de forma a permitir que a Data Factory detete a região para data lake store. Se quiser evitar este papel IAM, crie explicitamente um tempo de execução de [integração Azure](create-azure-integration-runtime.md#create-azure-ir) com a localização da Data Lake Store. Associe-os no serviço ligado à Data Lake Store, como mostra o seguinte exemplo.

Na Azure Data Factory, não é necessário especificar quaisquer propriedades para além das informações gerais da Data Lake Store no serviço ligado.

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para Azure Data Lake Store Gen1 em `location` definições no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade `location` do tipo no conjunto de dados deve ser definida para **AzureDataLakeStoreLocation**. | Sim      |
| folderPath | O caminho para uma pasta. Se pretender utilizar um wildcard para filtrar pastas, ignore esta definição e especifique-a nas definições de origem de atividade. | Não       |
| fileName   | O nome do ficheiro sob a pasta dadaPath. Se pretender utilizar um wildcard para filtrar ficheiros, ignore esta definição e especifique-a nas definições de origem de atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). Esta secção fornece uma lista de propriedades suportadas pela fonte e pia da Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para Azure Data Lake Store Gen1 em `storeSettings` definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade `storeSettings` do tipo em parte deve ser definida para **AzureDataLakeStoreReadSettings**. | Sim                                           |
| recursivo                | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Quando o recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres wildcard para filtrar as pastas de origem. <br>Os wildcards `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único). Utilize `^` para escapar se o seu nome real de pasta tiver um wildcard ou este char de fuga no seu interior. <br>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| nome wildcardFile         | O nome do ficheiro com caracteres wildcard sob a pasta dadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único). Utilize `^` para escapar se o seu nome real de pasta tiver um wildcard ou este char de fuga no seu interior. Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Sim, `fileName` se não for especificado no conjunto de dados |
| alteradoDatetimeStart    | Filtro de ficheiros com base no atributo Last Modificado. Os ficheiros são selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados. | Não                                            |
| alteradoDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Loja do Lago Azure Data como pia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para Azure Data Lake Store Gen1 em `storeSettings` configurações no sumidouro de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade `storeSettings` do tipo em parte deve ser definida para **AzureDataLakeStoreWriteDefinições**. | Sim      |
| copiarComportamento             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- PreserveHierarchy (predefinição)</b>: Preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro-alvo para a pasta-alvo.<br/><b>- Hierarquia flattena</b>: Todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não       |
| expiraçãoDataTime | Especifica o tempo de validade dos ficheiros escritos. O tempo é aplicado ao tempo UTC no formato "2020-03-01T08:00:00:00Z". Por defeito é NULO, o que significa que os ficheiros escritos nunca expiram. | Não |
| maxConcurrentConnections | O número de ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e ficheiro

Esta secção descreve o comportamento resultante do caminho da pasta e nome de ficheiro com filtros wildcard.

| folderPath | fileName | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vazio, utilização por defeito) | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vazio, utilização por defeito) | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta secção descreve o comportamento resultante da operação `recursive` `copyBehavior` de cópia para diferentes combinações e valores.

| recursivo | copiarComportamento | Estrutura de pasta de origem | Alvo resultante |
|:--- |:--- |:--- |:--- |
| true |preservar Hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5. |
| true |achatar a hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File5 |
| true |mergeFiles | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos File1 + File2 + File3 + File4 + File5 são fundidos num só ficheiro, com um nome de ficheiro autogerado. |
| false |preservar Hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com File3, File4 e File5 não foram captadas. |
| false |achatar a hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/><br/>Subpasta1 com File3, File4 e File5 não foram captadas. |
| false |mergeFiles | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos File1 + File2 são fundidos num ficheiro com nome de ficheiro autogerado. nome autogerado para File1<br/><br/>Subpasta1 com File3, File4 e File5 não foram captadas. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Preservar ACLs para Data Lake Storage Gen2

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para a Gen2 em geral, consulte [os dados da Cópia do Azure Data Lake Storage Gen1 para gen2 com a Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para obter um walk-through e boas práticas.

Se pretender replicar as listas de controlo de acesso (ACLs) juntamente com ficheiros de dados quando atualizar do Data Lake Storage Gen1 para data Lake Storage Gen2, consulte [Preserve ACLs a partir de Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapeando propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever ficheiros do Azure Data Lake Storage Gen1 em formato JSON, Avro, Delimited Text ou Parquet. Para obter mais informações, consulte a [transformação](data-flow-source.md) de origem e a transformação do [sumidouro](data-flow-sink.md) na funcionalidade de fluxo de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

Na transformação de origem, pode ler-se a partir de um contentor, pasta ou ficheiro individual no Azure Data Lake Storage Gen1. O separador **opções Source** permite-lhe gerir a forma como os ficheiros são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

**Caminho wildcard:** A utilização de um padrão wildcard instruirá a ADF a passar por cada pasta correspondente e arquivar numa única transformação de Origem. Esta é uma forma eficaz de processar vários ficheiros dentro de um único fluxo. Adicione vários padrões de correspondência wildcard com o sinal + que aparece ao pairar sobre o padrão wildcard existente.

Do seu recipiente de origem, escolha uma série de ficheiros que correspondam a um padrão. Só o recipiente pode ser especificado no conjunto de dados. O seu caminho wildcard deve, portanto, incluir também o seu caminho de pasta a partir da pasta raiz.

Exemplos wildcard:

* ```*```Representa qualquer conjunto de caracteres
* ```**```Representa a nidificação do diretório recursivo
* ```?```Substitui um personagem
* ```[]```Corresponde a um dos mais caracteres nos suportes

* ```/data/sales/**/*.csv```Obtém todos os ficheiros CSV em /data/vendas
* ```/data/sales/20??/**/```Recebe todos os ficheiros no século XX
* ```/data/sales/*/*/*.csv```Obtém ficheiros csv dois níveis em /data/vendas
* ```/data/sales/2004/*/12/[XY]1?.csv```Obtém todos os ficheiros CSV em 2004 em dezembro, começando com X ou Y pré-fixados por um número de dois dígitos

**Caminho raiz da partilha:** Se tiver pastas divididas na fonte ```key=value``` de ficheiros com um formato (por exemplo, ano=2019), então pode atribuir o nível superior dessa árvore de pasta de divisória a um nome de coluna no fluxo de dados de fluxo de dados de dados.

Em primeiro lugar, detete um wildcard para incluir todos os caminhos que são as pastas divididas mais os ficheiros de folhas que pretende ler.

![Definições de ficheiro de origem de partição](media/data-flow/partfile2.png "Definição de ficheiro de partição")

Utilize a definição do Caminho da Raiz da Partição para definir qual é o nível superior da estrutura da pasta. Quando visualizar o conteúdo dos seus dados através de uma pré-visualização de dados, verá que a ADF irá adicionar as divisórias resolvidas encontradas em cada um dos níveis das suas pastas.

![Caminho raiz da partição](media/data-flow/partfile1.png "Pré-visualização do caminho raiz da divisória")

**Lista de ficheiros:** Isto é um conjunto de ficheiros. Crie um ficheiro de texto que inclua uma lista de ficheiros relativos para processar. Aponte para este ficheiro de texto.

**Coluna para armazenar nome de ficheiro:** Guarde o nome do ficheiro fonte numa coluna nos seus dados. Introduza aqui um novo nome de coluna para armazenar a cadeia de nomes de ficheiros.

**Após a conclusão:** Opte por não fazer nada com o ficheiro fonte depois de o fluxo de dados ser executado, eliminar o ficheiro fonte ou mover o ficheiro fonte. Os caminhos para a mudança são relativos.

Para mover ficheiros de origem para outro local pós-processamento, primeiro selecione "Move" para a operação de ficheiros. Em seguida, definir o diretório "de" de. Se não estiver a utilizar nenhum wildcard para o seu caminho, então a definição "a partir" será a mesma pasta que a sua pasta fonte.

Se tiver um caminho de origem com wildcard, a sua sintaxe será assim abaixo:

```/data/sales/20??/**/*.csv```

Pode especificar "a partir"

```/data/sales```

E "para" como

```/backup/priorSales```

Neste caso, todos os ficheiros que foram obtidos em /data/vendas são transferidos para /backup/priorSales.

> [!NOTE]
> As operações de ficheiro saem apenas quando inicia o fluxo de dados a partir de uma execução de gasoduto (um depurador de gasoduto ou execução) que utiliza a atividade executar data Flow num pipeline. As operações de *ficheironão* funcionam no modo de depuração do Fluxo de Dados.

**Filtrar por último modificado:** Pode filtrar quais os ficheiros que processa especificando uma gama de datas de quando foram modificados pela última vez. Todas as datas-data são na UTC. 

### <a name="sink-properties"></a>Propriedades de pia

Na transformação da pia, pode escrever para um recipiente ou pasta em Azure Data Lake Storage Gen1. o separador **Definições** permite-lhe gerir a forma como os ficheiros são escritos.

![opções de afundar](media/data-flow/file-sink-settings.png "opções de afundar")

**Limpe a pasta:** Determina se a pasta de destino é ou não desmarcada antes de os dados forem escritos.

**Opção de nome** de ficheiro: Determina como os ficheiros de destino são nomeados na pasta de destino. As opções de nome do ficheiro são:
   * **Predefinição**: Permitir que a Spark nomeie ficheiros com base nas predefinições da PARTE.
   * **Padrão**: Introduza um padrão que enumera os seus ficheiros de saída por divisória. Por exemplo, **os empréstimos[n].csv** criarão empréstimos1.csv, empréstimos2.csv, e assim por diante.
   * **Por partição**: Introduza um nome de ficheiro por partição.
   * **Como dados na coluna**: Desloque o ficheiro de saída ao valor de uma coluna. O caminho é relativo ao recipiente dataset, não à pasta de destino. Se tiver um caminho de pasta no seu conjunto de dados, será ultrapassado.
   * **Saída para um único ficheiro:** Combine os ficheiros de saída divididos num único ficheiro nomeado. O caminho é relativo à pasta dataset. Por favor, esteja ciente de que a operação de fusão te pode possivelmente falhar com base no tamanho do nó. Esta opção não é recomendada para grandes conjuntos de dados.

**Cite todos:** Determina se deve encerrar todos os valores em cotações

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Obtenha propriedades de atividadede Metadados

Para saber mais detalhes sobre as propriedades, consulte [a atividade do GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para saber detalhes sobre as propriedades, consulte a [atividade de Eliminar](delete-activity.md)

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os seguintes modelos ainda são suportados como é para retrocompatibilidade. É-lhe sugerido que utilize o novo modelo mencionado nas secções acima, e a UI de autoria da ADF mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para **AzureDataLakeStoreFile**. |Sim |
| folderPath | Caminho para a pasta na Data Lake Store. Se não especificado, aponta para a raiz. <br/><br/>O filtro Wildcard é suportado. Os wildcards `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único). Utilize `^` para escapar se o seu nome real de pasta tiver um wildcard ou este char de fuga no seu interior. <br/><br/>Por exemplo: pasta de raiz/subpasta/. Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). |Não |
| fileName | Nome ou filtro wildcard para os ficheiros sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os `*` wildcards permitidos são `?` (corresponde a zero ou mais caracteres) e (corresponde a zero ou personagem único).<br/>- Exemplo 1:`"fileName": "*.csv"`<br/>- Exemplo 2:`"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de arquivo real tiver um wildcard ou este char de fuga dentro.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída e preservar a **hierarquia** não é especificada no sumidouro de atividade, a atividade de cópia gera automaticamente o nome do ficheiro com o seguinte padrão: "*Dados.. atividade executar ID GUID]. [GUID se AHierarquia Achatada]. [formato se configurado]. [compressão se configurado]*", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se copiar de uma fonte tabular usando um nome de mesa em vez de uma consulta, o padrão de nome é "*[nome de mesa].[ formato]. [compressão se configurado]*", por exemplo, "MyTable.csv". |Não |
| alteradoDatetimeStart | Filtro de ficheiros com base no atributo Last Modificado. Os ficheiros são selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> O desempenho global do movimento de dados é afetado, permitindo esta definição quando pretende fazer filtro de ficheiros com enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| alteradoDatetimeEnd | Filtro de ficheiros com base no atributo Last Modificado. Os ficheiros são selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> O desempenho global do movimento de dados é afetado, permitindo esta definição quando pretende fazer filtro de ficheiros com enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat**e **ParquetFormat**. Desloque a propriedade **tipo** em **formato** a um destes valores. Para mais informações, consulte as secções de [formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para mais informações, consulte [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)de compressão .<br/>Os tipos suportados são **GZip,** **Deflate,** **BZip2**e **ZipDeflate**.<br/>Os níveis suportados são **Ideais** e **Mais Rápidos.** |Não |

>[!TIP]
>Para copiar todos os ficheiros sob uma pasta, especifique apenas **pastaPath.**<br>Para copiar um único ficheiro com um nome específico, especifique **a pastaPath** com uma peça de pasta e **o nome** do ficheiro com um nome de ficheiro.<br>Para copiar um subconjunto de ficheiros sob uma pasta, especifique **a pastaPath** com uma peça de pasta e **o nome** do ficheiro com um filtro wildcard. 

**Exemplo:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Modelo de fonte de fonte de atividade de cópia legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A `type` propriedade da fonte de atividade de cópia deve ser definida para **AzureDataLakeStoreSource**. |Sim |
| recursivo | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Quando `recursive` é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não |
| maxConcurrentConnections | O número de ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Modelo de pia de atividade de cópia legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A `type` propriedade do afundado de atividade de cópia deve ser definida para **AzureDataLakeStoreSink**. |Sim |
| copiarComportamento | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- PreserveHierarchy (predefinição)</b>: Preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro-alvo para a pasta-alvo.<br/><b>- Hierarquia flattena</b>: Todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, o nome do ficheiro é autogerado. | Não |
| maxConcurrentConnections | O número de ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
