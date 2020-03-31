---
title: Mova dados do servidor SFTP utilizando a Fábrica de Dados Azure
description: Saiba como mover dados de um servidor SFTP em nuvem utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3f78934fb11dd4f9e34bf27d565d471d47f250b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265808"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Mova dados de um servidor SFTP usando a Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-sftp-connector.md)
> * [Versão 2 (versão atual)](../connector-sftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [o SFTPconnector em V2](../connector-sftp.md).

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de um servidor SFTP on-local/cloud para uma loja de dados de sink suportado. Este artigo baseia-se no artigo sobre [atividades](data-factory-data-movement-activities.md) de movimento de dados que apresenta uma visão geral do movimento de dados com atividade de cópia e a lista de lojas de dados suportadas como fontes/pias.

Atualmente, a fábrica de dados suporta apenas a transferência de dados de um servidor SFTP para outras lojas de dados, mas não para transferir dados de outras lojas de dados para um servidor SFTP. Suporta tanto os servidores SFTP no local como na nuvem.

> [!NOTE]
> A Atividade de Cópia não elimina o ficheiro fonte depois de ter sido copiado com sucesso para o destino. Se necessitar de eliminar o ficheiro fonte após uma cópia bem sucedida, crie uma atividade personalizada para apagar o ficheiro e utilizar a atividade no pipeline.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários suportados e tipos de autenticação
Pode utilizar este conector SFTP para copiar dados tanto dos servidores SFTP em nuvem como dos **servidores SFTP no local**. **Os** tipos básicos e de autenticação **SshPublicKey** são suportados quando se conectam ao servidor SFTP.

Ao copiar dados de um servidor SFTP no local, necessita de instalar um Gateway de Gestão de Dados no ambiente no local/VM Azure. Consulte o Portal de [Gestão](data-factory-data-management-gateway.md) de Dados para obter detalhes sobre o portal. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo sobre a configuração do portal e a sua utilização.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma fonte SFTP utilizando diferentes ferramentas/APIs.

- A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia. Para que as amostras da JSON copiem dados do servidor SFTP para o Armazenamento De Blob Azure, consulte [o Exemplo JSON: Copiar dados do servidor SFTP para](#json-example-copy-data-from-sftp-server-to-azure-blob) a secção de blob Azure deste artigo.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
O quadro seguinte fornece descrição para elementos JSON específicos do serviço ligado ftp.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo | A propriedade tipo deve `Sftp`ser definida para . |Sim |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta na qual o servidor SFTP está a ouvir. O valor padrão é: 21 |Não |
| authenticationType |Especificar tipo de autenticação. Valores permitidos: **Básico,** **SshPublicKey**. <br><br> Consulte [a utilização de autenticações básicas](#using-basic-authentication) e utilização de secções de [autenticação de chaves públicas SSH](#using-ssh-public-key-authentication) em mais propriedades e amostras JSON, respectivamente. |Sim |
| skipHostKeyValidação | Especifique se saltar a validação da chave do hospedeiro. | Não. O valor padrão: falso |
| anfitriãoKeyFingerprint | Especifique a impressão do dedo da chave hospedeira. | Sim, `skipHostKeyValidation` se o for falso.  |
| nome gateway |Nome do Portal de Gestão de Dados para ligar a um servidor SFTP no local. | Sim, se copiar dados de um servidor SFTP no local. |
| credenta encriptado | Credencial encriptada para aceder ao servidor SFTP. Gerado automaticamente quando especifica a autenticação básica (nome de utilizador + palavra-passe) ou autenticação SshPublicKey (nome de utilizador + caminho ou conteúdo privado) no assistente de cópia ou no diálogo popup ClickOnce. | Não. Aplicar apenas ao copiar dados de um servidor SFTP no local. |

### <a name="using-basic-authentication"></a>Utilização da autenticação básica

Para utilizar a autenticação básica, definida `authenticationType` como `Basic`, e especificar as seguintes propriedades para além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Utilizador que tenha acesso ao servidor SFTP. |Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

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

Para utilizar a autenticação da `authenticationType` chave `SshPublicKey`pública SSH, definida como , e especificar as seguintes propriedades para além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador |Utilizador que tenha acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique o caminho absoluto para o ficheiro chave privado a que o gateway pode aceder. | Especifique ou o `privateKeyPath` `privateKeyContent`. <br><br> Aplicar apenas ao copiar dados de um servidor SFTP no local. |
| privateKeyContent | Uma sequência serializada do conteúdo da chave privada. O Copy Wizard pode ler o ficheiro de chave privada e extrair automaticamente o conteúdo da chave privada. Se estiver a utilizar qualquer outra ferramenta/SDK, utilize a propriedade privateKeyPath. | Especifique ou o `privateKeyPath` `privateKeyContent`. |
| passFrase | Especifique a frase/palavra-passe de passe para desencriptar a chave privada se o ficheiro chave estiver protegido por uma frase de passe. | Sim, se o ficheiro da chave privada estiver protegido por uma frase de passe. |

> [!NOTE]
> O conector SFTP suporta a tecla RSA/DSA OpenSSH. Certifique-se de que o conteúdo do ficheiro chave começa com "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Se o ficheiro de teclas privada for um ficheiro de formato ppk, utilize a ferramenta Putty para converter de .ppk para formato OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exemplo: Autenticação SshPublicKey utilizando ficheiros chave privadoSPath

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: Autenticação SShPublicKey utilizando conteúdo de chave privada

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
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntode dados.

A secção **typeProperties** é diferente para cada tipo de conjunto de dados. Fornece informações específicas ao tipo de conjunto de dados. A secção TypeProperties para um conjunto de dados do conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Sub caminho para a pasta. Use o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte as definições de serviço ligados à amostra e definições de conjunto de dados, por exemplo.<br/><br/>Pode combinar esta propriedade com **partiçãoBy** para ter caminhos de pastas baseados em datas de início/fim de fatias. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPath** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt`(Exemplo: Dados.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| fileFilter |Especifique um filtro a utilizar para selecionar um subconjunto de ficheiros na pastaPath em vez de todos os ficheiros.<br/><br/>Os valores `*` permitidos são: (múltiplos caracteres) e `?` (personagem único).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter é aplicável para um conjunto de dados de FileShare de entrada. Esta propriedade não é suportada com HDFS. |Não |
| divididoBy |a divisãoBy pode ser usada para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Por exemplo, pastaPath parametrizado para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência Binário. Verdadeiro para o modo binário e falso ASCII. Valor padrão: Verdadeiro. Esta propriedade só pode ser utilizada quando o tipo de serviço associado é de tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e ficheiroSO filtro não pode ser utilizado simultaneamente.

### <a name="using-partionedby-property"></a>Usando propriedade partionedBy
Tal como mencionado na secção anterior, pode especificar uma pasta dinâmicaPath, nome de ficheiro para dados de séries de tempo com divisóriaby. Pode fazê-lo com as macros data Factory e a variável do sistema SliceStart, SliceEnd que indicam o período lógico de tempo para uma determinada fatia de dados.

Para conhecer conjuntos de dados de séries de tempo, agendamento e fatias, Consulte a [Criação](data-factory-create-datasets.md)de Conjuntos de Dados, [Agendamento & Execução](data-factory-scheduling-and-execution.md)e Criação de artigos de [Pipelines.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Amostra 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo {Slice} é substituído pelo valor da variável sliceStart do sistema data Factory no formato (YYYYMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. A pastaCaminho é diferente para cada fatia. Exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

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
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades da pastaPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

Considerando que as propriedades disponíveis na secção typeProperties da atividade variam com cada tipo de atividade. Para a atividade da Cópia, as propriedades do tipo variam consoante os tipos de fontes e pias.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Consulte [os formatos de Arquivo e compressão no](data-factory-supported-file-and-compression-formats.md) artigo da Azure Data Factory sobre detalhes.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Exemplo JSON: Copiar dados do servidor SFTP para o blob Azure
O exemplo que se segue fornece definições jSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados da fonte SFTP para o Armazenamento De Blob Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer fonte para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

> [!IMPORTANT]
> Esta amostra fornece cortes JSON. Não inclui instruções passo a passo para a criação da fábrica de dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo.

A amostra tem as seguintes entidades fabris de dados:

* Um serviço ligado de [sftp](#linked-service-properties)tipo.
* Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de um servidor SFTP para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado à SFTP**

Este exemplo utiliza a autenticação básica com o nome do utilizador e a palavra-passe em texto simples. Também pode utilizar uma das seguintes formas:

* Autenticação básica com credenciais encriptadas
* Autenticação de chaves públicas SSH

Consulte a secção de [serviço ligada ftp](#linked-service-properties) para diferentes tipos de autenticação que pode utilizar.

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

Este conjunto de dados refere-se `mysharedfolder` à `test.csv`pasta E ficheiro SFTP . O oleoduto copia o ficheiro para o destino.

Definição "externa": "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

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

**Pipeline com atividade de Cópia**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **FileSystemSource** e o tipo **de pia** está definido para **BlobSink**.

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

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

* [Copy Activity tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para instruções passo a passo para criar um pipeline com uma Atividade de Cópia.
