---
title: Copiar dados de e para Snowflake
description: Saiba como copiar dados de e para Snowflake utilizando a Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: 5bc64985401fce1c58a985b6b9fdead620c9aa8f
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048181"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>Copiar dados de e para Snowflake utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como utilizar a atividade Copy na Azure Data Factory para copiar dados de e para Snowflake. Para obter mais informações sobre a Data Factory, consulte o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector snowflake é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com uma tabela [de matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Para a atividade Copy, este conector Snowflake suporta as seguintes funções:

- Copie dados do Snowflake que utiliza a COPY de Snowflake no comando [[local]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) para obter o melhor desempenho.
- Copie os dados para Snowflake que tira partido da COPY de Snowflake no comando [[tabela]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) para obter o melhor desempenho. Apoia o Floco de Neve em Azure.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que definem entidades da Data Factory específicas de um conector snowflake.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para um serviço ligado a Floco de Neve.

| Propriedade         | Descrição                                                  | Obrigatório |
| :--------------- | :----------------------------------------------------------- | :------- |
| tipo             | A propriedade do tipo deve ser definida para **Snowflake**.              | Yes      |
| conexãoStragem | Especifica a informação necessária para se ligar à instância do Floco de Neve. Pode optar por colocar senha ou cadeia de conexão inteira no Cofre da Chave Azure. Consulte os exemplos abaixo da tabela, bem como as credenciais da Loja no artigo [Azure Key Vault,](store-credentials-in-key-vault.md) para obter mais detalhes.<br><br>Algumas configurações típicas:<br>- **Nome da conta:** O  [nome completo](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) da sua conta Snowflake (incluindo segmentos adicionais que identificam a região e a plataforma cloud), por exemplo, xy12345.east-us-2.azure.<br/>- **Nome do utilizador:** O nome de login do utilizador para a ligação.<br>- **Senha:** A senha para o utilizador.<br>- **Base de dados:** A base de dados predefinido para utilizar uma vez ligada. Deve ser uma base de dados existente para a qual a função especificada tem privilégios.<br>- **Armazém:** O armazém virtual para usar uma vez ligado. Deve ser um armazém existente para o qual o papel especificado tem privilégios.<br>- **Função:** A função de controlo de acesso predefinido a ser utilizada na sessão de Flocos de Neve. A função especificada deve ser uma função existente que já tenha sido atribuída ao utilizador especificado. O papel de incumprimento é o PÚBLICO. | Yes      |
| connectVia       | O [tempo de integração](concepts-integration-runtime.md) que é usado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou um tempo de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | No       |

**Exemplo:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Senha no cofre da chave Azure:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
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

As seguintes propriedades são suportadas para o conjunto de dados snowflake.

| Propriedade  | Descrição                                                  | Obrigatório                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| tipo      | A propriedade do tipo do conjunto de dados deve ser definida para **SnowflakeTable**. | Yes                         |
| esquema | O nome do esquema. |Não para a fonte, sim para a pia.  |
| tabela | Nome da mesa/vista. |Não para a fonte, sim para a pia.  |

**Exemplo:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte de Floco de Neve e pia.

### <a name="snowflake-as-the-source"></a>Floco de neve como a fonte

O conector snowflake utiliza o COPY de Snowflake no comando [[local]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) para obter o melhor desempenho.

Se a loja de dados e o formato da pia forem suportados de forma nativa pelo comando Snowflake COPY, pode utilizar a atividade Copy para copiar diretamente do Snowflake para afundar. Para mais informações, consulte [a cópia direta do Floco de Neve.](#direct-copy-from-snowflake) Caso contrário, utilize [uma cópia encenada](#staged-copy-from-snowflake)incorporada do Floco de Neve.

Para copiar dados de Snowflake, as seguintes propriedades são suportadas na secção **de origem da** atividade copy.

| Propriedade                     | Descrição                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tipo                         | A propriedade tipo da fonte de atividade copy deve ser definida para **SnowflakeSource**. | Yes      |
| consulta          | Especifica a consulta SQL para ler dados de Snowflake.<br>A execução do procedimento armazenado não é suportada. | No       |
| exportaçõesSettings | Configurações avançadas usadas para recuperar dados de Snowflake. Pode configurar os suportados pelo COPY no comando que a Data Factory passará quando invocar a declaração. | No       |
| ***Em `exportSettings` :*** |  |  |
| tipo | O tipo de comando de exportação, definido para **SnowflakeExportCopyCommand**. | Yes |
| opçõescopy adicionais | Opções de cópia adicionais, fornecidas como um dicionário de pares de valores-chave. Exemplos: MAX_FILE_SIZE, OVERWRITE. Para obter mais informações, consulte [as Opções de Cópia snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | No |
| opções adicionais | Opções adicionais de formato de ficheiro que são fornecidas ao comando COPY como um dicionário de pares de valor-chave. Exemplos: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Para obter mais informações, consulte [as opções do tipo de formato snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-from-snowflake"></a>Cópia direta de Snowflake

Se a sua loja de dados e formato de lavatório satisfaçam os critérios descritos nesta secção, pode utilizar a atividade Copy para copiar diretamente de Snowflake para afundar. A Data Factory verifica as definições e falha a atividade copy executada se os seguintes critérios não forem cumpridos:

- O **serviço ligado à pia** é o armazenamento [**Azure Blob**](connector-azure-blob-storage.md) com autenticação de assinatura de acesso **partilhado.**

- O formato de **dados** da pia é de **Parquet,** **texto delimitado,** ou **JSON** com as seguintes configurações:

    - Para o formato **Parquet,** o codec de compressão é **Nenhum,** **Snappy,** ou **Lzo.**
    - Para formato **de texto delimitado:**
        - `rowDelimiter` é **\r\n**, ou qualquer personagem único.
        - `compression` não pode ser **compressões,** **gzip,** **bzip2,** ou **esvaziar**.
        - `encodingName` é deixado como padrão ou definido para **utf-8**.
        - `quoteChar` é **citação dupla,** **citação única,** ou **corda vazia** (sem carvão de citação).
    - Para o formato **JSON,** a cópia direta suporta apenas o caso de que a tabela ou resultado da consulta de snowflake de origem tem apenas uma coluna e o tipo de dados desta coluna é **VARIANT**, **OBJECT**, ou **ARRAY**.
        - `compression` não pode ser **compressões,** **gzip,** **bzip2,** ou **esvaziar**.
        - `encodingName` é deixado como padrão ou definido para **utf-8**.
        - `filePattern` na pia de atividade de cópia é deixada como padrão ou definida para **definirOfObjects**.

- Na fonte de atividade de cópia, `additionalColumns` não é especificado.
- O mapeamento da coluna não é especificado.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Cópia encenada de Snowflake

Quando a sua loja ou formato de dados da pia não for compatível com o comando Snowflake COPY, como mencionado na última secção, ative a cópia encenada incorporada utilizando uma instância de armazenamento provisória de Azure Blob. A funcionalidade de cópia encenada também lhe proporciona uma melhor produção. A Data Factory exporta dados de Snowflake para o armazenamento de encenação, depois copia os dados para afundar e, finalmente, limpa os seus dados temporários do armazenamento de encenação. Consulte [a cópia encenada](copy-activity-performance-features.md#staged-copy) para obter mais informações sobre a cópia dos dados utilizando a encenação.

Para utilizar esta funcionalidade, crie um [serviço ligado ao armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties) que se refere à conta de armazenamento Azure como a encenação provisória. Em seguida, especifique as `enableStaging` propriedades e propriedades na atividade `stagingSettings` Copy.

> [!NOTE]
> O serviço ligado ao armazenamento Azure Blob deve utilizar a autenticação de assinatura de acesso partilhado, conforme exigido pelo comando Snowflake COPY. 

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>Floco de neve como pia

O conector snowflake utiliza o COPY de Snowflake no comando [[mesa]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) para obter o melhor desempenho. Suporta escrever dados para Snowflake em Azure.

Se a loja de dados de origem e o formato forem suportados de forma nativa pelo comando Snowflake COPY, pode utilizar a atividade Copy para copiar diretamente da fonte para Snowflake. Para mais informações, consulte [a cópia direta do Floco de Neve.](#direct-copy-to-snowflake) Caso contrário, utilize [uma cópia encenada](#staged-copy-to-snowflake)incorporada para o Floco de Neve.

Para copiar dados para Snowflake, as seguintes propriedades são suportadas na secção de **lavatório** de atividades Copy.

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| tipo              | A propriedade tipo da pia de atividade copy, definida para **SnowflakeSink**. | Yes                                           |
| preCopyScript     | Especifique uma consulta SQL para a atividade copy para executar antes de escrever dados em Snowflake em cada corrida. Utilize esta propriedade para limpar os dados pré-carregados. | No                                            |
| importaçõesS | Definições avançadas usadas para escrever dados em Snowflake. Pode configurar os suportados pelo COPY no comando que a Data Factory passará quando invocar a declaração. | No |
| ***Em `importSettings` :*** |                                                              |  |
| tipo | O tipo de comando de importação, definido para **SnowflakeImportCopyCommand**. | Yes |
| opçõescopy adicionais | Opções de cópia adicionais, fornecidas como um dicionário de pares de valores-chave. Exemplos: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. Para obter mais informações, consulte [as Opções de Cópia snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | No |
| opções adicionais | Opções adicionais de formato de ficheiro fornecidas ao comando COPY, fornecidas como um dicionário de pares de valores-chave. Exemplos: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Para obter mais informações, consulte [as opções do tipo de formato snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-to-snowflake"></a>Cópia direta para Snowflake

Se a sua loja de dados de origem e formato satisfaçam os critérios descritos nesta secção, pode utilizar a atividade Copy para copiar diretamente da fonte para Snowflake. A Azure Data Factory verifica as definições e falha a atividade copy executada se os seguintes critérios não forem cumpridos:

- O **serviço ligado à fonte** é o armazenamento [**Azure Blob**](connector-azure-blob-storage.md) com autenticação de assinatura de acesso **partilhado.**

- O **formato de dados de origem** é **Parquet,** **Texto Delimitado,** ou **JSON** com as seguintes configurações:

    - Para o formato **Parquet,** o codec de compressão é **Nenhum,** ou **Snappy**.

    - Para formato **de texto delimitado:**
        - `rowDelimiter` é **\r\n**, ou qualquer personagem único. Se o delimiter de linha não for "\r\n", `firstRowAsHeader` tem de ser **falso,** e `skipLineCount` não está especificado.
        - `compression` não pode ser **compressões,** **gzip,** **bzip2,** ou **esvaziar**.
        - `encodingName` é deixado como padrão ou definido para "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859"-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255".
        - `quoteChar` é **citação dupla,** **citação única,** ou **corda vazia** (sem carvão de citação).
    - Para o formato **JSON,** a cópia direta suporta apenas o caso de afundar a tabela Snowflake apenas tem uma única coluna e o tipo de dados desta coluna é **VARIANT**, **OBJECT**, ou **ARRAY**.
        - `compression` não pode ser **compressões,** **gzip,** **bzip2,** ou **esvaziar**.
        - `encodingName` é deixado como padrão ou definido para **utf-8**.
        - O mapeamento da coluna não é especificado.

- Na fonte de atividade copy: 

   -  `additionalColumns` não é especificado.
   - Se a sua fonte for uma pasta, `recursive` está definida como verdadeira.
   - `prefix`, `modifiedDateTimeStart` `modifiedDateTimeEnd` e `enablePartitionDiscovery` não estão especificados.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Cópia encenada para Floco de Neve

Quando a sua loja ou formato de dados da pia não for compatível com o comando Snowflake COPY, como mencionado na última secção, ative a cópia encenada incorporada utilizando uma instância de armazenamento provisória de Azure Blob. A funcionalidade de cópia encenada também lhe proporciona uma melhor produção. A Data Factory converte automaticamente os dados para satisfazer os requisitos do formato de dados do Floco de Neve. Em seguida, invoca o comando COPY para carregar dados em Floco de Neve. Finalmente, limpa os seus dados temporários do armazenamento de bolhas. Consulte [a cópia encenada](copy-activity-performance-features.md#staged-copy) para obter mais informações sobre a cópia dos dados utilizando a encenação.

Para utilizar esta funcionalidade, crie um [serviço ligado ao armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties) que se refere à conta de armazenamento Azure como a encenação provisória. Em seguida, especifique as `enableStaging` propriedades e propriedades na atividade `stagingSettings` Copy.

> [!NOTE]
> O serviço ligado ao armazenamento Azure Blob tem de utilizar a autenticação de assinatura de acesso partilhado, conforme exigido pelo comando Snowflake COPY.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter mais informações sobre as propriedades, consulte [a atividade da Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes

Para uma lista de lojas de dados suportadas como fontes e pias por copy atividade na Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
