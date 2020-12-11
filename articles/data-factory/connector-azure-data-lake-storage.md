---
title: Copiar e transformar dados em Azure Data Lake Storage Gen2
description: Aprenda a copiar dados de e para a Azure Data Lake Storage Gen2 e transforme dados em Azure Data Lake Storage Gen2 utilizando a Azure Data Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: 02cd14e29cfd51709e0a26e951a3124eb88bbd09
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094612"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiar e transformar dados em Azure Data Lake Storage Gen2 usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 (ADLS Gen2) é um conjunto de capacidades dedicadas à análise de big data incorporadas no [armazenamento Azure Blob.](../storage/blobs/storage-blobs-introduction.md) Pode utilizá-lo para interagir com os dados ao utilizar os paradigmas de armazenamento de objetos e do sistema de ficheiros.

Este artigo descreve como utilizar a Atividade de Cópia no Azure Data Factory para copiar dados de/para o Azure Data Lake Storage Gen2 e utilizar o Fluxo de Dados para transformar dados no Azure Data Lake Storage Gen2. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

>[!TIP]
>Para o cenário de migração de datas ou data warehouse, saiba mais com [a Use Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para Azure.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Data Lake Storage Gen2 é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Para a atividade copy, com este conector pode:

- Copie os dados de/para Azure Data Lake Storage Gen2 utilizando a chave de conta, o principal do serviço ou identidades geridas para autenticações de recursos Azure.
- Copie ficheiros como-é ou analise ou gere ficheiros com [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs.md).
- [Preservar metadados de ficheiros durante a cópia](#preserve-metadata-during-copy).
- [Preservar ACLs](#preserve-acls) ao copiar da Azure Data Lake Storage Gen1/Gen2.

## <a name="get-started"></a>Introdução

>[!TIP]
>Para obter uma análise de como utilizar o conector Gen2 de armazenamento de dados, consulte os dados de [carga no Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem informações sobre propriedades que são usadas para definir entidades da Data Factory específicas da Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

O conector Azure Data Lake Storage Gen2 suporta os seguintes tipos de autenticação. Consulte as secções correspondentes para mais detalhes:

- [Autenticação chave de conta](#account-key-authentication)
- [Autenticação principal do serviço](#service-principal-authentication)
- [Identidades geridas para autenticação de recursos Azure](#managed-identity)

>[!NOTE]
>- Se pretender utilizar o tempo de funcionamento da integração do Azure público para se ligar ao Data Lake Storage Gen2, aproveitando os **serviços de Permitir que a Microsoft tenha acesso a esta** opção de conta de armazenamento ativada na firewall de armazenamento Azure, deve utilizar a [autenticação de identidade gerida.](#managed-identity)
>- Quando utilizar a declaração PolyBase ou COPY para carregar dados no Azure Synapse Analytics, se a sua origem ou encenação data Lake Storage Gen2 estiver configurada com um ponto final da Rede Virtual Azure, deve utilizar a autenticação de identidade gerida conforme exigido pela Synapse. Consulte a secção [de autenticação de identidade gerida](#managed-identity) com mais pré-requisitos de configuração.

### <a name="account-key-authentication"></a>Autenticação chave de conta

Para utilizar a autenticação da chave de armazenamento, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureBlobFS**. |Sim |
| url | Ponto final para data lake storage Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net` . | Sim |
| contaKey | Chave de conta para Data Lake Storage Gen2. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou um tempo de integração auto-hospedado se a sua loja de dados estiver numa rede privada. Se esta propriedade não for especificada, o tempo de execução de integração Azure padrão é usado. |Não |

>[!NOTE]
>O ponto final secundário do sistema de ficheiros ADLS não é suportado quando se utiliza a autenticação da chave de conta. Pode utilizar outros tipos de autenticação.

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

1. Registe uma entidade de candidatura no Azure Ative Directory (Azure AD) seguindo os passos no Registo da [sua candidatura junto de um inquilino da Ad Azure.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:

    - ID da aplicação
    - Chave de aplicação
    - ID do inquilino

2. Conceda ao diretor de serviço a permissão adequada. Veja exemplos sobre como funciona a permissão no Data Lake Storage Gen2 a partir de listas de controlo de [acesso em ficheiros e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Como fonte**: No Explorador de Armazenamento, conceda pelo menos a permissão **de executar** para TODAS as pastas a montante e o sistema de ficheiros, juntamente com a permissão **de Leitura** para que os ficheiros copiem. Em alternativa, no controlo de acesso (IAM), conceda pelo menos a função **de Leitor de Dados blob de armazenamento.**
    - **Como pia**: No Explorador de Armazenamento, conceda pelo menos a permissão **de executar** para todas as pastas a montante e para o sistema de ficheiros, juntamente com a permissão **de escrever** para a pasta da pia. Em alternativa, no controlo de acesso (IAM), conceda pelo menos a função **de Contribuinte de Dados blob de armazenamento.**

>[!NOTE]
>Se utilizar a UI da Data Factory para autor e o principal de serviço não estiver definido com a função "Storage Blob Data Reader/Contributor" no IAM, ao fazer a ligação de teste ou as pastas de navegação/navegação/navegação,escolha "Testar a ligação ao caminho do ficheiro" ou "Navegar pelo caminho especificado", e especificar um caminho com a permissão **de Leitura + Executar** para continuar.

Estas propriedades são suportadas para o serviço ligado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureBlobFS**. |Sim |
| url | Ponto final para data lake storage Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net` . | Sim |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. | Sim |
| ServiçoPrincipalCredentialType | O tipo de credencial a utilizar para a autenticação principal do serviço. Os valores permitidos são **ServicePrincipalKey** e **ServicePrincipalCert**. | Sim |
| serviçoPrincipalCredential | A credencial principal de serviço. <br/> Quando utilizar **o ServicePrincipalKey** como tipo de credencial, especifique a chave da aplicação. Marque este campo como **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). <br/> Quando utilizar **o ServicePrincipalCert** como credencial, consulte um certificado no Cofre da Chave Azure. | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). <br/> Esta propriedade ainda é suportada como é para `servicePrincipalId`  +  `servicePrincipalKey` . À medida que a ADF adiciona a autenticação do novo certificado principal de serviço, o novo modelo de autenticação principal do serviço é `servicePrincipalId`  +  `servicePrincipalCredentialType`  +  `servicePrincipalCredential` . | Não |
| inquilino | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim |
| AzureCloudType | Para a autenticação principal do serviço, especifique o tipo de ambiente em nuvem Azure para o qual a sua aplicação Azure Ative Directory está registada. <br/> Os valores permitidos são **AzurePublic,** **AzureChina,** **AzureUsGovernment,** e **AzureGermany**. Por padrão, o ambiente em nuvem da fábrica de dados é utilizado. | Não |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou um tempo de integração auto-hospedado se a sua loja de dados estiver numa rede privada. Se não for especificado, utiliza-se o tempo de execução da integração Azure predefinido. |Não |

**Exemplo: utilização da autenticação principal do serviço**

Também pode armazenar a chave principal do serviço no Cofre da Chave Azure.

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
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

**Exemplo: utilização da autenticação do certificado principal de serviço**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos da Azure,](data-factory-service-identity.md)que representa esta fábrica de dados específica. Pode utilizar diretamente esta identidade gerida para a autenticação gen2 de armazenamento de dados, semelhante à utilização do seu próprio chefe de serviço. Permite que esta fábrica designada aceda e copie dados para ou a partir do seu Data Lake Storage Gen2.

Para utilizar identidades geridas para a autenticação de recursos Azure, siga estes passos.

1. [Recupere a data factory gerindo informações de identidade](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor do **ID** do objeto de identidade gerido gerado juntamente com a sua fábrica.

2. Conceda a permissão de identidade gerida. Veja exemplos sobre como funciona a permissão no Data Lake Storage Gen2 a partir de listas de controlo de [acesso em ficheiros e diretórios.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Como fonte**: No Explorador de Armazenamento, conceda pelo menos a permissão **de executar** para TODAS as pastas a montante e o sistema de ficheiros, juntamente com a permissão **de Leitura** para que os ficheiros copiem. Em alternativa, no controlo de acesso (IAM), conceda pelo menos a função **de Leitor de Dados blob de armazenamento.**
    - **Como pia**: No Explorador de Armazenamento, conceda pelo menos a permissão **de executar** para todas as pastas a montante e para o sistema de ficheiros, juntamente com a permissão **de escrever** para a pasta da pia. Em alternativa, no controlo de acesso (IAM), conceda pelo menos a função **de Contribuinte de Dados blob de armazenamento.**

>[!NOTE]
>Se utilizar a UI da Data Factory para autor e a identidade gerida não for definida com a função "Storage Blob Data Reader/Contributor" no IAM, ao fazer a ligação de teste ou as pastas de navegação/navegação/navegação,escolha "Testar a ligação ao caminho do ficheiro" ou "Navegar pelo caminho especificado", e especificar um caminho com a permissão **de Leitura + Executar** para continuar.

>[!IMPORTANT]
>Se utilizar a declaração PolyBase ou COPY para carregar dados da Data Lake Storage Gen2 em Azure Synapse Analytics, quando utilizar a autenticação de identidade gerida para data lake storage gen2, certifique-se de que também segue os passos 1 a 3 [nesta orientação.](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage) Esses passos registarão o seu servidor com Azure AD e atribuirão a função de Contribuinte de Dados de Armazenamento blob ao seu servidor. A Fábrica de Dados trata do resto. Se configurar o armazenamento blob com um ponto final da Rede Virtual Azure, também precisa de permitir **que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** ligados no menu de firewalls da conta de armazenamento Azure e redes **virtuais,** conforme exigido pelo Synapse.

Estas propriedades são suportadas para o serviço ligado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureBlobFS**. |Sim |
| url | Ponto final para data lake storage Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net` . | Sim |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou um tempo de integração auto-hospedado se a sua loja de dados estiver numa rede privada. Se não for especificado, utiliza-se o tempo de execução da integração Azure predefinido. |Não |

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

As seguintes propriedades são suportadas para data lake storage gen2 em `location` configurações no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade tipo em baixo `location` no conjunto de dados deve ser definida para **AzureBlobFSLocation**. | Sim      |
| sistema de ficheiros | O nome do sistema de ficheiros Data Lake Storage Gen2.                              | Não       |
| folderPath | O caminho para uma pasta sob o sistema de ficheiros dado. Se pretender utilizar um wildcard para filtrar pastas, ignore esta definição e especifique-a nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro sob o dado ficheiroSystem + pastaPa. Se pretender utilizar um wildcard para filtrar ficheiros, ignore esta definição e especifique-a nas definições de fonte de atividade. | Não       |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades,](copy-activity-overview.md#configuration) consulte configurações de atividades de Copy e [Pipelines e atividades](concepts-pipelines-activities.md). Esta secção fornece uma lista de propriedades suportadas pela fonte e pia de armazenamento do Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 como tipo de origem

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Tem várias opções para copiar dados da ADLS Gen2:

- Cópia do caminho especificado no conjunto de dados.
- Filtrar wildcard contra o caminho da pasta ou nome do ficheiro, ver `wildcardFolderPath` e `wildcardFileName` .
- Copie os ficheiros definidos num dado ficheiro de texto como conjunto de ficheiros, ver `fileListPath` .

As seguintes propriedades são suportadas para data lake storage gen2 sob `storeSettings` configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade tipo em baixo `storeSettings` deve ser definida para **AzureBlobFSReadSettings**. | Sim                                           |
| **_Localize os ficheiros para copiar:_* _ |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar a partir do sistema de ficheiros dado ou do caminho da pasta/ficheiro especificado no conjunto de dados. Se pretender copiar todos os ficheiros de um sistema/pasta de ficheiros, especificar ainda `wildcardFileName` como `_` . |  |
| OPÇÃO 2: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard sob o sistema de ficheiros configurado no conjunto de dados para filtrar as pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior. <br>Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 2: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard no sistema de ficheiros dado + folderPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior.  Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de ficheiros<br>- fileListPath | Indica copiar um determinado conjunto de ficheiros. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar, um ficheiro por linha, que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Ao utilizar esta opção, não especifique o nome do ficheiro no conjunto de dados. Ver mais exemplos em [exemplos da lista de ficheiros.](#file-list-examples) |Não |
| ***Definições adicionais:** _ |  | |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Note que quando a recursiva é definida como verdadeira e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia. <br>Os valores permitidos são _ *verdadeiros** (padrão) e **falsos**.<br>Esta propriedade não se aplica quando se `fileListPath` configura. |Não |
| eliminarFilesAfterCompletion | Indica se os ficheiros binários serão eliminados da loja de origem depois de se mudarem com sucesso para a loja de destino. A eliminação do ficheiro é por ficheiro, pelo que quando a atividade da cópia falhar, verá que alguns ficheiros já foram copiados para o destino e eliminados da fonte, enquanto outros ainda permanecem na loja de origem. <br/>Esta propriedade é válida apenas em cenário de cópia de ficheiros binários. O valor predefinido: falso. |Não |
| modificadoDatetimeStart    | Filtro de ficheiros com base no atributo: Última Modificação. <br>Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário utc no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributo de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem valor de data mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é maior ou igual com o valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem valor de data mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.<br/>Esta propriedade não se aplica quando se `fileListPath` configura. | Não                                            |
| modificadoDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| permitirPartitionDiscovery | Para os ficheiros que são divididos, especifique se analisar as divisórias do caminho do ficheiro e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **falsos** (padrão) e **verdadeiros.** | Não                                            |
| partitionRootPath | Quando a descoberta da partição estiver ativada, especifique o caminho da raiz absoluta para ler as pastas partidas como colunas de dados.<br/><br/>Se não for especificado, por defeito,<br/>- Quando utiliza o caminho do ficheiro no conjunto de dados ou na lista de ficheiros na fonte, o caminho da raiz da partição é o caminho configurado no conjunto de dados.<br/>- Quando utiliza o filtro de pasta wildcard, o caminho da raiz da partição é o sub-caminho antes do primeiro wildcard.<br/><br/>Por exemplo, assumindo que configura o caminho no conjunto de dados como "raiz/pasta/ano=2020/mês=08/dia=27":<br/>- Se especificar o caminho da raiz da partição como "raiz/pasta/ano=2020", a atividade da cópia gerará mais duas colunas `month` e com o valor `day` "08" e "27", respectivamente, para além das colunas dentro dos ficheiros.<br/>- Se não for especificado o caminho da raiz da partição, não será gerada nenhuma coluna extra. | Não                                            |
| maxConcurrentConnections | O número de ligações a ligar ao armazém simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                            |

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

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

As seguintes propriedades são suportadas para data lake storage gen2 em `storeSettings` configurações em pia de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade tipo em baixo `storeSettings` deve ser definida para **AzureBlobFSWriteSettings**. | Sim      |
| copyOportundo             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: Preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro-alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível da pasta alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não       |
| blockSizeInMB | Especifique o tamanho do bloco em MB usado para escrever dados para ADLS Gen2. Saiba mais [sobre Block Blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>O valor permitido é **entre 4 MB e 100 MB.** <br/>Por predefinição, a ADF determina automaticamente o tamanho do bloco com base no tipo e dados da sua loja de origem. Para a cópia não binária na ADLS Gen2, o tamanho do bloco predefinido é de 100 MB de modo a encaixar no máximo os dados de 4,95-TB. Pode não ser ótimo quando os seus dados não são grandes, especialmente quando utiliza o Tempo de Execução de Integração Auto-hospedado com uma rede deficiente, resultando em tempo de funcionamento ou problema de desempenho. Pode especificar explicitamente um tamanho de bloco, enquanto certifique-se de que o blocoSizeInMB*50000 é grande o suficiente para armazenar os dados, caso contrário, a execução da atividade da cópia falhará. | Não |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não       |

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

Esta secção descreve o comportamento resultante do caminho da pasta e nome do ficheiro com filtros wildcard.

| folderPath | fileName | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vazio, uso padrão) | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vazio, uso padrão) | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de ficheiros

Esta secção descreve o comportamento resultante da utilização do caminho da lista de ficheiros na fonte de atividade de cópia.

Assumindo que tem a seguinte estrutura de pasta de origem e quer copiar os ficheiros em negrito:

| Estrutura de origem da amostra                                      | Conteúdo em FileListToCopy.txt                             | Configuração ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| sistema de ficheiros<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sub-página1/File3.csv<br>Sub-página1/File5.csv | **No conjunto de dados:**<br>- Sistema de ficheiros: `filesystem`<br>- Caminho da pasta: `FolderA`<br><br>**Na fonte de atividade de cópia:**<br>- Caminho da lista de ficheiros: `filesystem/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar, um ficheiro por linha com o caminho relativo ao caminho configurado no conjunto de dados. |


### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos recursivos e copyBehavior

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursivos e de cópias.

| recursivo | copyOportundo | Estrutura de pasta de origem | Alvo resultante |
|:--- |:--- |:--- |:--- |
| true |preservarHierarquia | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |achatamento | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File5 |
| true |fusõesFilias | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 os conteúdos são fundidos num ficheiro com um nome de ficheiro autogerado. |
| false |preservarHierarquia | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subfolder1 com File3, File4 e File5 não é recolhido. |
| false |achatamento | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/><br/>Subfolder1 com File3, File4 e File5 não é recolhido. |
| false |fusõesFilias | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;O conteúdo do Ficheiro1 + Ficheiro2 é fundido num ficheiro com um nome de ficheiro autogerido. nome autogerado para File1<br/><br/>Subfolder1 com File3, File4 e File5 não é recolhido. |

## <a name="preserve-metadata-during-copy"></a>Preservar metadados durante a cópia

Quando copiar ficheiros da Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 para Azure Data Lake Storage Gen2/Azure Blob, pode optar por preservar os metadados do ficheiro juntamente com os dados. Saiba mais [com os metadados da Preserve.](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> Preservar ACLs a partir de Data Lake Storage Gen1/Gen2

Ao copiar ficheiros da Azure Data Lake Storage Gen1/Gen2 para a Gen2, pode optar por preservar as listas de controlo de acessos POSIX (ACLs) juntamente com dados. Saiba mais a partir de [ACLs preserve de Data Lake Storage Gen1/Gen2 para Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para a Gen2 em geral, consulte [os dados da Cópia de Azure Data Lake Storage Gen1 para a Gen2 com a Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para uma passagem e boas práticas.

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Quando estiver a transformar dados em fluxos de dados de mapeamento, pode ler e escrever ficheiros da Azure Data Lake Storage Gen2 nos seguintes formatos:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Modelo de dados comum (pré-visualização)](format-common-data-model.md#mapping-data-flow-properties)
* [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

As definições específicas do formato estão localizadas na documentação para este formato. Para obter mais informações, consulte [a transformação de Fonte no fluxo de dados de mapeamento](data-flow-source.md) e [transformação de sumidouro no fluxo de dados de mapeamento.](data-flow-sink.md)

### <a name="source-transformation"></a>Transformação de origem

Na transformação da fonte, pode ler-se a partir de um recipiente, pasta ou ficheiro individual na Azure Data Lake Storage Gen2. O separador **opções Source** permite-lhe gerir a forma como os ficheiros são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

**Caminho wildcard:** A utilização de um padrão wildcard instruirá a ADF a rodar através de cada pasta e ficheiro correspondente numa única transformação source. Esta é uma forma eficaz de processar vários ficheiros dentro de um único fluxo. Adicione vários padrões de correspondência wildcard com o sinal + que aparece ao pairar sobre o seu padrão wildcard existente.

A partir do seu recipiente de origem, escolha uma série de ficheiros que correspondam a um padrão. Apenas o recipiente pode ser especificado no conjunto de dados. O seu percurso wildcard deve, portanto, incluir também o seu caminho de pasta a partir da pasta raiz.

Exemplos wildcard:

* ```*``` Representa qualquer conjunto de caracteres
* ```**``` Representa o nidificação de diretório recursivo
* ```?``` Substitui um personagem
* ```[]``` Corresponde a um dos mais caracteres nos parênteses

* ```/data/sales/**/*.csv``` Obtém todos os ficheiros csv em /dados/vendas
* ```/data/sales/20??/**/``` Recebe todos os ficheiros do século XX
* ```/data/sales/*/*/*.csv``` Obtém ficheiros csv dois níveis em /dados/vendas
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtém todos os ficheiros csv em 2004 em dezembro começando com X ou Y prefixados por um número de dois dígitos

**Caminho da raiz da partição:** Se tiver pastas divididas na sua fonte de ficheiro com um ```key=value``` formato (por exemplo, ano=2019), então pode atribuir o nível superior dessa caixa de divisória a um nome de coluna no fluxo de dados do seu fluxo de dados.

Em primeiro lugar, desajuste um wildcard para incluir todos os caminhos que são as pastas divididas mais os ficheiros de folhas que deseja ler.

![Definições de ficheiros de fonte de partição](media/data-flow/partfile2.png "Definição de ficheiro de partição")

Utilize a definição do Caminho da Raiz da Partição para definir qual é o nível superior da estrutura da pasta. Quando visualizar o conteúdo dos seus dados através de uma pré-visualização de dados, verá que a ADF adicionará as divisórias resolvidas encontradas em cada um dos níveis de pasta.

![Caminho da raiz da partição](media/data-flow/partfile1.png "Pré-visualização do caminho da raiz da partição")

**Lista de ficheiros:** Este é um conjunto de arquivos. Crie um ficheiro de texto que inclua uma lista de ficheiros de caminhos relativos para processar. Aponte para este ficheiro de texto.

**Coluna para armazenar nome de ficheiro:** Guarde o nome do ficheiro de origem numa coluna nos seus dados. Introduza aqui um novo nome de coluna para armazenar a cadeia de nomes de ficheiros.

**Após a conclusão:** Opte por não fazer nada com o ficheiro de origem após o fluxo de dados, eliminar o ficheiro de origem ou mover o ficheiro de origem. Os caminhos para a mudança são relativos.

Para mover ficheiros de origem para outro pós-processamento de localização, selecione primeiro "Mover" para a operação de ficheiros. Em seguida, definir o diretório "de" Se não estiver a utilizar nenhum wildcard para o seu caminho, então a definição "a partir" será a mesma pasta que a sua pasta de origem.

Se tiver um caminho de origem com wildcard, a sua sintaxe será assim abaixo:

```/data/sales/20??/**/*.csv```

Pode especificar "a partir" como

```/data/sales```

E "para" como

```/backup/priorSales```

Neste caso, todos os ficheiros que foram obtidos em /dados/vendas são transferidos para /backup/priorSales.

> [!NOTE]
> As operações de ficheiros só funcionam quando inicia o fluxo de dados a partir de uma execução de gasoduto (um depurar do gasoduto ou execução de execução) que utiliza a atividade do Fluxo de Dados executar num oleoduto. As operações de *ficheiro não* são executadas no modo de depuramento do Fluxo de Dados.

**Filtrar por última modificação:** Pode filtrar quais os ficheiros que processa especificando um intervalo de datas de quando foram modificados pela última vez. Todos os horários estão na UTC. 

### <a name="sink-properties"></a>Propriedades de pia

Na transformação do lavatório, pode escrever para um recipiente ou pasta em Azure Data Lake Storage Gen2. o separador **Definições** permite-lhe gerir a forma como os ficheiros são escritos.

![opções de afundar](media/data-flow/file-sink-settings.png "opções de afundar")

**Limpe a pasta:** Determina se a pasta de destino é ou não apurada antes de os dados serem escritos.

**Opção nome de ficheiro:** Determina como os ficheiros de destino são nomeados na pasta de destino. As opções de nome do ficheiro são:
   * **Predefinição**: Permita que o Spark nomeie ficheiros com base em predefinições PART.
   * **Padrão**: Introduza um padrão que enumera os seus ficheiros de saída por partição. Por exemplo, **os empréstimos[n].csv** criarão loans1.csv, loans2.csv, e assim por diante.
   * **Por partição:** Introduza um nome de ficheiro por partição.
   * **Como dados na coluna**: Desave o ficheiro de saída ao valor de uma coluna. O caminho é relativo ao recipiente do conjunto de dados, não à pasta de destino. Se tiver um caminho de pasta no seu conjunto de dados, será ultrapassado.
   * **Saída para um único ficheiro**: Combine os ficheiros de saída divididos num único ficheiro nomeado. O caminho é relativo à pasta do conjunto de dados. Por favor, esteja ciente de que a operação de fusão te pode possivelmente falhar com base no tamanho do nó. Esta opção não é recomendada para grandes conjuntos de dados.

**Citar tudo:** Determina se deve incluir todos os valores em ações

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade getMetadata

Para saber mais detalhes sobre as propriedades, consulte a [atividade da GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para obter detalhes sobre as propriedades, verifique [a atividade de Eliminar](delete-activity.md)

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os modelos seguintes ainda são suportados como é para retrocompatibilidade. Sugere-se que utilize o novo modelo mencionado nas secções acima, e a UI de autoria da ADF passou a gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **AzureBlobFSFile**. |Sim |
| folderPath | Caminho para a pasta em Data Lake Storage Gen2. Se não for especificado, aponta para a raiz. <br/><br/>O filtro Wildcard é suportado. Os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou single character). Use `^` para escapar se o nome da sua pasta tiver um wildcard ou se este char de fuga estiver dentro. <br/><br/>Exemplos: sistema de ficheiros/pasta/. Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). |Não |
| fileName | Nome ou filtro wildcard para os ficheiros sob a especificada "folderPath". Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais).<br/>- Exemplo 1: `"fileName": "*.csv"`<br/>- Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de ficheiro real tiver um wildcard ou se este char de fuga estiver lá dentro.<br/><br/>Quando o data de ficheiro não é especificado para um conjunto de dados de saída e **a preservaçãoHierarquia** não é especificado na pia da atividade, a atividade da cópia gera automaticamente o nome do ficheiro com o seguinte padrão: "*Dados.". atividade executar ID GUID]. [GUID se FlattenHierarchy]. [formato se configurado]. [compressão se configurado]*", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se copiar de uma fonte tabular usando um nome de mesa em vez de uma consulta, o padrão de nome é "*[nome de mesa].[ formato]. [compressão se configurado]*", por exemplo, "MyTable.csv". |Não |
| modificadoDatetimeStart | Filtro de ficheiros com base no atributo Última Modificação. Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral do movimento de dados é afetado por permitir esta definição quando pretende fazer filtro de ficheiros com enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| modificadoDatetimeEnd | Filtro de ficheiros com base no atributo Última Modificação. Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral do movimento de dados é afetado por permitir esta definição quando pretende fazer filtro de ficheiros com enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, os seguintes tipos de formato de ficheiros são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat** e **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em **formato.** Para mais informações, consulte o [formato Text](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e secções [de formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos suportados são **GZip,** **Deflate,** **BZip2** e **ZipDeflate**.<br/>Os níveis suportados são **ideais** e **mais rápidos.** |Não |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique apenas **o apêndio.**<br>Para copiar um único ficheiro com um nome próprio, especifique **a pastaPath** com uma peça de pasta e **arquiveme** com um nome de ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **a pastaPath** com uma peça de pasta e **arquive o nome** de ficheiros com um filtro wildcard. 

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de origem de atividade de cópia de legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para **AzureBlobFSSource**. |Sim |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Quando a recursiva é definida como verdadeira e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia.<br/>Os valores permitidos são **verdadeiros** (padrão) e **falsos.** | Não |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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

### <a name="legacy-copy-activity-sink-model"></a>Modelo de pia de atividade de cópia legacy

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do lavatório de atividade de cópia deve ser definida para **AzureBlobFSSink**. |Sim |
| copyOportundo | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: Preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro-alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível da pasta alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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

Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)