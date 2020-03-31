---
title: Copiar e transformar dados em Azure Data Lake Storage Gen2
description: Saiba como copiar dados de e para o Azure Data Lake Storage Gen2 e transforme dados em Azure Data Lake Storage Gen2 utilizando a Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 3c7ff0061a57d1a1a7525ec03b4f77c117415ca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155855"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiar e transformar dados no Azure Data Lake Storage Gen2 utilizando a Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) é um conjunto de capacidades dedicadas à análise de big data incorporada no [armazenamento de Blob Azure.](../storage/blobs/storage-blobs-introduction.md) Pode usá-los para interagir com os seus dados utilizando tanto o sistema de ficheiros como os paradigmas de armazenamento de objetos.

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para o Azure Data Lake Storage Gen2, e usar o Data Flow para transformar dados em Azure Data Lake Storage Gen2. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Data Lake Storage Gen2 é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Para a atividade de Cópia, com este conector pode:

- Copie dados de/para Azure Data Lake Storage Gen2 utilizando a chave de conta, o diretor de serviço ou identidades geridas para autenticações de recursos Azure.
- Copiar ficheiros como estão ou analisar ou gerar ficheiros com [formatos de ficheiros suportados e codificadores](supported-file-formats-and-compression-codecs.md)de compressão .
- [Preservar metadados](#preserve-metadata-during-copy)de ficheiros durante a cópia .
- [Preservar os ACLs](#preserve-acls) ao copiar a partir de Azure Data Lake Storage Gen1/Gen2.

>[!IMPORTANT]
>Se permitir que os **serviços da Microsoft fidedignos acedam a esta** opção de conta de armazenamento nas definições de firewall do Azure Storage e pretenda utilizar o tempo de execução de integração do Azure para se ligar ao seu Data Lake Storage Gen2, deve utilizar a [autenticação](#managed-identity) de identidade gerida para a ADLS Gen2.

>[!TIP]
>Se ativar o espaço de nomehialógico, não existe atualmente interoperabilidade de operações entre Blob e Data Lake Storage Gen2 APIs. Se tiver atingido o erro "ErrorCode=FilesystemNotFound" com a mensagem "O sistema de ficheiros especificado não existe", é causado pelo sistema de ficheiros sink especificado que foi criado através da API Blob em vez de Data Lake Storage Gen2 API em outros lugares. Para corrigir o problema, especifique um novo sistema de ficheiros com um nome que não existe como o nome de um recipiente Blob. Em seguida, data Factory cria automaticamente esse sistema de ficheiros durante a cópia de dados.

## <a name="get-started"></a>Introdução

>[!TIP]
>Para uma análise de como utilizar o conector Data Lake Storage Gen2, consulte [os dados de carga no Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem informações sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

O conector Azure Data Lake Storage Gen2 suporta os seguintes tipos de autenticação. Consulte as secções correspondentes para obter mais detalhes:

- [Autenticação da chave da conta](#account-key-authentication)
- [Autenticação do principal de serviço](#service-principal-authentication)
- [Identidades geridas para autenticação de recursos Azure](#managed-identity)

>[!NOTE]
>Ao utilizar a PolyBase para carregar dados no Armazém de Dados SQL, se a sua fonte data Lake Storage Gen2 estiver configurada com ponto final da Rede Virtual, deve utilizar a autenticação de identidade gerida conforme exigido pela PolyBase. Consulte a secção de [autenticação](#managed-identity) de identidade gerida com mais pré-requisitos de configuração.

### <a name="account-key-authentication"></a>Autenticação da chave da conta

Para utilizar a autenticação da chave da conta de armazenamento, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureBlobFS**. |Sim |
| url | Ponto final para Data Lake Storage `https://<accountname>.dfs.core.windows.net`Gen2 com o padrão de . | Sim |
| contaChave | Chave de conta para Data Lake Storage Gen2. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o tempo de execução da integração Azure ou um tempo de execução de integração auto-hospedado se a sua loja de dados estiver numa rede privada. Se esta propriedade não for especificada, o tempo de execução de integração do Azure padrão é usado. |Não |

>[!NOTE]
>O ponto final do sistema de ficheiros ADLS secundário não é suportado quando utilizar a autenticação da chave da conta. Pode utilizar outros tipos de autenticação.

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para utilizar a autenticação principal do serviço, siga estes passos.

1. Registe uma entidade de candidatura no Azure Ative Directory (Azure AD) seguindo as etapas em Registar a sua candidatura junto de [um inquilino da Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:

    - ID da aplicação
    - Chave de aplicação
    - ID do inquilino

2. Conceda a permissão adequada ao diretor de serviço. Veja exemplos de como a permissão funciona em Data Lake Storage Gen2 a partir de listas de controlo de [acesso em ficheiros e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Como fonte**: No Storage Explorer, conceda pelo menos a permissão **de execução** para todas as pastas a montante e para o sistema de ficheiros, juntamente com a permissão **de leitura** para os ficheiros copiarem. Alternativamente, no controlo de acesso (IAM), conceda pelo menos o papel de Leitor de **Dados blob** de armazenamento.
    - **Como pia**: No Storage Explorer, conceda pelo menos a permissão **de executar** todas as pastas a montante e o sistema de ficheiros, juntamente com a permissão **de Escrita** para a pasta do lavatório. Alternativamente, no controlo de acesso (IAM), conceda pelo menos o papel de Contribuinte de **Dados blob** de armazenamento.

>[!NOTE]
>Se utilizar a Data Factory UI para autor e o diretor de serviço não estiver definido com a função "Storage Blob Data Reader/Contributor" no IAM, ao fazer a ligação de teste ou navegar/navegar pastas, escolha "Test connection to file path" ou "Browse do caminho especificado", e especifique um caminho com a permissão **de leitura + executar** para continuar.

Estas propriedades são suportadas para o serviço ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureBlobFS**. |Sim |
| url | Ponto final para Data Lake Storage `https://<accountname>.dfs.core.windows.net`Gen2 com o padrão de . | Sim |
| serviçoPrincipalId | Especifique a identificação do cliente do pedido. | Sim |
| serviçoPrincipalKey | Especifique a chave da aplicação. Marque este `SecureString` campo como um para armazená-lo de forma segura na Data Factory. Ou pode [fazer referência a um segredo armazenado no Cofre de Chaves Azure.](store-credentials-in-key-vault.md) | Sim |
| inquilino | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o tempo de execução da integração Azure ou um tempo de execução de integração auto-hospedado se a sua loja de dados estiver numa rede privada. Se não for especificado, o tempo de execução de integração do Azure padrão é utilizado. |Não |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos Azure,](data-factory-service-identity.md)que representa esta fábrica de dados específica. Pode utilizar diretamente esta identidade gerida para a autenticação Data Lake Storage Gen2, semelhante à utilização do seu próprio diretor de serviço. Permite que esta fábrica designada aceda e copie dados de ou para o seu Data Lake Storage Gen2.

Para utilizar identidades geridas para autenticação de recursos Azure, siga estes passos.

1. [Recuperar a Data Factory geriu informações](data-factory-service-identity.md#retrieve-managed-identity) de identidade copiando o valor do ID do objeto de **identidade gerido** gerado juntamente com a sua fábrica.

2. Conceda a identidade gerida a devida permissão. Consulte exemplos de como a permissão funciona em Data Lake Storage Gen2 a partir de listas de controlo de [acesso em ficheiros e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Como fonte**: No Storage Explorer, conceda pelo menos a permissão **de execução** para todas as pastas a montante e para o sistema de ficheiros, juntamente com a permissão **de leitura** para os ficheiros copiarem. Alternativamente, no controlo de acesso (IAM), conceda pelo menos o papel de Leitor de **Dados blob** de armazenamento.
    - **Como pia**: No Storage Explorer, conceda pelo menos a permissão **de executar** todas as pastas a montante e o sistema de ficheiros, juntamente com a permissão **de Escrita** para a pasta do lavatório. Alternativamente, no controlo de acesso (IAM), conceda pelo menos o papel de Contribuinte de **Dados blob** de armazenamento.

>[!NOTE]
>Se utilizar a Data Factory UI para autor e a identidade gerida não for definida com a função "Storage Blob Data Reader/Contributor" no IAM, ao fazer a ligação de teste ou navegar/navegar pastas, escolha "Testar a ligação ao caminho de ficheiros" ou "Navegar a partir de um caminho especificado", e especificar um caminho com a permissão **De Ler + Executar** para continuar.

>[!IMPORTANT]
>Se utilizar a PolyBase para carregar dados do Data Lake Storage Gen2 para o SQL Data Warehouse, quando utilizar a autenticação de identidade gerida para data lake storage Gen2, certifique-se de que também segue os passos 1 e 2 [nesta orientação](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) para 1) registe o seu servidor de Base de Dados SQL com o Azure Ative Directory (Azure AD) e 2) atribuindo a função de Colaborador de Dados de Armazenamento Blob ao seu servidor de base de dados SQL; o resto é tratado pela Data Factory. Se o seu Data Lake Storage Gen2 estiver configurado com um ponto final da Rede Virtual Azure, para utilizar a PolyBase para carregar dados a partir dele, deve utilizar a autenticação de identidade gerida conforme exigido pela PolyBase.

Estas propriedades são suportadas para o serviço ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureBlobFS**. |Sim |
| url | Ponto final para Data Lake Storage `https://<accountname>.dfs.core.windows.net`Gen2 com o padrão de . | Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o tempo de execução da integração Azure ou um tempo de execução de integração auto-hospedado se a sua loja de dados estiver numa rede privada. Se não for especificado, o tempo de execução de integração do Azure padrão é utilizado. |Não |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para `location` Data Lake Storage Gen2 em definições no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade `location` do tipo no conjunto de dados deve ser definida para **A Localização AzureBlobFS .** | Sim      |
| sistema de ficheiros | O nome do sistema de ficheiros Data Lake Storage Gen2.                              | Não       |
| folderPath | O caminho para uma pasta sob o sistema de ficheiros dado. Se pretender utilizar um wildcard para filtrar pastas, ignore esta definição e especifique-a nas definições de origem de atividade. | Não       |
| fileName   | O nome do ficheiro sob o ficheiro dadoSystem + pastaPath. Se pretender utilizar um wildcard para filtrar ficheiros, ignore esta definição e especifique-a nas definições de origem de atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [configurações](copy-activity-overview.md#configuration) de atividade seletivas e [Pipelines e atividades.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 como um tipo de fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para `storeSettings` Data Lake Storage Gen2 em definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade `storeSettings` do tipo em baixo deve ser definida para **AzureBlobFSReadSettings**. | Sim                                           |
| recursivo                | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Quando o recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres wildcard sob o sistema de ficheiros dado configurado no conjunto de dados para filtrar as pastas de origem. <br>Os wildcards `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único). Utilize `^` para escapar se o seu nome real de pasta tiver um wildcard ou este char de fuga no seu interior. <br>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| nome wildcardFile         | O nome do ficheiro com caracteres wildcard no sistema de ficheiros dado + pastaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único). Utilize `^` para escapar se o seu nome real de pasta tiver um wildcard ou este char de fuga no seu interior. Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Sim, `fileName` se não for especificado no conjunto de dados |
| alteradoDatetimeStart    | Filtro de ficheiros com base no atributo Last Modificado. Os ficheiros são selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados. | Não                                            |
| alteradoDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 como um tipo de pia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para `storeSettings` Data Lake Storage Gen2 em configurações em sumidouro de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade `storeSettings` do tipo em baixo deve ser definida para **AzureBlobFSWriteSettings**. | Sim      |
| copiarComportamento             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- PreserveHierarchy (predefinição)</b>: Preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro-alvo para a pasta-alvo.<br/><b>- Hierarquia flattena</b>: Todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não       |
| blockSizeInMB | Especifique o tamanho do bloco em MB usado para escrever dados para ADLS Gen2. Saiba mais [sobre block blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>O valor permitido é **entre 4 e 100 MB.** <br/>Por predefinição, a ADF determina automaticamente o tamanho do bloco com base no tipo e dados da loja de origem. Para cópias não binárias em ADLS Gen2, o tamanho do bloco predefinido é de 100 MB de modo a encaixar no máximo dados de 4,95 TB. Pode não ser ideal quando os seus dados não são grandes, especialmente quando utiliza o Tempo de Funcionamento de Integração Auto-hospedado com uma rede deficiente, resultando num tempo de funcionamento ou problema de desempenho. Pode especificar explicitamente um tamanho de bloco, enquanto garantir que o blockSizeInMB*50000 é grande o suficiente para armazenar os dados, caso contrário a execução da atividade de cópia falhará. | Não |
| maxConcurrentConnections | O número de ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSettings",
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

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos recursivos e copyBehavior

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursivos e copyBehavior.

| recursivo | copiarComportamento | Estrutura de pasta de origem | Alvo resultante |
|:--- |:--- |:--- |:--- |
| true |preservar Hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 |
| true |achatar a hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File5 |
| true |mergeFiles | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 conteúdos são fundidos num só ficheiro com um nome de ficheiro autogerado. |
| false |preservar Hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com File3, File4 e File5 não foi captada. |
| false |achatar a hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/><br/>Subpasta1 com File3, File4 e File5 não foi captada. |
| false |mergeFiles | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos File1 + File2 são fundidos num só ficheiro com um nome de ficheiro autogerado. nome autogerado para File1<br/><br/>Subpasta1 com File3, File4 e File5 não foi captada. |

## <a name="preserve-metadata-during-copy"></a>Preservar metadados durante a cópia

Quando copia ficheiros da Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 para O Armazenamento de Lagos Azure Data Gen2/Azure Blob, pode optar por preservar os metadados do ficheiro juntamente com os dados. Saiba mais com [a Preserve metadados.](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>Preservar ACLs de Data Lake Storage Gen1/Gen2

Quando copia ficheiros de Azure Data Lake Storage Gen1/Gen2 para Gen2, pode optar por preservar as listas de controlo de acesso POSIX (ACLs) juntamente com os dados. Saiba mais entre [os CaLs de Reserva de Data Lake Storage Gen1/Gen2 para gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para a Gen2 em geral, consulte [os dados da Cópia do Azure Data Lake Storage Gen1 para gen2 com a Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para obter um walk-through e boas práticas.

## <a name="mapping-data-flow-properties"></a>Mapeando propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever ficheiros do Azure Data Lake Storage Gen2 em formato JSON, Avro, Delimited Text ou Parquet. Para obter mais informações, consulte a [transformação](data-flow-source.md) de origem e a transformação do [sumidouro](data-flow-sink.md) na funcionalidade de fluxo de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

Na transformação de origem, pode ler-se a partir de um contentor, pasta ou ficheiro individual no Azure Data Lake Storage Gen2. O separador **opções Source** permite-lhe gerir a forma como os ficheiros são lidos. 

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

Na transformação da pia, pode escrever para um recipiente ou pasta em Azure Data Lake Storage Gen2. o separador **Definições** permite-lhe gerir a forma como os ficheiros são escritos.

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
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para **AzureBlobFSFile**. |Sim |
| folderPath | Caminho para a pasta em Data Lake Storage Gen2. Se não especificado, aponta para a raiz. <br/><br/>O filtro Wildcard é suportado. Os wildcards `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único). Utilize `^` para escapar se o seu nome real de pasta tiver um wildcard ou se este carvão de fuga estiver dentro. <br/><br/>Exemplos: sistema de ficheiros/pasta/. Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). |Não |
| fileName | Nome ou filtro wildcard para os ficheiros sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os `*` wildcards permitidos são `?` (corresponde a zero ou mais caracteres) e (corresponde a zero ou personagem único).<br/>- Exemplo 1:`"fileName": "*.csv"`<br/>- Exemplo 2:`"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de arquivo real tiver um wildcard ou este char de fuga está dentro.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída e preservar a **hierarquia** não é especificada no sumidouro de atividade, a atividade de cópia gera automaticamente o nome do ficheiro com o seguinte padrão: "*Dados.. atividade executar ID GUID]. [GUID se AHierarquia Achatada]. [formato se configurado]. [compressão se configurado]*", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se copiar de uma fonte tabular usando um nome de mesa em vez de uma consulta, o padrão de nome é "*[nome de mesa].[ formato]. [compressão se configurado]*", por exemplo, "MyTable.csv". |Não |
| alteradoDatetimeStart | Filtro de ficheiros com base no atributo Last Modificado. Os ficheiros são selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> O desempenho global do movimento de dados é afetado, permitindo esta definição quando pretende fazer filtro de ficheiros com enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| alteradoDatetimeEnd | Filtro de ficheiros com base no atributo Last Modificado. Os ficheiros são selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> O desempenho global do movimento de dados é afetado, permitindo esta definição quando pretende fazer filtro de ficheiros com enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat**e **ParquetFormat**. Desloque a propriedade **tipo** em **formato** a um destes valores. Para mais informações, consulte as secções de [formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para mais informações, consulte [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)de compressão .<br/>Os tipos suportados são **GZip,** **Deflate,** **BZip2**e **ZipDeflate**.<br/>Os níveis suportados são **Ideais** e **Mais Rápidos.** |Não |

>[!TIP]
>Para copiar todos os ficheiros sob uma pasta, especifique apenas **pastaPath.**<br>Para copiar um único ficheiro com um nome dado, especifique **a pastaPath** com uma peça de pasta e **o nome** do ficheiro com um nome de ficheiro.<br>Para copiar um subconjunto de ficheiros sob uma pasta, especifique **a pastaPath** com uma peça de pasta e **o nome** do ficheiro com um filtro wildcard. 

**Exemplo:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **AzureBlobFSSource**. |Sim |
| recursivo | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Quando o recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia.<br/>Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não |
| maxConcurrentConnections | O número de ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
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
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para **AzureBlobFSSink**. |Sim |
| copiarComportamento | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- PreserveHierarchy (predefinição)</b>: Preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro-alvo para a pasta-alvo.<br/><b>- Hierarquia flattena</b>: Todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não |
| maxConcurrentConnections | O número de ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
