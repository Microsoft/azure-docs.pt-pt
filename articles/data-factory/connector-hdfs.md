---
title: Copiar dados do HDFS utilizando a Azure Data Factory
description: Saiba como copiar dados de uma fonte hdfs em nuvem ou no local para lojas de dados de sumidouros suportados utilizando a atividade copy num pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 6670d6dc676ebefa149815253d5ce65c8a9b1abe
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680943"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Copie os dados do servidor HDFS utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-hdfs-connector.md)
> * [Versão atual](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do servidor Hadoop Distributed File System (HDFS). Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

O conector HDFS é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de origem e pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, o conector HDFS suporta:

- Copiar ficheiros utilizando o *Windows* (Kerberos) ou a autenticação *anónima.*
- Copiar ficheiros utilizando o protocolo *webhdfs* ou suporte *DistCp incorporado.*
- Copiar ficheiros como está ou através da análise ou geração de ficheiros com os [formatos de ficheiros suportados e os codecs de compressão](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Certifique-se de que o tempo de execução da integração pode aceder a *todos os* [servidor de nó de nome]:[porta de nó de nome] e [servidores de nó de dados]:[porta de nó de dados] do cluster Hadoop. A porta de nó de nome padrão é 50070, e a porta de nó de dados padrão é 50075.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do HDFS.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao HDFS:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade *tipo* deve ser definida para *Hdfs*. | Sim |
| url |O URL para o HDFS |Sim |
| authenticationType | Os valores permitidos são *Anónimos* ou *Windows*. <br><br> Para configurar o ambiente no local, consulte a [autenticação Use Kerberos para a secção de conector HDFS.](#use-kerberos-authentication-for-the-hdfs-connector) |Sim |
| userName |O nome de utilizador para a autenticação do Windows. Para a autenticação de Kerberos, especifique **\<username> @ \<domain> .com**. |Sim (para autenticação do Windows) |
| palavra-passe |A palavra-passe para a autenticação do Windows. Marque este campo como um SecureString para armazená-lo de forma segura na sua fábrica de dados, ou [fazer referência a um segredo armazenado num cofre de chaves Azure](store-credentials-in-key-vault.md). |Sim (para autenticação do Windows) |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Para saber mais, consulte a secção [Pré-Requisitos.](#prerequisites) Se o tempo de execução da integração não for especificado, o serviço utiliza o tempo de execução de integração Azure predefinido. |Não |

**Exemplo: utilização da autenticação anónima**

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

**Exemplo: utilização da autenticação do Windows**

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [datasets na Azure Data Factory](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para HDFS `location` em definições no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade *tipo* em baixo `location` no conjunto de dados deve ser definida para *HdfsLocation*. | Sim      |
| folderPath | O caminho para a pasta. Se pretender utilizar um wildcard para filtrar a pasta, ignore esta definição e especifique o caminho nas definições de origem da atividade. | Não       |
| fileName   | O nome do ficheiro na pasta especificadaPath. Se pretender utilizar um wildcard para filtrar ficheiros, ignore esta definição e especifique o nome do ficheiro nas definições de origem da atividade. | Não       |

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

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades, consulte Pipelines e atividades na Azure Data Factory.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades que são suportadas pela fonte do HDFS.

### <a name="hdfs-as-source"></a>HDFS como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para HDFS `storeSettings` em definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade *tipo* em baixo `storeSettings` deve ser definida para **HdfsReadSettings**. | Sim                                           |
| **_Localizar os ficheiros para copiar_* _ |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar a partir da pasta ou do caminho do ficheiro especificado no conjunto de dados. Se pretender copiar todos os ficheiros de uma pasta, especificar ainda `wildcardFileName` como `_` . |  |
| OPÇÃO 2: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard para filtrar pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais). Use `^` para escapar se o nome da sua pasta tiver um wildcard ou este personagem de fuga no interior. <br>Para mais exemplos, consulte [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 2: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard sob a pasta especificadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome do seu ficheiro real tiver um wildcard ou este personagem de fuga dentro.  Para mais exemplos, consulte [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de ficheiros<br>- fileListPath | Indica copiar um conjunto de ficheiros especificado. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar (um ficheiro por linha, com o caminho relativo para o caminho configurado no conjunto de dados).<br/>Quando utilizar esta opção, não especifique o nome do ficheiro no conjunto de dados. Para mais exemplos, consulte [exemplos da lista de ficheiros.](#file-list-examples) |Não |
| ***Configurações adicionais** _ |  | |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Quando `recursive` está programado para _true* e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobragem vazia não é copiada ou criada na pia. <br>Os valores permitidos são *verdadeiros* (padrão) e *falsos.*<br>Esta propriedade não se aplica quando se `fileListPath` configura. |Não |
| eliminarFilesAfterCompletion | Indica se os ficheiros binários serão eliminados da loja de origem depois de se mudarem com sucesso para a loja de destino. A eliminação do ficheiro é por ficheiro, pelo que quando a atividade da cópia falhar, verá que alguns ficheiros já foram copiados para o destino e eliminados da fonte, enquanto outros ainda permanecem na loja de origem. <br/>Esta propriedade é válida apenas em cenário de cópia de ficheiros binários. O valor predefinido: falso. |Não |
| modificadoDatetimeStart    | Os ficheiros são filtrados com base no atributo *Last Modified*. <br>Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do alcance de `modifiedDatetimeStart` `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.<br/>Esta propriedade não se aplica quando se `fileListPath` configura. | Não                                            |
| modificadoDatetimeEnd      | O mesmo que acima.  
| permitirPartitionDiscovery | Para os ficheiros que são divididos, especifique se analisar as divisórias do caminho do ficheiro e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **falsos** (padrão) e **verdadeiros.** | Não                                            |
| partitionRootPath | Quando a descoberta da partição estiver ativada, especifique o caminho da raiz absoluta para ler as pastas partidas como colunas de dados.<br/><br/>Se não for especificado, por defeito,<br/>- Quando utiliza o caminho do ficheiro no conjunto de dados ou na lista de ficheiros na fonte, o caminho da raiz da partição é o caminho configurado no conjunto de dados.<br/>- Quando utiliza o filtro de pasta wildcard, o caminho da raiz da partição é o sub-caminho antes do primeiro wildcard.<br/><br/>Por exemplo, assumindo que configura o caminho no conjunto de dados como "raiz/pasta/ano=2020/mês=08/dia=27":<br/>- Se especificar o caminho da raiz da partição como "raiz/pasta/ano=2020", a atividade da cópia gerará mais duas colunas `month` e com o valor `day` "08" e "27", respectivamente, para além das colunas dentro dos ficheiros.<br/>- Se não for especificado o caminho da raiz da partição, não será gerada nenhuma coluna extra. | Não                                            |
| maxConcurrentConnections | O número de ligações que podem ligar-se ao armazém simultaneamente. Especifique um valor apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                            |
| **_Definições de DistCp_* _ |  | |
| distcpSettings | O grupo de propriedades para usar quando você usar HDFS DistCp. | Não |
| resourceManagerEndpoint | O ponto final do YARN (Mais Um Negociador de Recursos) | Sim, se usar o DistCp |
| tempScriptPath | Um caminho de pasta que é usado para armazenar o script de comando DistCp temporário. O ficheiro de script é gerado pela Data Factory e será removido após o fim da função Copy. | Sim, se usar o DistCp |
| distcpOptions | Opções adicionais fornecidas ao comando DistCp. | Não |

_ *Exemplo:**

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e ficheiro

Esta secção descreve o comportamento resultante se utilizar um filtro wildcard com o caminho da pasta e o nome do ficheiro.

| folderPath | fileName             | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em **negrito** são recuperados) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (vazio, uso padrão) | false     | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (vazio, uso padrão) | true      | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de ficheiros

Esta secção descreve o comportamento que resulta da utilização de um caminho de lista de ficheiros na fonte de atividade do Copy. Assume que tem a seguinte estrutura de pasta de origem e pretende copiar os ficheiros que estão em negrito:

| Estrutura de origem da amostra                                      | Conteúdo em FileListToCopy.txt                             | Configuração da Fábrica de Dados Azure                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| raiz<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sub-página1/File3.csv<br>Sub-página1/File5.csv | **No conjunto de dados:**<br>- Caminho da pasta: `root/FolderA`<br><br>**Na fonte de atividade copy:**<br>- Caminho da lista de ficheiros: `root/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar (um ficheiro por linha, com o caminho relativo para o caminho configurado no conjunto de dados). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Utilize o DistCp para copiar dados do HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) é uma ferramenta de linha de comando nativa hadoop para fazer uma cópia distribuída num cluster Hadoop. Quando você dirige um comando no DistCp, ele primeiro lista todos os ficheiros a serem copiados e, em seguida, cria vários trabalhos de Mapa no cluster Hadoop. Cada trabalho no Mapa faz uma cópia binária da fonte até à pia.

A atividade Copy suporta a utilização do DistCp para copiar ficheiros como está no armazenamento do Azure Blob (incluindo [cópia encenada)](copy-activity-performance.md)ou numa loja de data lake Azure. Neste caso, o DistCp pode tirar partido do poder do seu cluster em vez de correr no tempo de integração auto-hospedado. A utilização do DistCp proporciona uma melhor produção de cópia, especialmente se o seu cluster for muito poderoso. Com base na configuração na sua fábrica de dados, a atividade Copy constrói automaticamente um comando DistCp, submete-o ao seu cluster Hadoop e monitoriza o estado da cópia.

### <a name="prerequisites"></a>Pré-requisitos

Para utilizar o DistCp para copiar ficheiros como é do armazenamento de HDFS a Azure Blob (incluindo cópia encenada) ou da loja de data lake Azure, certifique-se de que o seu cluster Hadoop cumpre os seguintes requisitos:

* Os serviços MapReduce e YARN estão ativados.  
* A versão YARN é 2.5 ou mais tarde.  
* O servidor HDFS está integrado na sua loja de dados-alvo: **Azure Blob ou** **Azure Data Lake Store (ADLS Gen1)**: 

    - O Azure Blob FileSystem é suportado de forma nativa desde Hadoop 2.7. Basta especificar o caminho JAR na configuração do ambiente Hadoop.
    - O Azure Data Lake Store FileSystem está embalado a partir de Hadoop 3.0.0-alpha1. Se a sua versão de cluster Hadoop for mais cedo do que essa versão, precisa de importar manualmente pacotes JAR relacionados com a Azure Data Lake Store (azure-datalake-store.jar) para o cluster a partir [daqui](https://hadoop.apache.org/releases.html), e especificar o caminho do ficheiro JAR na configuração do ambiente Hadoop.

* Prepare uma pasta temporária em HDFS. Esta pasta temporária é usada para armazenar um script de concha DistCp, por isso ocupará o espaço ao nível do KB.
* Certifique-se de que a conta de utilizador fornecida no serviço ligado ao HDFS tem permissão para:
   * Submeta um requerimento em YARN.
   * Crie uma subpasta e leia/escreva ficheiros sob a pasta temporária.

### <a name="configurations"></a>Configurações

Para configurações e exemplos relacionados com o DistCp, aceda ao [HDFS como secção de origem.](#hdfs-as-source)

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Utilize a autenticação Kerberos para o conector HDFS

Existem duas opções para configurar o ambiente no local para utilizar a autenticação Kerberos para o conector HDFS. Pode escolher o que melhor se adequa à sua situação.
* Opção 1: [Junte-se a uma máquina de execução de integração auto-hospedada no reino de Kerberos](#kerberos-join-realm)
* Opção 2: [Permitir a confiança mútua entre o domínio Windows e o reino de Kerberos](#kerberos-mutual-trust)

Para qualquer uma das opções, certifique-se de ligar webhdfs para o cluster Hadoop:

1. Crie o HTTP principal e keytab para webhdfs.

    > [!IMPORTANT]
    > O principal http Kerberos deve começar com "**HTTP"** de acordo com a especificação Kerberos HTTP SPNEGO. Saiba mais a partir [daqui.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#HDFS_Configuration_Options)

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. Opções de configuração HDFS: adicione as seguintes três propriedades em `hdfs-site.xml` .
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opção 1: Junte-se a uma máquina de execução de integração auto-hospedada no reino de Kerberos

#### <a name="requirements"></a>Requisitos

* A máquina de execução de integração auto-hospedada precisa de se juntar ao reino de Kerberos e não pode aderir a nenhum domínio windows.

#### <a name="how-to-configure"></a>Como configurar

**No servidor KDC:**

Crie um principal para a Azure Data Factory usar e especificar a palavra-passe.

> [!IMPORTANT]
> O nome de utilizador não deve conter o nome de hospedeiro.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**Na máquina de execução de integração auto-hospedada:**

1.  Executar o utilitário Ksetup para configurar o servidor e o reino do Centro de Distribuição de Chaves Kerberos (KDC).

    A máquina deve ser configurada como membro de um grupo de trabalho, porque um reino Kerberos é diferente de um domínio Windows. Pode alcançar esta configuração definindo o reino de Kerberos e adicionando um servidor KDC executando os seguintes comandos. Substitua *REALM.COM* pelo seu próprio nome de reino.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Depois de executar estes comandos, reinicie a máquina.

2.  Verifique a configuração com o `Ksetup` comando. A saída deve ser como:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**Na sua fábrica de dados:**

* Configure o conector HDFS utilizando a autenticação do Windows juntamente com o nome principal e a palavra-passe kerberos para ligar à fonte de dados HDFS. Para obter detalhes de configuração, consulte a secção [de propriedades de serviço ligadas ao HDFS.](#linked-service-properties)

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opção 2: Permitir a confiança mútua entre o domínio Windows e o reino de Kerberos

#### <a name="requirements"></a>Requisitos

*   A máquina de execução de integração auto-hospedada deve juntar-se a um domínio Windows.
*   Precisa de permissão para atualizar as definições do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar

> [!NOTE]
> Substitua REALM.COM e AD.COM no seguinte tutorial pelo seu próprio nome de reino e controlador de domínio.

**No servidor KDC:**

1. Editar a configuração KDC no ficheiro *krb5.conf* para permitir que a KDC confie no domínio windows, referindo-se ao modelo de configuração a seguir. Por predefinição, a configuração está localizada em */etc/krb5.conf*.

   ```config
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
    ```

   Depois de configurar o ficheiro, reinicie o serviço KDC.

2. Prepare um principal chamado *krbtgt/REALM.COM \@ AD.COM* no servidor KDC com o seguinte comando:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. No *hadoop.security.auth_to_local* ficheiro de configuração de serviço HDFS, adicione `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**No controlador de domínio:**

1.  Executar os `Ksetup` seguintes comandos para adicionar uma entrada de reino:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Estabeleça confiança do domínio windows para o reino de Kerberos. [palavra-passe] é a palavra-passe para o principal *krbtgt/REALM.COM \@ AD.COM*.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Selecione o algoritmo de encriptação que é usado em Kerberos.

    a. Selecione a política **do** grupo de  >  **gestão** do servidor  >    >    >  **Padrão ou política de domínio ativo** e, em seguida, selecione **Editar**.

    b. No painel do Editor de **Gestão de Políticas de Grupo,** selecione Políticas **de Configuração de**  >    >  **Computadores**  >  **Configurações de Segurança Definições**  >  **de Segurança Opções de** Segurança Locais  >  e, em seguida, configure a segurança **da rede: Configurar tipos de encriptação permitidos para Kerberos**.

    c. Selecione o algoritmo de encriptação que pretende utilizar quando se ligar ao servidor KDC. Pode selecionar todas as opções.

    ![Screenshot do painel "Segurança da rede: Configurar tipos de encriptação permitidos para Kerberos"](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Utilize o `Ksetup` comando para especificar o algoritmo de encriptação a utilizar no reino especificado.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Crie o mapeamento entre a conta de domínio e o principal Kerberos, para que possa utilizar o principal Kerberos no domínio Windows.

    a. Selecione **ferramentas administrativas**  >  **Utilizadores e Computadores de Diretório Ativo**.

    b. Configure funcionalidades avançadas selecionando **Ver**  >  **Funcionalidades Avançadas**.

    c. No painel **de Funcionalidades Avançadas,** clique à direita na conta à qual pretende criar mapeamentos e, no painel **de mapeamentos de nomes,** selecione o separador **Nomes Kerberos.**

    d. Adicione um diretor do reino.

       ![O painel de "Mapeamento de Identidade de Segurança"](media/connector-hdfs/map-security-identity.png)

**Na máquina de execução de integração auto-hospedada:**

* Executar os `Ksetup` seguintes comandos para adicionar uma entrada de reino.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Na sua fábrica de dados:**

* Configure o conector HDFS utilizando a autenticação do Windows juntamente com a sua conta de domínio ou com o principal kerberos para se ligar à fonte de dados HDFS. Para obter detalhes de configuração, consulte a secção [de propriedades de serviço ligadas ao HDFS.](#linked-service-properties)

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter informações sobre as propriedades da atividade da Lookup, consulte [a atividade da Lookup na Azure Data Factory.](control-flow-lookup-activity.md)

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para obter informações sobre as propriedades da atividade de Apagar, consulte [a atividade de Excluir na Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os modelos seguintes ainda são suportados como é para retrocompatibilidade. Recomendamos que utilize o novo modelo previamente discutido, porque a UI da Azure Data Factory passou a gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade *tipo* do conjunto de dados deve ser definida para *FileShare* |Sim |
| folderPath | O caminho para a pasta. Um filtro wildcard é suportado. Os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único personagem); use `^` para escapar se o nome do seu ficheiro real tiver um wildcard ou este personagem de fuga dentro. <br/><br/>Exemplos: rootfolder/subfolder/, ver mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). |Sim |
| fileName |  O nome ou filtro wildcard para os ficheiros sob a especificada "folderPath". Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único carácter).<br/>- Exemplo 1: `"fileName": "*.csv"`<br/>- Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome da sua pasta tiver um wildcard ou este personagem de fuga no interior. |Não |
| modificadoDatetimeStart | Os ficheiros são filtrados com base no atributo *Last Modified*. Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do alcance de `modifiedDatetimeStart` `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br/><br/> Esteja ciente de que o desempenho geral do movimento de dados será afetado, permitindo esta definição quando pretende aplicar um filtro de ficheiros a um grande número de ficheiros. <br/><br/> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| modificadoDatetimeEnd | Os ficheiros são filtrados com base no atributo *Last Modified*. Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do alcance de `modifiedDatetimeStart` `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br/><br/> Esteja ciente de que o desempenho geral do movimento de dados será afetado, permitindo esta definição quando pretende aplicar um filtro de ficheiros a um grande número de ficheiros. <br/><br/> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| Não |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ficheiros com um formato específico, suportam-se os seguintes tipos de formato de ficheiro: *TextFormat,* *JsonFormat,* *AvroFormat,* *OrcFormat,* *ParquetFormat*. Desa um destes valores, o *tipo* de propriedade em formato. Para mais informações, consulte o [formato Text](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e secções [de formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos suportados são: *Gzip,* *Deflate,* *Bzip2* e *ZipDeflate*.<br/>Os níveis suportados são: *Ideal* e *Mais rápido*. |Não |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique apenas **o apêndio.**<br>Para copiar um único ficheiro com um nome especificado, especifique **a pastaPata** com a peça de pasta e **o nome de ficheiro** com o nome do ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **a pastaPata** com a peça de pasta e **o nome de ficheiro** com o filtro wildcard.

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de atividade de cópia legacy Copy

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade *tipo* da fonte de atividade copy deve ser definida para *HdfsSource*. |Sim |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Quando a recursiva é definida como *verdadeira* e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não será copiado ou criado na pia.<br/>Os valores permitidos são *verdadeiros* (padrão) e *falsos.* | Não |
| distcpSettings | O grupo de propriedades quando você está usando HDFS DistCp. | Não |
| resourceManagerEndpoint | O ponto final do Gestor de Recursos YARN | Sim, se usar o DistCp |
| tempScriptPath | Um caminho de pasta que é usado para armazenar o script de comando DistCp temporário. O ficheiro de script é gerado pela Data Factory e será removido após o fim da função Copy. | Sim, se usar o DistCp |
| distcpOptions | Opções adicionais são fornecidas ao comando DistCp. | Não |
| maxConcurrentConnections | O número de ligações que podem ligar-se ao armazém simultaneamente. Especifique um valor apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

**Exemplo: Fonte hdfs na atividade copy usando DistCp**

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

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados que são suportadas como fontes e sumidouros pela atividade Copy na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
