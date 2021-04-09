---
title: Mover dados do servidor SFTP usando a Azure Data Factory
description: Saiba como mover dados de um servidor SFTP na nuvem utilizando a Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2018
robots: noindex
ms.openlocfilehash: b40e9dc83629362da899d2b5ff29ad42e21c4e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382655"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Mover dados de um servidor SFTP usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-sftp-connector.md)
> * [Versão 2 (versão atual)](../connector-sftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte a [ligactor SFTP em V2](../connector-sftp.md).

Este artigo descreve como utilizar a Atividade de Cópia na Azure Data Factory para mover dados de um servidor SFTP no local/nuvem para uma loja de dados de lavatórios suportados. Este artigo baseia-se no artigo de atividades de movimento de [dados](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia e a lista de lojas de dados suportadas como fontes/pias.

Atualmente, a fábrica de dados suporta apenas a transferência de dados de um servidor SFTP para outras lojas de dados, mas não para transferir dados de outras lojas de dados para um servidor SFTP. Suporta servidores SFTP no local e na nuvem.

> [!NOTE]
> A Copy Activity não apaga o ficheiro de origem depois de ter sido copiado com sucesso para o destino. Se precisar de eliminar o ficheiro de origem após uma cópia bem sucedida, crie uma atividade personalizada para eliminar o ficheiro e utilizar a atividade no pipeline.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários apoiados e tipos de autenticação
Pode utilizar este conector SFTP para copiar dados tanto dos **servidores SFTP na nuvem como dos servidores SFTP no local.** **Os** tipos de autenticação Basic e **SshPublicKey** são suportados ao ligar-se ao servidor SFTP.

Ao copiar dados de um servidor SFTP no local, é necessário instalar um Gateway de Gestão de Dados no ambiente no local/Azure VM. Consulte [o Gateway de Gestão de Dados](data-factory-data-management-gateway.md) para obter mais detalhes no gateway. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo sobre a configuração do gateway e a sua utilização.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma fonte SFTP utilizando diferentes ferramentas/APIs.

- A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia. Para que as amostras de JSON copiem dados do servidor SFTP para o Azure Blob Storage, consulte [JSON Exemplo: Copiar dados do servidor SFTP para a secção blob Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) deste artigo.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela a seguir fornece uma descrição para elementos JSON específicos do serviço ligado FTP.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo | A propriedade tipo deve ser definida para `Sftp` . |Yes |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Yes |
| porta |Porta na qual o servidor SFTP está a ouvir. O valor predefinido é: 21 |No |
| authenticationType |Especificar o tipo de autenticação. Valores permitidos: **Básico,** **SshPublicKey**. <br><br> Consulte a [Utilização de autenticação básica](#using-basic-authentication) e utilização de secções [de autenticação de chaves públicas SSH](#using-ssh-public-key-authentication) em mais propriedades e amostras de JSON, respectivamente. |Yes |
| skipHostKeyValidation | Especificar se deve ignorar a validação da chave do anfitrião. | N.º O valor predefinido: falso |
| hostKeyFingerprint | Especifique a impressão do dedo da chave do anfitrião. | Sim, se o `skipHostKeyValidation` é falso.  |
| gatewayName |Nome do Gateway de Gestão de Dados para ligar a um servidor SFTP no local. | Sim, se copiar dados de um servidor SFTP no local. |
| criptografadoCredential | Credencial encriptada para aceder ao servidor SFTP. Gerada automaticamente quando especifica a autenticação básica (nome de utilizador + palavra-passe) ou autenticação SshPublicKey (nome de utilizador + caminho ou conteúdo de chave privada) no assistente de cópia ou no diálogo popup ClickOnce. | N.º Aplicar apenas ao copiar dados de um servidor SFTP no local. |

### <a name="using-basic-authentication"></a>Utilização de autenticação básica

Para utilizar a autenticação básica, definir `authenticationType` como `Basic` , e especificar as seguintes propriedades para além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| nome de utilizador | Utilizador que tenha acesso ao servidor SFTP. |Yes |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Yes |

#### <a name="example-basic-authentication"></a>Exemplo: Autenticação básica
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: Autenticação básica com credencial encriptada

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Utilização da autenticação de chaves públicas SSH

Para utilizar a autenticação da chave pública SSH, definida `authenticationType` como `SshPublicKey` , e especificar as seguintes propriedades para além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| nome de utilizador |Utilizador que tem acesso ao servidor SFTP |Yes |
| privateKeyPath | Especifique o caminho absoluto para o ficheiro chave privado que o gateway pode aceder. | Especificar o `privateKeyPath` ou `privateKeyContent` . . <br><br> Aplicar apenas ao copiar dados de um servidor SFTP no local. |
| privateKeyContent | Uma cadeia serializada do conteúdo da chave privada. O Copy Wizard pode ler o ficheiro de chave privada e extrair automaticamente o conteúdo da chave privada. Se estiver a utilizar qualquer outra ferramenta/SDK, utilize a propriedade privateKeyPath. | Especificar o `privateKeyPath` ou `privateKeyContent` . . |
| passPhrase | Especifique a frase/palavra-passe para desencriptar a chave privada se o ficheiro chave estiver protegido por uma frase de passe. | Sim, se o ficheiro de chave privada estiver protegido por uma frase de passe. |

> [!NOTE]
> O conector SFTP suporta a chave RSA/DSA OpenSSH. Certifique-se de que o conteúdo do ficheiro chave começa com "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Se o ficheiro de chave privada for um ficheiro de formato ppk, utilize a ferramenta Putty para converter de .ppk para formato OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exemplo: Autenticação SshPublicKey usando ficheiro de chave privadaApa

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: Autenticação SshPublicKey utilizando conteúdo de chave privada

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A secção **typeProperties** é diferente para cada tipo de conjunto de dados. Fornece informações específicas do tipo de conjunto de dados. A secção typeProperties para um conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Sub caminho para a pasta. Utilize o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte por exemplo o serviço ligado à amostra e definições de conjunto de dados.<br/><br/>Pode combinar esta propriedade com **partição Para** ter caminhos de pasta baseados em intervalos de datas de início/fim da fatia. |Yes |
| fileName |Especifique o nome do ficheiro na **pastaPata** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o data de ficheiro não for especificado para um conjunto de dados de saída, o nome do ficheiro gerado estará no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| ficheiroFiltro |Especifique um filtro a ser utilizado para selecionar um subconjunto de ficheiros na pastaPalho em vez de todos os ficheiros.<br/><br/>Os valores permitidos são: `*` (múltiplos caracteres) e `?` (único carácter).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter é aplicável para um conjunto de dados de FileShare de entrada. Esta propriedade não é suportada com HDFS. |No |
| partitionedBy |partitionedBy pode ser usado para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Por exemplo, a pastaPata parametrizada para cada hora de dados. |No |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |No |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate**. Os níveis suportados são: **Ideal** e **Mais rápido**. Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useTransfera |Especificar se utiliza o modo de transferência binário. Verdade para o modo binário e falso ASCII. Valor predefinido: Verdadeiro. Esta propriedade só pode ser usada quando o tipo de serviço associado ligado é do tipo: FtpServer. |No |

> [!NOTE]
> o nome de ficheiro e o ficheiroFiltro não podem ser utilizados simultaneamente.

### <a name="using-partionedby-property"></a>Usando propriedade partionedBy
Como mencionado na secção anterior, pode especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo com partitionedBy. Pode fazê-lo com as macros data factory e a variável do sistema SliceStart, SliceEnd que indicam o período de tempo lógico para uma determinada fatia de dados.

Para conhecer os conjuntos de dados, agendamentos e fatias de séries de tempo, consulte [a criação de conjuntos de dados,](data-factory-create-datasets.md) [agendamento & execução](data-factory-scheduling-and-execution.md)e [criando artigos de Pipelines.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Amostra 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo {Slice} é substituído pelo valor da variável do sistema de data factory SliceStart no formato (YYYYMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. A pastaApata é diferente para cada fatia. Exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Amostra 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas por propriedades de pastaPapa e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na secção de tipos de propriedades da atividade variam com cada tipo de atividade. Para a atividade copy, as propriedades do tipo variam dependendo dos tipos de fontes e pias.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Consulte [os formatos de arquivo e compressão no artigo da Azure Data Factory](data-factory-supported-file-and-compression-formats.md) sobre detalhes.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Exemplo JSON: Copiar dados do servidor SFTP para a blob Azure
O exemplo a seguir fornece definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados da fonte SFTP para o Azure Blob Storage. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados usando a Atividade de Cópia na Fábrica de Dados Azure.

> [!IMPORTANT]
> Esta amostra fornece snippets JSON. Não inclui instruções passo a passo para a criação da fábrica de dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo.

A amostra tem as seguintes entidades de fábrica de dados:

* Um serviço ligado de [tipo sftp](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de um servidor SFTP para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado SFTP**

Este exemplo utiliza a autenticação básica com o nome de utilizador e a palavra-passe em texto simples. Também pode utilizar uma das seguintes formas:

* Autenticação básica com credenciais encriptadas
* Autenticação de chave pública SSH

Consulte a secção [de serviços com ligação FTP](#linked-service-properties) para diferentes tipos de autenticação que pode utilizar.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Serviço ligado do Storage do Azure**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada SFTP**

Este conjunto de dados refere-se à pasta e ficheiro SFTP `mysharedfolder` `test.csv` . O oleoduto copia o ficheiro para o destino.

Definição "externa": "verdadeiro" informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Conjunto de dados dos Blobs do Azure**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline com atividade de Copy**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **FileSystemSource** e o tipo **de pia** é definido para **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

* [Copiar tutorial de atividade](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para instruções passo a passo para a criação de um oleoduto com uma Atividade de Cópia.
