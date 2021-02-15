---
title: Copiar dados do Serviço de Armazenamento Simples da Amazon (S3)
description: Saiba como copiar dados do Amazon Simple Storage Service (S3) para armazenar dados de sumidouros suportados utilizando a Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/14/2021
ms.openlocfilehash: 2680c930bfa8451eec7dd518d3c535e0d04046cc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387891"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Copie os dados do Serviço de Armazenamento Simples da Amazon utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versão atual](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do Amazon Simple Storage Service (Amazon S3). Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

>[!TIP]
>Para saber mais sobre o cenário de migração de dados da Amazon S3 para o Azure Storage, consulte [a Use Azure Data Factory para migrar dados do Amazon S3 para o Azure Storage](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Amazon S3 é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, este conector Amazon S3 suporta a cópia de ficheiros como está ou a analisar ficheiros com os [formatos de ficheiros suportados e os codecs de compressão](supported-file-formats-and-compression-codecs.md). Também pode optar por [preservar os metadados de ficheiros durante a cópia.](#preserve-metadata-during-copy) O conector utiliza [a versão 4 da assinatura AWS](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) para autenticar pedidos para S3.

>[!TIP]
>Pode utilizar este conector Amazon S3 para copiar dados de *qualquer fornecedor de armazenamento compatível com S3*, como o Google Cloud [Storage](connector-google-cloud-storage.md). Especifique o URL de serviço correspondente na configuração do serviço ligado.

## <a name="required-permissions"></a>Permissões obrigatórias

Para copiar dados do Amazon S3, certifique-se de que lhe foram concedidas as seguintes permissões para operações de objetos Amazon S3: `s3:GetObject` e `s3:GetObjectVersion` .

Se utilizar a UI da Data Factory para autor, são necessárias permissões adicionais `s3:ListAllMyBuckets` para `s3:ListBucket` / `s3:GetBucketLocation` operações como testar a ligação ao serviço ligado e navegar a partir da raiz. Se não quiser conceder estas permissões, pode escolher as opções de "Testar a ligação para o caminho do ficheiro" ou "Navegar a partir do caminho especificado" a partir da UI.

Para obter a lista completa das permissões do Amazon S3, consulte [especificar permissões numa política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) no site da AWS.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do Amazon S3.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para um serviço ligado à Amazon S3:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **AmazonS3**. | Yes |
| authenticationType | Especifique o tipo de autenticação utilizado para ligar ao Amazon S3. Pode optar por utilizar as chaves de acesso para uma conta AWS Identity and Access Management (IAM) ou [credenciais de segurança temporárias.](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)<br>Os valores permitidos são: `AccessKey` (padrão) e `TemporarySecurityCredentials` . |No |
| accessKeyId | Identificação da chave de acesso secreta. |Yes |
| SecretAccessKey | A chave de acesso secreto em si. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). |Yes |
| sessionToken | Aplicável ao utilizar a autenticação [temporária de credenciais de segurança.](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) Saiba como solicitar credenciais de [segurança temporárias](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getsessiontoken) à AWS.<br>Nota AWS a credencial temporária expira entre 15 minutos a 36 horas com base em configurações. Certifique-se de que a sua credencial é válida quando a atividade executa, especialmente para a carga de trabalho operacionalizada - por exemplo, pode aurépi-la periodicamente e armazená-la no Cofre da Chave Azure.<br>Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). |No |
| serviceUrl | Especifique o ponto final personalizado S3 se estiver a copiar dados de um fornecedor de armazenamento compatível com S3, para além do serviço oficial Amazon S3. Por exemplo, para copiar dados do Google Cloud Storage, especifique `https://storage.googleapis.com` . | No |
| forcePathStyle | Indica se deve utilizar o acesso ao [estilo do caminho](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) S3 em vez de acesso virtual ao estilo hospedado. Os valores permitidos são: **falso** (padrão), **verdadeiro.**<br>Se estiver a ligar-se ao fornecedor de armazenamento compatível com S3, para além do serviço oficial amazon S3, e essa loja de dados requer acesso ao estilo do caminho (por exemplo, [Oracle Cloud Storage),](https://docs.oracle.com/iaas/Content/Object/Tasks/s3compatibleapi.htm)desempenhe esta propriedade como verdadeira. Verifique a documentação de cada loja de dados sobre se o acesso ao estilo do caminho é necessário ou não. |No |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou o tempo de integração auto-hospedado (se a sua loja de dados estiver numa rede privada). Se esta propriedade não for especificada, o serviço utiliza o tempo de execução de integração Azure padrão. |No |

>[!TIP]
>Especifique o URL de serviço S3 personalizado se estiver a copiar dados de um armazenamento compatível com S3 que não seja o serviço oficial amazon S3.

**Exemplo: utilização da autenticação da chave de acesso**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: utilização de autenticação de credencial de segurança temporária**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "authenticationType": "TemporarySecurityCredentials",
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "sessionToken": {
                "type": "SecureString",
                "value": "<session token>"
            }
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

As seguintes propriedades são suportadas para o Amazon S3 `location` em configurações num conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade **tipo** `location` em baixo num conjunto de dados deve ser definida para **AmazonS3Location**. | Yes      |
| baldeName | O nome do balde S3.                                          | Yes      |
| folderPath | O caminho para a pasta sob o balde dado. Se pretender utilizar um wildcard para filtrar a pasta, ignore esta definição e especifique-a nas definições de origem de atividade. | No       |
| fileName   | O nome do ficheiro sob o caminho do balde e da pasta. Se pretender utilizar um wildcard para filtrar ficheiros, ignore esta definição e especifique-a nas definições de origem de atividade. | No       |
| versão | A versão do objeto S3, se a versão S3 estiver ativada. Se não for especificado, a versão mais recente será recolhida. |No |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades que a fonte do Amazon S3 suporta.

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3 como tipo de fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para o Amazon S3 `storeSettings` em configurações numa fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| tipo                     | A propriedade **tipo** em baixo `storeSettings` deve ser definida para **AmazonS3ReadSettings**. | Yes                                                         |
| ***Localize os ficheiros para copiar:*** |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar a partir do balde dado ou do caminho da pasta/ficheiro especificado no conjunto de dados. Se pretender copiar todos os ficheiros de um balde ou pasta, especificar ainda `wildcardFileName` como `*` . |  |
| OPÇÃO 2: Prefixo S3<br>- prefixo | Prefixo para o nome da chave S3 sob o balde dado configurado num conjunto de dados para filtrar ficheiros S3 de origem. As teclas S3 cujos nomes começam `bucket_in_dataset/this_prefix` por ser selecionadas. Utiliza o filtro do lado de serviço da S3, que proporciona um melhor desempenho do que um filtro wildcard.<br/><br/>Quando utilizar o prefixo e optar por copiar para a pia baseada em ficheiros com hierarquia de preservação, note o sub-caminho após a preservação do último "/" no prefixo. Por exemplo, tem fonte  `bucket/folder/subfolder/file.txt` , e configurar prefixo como `folder/sub` , então o caminho de arquivo preservado é `subfolder/file.txt` . | No |
| OPÇÃO 3: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard sob o balde dado configurado num conjunto de dados para filtrar pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais). Use `^` para escapar se o nome da sua pasta tiver um wildcard ou este personagem de fuga no interior. <br>Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | No                                            |
| OPÇÃO 3: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard sob o caminho do balde e da pasta (ou caminho da pasta wildcard) para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais). Use `^` para escapar se o seu nome de ficheiro tiver um wildcard ou este personagem de fuga dentro.  Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Yes |
| OPÇÃO 4: uma lista de ficheiros<br>- fileListPath | Indica copiar um determinado conjunto de ficheiros. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar, um ficheiro por linha, que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Quando estiver a utilizar esta opção, não especifique um nome de ficheiro no conjunto de dados. Ver mais exemplos em [exemplos da lista de ficheiros.](#file-list-examples) |No |
| ***Definições adicionais:*** |  | |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Note que quando **a recursiva** é definida como **verdadeira** e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia. <br>Os valores permitidos são **verdadeiros** (padrão) e **falsos.**<br>Esta propriedade não se aplica quando se `fileListPath` configura. |No |
| eliminarFilesAfterCompletion | Indica se os ficheiros binários serão eliminados da loja de origem depois de se mudarem com sucesso para a loja de destino. A eliminação do ficheiro é por ficheiro, pelo que quando a atividade da cópia falhar, verá que alguns ficheiros já foram copiados para o destino e eliminados da fonte, enquanto outros ainda permanecem na loja de origem. <br/>Esta propriedade é válida apenas em cenário de cópia de ficheiros binários. O valor predefinido: falso. |No |
| modificadoDatetimeStart    | Os ficheiros são filtrados com base no atributo: última modificada. <br>Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado a um fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser **NUAS,** o que significa que nenhum filtro de atributos de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tiver um valor de data, mas é `modifiedDatetimeEnd` **NU,** serão selecionados os ficheiros cujo último atributo modificado é superior ou igual ao valor da data.  Quando `modifiedDatetimeEnd` tiver um valor de data mas é `modifiedDatetimeStart` **NU,** serão selecionados os ficheiros cujo último atributo modificado é inferior ao valor da data.<br/>Esta propriedade não se aplica quando se `fileListPath` configura. | No                                            |
| modificadoDatetimeEnd      | Mesmo que acima.                                               | No                                                          |
| permitirPartitionDiscovery | Para os ficheiros que são divididos, especifique se analisar as divisórias do caminho do ficheiro e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **falsos** (padrão) e **verdadeiros.** | No                                            |
| partitionRootPath | Quando a descoberta da partição estiver ativada, especifique o caminho da raiz absoluta para ler as pastas partidas como colunas de dados.<br/><br/>Se não for especificado, por defeito,<br/>- Quando utiliza o caminho do ficheiro no conjunto de dados ou na lista de ficheiros na fonte, o caminho da raiz da partição é o caminho configurado no conjunto de dados.<br/>- Quando utiliza o filtro de pasta wildcard, o caminho da raiz da partição é o sub-caminho antes do primeiro wildcard.<br/>- Quando se utiliza prefixo, o caminho da raiz da partição é sub-caminho antes do último "/". <br/><br/>Por exemplo, assumindo que configura o caminho no conjunto de dados como "raiz/pasta/ano=2020/mês=08/dia=27":<br/>- Se especificar o caminho da raiz da partição como "raiz/pasta/ano=2020", a atividade da cópia gerará mais duas colunas `month` e com o valor `day` "08" e "27", respectivamente, para além das colunas dentro dos ficheiros.<br/>- Se não for especificado o caminho da raiz da partição, não será gerada nenhuma coluna extra. | No                                            |
| maxConcurrentConnections | O número de ligações simultâneas à loja de dados. Especifique apenas quando pretende limitar as ligações simultâneas à loja de dados. | No                                                          |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e ficheiro

Esta secção descreve o comportamento resultante do caminho da pasta e nome do ficheiro com filtros wildcard.

| balde | key | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| balde | `Folder*/*` | false | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*` | true | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*.csv` | false | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*.csv` | true | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de ficheiros

Esta secção descreve o comportamento resultante da utilização de um caminho de lista de ficheiros numa fonte de atividade copy.

Assuma que tem a seguinte estrutura de pasta de origem e quer copiar os ficheiros em negrito:

| Estrutura de origem da amostra                                      | Conteúdo em FileListToCopy.txt                             | Configuração da Fábrica de Dados                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sub-página1/File3.csv<br>Sub-página1/File5.csv | **No conjunto de dados:**<br>- Balde: `bucket`<br>- Caminho da pasta: `FolderA`<br><br>**Na fonte de atividade do Copy:**<br>- Caminho da lista de ficheiros: `bucket/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar, um ficheiro por linha, com o caminho relativo para o caminho configurado no conjunto de dados. |

## <a name="preserve-metadata-during-copy"></a>Preservar metadados durante a cópia

Ao copiar ficheiros do Amazon S3 para a Azure Data Lake Storage Gen2 ou do armazenamento Azure Blob, pode optar por preservar os metadados do ficheiro juntamente com os dados. Saiba mais [com os metadados da Preserve.](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade getMetadata

Para obter detalhes sobre as propriedades, consulte a [atividade da GetMetadata.](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para obter detalhes sobre as propriedades, verifique [a atividade de Excluir](delete-activity.md).

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os modelos seguintes ainda são suportados como é para retrocompatibilidade. Sugerimos que use o novo modelo mencionado anteriormente. A UI de autoria da Data Factory passou a gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** do conjunto de dados deve ser definida para **AmazonS3Object**. |Yes |
| baldeName | O nome do balde S3. O filtro wildcard não é suportado. |Sim para a atividade Copy ou Lookup, não para a atividade GetMetadata |
| key | O nome ou filtro wildcard da chave do objeto S3 sob o balde especificado. Só se aplica quando a propriedade do **prefixo** não é especificada. <br/><br/>O filtro wildcard é suportado tanto para a parte da pasta como para a parte do nome do ficheiro. Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais).<br/>- Exemplo 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- Exemplo 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Veja mais exemplos nos [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). Use `^` para escapar se a sua pasta ou nome de ficheiro tiver um wildcard ou este personagem de fuga no interior. |No |
| prefixo | Prefixo para a tecla de objeto S3. São selecionados objetos cujas teclas começam com este prefixo. Só se aplica quando a propriedade **chave** não é especificada. |No |
| versão | A versão do objeto S3, se a versão S3 estiver ativada. Se uma versão não for especificada, a versão mais recente será recolhida. |No |
| modificadoDatetimeStart | Os ficheiros são filtrados com base no atributo: última modificada. Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Tenha em atenção que ativar esta definição afetará o desempenho geral do movimento de dados quando pretender filtrar grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser **NUAS,** o que significa que nenhum filtro de atributos de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tiver um valor de data, mas é `modifiedDatetimeEnd` **NU,** serão selecionados os ficheiros cujo último atributo modificado é superior ou igual ao valor da data.  Quando `modifiedDatetimeEnd` tiver um valor de data mas é `modifiedDatetimeStart` NU, os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| No |
| modificadoDatetimeEnd | Os ficheiros são filtrados com base no atributo: última modificada. Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Tenha em atenção que ativar esta definição afetará o desempenho geral do movimento de dados quando pretender filtrar grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser **NUAS,** o que significa que nenhum filtro de atributos de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tiver um valor de data, mas é `modifiedDatetimeEnd` **NU,** serão selecionados os ficheiros cujo último atributo modificado é superior ou igual ao valor da data.  Quando `modifiedDatetimeEnd` tiver um valor de data mas é `modifiedDatetimeStart` **NU,** serão selecionados os ficheiros cujo último atributo modificado é inferior ao valor da data.| No |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, suportam-se os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em **formato.** Para mais informações, consulte o [formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e secções [de formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos suportados são **GZip,** **Deflate,** **BZip2** e **ZipDeflate**.<br/>Os níveis suportados são **ideais** e **mais rápidos.** |No |

>[!TIP]
>Para copiar todos os ficheiros sob uma pasta, especifique o **balde** Para o balde e **o prefixo** para a parte da pasta.
>
>Para copiar um único ficheiro com um nome próprio, especifique o **balde** para o balde e **a chave** para a parte da pasta mais o nome do ficheiro.
>
>Para copiar um subconjunto de ficheiros sob uma pasta, especifique o **baldeName** para o balde e **a chave** para a parte da pasta mais o filtro wildcard.

**Exemplo: utilização de prefixo**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**Exemplo: utilização de chave e versão (opcional)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>Modelo de origem legado para a atividade copy

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de atividade copy deve ser definida como **FileSystemSource**. |Yes |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Note que quando **a recursiva** é definida como **verdadeira** e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobragem vazia não será copiada ou criada na pia.<br/>Os valores permitidos são **verdadeiros** (padrão) e **falsos.** | No |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar as ligações simultâneas à loja de dados. | No |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados que a atividade copy na Azure Data Factory suporta como fontes e pias, consulte [lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
