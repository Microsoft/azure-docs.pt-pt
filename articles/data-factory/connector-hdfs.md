---
title: Copiar dados do HDFS usando o Azure Data Factory
description: Saiba como copiar dados de uma nuvem ou fonte de HDFS local para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 49285ac4a15cb52f04f6d629d80449eb515c33fa
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680869"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Copiar dados do HDFS usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-hdfs-connector.md)
> * [Versão atual](connector-hdfs.md)

Este artigo descreve como copiar dados do servidor HDFS. Para saber mais sobre Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector HDFS tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Especificamente, esse conector HDFS dá suporte a:

- Copiar arquivos usando o **Windows** (Kerberos) ou autenticação **anônima** .
- Copiar arquivos usando o protocolo **webhdfs** ou o suporte **interno a DistCp** .
- Copiando arquivos no estado em que se encontram ou analisando/gerando arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Verifique se o Integration Runtime pode acessar **todos** os [servidor de nó de nome]: [porta do nó de nome] e [servidores de nó de dados]: [porta do nó de dados] do cluster Hadoop. O padrão [porta do nó de nome] é 50070 e o padrão [porta do nó de dados] é 50075.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao HDFS.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do HDFS:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **HDFS**. | Sim |
| url |URL para o HDFS |Sim |
| authenticationType | Os valores permitidos são: **Anonymous**ou **Windows**. <br><br> Para usar a **autenticação Kerberos** para o conector HDFS, consulte [esta seção](#use-kerberos-authentication-for-hdfs-connector) para configurar seu ambiente local de acordo. |Sim |
| Usu |Nome de usuário para autenticação do Windows. Para a autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para autenticação do Windows) |
| palavra-passe |Senha para autenticação do Windows. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim (para autenticação do Windows) |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

**Exemplo: usando a autenticação anônima**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: usando a autenticação do Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para HDFS em configurações de `location` no conjunto de base de formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade Type em `location` no conjunto de texto deve ser definida como **HdfsLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o folderPath fornecido. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

### <a name="legacy-dataset-model"></a>Modelo de conjunto de DataSet herdado

>[!NOTE]
>O modelo de conjunto de itens a seguir ainda tem suporte como está para compatibilidade com versões anteriores. Você deve usar o novo modelo mencionado na seção acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **FileShare** |Sim |
| folderPath | Caminho para a pasta. Há suporte para o filtro curinga, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome de arquivo real tiver curinga ou este caractere de escape dentro de. <br/><br/>Exemplos: RootFolder/subfolder/, veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim |
| fileName |  **Nome ou filtro curinga** para os arquivos sob o "FolderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de pontos apontará para todos os arquivos na pasta. <br/><br/>Para filtros, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| ao | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída.<br/><br/>Se você quiser analisar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format)e seções de [formato parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Não (somente para o cenário de cópia binária) |
| çã | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [formatos de arquivo com suporte e codecs de compactação](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**.<br/>Os níveis com suporte são: **ideal** e **mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **FolderPath** apenas.<br>Para copiar um único arquivo com um nome específico, especifique **FolderPath** com a parte da **pasta e o nome do** arquivo com o nome do arquivos.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **FolderPath** com a parte da pasta e o **nome do arquivo** com o filtro curinga.

**Exemplo:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
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

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do HDFS.

### <a name="hdfs-as-source"></a>HDFS como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para HDFS em configurações de `storeSettings` na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade Type em `storeSettings` deve ser definida como **HdfsReadSetting**. | Sim                                           |
| recursiva                | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome do arquivo com caracteres curinga sob o folderPath/wildcardFolderPath fornecido para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro.  Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no DataSet |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado. | Não                                            |
| modifiedDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| distcpSettings | Grupo de propriedades ao usar HDFS DistCp. | Não |
| resourceManagerEndpoint | O ponto de extremidade do Gerenciador de recursos yarn | Sim se estiver usando DistCp |
| tempScriptPath | Um caminho de pasta usado para armazenar o script de comando Temp DistCp. O arquivo de script é gerado pelo Data Factory e será removido após a conclusão do trabalho de cópia. | Sim se estiver usando DistCp |
| distcpOptions | Opções adicionais fornecidas para o comando DistCp. | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSetting",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
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

#### <a name="legacy-source-model"></a>Modelo de origem herdado

>[!NOTE]
>O modelo de origem de cópia a seguir ainda tem suporte como está para compatibilidade com versões anteriores. Você deve usar o novo modelo mencionado acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **HdfsSource** |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observação Quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, a pasta/subpasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não |
| distcpSettings | Grupo de propriedades ao usar HDFS DistCp. | Não |
| resourceManagerEndpoint | O ponto de extremidade do Gerenciador de recursos yarn | Sim se estiver usando DistCp |
| tempScriptPath | Um caminho de pasta usado para armazenar o script de comando Temp DistCp. O arquivo de script é gerado pelo Data Factory e será removido após a conclusão do trabalho de cópia. | Sim se estiver usando DistCp |
| distcpOptions | Opções adicionais fornecidas para o comando DistCp. | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo: origem do HDFS na atividade de cópia usando DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Saiba mais sobre como usar o DistCp para copiar dados do HDFS com eficiência na próxima seção.

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e o nome do arquivo com filtros curinga.

| folderPath | fileName             | recursiva | Estrutura da pasta de origem e resultado do filtro (arquivos em **negrito** são recuperados) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (vazio, usar padrão) | false     | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**arquivo2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | (vazio, usar padrão) | true      | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**arquivo2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | false     | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | true      | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Usar DistCp para copiar dados do HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) é uma ferramenta de linha de comando nativa do Hadoop para fazer a cópia distribuída em um cluster Hadoop. Ao executar um comando Distcp, primeiro ele listará todos os arquivos a serem copiados, criará vários trabalhos de mapa no cluster Hadoop, e cada trabalho de mapa fará a cópia binária da origem para o coletor.

Suporte à atividade de cópia usando DistCp para copiar arquivos no estado em que se encontram no blob do Azure (incluindo [cópia em etapas](copy-activity-performance.md)) ou Azure data Lake Store; nesse caso, ele pode aproveitar totalmente a potência do cluster em vez de executar no Integration Runtime de hospedagem interna. Ele fornecerá uma melhor taxa de transferência de cópia, especialmente se o cluster for muito potente. Com base em sua configuração no Azure Data Factory, a atividade de cópia cria automaticamente um comando distcp, envia para o cluster Hadoop e monitora o status da cópia.

### <a name="prerequisites"></a>Pré-requisitos

Para usar o DistCp para copiar arquivos no estado em que se encontram do HDFS para o blob do Azure (incluindo cópia em etapas) ou Azure Data Lake Store, verifique se o cluster do Hadoop atende aos requisitos abaixo:

1. Os serviços MapReduce e yarn estão habilitados.
2. A versão yarn é 2,5 ou superior.
3. O servidor HDFS é integrado ao armazenamento de dados de destino-blob do Azure ou Azure Data Lake Store:

    - O sistema de arquivos de blob do Azure tem suporte nativo desde o Hadoop 2,7. Você só precisa especificar o caminho jar na configuração do Hadoop env.
    - Azure Data Lake Store FileSystem é empacotado a partir do Hadoop 3.0.0-alpha1. Se o cluster Hadoop for inferior a essa versão, você precisará importar manualmente os pacotes JAR relacionados ao ADLS (Azure-datalake-Store. jar) para o cluster [aqui](https://hadoop.apache.org/releases.html)e especificar o caminho jar na configuração do Hadoop env.

4. Prepare uma pasta temporária no HDFS. Essa pasta Temp é usada para armazenar o script de shell DistCp, portanto, ele ocupará o espaço em nível de KB.
5. Verifique se a conta de usuário fornecida no serviço vinculado do HDFS tem permissão para um) enviar aplicativo em yarn; b) tem a permissão para criar subpastas, ler/gravar arquivos na pasta Temp acima.

### <a name="configurations"></a>Configurações

Consulte Configurações e exemplos relacionados ao DistCp no [HDFS como seção de origem](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Usar a autenticação Kerberos para o conector HDFS

Há duas opções para configurar o ambiente local para usar a autenticação Kerberos no conector do HDFS. Você pode escolher o que melhor se adapta ao seu caso.
* Opção 1: [ingressar o computador de Integration Runtime auto-hospedado no realm do Kerberos](#kerberos-join-realm)
* Opção 2: [habilitar a confiança mútua entre o domínio do Windows e o realm do Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: ingressar o computador de Integration Runtime auto-hospedado no realm do Kerberos

#### <a name="requirements"></a>Requisitos

* O computador de Integration Runtime auto-hospedado precisa ingressar no realm Kerberos e não pode ingressar em nenhum domínio do Windows.

#### <a name="how-to-configure"></a>Como configurar

**No computador Integration Runtime auto-hospedado:**

1.  Execute o utilitário **Ksetup** para configurar o servidor e o realm KDC Kerberos.

    O computador deve ser configurado como um membro de um grupo de trabalho, pois um realm Kerberos é diferente de um domínio do Windows. Isso pode ser feito definindo o realm do Kerberos e adicionando um servidor KDC da seguinte maneira. Substitua *realm.com* pelo seu respectivo Realm, conforme necessário.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reinicie** o computador depois de executar estes dois comandos.

2.  Verifique a configuração com o comando **Ksetup** . A saída deve ser semelhante a:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Em Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** junto com seu nome de entidade de segurança e senha Kerberos para se conectar à fonte de dados HDFS. Verifique a seção [Propriedades do serviço vinculado do HDFS](#linked-service-properties) em detalhes da configuração.

### <a name="kerberos-mutual-trust"></a>Opção 2: habilitar a confiança mútua entre o domínio do Windows e o realm do Kerberos

#### <a name="requirements"></a>Requisitos

*   O computador de Integration Runtime auto-hospedado deve ingressar em um domínio do Windows.
*   Você precisa de permissão para atualizar as configurações do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial a seguir com seu próprio território e controlador de domínio respectivos, conforme necessário.

**No servidor KDC:**

1. Edite a configuração do KDC no arquivo **krb5. conf** para permitir que o KDC confie no domínio do Windows referente ao seguinte modelo de configuração. Por padrão, a configuração está localizada em **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Reinicie** o serviço KDC após a configuração.

2. Prepare uma entidade de segurança chamada **krbtgt/realm. com\@AD.com** no servidor KDC com o seguinte comando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Em **Hadoop. Security. auth_to_local** arquivo de configuração do serviço HDFS, adicione `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**No controlador de domínio:**

1.  Execute os seguintes comandos **Ksetup** para adicionar uma entrada de realm:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabeleça confiança do domínio do Windows para o realm do Kerberos. [password] é a senha para a entidade de segurança **krbtgt/realm. COM\@AD.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione o algoritmo de criptografia usado no Kerberos.

    1. Vá para Gerenciador do Servidor > gerenciamento de Política de Grupo > domínio > objetos política de grupo > a política de domínio padrão ou ativa e edite.

    2. Na janela pop-up **Editor de gerenciamento de política de grupo** , vá para configuração do computador > políticas > configurações do Windows > configurações de segurança > políticas locais > opções de segurança e configure a **segurança de rede: configurar tipos de criptografia permitido para Kerberos**.

    3. Selecione o algoritmo de criptografia que você deseja usar ao se conectar ao KDC. Normalmente, você pode simplesmente selecionar todas as opções.

        ![Configurar tipos de criptografia para Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Use o comando **Ksetup** para especificar o algoritmo de criptografia a ser usado no realm específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e a entidade de segurança Kerberos para usar a entidade de segurança Kerberos no domínio do Windows.

    1. Inicie as ferramentas administrativas > **Active Directory usuários e computadores**.

    2. Configure recursos avançados clicando em **exibir** > **recursos avançados**.

    3. Localize a conta para a qual você deseja criar mapeamentos e clique com o botão direito do mouse para exibir **mapeamentos de nome** > clique na guia **nomes Kerberos** .

    4. Adicione uma entidade de segurança do realm.

        ![Mapear identidade de segurança](media/connector-hdfs/map-security-identity.png)

**No computador Integration Runtime auto-hospedado:**

* Execute os comandos **Ksetup** a seguir para adicionar uma entrada de realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Em Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** junto com sua conta de domínio ou entidade de segurança Kerberos para se conectar à fonte de dados HDFS. Verifique a seção [Propriedades do serviço vinculado do HDFS](#linked-service-properties) em detalhes da configuração.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
