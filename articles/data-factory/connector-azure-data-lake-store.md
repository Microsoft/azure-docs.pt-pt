---
title: Copiar dados para ou a partir da Azure Data Lake Storage Gen1
description: Saiba como copiar dados de lojas de dados de origem suportadas para a Azure Data Lake Store, ou da Data Lake Store para lojas de pias suportadas, utilizando a Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/31/2020
ms.openlocfilehash: a96cbd709fb5678e97202e222a7a335140b4da9a
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637841"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiar dados para ou a partir de Azure Data Lake Storage Gen1 usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados de e para a Azure Data Lake Storage Gen1. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Data Lake Storage Gen1 é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md) 
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, com este conector pode:

- Copie os ficheiros utilizando um dos seguintes métodos de autenticação: o principal do serviço ou identidades geridas para os recursos Azure.
- Copie ficheiros como é ou analise ou gere ficheiros com os [formatos de ficheiros suportados e os codecs de compressão](supported-file-formats-and-compression-codecs.md).
- [Preservar ACLs](#preserve-acls-to-data-lake-storage-gen2) ao copiar para Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Se copiar dados utilizando o tempo de integração auto-hospedado, configuure a firewall corporativa para permitir o tráfego de saída para e para a `<ADLS account name>.azuredatalakestore.net` `login.microsoftonline.com/<tenant>/oauth2/token` porta 443. Este último é o Serviço Azure Security Token que o tempo de integração precisa de comunicar para obter o token de acesso.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter uma análise de como utilizar o conector Azure Data Lake Store, consulte os dados de [carga na Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem informações sobre propriedades que são usadas para definir entidades da Data Factory específicas da Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Azure Data Lake Store:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | O `type` imóvel deve ser definido para **AzureDataLakeStore** . | Sim |
| dataLakeStoreUri | Informação sobre a conta da Azure Data Lake Store. Esta informação requer um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/` . | Sim |
| subscriptionId | O ID de assinatura Azure a que pertence a conta Data Lake Store. | Necessário para a pia |
| resourceGroupName | O nome do grupo de recursos Azure a que pertence a conta Data Lake Store. | Necessário para a pia |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou um tempo de integração auto-hospedado se a sua loja de dados estiver localizada numa rede privada. Se esta propriedade não for especificada, o tempo de execução de integração Azure padrão é usado. |Não |

### <a name="use-service-principal-authentication"></a>Utilizar a autenticação principal do serviço

Para utilizar a autenticação principal do serviço, siga estes passos.

1. Registe uma entidade de aplicação no Azure Ative Directory e conceda-lhe acesso à Data Lake Store. Para etapas detalhadas, consulte [a autenticação do Serviço ao Serviço.](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:

    - ID da Aplicação
    - Chave de aplicação
    - ID do inquilino

2. Conceda ao diretor de serviço a permissão adequada. Veja exemplos sobre como funciona a permissão no Data Lake Storage Gen1 do controlo de [acessos na Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como fonte** : No Data **Explorer**  >  **Access** , conceda pelo menos a permissão **de executar** para todas as pastas a montante, incluindo a raiz, juntamente com a permissão de **Leitura** para que os ficheiros copiem. Pode optar por adicionar a **Esta pasta e a todas as crianças** para recursiva, e adicionar como uma **permissão de acesso e uma entrada de permissão por defeito** . Não há requisitos sobre o controlo de acesso ao nível da conta (IAM).
    - **Como pia** : No Data **explorer**  >  **Access** , conceda pelo menos a permissão **de executar** para todas as pastas a montante, incluindo a raiz, juntamente com a permissão de **escrever** para a pasta do lavatório. Pode optar por adicionar a **Esta pasta e a todas as crianças** para recursiva, e adicionar como uma **permissão de acesso e uma entrada de permissão por defeito** .

As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como um `SecureString` para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique as informações do inquilino, como o nome de domínio ou o ID do inquilino, segundo o qual a sua aplicação reside. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim |
| AzureCloudType | Para a autenticação principal do serviço, especifique o tipo de ambiente em nuvem Azure para o qual a sua aplicação Azure Ative Directory está registada. <br/> Os valores permitidos são **AzurePublic,** **AzureChina,** **AzureUsGovernment,** e **AzureGermany** . Por padrão, o ambiente em nuvem da fábrica de dados é utilizado. | Não |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Utilizar identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos da Azure,](data-factory-service-identity.md)que representa esta fábrica de dados específica. Pode utilizar diretamente esta identidade gerida para a autenticação da Data Lake Store, semelhante à utilização do seu próprio chefe de serviço. Permite que esta fábrica designada aceda e copie dados para ou a partir da Data Lake Store.

Para utilizar identidades geridas para autenticação de recursos Azure, siga estes passos.

1. [Recupere a informação de identidade gerida](data-factory-service-identity.md#retrieve-managed-identity) pela fábrica de dados copiando o valor do "ID de aplicação de identidade de serviço" gerado juntamente com a sua fábrica.

2. Conceda o acesso de identidade gerido à Data Lake Store. Veja exemplos sobre como funciona a permissão no Data Lake Storage Gen1 do controlo de [acessos na Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como fonte** : No Data **Explorer**  >  **Access** , conceda pelo menos a permissão **de executar** para todas as pastas a montante, incluindo a raiz, juntamente com a permissão de **Leitura** para que os ficheiros copiem. Pode optar por adicionar a **Esta pasta e a todas as crianças** para recursiva, e adicionar como uma **permissão de acesso e uma entrada de permissão por defeito** . Não há requisitos sobre o controlo de acesso ao nível da conta (IAM).
    - **Como pia** : No Data **explorer**  >  **Access** , conceda pelo menos a permissão **de executar** para todas as pastas a montante, incluindo a raiz, juntamente com a permissão de **escrever** para a pasta do lavatório. Pode optar por adicionar a **Esta pasta e a todas as crianças** para recursiva, e adicionar como uma **permissão de acesso e uma entrada de permissão por defeito** .

Na Azure Data Factory, não precisa de especificar quaisquer propriedades além das informações gerais da Data Lake Store no serviço ligado.

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

As seguintes propriedades são suportadas para Azure Data Lake Store Gen1 `location` em configurações no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade tipo em baixo `location` no conjunto de dados deve ser definida para **AzureDataLakeStoreLocation** . | Sim      |
| folderPath | O caminho para uma pasta. Se pretender utilizar um wildcard para filtrar pastas, ignore esta definição e especifique-a nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro sob a pasta DadaPa. Se pretender utilizar um wildcard para filtrar ficheiros, ignore esta definição e especifique-a nas definições de fonte de atividade. | Não       |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades,](concepts-pipelines-activities.md)consulte Pipelines . Esta secção fornece uma lista de propriedades suportadas pela fonte e pia da Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para Azure Data Lake Store Gen1 `storeSettings` em definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------- |
| tipo                     | A propriedade tipo em baixo `storeSettings` deve ser definida para **AzureDataLakeStoreReadSettings** . | Sim                                          |
| **_Localize os ficheiros para copiar:_* _ |  |  |
| OPÇÃO 1: caminho estático<br> | Cópia do caminho da pasta/ficheiro especificado no conjunto de dados. Se pretender copiar todos os ficheiros de uma pasta, especificar ainda `wildcardFileName` como `_` . |  |
| OPÇÃO 2: intervalo de nomes<br>- listAfter | Recupere as pastas/ficheiros cujo nome está atrás deste valor alfabeticamente (exclusivo). Utiliza o filtro do lado do serviço para a ADLS Gen1, que proporciona um melhor desempenho do que um filtro wildcard. <br/>A fábrica de dados aplica este filtro ao caminho definido no conjunto de dados, e apenas um nível de entidade é suportado. Veja mais exemplos em [exemplos de filtro de gama Name](#name-range-filter-examples). | Não |
| OPÇÃO 2: intervalo de nomes<br/>- lista Antes de | Recupere as pastas/ficheiros cujo nome esteja antes deste valor alfabeticamente (inclusive). Utiliza o filtro do lado do serviço para a ADLS Gen1, que proporciona um melhor desempenho do que um filtro wildcard.<br>A fábrica de dados aplica este filtro ao caminho definido no conjunto de dados, e apenas um nível de entidade é suportado. Veja mais exemplos em [exemplos de filtro de gama Name](#name-range-filter-examples). | Não |
| OPÇÃO 3: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard para filtrar pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior. <br>Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard sob a pasta DadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior.  Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 4: uma lista de ficheiros<br>- fileListPath | Indica copiar um determinado conjunto de ficheiros. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar, um ficheiro por linha, que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Ao utilizar esta opção, não especifique o nome do ficheiro no conjunto de dados. Ver mais exemplos em [exemplos da lista de ficheiros.](#file-list-examples) |Não |
| ***Definições adicionais:** _ |  | |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Note que quando a recursiva é definida como verdadeira e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia. <br>Os valores permitidos são _ *verdadeiros* * (padrão) e **falsos** .<br>Esta propriedade não se aplica quando se `fileListPath` configura. |Não |
| eliminarFilesAfterCompletion | Indica se os ficheiros binários serão eliminados da loja de origem depois de se mudarem com sucesso para a loja de destino. A eliminação do ficheiro é por ficheiro, pelo que quando a atividade da cópia falhar, verá que alguns ficheiros já foram copiados para o destino e eliminados da fonte, enquanto outros ainda permanecem na loja de origem. <br/>Esta propriedade é válida apenas em cenário de cópia de ficheiros binários. O valor predefinido: falso. |Não |
| modificadoDatetimeStart    | Filtro de ficheiros com base no atributo: Última Modificação. <br>Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário utc no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributo de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem valor de data mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é maior ou igual com o valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem valor de data mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.<br/>Esta propriedade não se aplica quando se `fileListPath` configura. | Não                                            |
| modificadoDatetimeEnd      | O mesmo que acima.                                               | Não                                           |
| permitirPartitionDiscovery | Para os ficheiros que são divididos, especifique se analisar as divisórias do caminho do ficheiro e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **falsos** (padrão) e **verdadeiros.** | Não                                            |
| partitionRootPath | Quando a descoberta da partição estiver ativada, especifique o caminho da raiz absoluta para ler as pastas partidas como colunas de dados.<br/><br/>Se não for especificado, por defeito,<br/>- Quando utiliza o caminho do ficheiro no conjunto de dados ou na lista de ficheiros na fonte, o caminho da raiz da partição é o caminho configurado no conjunto de dados.<br/>- Quando utiliza o filtro de pasta wildcard, o caminho da raiz da partição é o sub-caminho antes do primeiro wildcard.<br/><br/>Por exemplo, assumindo que configura o caminho no conjunto de dados como "raiz/pasta/ano=2020/mês=08/dia=27":<br/>- Se especificar o caminho da raiz da partição como "raiz/pasta/ano=2020", a atividade da cópia gerará mais duas colunas `month` e com o valor `day` "08" e "27", respectivamente, para além das colunas dentro dos ficheiros.<br/>- Se não for especificado o caminho da raiz da partição, não será gerada nenhuma coluna extra. | Não                                            |
| maxConcurrentConnections | O número de ligações a ligar ao armazém simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                           |

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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store como pia

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

As seguintes propriedades são suportadas para Azure Data Lake Store Gen1 `storeSettings` em configurações no lavatório de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade tipo em baixo `storeSettings` deve ser definida para **AzureDataLakeStoreWriteSettings** . | Sim      |
| copyOportundo             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: Preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro-alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível da pasta alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não       |
| expiraçãoDateTime | Especifica o prazo de validade dos ficheiros escritos. O tempo é aplicado ao tempo UTC no formato de "2020-03-01T08:00:00Z". Por predefinição é NU, o que significa que os ficheiros escritos nunca expiram. | Não |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não       |

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
### <a name="name-range-filter-examples"></a>Exemplos de filtro de gama de nomes

Esta secção descreve o comportamento resultante dos filtros da gama de nomes.

| Estrutura de origem da amostra | Configuração ADF | Resultado |
|:--- |:--- |:--- |
|raiz<br/>&nbsp;&nbsp;&nbsp;&nbsp;um<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;machado<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;bx.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;c<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;cx.csv| **No conjunto de dados:**<br>- Caminho da pasta: `root`<br><br>**Na fonte de atividade de cópia:**<br>- Lista depois: `a`<br>- Lista antes: `b`| Em seguida, os seguintes ficheiros serão copiados:<br><br>raiz<br/>&nbsp;&nbsp;&nbsp;&nbsp;machado<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv |

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
| raiz<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sub-página1/File3.csv<br>Sub-página1/File5.csv | **No conjunto de dados:**<br>- Caminho da pasta: `root/FolderA`<br><br>**Na fonte de atividade de cópia:**<br>- Caminho da lista de ficheiros: `root/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar, um ficheiro por linha com o caminho relativo ao caminho configurado no conjunto de dados. |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações `recursive` e `copyBehavior` valores.

| recursivo | copyOportundo | Estrutura de pasta de origem | Alvo resultante |
|:--- |:--- |:--- |:--- |
| true |preservarHierarquia | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |achatamento | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File5 |
| true |fusõesFilias | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;O conteúdo do File1 + File2 + File3 + File4 + File5 é fundido num único ficheiro, com um nome de ficheiro autogerado. |
| false |preservarHierarquia | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subfolder1 com File3, File4 e File5 não são recolhidos. |
| false |achatamento | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/><br/>Subfolder1 com File3, File4 e File5 não são recolhidos. |
| false |fusõesFilias | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;O conteúdo do Ficheiro1 + Ficheiro2 é fundido num ficheiro com nome de ficheiro autogerido. nome autogerado para File1<br/><br/>Subfolder1 com File3, File4 e File5 não são recolhidos. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Preservar ACLs para data lake storage gen2

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para a Gen2 em geral, consulte [os dados da Cópia de Azure Data Lake Storage Gen1 para a Gen2 com a Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para uma passagem e boas práticas.

Se pretender replicar as listas de controlo de acesso (ACLs) juntamente com ficheiros de dados quando atualizar da Data Lake Storage Gen1 para data lake storage gen2, consulte [Preserve ACLs da Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Quando estiver a transformar dados em fluxos de dados de mapeamento, pode ler e escrever ficheiros da Azure Data Lake Storage Gen1 nos seguintes formatos:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

As definições específicas do formato estão localizadas na documentação para este formato. Para obter mais informações, consulte [a transformação de Fonte no fluxo de dados de mapeamento](data-flow-source.md) e [transformação de sumidouro no fluxo de dados de mapeamento.](data-flow-sink.md)

### <a name="source-transformation"></a>Transformação de origem

Na transformação da fonte, pode ler-se a partir de um recipiente, pasta ou ficheiro individual na Azure Data Lake Storage Gen1. O separador **opções Source** permite-lhe gerir a forma como os ficheiros são lidos. 

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

Na transformação do lavatório, pode escrever para um recipiente ou pasta na Azure Data Lake Storage Gen1. o separador **Definições** permite-lhe gerir a forma como os ficheiros são escritos.

![opções de afundar](media/data-flow/file-sink-settings.png "opções de afundar")

**Limpe a pasta:** Determina se a pasta de destino é ou não apurada antes de os dados serem escritos.

**Opção nome de ficheiro:** Determina como os ficheiros de destino são nomeados na pasta de destino. As opções de nome do ficheiro são:
   * **Predefinição** : Permita que o Spark nomeie ficheiros com base em predefinições PART.
   * **Padrão** : Introduza um padrão que enumera os seus ficheiros de saída por partição. Por exemplo, **os empréstimos[n].csv** criarão loans1.csv, loans2.csv, e assim por diante.
   * **Por partição:** Introduza um nome de ficheiro por partição.
   * **Como dados na coluna** : Desave o ficheiro de saída ao valor de uma coluna. O caminho é relativo ao recipiente do conjunto de dados, não à pasta de destino. Se tiver um caminho de pasta no seu conjunto de dados, será ultrapassado.
   * **Saída para um único ficheiro** : Combine os ficheiros de saída divididos num único ficheiro nomeado. O caminho é relativo à pasta do conjunto de dados. Por favor, esteja ciente de que a operação de fusão te pode possivelmente falhar com base no tamanho do nó. Esta opção não é recomendada para grandes conjuntos de dados.

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
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **AzureDataLakeStoreFile** . |Sim |
| folderPath | Caminho para a pasta na Data Lake Store. Se não for especificado, aponta para a raiz. <br/><br/>O filtro Wildcard é suportado. Os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou single character). Use `^` para escapar se o nome da sua pasta tiver um wildcard ou este char de fuga no interior. <br/><br/>Por exemplo: rootfolder/subfolder/. Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). |Não |
| fileName | Nome ou filtro wildcard para os ficheiros sob a especificada "folderPath". Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais).<br/>- Exemplo 1: `"fileName": "*.csv"`<br/>- Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de ficheiro real tiver um wildcard ou este char de fuga dentro.<br/><br/>Quando o data de ficheiro não é especificado para um conjunto de dados de saída e **a preservaçãoHierarquia** não é especificado na pia da atividade, a atividade da cópia gera automaticamente o nome do ficheiro com o seguinte padrão: " *Dados.". atividade executar ID GUID]. [GUID se FlattenHierarchy]. [formato se configurado]. [compressão se configurado]* ", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se copiar de uma fonte tabular usando um nome de mesa em vez de uma consulta, o padrão de nome é " *[nome de mesa].[ formato]. [compressão se configurado]* ", por exemplo, "MyTable.csv". |Não |
| modificadoDatetimeStart | Filtro de ficheiros com base no atributo Última Modificação. Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral do movimento de dados é afetado por permitir esta definição quando pretende fazer filtro de ficheiros com enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| modificadoDatetimeEnd | Filtro de ficheiros com base no atributo Última Modificação. Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral do movimento de dados é afetado por permitir esta definição quando pretende fazer filtro de ficheiros com enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, os seguintes tipos de formato de ficheiros são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat** e **ParquetFormat** . Desa um destes valores, o **tipo** de propriedade em **formato.** Para mais informações, consulte o [formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e secções [de formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos suportados são **GZip,** **Deflate,** **BZip2** e **ZipDeflate** .<br/>Os níveis suportados são **ideais** e **mais rápidos.** |Não |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique apenas **o apêndio.**<br>Para copiar um único ficheiro com um nome específico, especifique **a pastaPath** com uma peça de pasta e **arquiveme** com um nome de ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **a pastaPath** com uma peça de pasta e **arquive o nome** de ficheiros com um filtro wildcard. 

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de origem de atividade de cópia de legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A `type` propriedade da fonte de atividade de cópia deve ser definida para **AzureDataLakeStoreSource** . |Sim |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Quando `recursive` é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobragem vazia não é copiada ou criada na pia. Os valores permitidos são **verdadeiros** (padrão) e **falsos.** | Não |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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

### <a name="legacy-copy-activity-sink-model"></a>Modelo de pia de atividade de cópia legacy

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A `type` propriedade do lavatório de atividade de cópia deve ser definida para **AzureDataLakeStoreSink** . |Sim |
| copyOportundo | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: Preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro-alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível da pasta alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, o nome do ficheiro é autogerado. | Não |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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

Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)