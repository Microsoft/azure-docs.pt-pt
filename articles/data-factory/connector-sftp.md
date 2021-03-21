---
title: Copiar dados de e para o servidor SFTP
description: Saiba como copiar dados de e para o servidor SFTP utilizando a Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 19b32bed15a4d292a7427d8401e777c7761e45a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592035"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Copie os dados de e para o servidor SFTP utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-sftp-connector.md)
> * [Versão atual](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados de e para o servidor FTP (SFTP) seguro. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

O conector SFTP é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, o conector SFTP suporta:

- Copiar ficheiros de e para o servidor SFTP utilizando a **autenticação básica, ssh pública** ou **multi-factor.** 
- Copiar ficheiros como está ou através da análise ou geração de ficheiros com os [formatos de ficheiros suportados e os codecs de compressão](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas da SFTP.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado SFTP:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para *Sftp*. |Yes |
| anfitrião | O nome ou endereço IP do servidor SFTP. |Yes |
| porta | A porta na qual o servidor SFTP está a ouvir.<br/>O valor permitido é um número inteiro, e o valor padrão é *de 22*. |No |
| skipHostKeyValidation | Especificar se deve ignorar a validação da chave do anfitrião.<br/>Os valores permitidos são *verdadeiros* e *falsos* (padrão).  | No |
| hostKeyFingerprint | Especifique a impressão digital da chave hospedeira. | Sim, se o "skipHostKeyValidation" estiver definido como falso.  |
| authenticationType | Especifique o tipo de autenticação.<br/>Os valores permitidos são *Basic,* *SshPublicKey* e *MultiFactor*. Para obter mais propriedades, consulte a secção [de autenticação básica Utilizar.](#use-basic-authentication) Para os exemplos JSON, consulte a secção [de autenticação da chave pública Use SSH.](#use-ssh-public-key-authentication) |Yes |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Para saber mais, consulte a secção [Pré-Requisitos.](#prerequisites) Se o tempo de execução da integração não for especificado, o serviço utiliza o tempo de execução de integração Azure predefinido. |No |

### <a name="use-basic-authentication"></a>Utilizar a autenticação básica

Para utilizar a autenticação básica, desaprova a propriedade *autenticaçãoType* para *Basic,* e especifique as seguintes propriedades para além das propriedades genéricas do conector SFTP que foram introduzidas na secção anterior:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O utilizador que tem acesso ao servidor SFTP. |Yes |
| palavra-passe | A palavra-passe para o utilizador (userName). Marque este campo como um SecureString para armazená-lo de forma segura na sua fábrica de dados, ou [fazer referência a um segredo armazenado num cofre de chaves Azure](store-credentials-in-key-vault.md). | Yes |

**Exemplo:**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Utilizar a autenticação de chaves públicas SSH

Para utilizar a autenticação de chaves públicas SSH, descreva a propriedade "authenticationType" como **SshPublicKey**, e especifique as seguintes propriedades além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O utilizador que tem acesso ao servidor SFTP. |Yes |
| privateKeyPath | Especifique o caminho absoluto para o ficheiro chave privado que o tempo de integração pode aceder. Isto só se aplica quando o tipo de tempo de integração auto-hospedado é especificado em "connectVia". | Especificar ou `privateKeyPath` `privateKeyContent` especificar ou .  |
| privateKeyContent | Base64 codificado conteúdo de chave privada SSH. A chave privada SSH deve ser o formato OpenSSH. Marque este campo como um SecureString para armazená-lo de forma segura na sua fábrica de dados, ou [fazer referência a um segredo armazenado num cofre de chaves Azure](store-credentials-in-key-vault.md). | Especificar ou `privateKeyPath` `privateKeyContent` especificar ou . |
| passPhrase | Especifique a frase de passe ou a palavra-passe para desencriptar a chave privada se o ficheiro chave ou o conteúdo da chave estiverem protegidos por uma frase de passe. Marque este campo como um SecureString para armazená-lo de forma segura na sua fábrica de dados, ou [fazer referência a um segredo armazenado num cofre de chaves Azure](store-credentials-in-key-vault.md). | Sim, se o ficheiro de chave privada ou o conteúdo da chave estiverem protegidos por uma frase de passe. |

> [!NOTE]
> O conector SFTP suporta uma chave RSA/DSA OpenSSH. Certifique-se de que o conteúdo do ficheiro chave começa com "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Se o ficheiro de chave privada for um ficheiro de formato PPK, utilize a ferramenta PuTTY para converter do formato PPK para OpenSSH. 

**Exemplo 1: Autenticação SshPublicKey utilizando ficheiro de chave privadaApa**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: Autenticação SshPublicKey utilizando conteúdo de chave privada**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-multi-factor-authentication"></a>Use a autenticação de vários fatores

Para utilizar a autenticação multi-factor que é uma combinação de autenticações básicas e SSH de chaves públicas, especifique o nome de utilizador, a palavra-passe e a informação de chave privada descrita nas secções acima.

**Exemplo: autenticação de vários fatores**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<host>",
            "port": 22,
            "authenticationType": "MultiFactor",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 encoded private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<passphrase for private key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para SFTP `location` em definições no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade *tipo* `location` em conjunto de dados deve ser definida como *SftpLocation*. | Yes      |
| folderPath | O caminho para a pasta. Se pretender utilizar um wildcard para filtrar a pasta, ignore esta definição e especifique o caminho nas definições de origem da atividade. | No       |
| fileName   | O nome do ficheiro na pasta especificadaPath. Se pretender utilizar um wildcard para filtrar ficheiros, ignore esta definição e especifique o nome do ficheiro nas definições de origem da atividade. | No       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades que são suportadas pela fonte SFTP.

### <a name="sftp-as-source"></a>SFTP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para SFTP sob as `storeSettings` definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade *tipo* em baixo `storeSettings` deve ser definida para *SftpReadSettings*. | Yes                                           |
| ***Localizar os ficheiros para copiar*** |  |  |
| OPÇÃO 1: caminho estático<br> | Cópia do caminho da pasta/ficheiro especificado no conjunto de dados. Se pretender copiar todos os ficheiros de uma pasta, especificar ainda `wildcardFileName` como `*` . |  |
| OPÇÃO 2: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard para filtrar pastas de origem. <br>Os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único personagem); use `^` para escapar se o nome da sua pasta tiver um wildcard ou este char de fuga no interior. <br>Para mais exemplos, consulte [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | No                                            |
| OPÇÃO 2: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard sob a pasta especificadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único personagem); use `^` para escapar se o seu nome de ficheiro real tiver wildcard ou este char de fuga no interior.  Para mais exemplos, consulte [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Yes |
| OPÇÃO 3: uma lista de ficheiros<br>- fileListPath | Indica copiar um conjunto de ficheiros especificado. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar (um ficheiro por linha, com o caminho relativo para o caminho configurado no conjunto de dados).<br/>Quando utilizar esta opção, não especifique o nome do ficheiro no conjunto de dados. Para mais exemplos, consulte [exemplos da lista de ficheiros.](#file-list-examples) |No |
| ***Definições adicionais*** |  | |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Quando a recursiva é definida como verdadeira e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia. <br>Os valores permitidos são *verdadeiros* (padrão) e *falsos.*<br>Esta propriedade não se aplica quando se `fileListPath` configura. |No |
| eliminarFilesAfterCompletion | Indica se os ficheiros binários serão eliminados da loja de origem depois de se mudarem com sucesso para a loja de destino. A eliminação do ficheiro é por ficheiro, pelo que quando a atividade da cópia falhar, verá que alguns ficheiros já foram copiados para o destino e eliminados da fonte, enquanto outros ainda permanecem na loja de origem. <br/>Esta propriedade é válida apenas em cenário de cópia de ficheiros binários. O valor predefinido: falso. |No |
| modificadoDatetimeStart    | Os ficheiros são filtrados com base no atributo *Last Modified*. <br>Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do alcance de `modifiedDatetimeStart` `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.<br/>Esta propriedade não se aplica quando se `fileListPath` configura. | No                                            |
| modificadoDatetimeEnd      | Mesmo que acima.                                               | No                                            |
| permitirPartitionDiscovery | Para os ficheiros que são divididos, especifique se analisar as divisórias do caminho do ficheiro e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **falsos** (padrão) e **verdadeiros.** | No                                            |
| partitionRootPath | Quando a descoberta da partição estiver ativada, especifique o caminho da raiz absoluta para ler as pastas partidas como colunas de dados.<br/><br/>Se não for especificado, por defeito,<br/>- Quando utiliza o caminho do ficheiro no conjunto de dados ou na lista de ficheiros na fonte, o caminho da raiz da partição é o caminho configurado no conjunto de dados.<br/>- Quando utiliza o filtro de pasta wildcard, o caminho da raiz da partição é o sub-caminho antes do primeiro wildcard.<br/><br/>Por exemplo, assumindo que configura o caminho no conjunto de dados como "raiz/pasta/ano=2020/mês=08/dia=27":<br/>- Se especificar o caminho da raiz da partição como "raiz/pasta/ano=2020", a atividade da cópia gerará mais duas colunas `month` e com o valor `day` "08" e "27", respectivamente, para além das colunas dentro dos ficheiros.<br/>- Se não for especificado o caminho da raiz da partição, não será gerada nenhuma coluna extra. | No                                            |
| maxConcurrentConnections | O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-a-sink"></a>SFTP como um lavatório

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

As seguintes propriedades são suportadas para SFTP `storeSettings` em configurações num lavatório de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade *tipo* em baixo `storeSettings` deve ser definida para *SftpWriteSettings*. | Yes      |
| copyOportundo             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: Preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro-alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível da pasta alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | No       |
| maxConcurrentConnections | O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas. | No       |
| useTempFileRename | Indique se deve fazer o upload para ficheiros temporários e renomeá-los, ou escrever diretamente para a pasta alvo ou para a localização do ficheiro. Por predefinição, a Azure Data Factory escreve primeiro para ficheiros temporários e depois rebatiza-os quando o upload está terminado. Esta sequência ajuda a (1) evitar conflitos que possam resultar num ficheiro corrompido se tiver outros processos escritos para o mesmo ficheiro, e (2) garantir que a versão original do ficheiro existe durante a transferência. Se o seu servidor SFTP não suportar uma operação de renome, desative esta opção e certifique-se de que não tem uma escrita simultânea para o ficheiro alvo. Para mais informações, consulte a ponta de resolução de problemas no final desta tabela. | N.º O valor predefinido é *verdadeiro.* |
| operaçãoTimeout | O tempo de espera antes de cada pedido de escrita para o servidor SFTP horas fora. O valor predefinido é de 60 min (01:00:00).|No |

>[!TIP]
>Se receber o erro "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied", ou "SftpOperationFail" quando estiver a escrever dados no SFTP, e o utilizador SFTP que utiliza *tem* as permissões adequadas, verifique se o seu sistema de suporte ao servidor SFTP está a funcionar. Se não for, desative o upload com a opção de **ficheiro temporário** `useTempFileRename` () e tente novamente. Para saber mais sobre esta propriedade, consulte a tabela anterior. Se utilizar um tempo de integração auto-hospedado para a atividade Copy, certifique-se de que utiliza a versão 4.6 ou mais tarde.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e ficheiro

Esta secção descreve o comportamento que resulta da utilização de filtros wildcard com caminhos de pasta e nomes de ficheiros.

| folderPath | fileName | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, uso padrão) | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vazio, uso padrão) | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de ficheiros

Esta tabela descreve o comportamento que resulta da utilização de um caminho de lista de ficheiros na fonte de atividade do Copy. Assume que tem a seguinte estrutura de pasta de origem e pretende copiar os ficheiros que estão em negrito:

| Estrutura de origem da amostra                                      | Conteúdo em FileListToCopy.txt                             | Configuração da Fábrica de Dados Azure                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| raiz<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sub-página1/File3.csv<br>Sub-página1/File5.csv | **No conjunto de dados:**<br>- Caminho da pasta: `root/FolderA`<br><br>**Na fonte de atividade copy:**<br>- Caminho da lista de ficheiros: `root/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar (um ficheiro por linha, com o caminho relativo para o caminho configurado no conjunto de dados). |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter informações sobre as propriedades da atividade da Lookup, consulte [a atividade da Lookup na Azure Data Factory.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade getMetadata

Para obter informações sobre as propriedades da atividade GetMetadata, consulte [a atividade da GetMetadata na Azure Data Factory.](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para obter informações sobre as propriedades da atividade de Apagar, consulte [a atividade de Excluir na Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os modelos seguintes ainda são suportados como é para retrocompatibilidade. Recomendamos que utilize o novo modelo previamente discutido, porque a UI da Azure Data Factory passou a gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade *tipo* do conjunto de dados deve ser definida para *FileShare*. |Yes |
| folderPath | O caminho para a pasta. Um filtro wildcard é suportado. Os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único personagem); use `^` para escapar se o seu nome de ficheiro real tiver um wildcard ou este char de fuga no interior. <br/><br/>Exemplos: rootfolder/subfolder/, ver mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). |Yes |
| fileName |  **Nome ou filtro wildcard** para os ficheiros sob a especificada "folderPath". Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os wildcards permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único carácter).<br/>- Exemplo 1: `"fileName": "*.csv"`<br/>- Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior. |No |
| modificadoDatetimeStart | Os ficheiros são filtrados com base no atributo *Last Modified*. Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do alcance de `modifiedDatetimeStart` `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br/><br/> O desempenho geral do movimento de dados será afetado permitindo esta definição quando pretender fazer filtro de ficheiros a partir de um grande número de ficheiros. <br/><br/> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| No |
| modificadoDatetimeEnd | Os ficheiros são filtrados com base no atributo *Last Modified*. Os ficheiros são selecionados se o seu último tempo modificado estiver dentro do alcance de `modifiedDatetimeStart` `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br/><br/> O desempenho geral do movimento de dados será afetado permitindo esta definição quando pretender fazer filtro de ficheiros a partir de um grande número de ficheiros. <br/><br/> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributos de ficheiro é aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem um valor de data, mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é superior ou igual ao valor da data são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de data, mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data são selecionados.| No |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ficheiros com um formato específico, suportam-se os seguintes tipos de formato de ficheiro: *TextFormat,* *JsonFormat,* *AvroFormat,* *OrcFormat* e *ParquetFormat*. Desa um destes valores, o *tipo* de propriedade em formato. Para mais informações, consulte [o formato Text,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e secções [de formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos suportados são *GZip,* *Deflate,* *BZip2* e *ZipDeflate*.<br/>Os níveis suportados são *ideais* e *mais rápidos.* |No |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique apenas *o apêndio.*<br>Para copiar um único ficheiro com um nome especificado, especifique *a pastaPata* com a parte da pasta e *o nome de ficheiro* com o nome do ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique *a pastaPata* com a parte da pasta e *arquive o nome* com o filtro wildcard.

>[!NOTE]
>Se estiver a utilizar a propriedade *fileFilter* para o filtro de ficheiros, ainda está suportado como está, mas recomendamos que utilize a nova capacidade de filtro adicionada ao nome de *ficheiros* a partir de agora.

**Exemplo:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
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
| tipo | A propriedade *tipo* da fonte de atividade copy deve ser definida para *FileSystemSource* |Yes |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Quando a recursiva é definida como *verdadeira* e a pia é uma loja baseada em ficheiros, as pastas vazias e as sub-dobras não serão copiadas ou criadas na pia.<br/>Os valores permitidos são *verdadeiros* (padrão) e *falsos* | No |
| maxConcurrentConnections |O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
Para obter uma lista de lojas de dados que são suportadas como fontes e sumidouros pela atividade Copy na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
