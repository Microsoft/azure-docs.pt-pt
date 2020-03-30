---
title: Mover dados do HDFS no local
description: Saiba como mover dados a partir do HDFS no local utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7652ab72fb972230d98913c2d7e2601737982532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924353"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover dados do HDFS no local utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-hdfs-connector.md)
> * [Versão 2 (versão atual)](../connector-hdfs.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector HDFS em V2](../connector-hdfs.md).

Este artigo explica como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de um HDFS no local. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados do HDFS para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de um HDFS no local para outras lojas de dados, mas não para a transferência de dados de outras lojas de dados para um HDFS no local.

> [!NOTE]
> A Atividade de Cópia não elimina o ficheiro fonte depois de ter sido copiado com sucesso para o destino. Se necessitar de eliminar o ficheiro fonte após uma cópia bem sucedida, crie uma atividade personalizada para apagar o ficheiro e utilizar a atividade no pipeline. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Permitindo a conectividade
O serviço Data Factory suporta a ligação ao HDFS no local utilizando o Gateway de Gestão de Dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para saber sobre o Gateway de Gestão de Dados e instruções passo a passo sobre a configuração do portal. Utilize a porta de entrada para ligar ao HDFS mesmo que esteja hospedado num VM Azure IaaS.

> [!NOTE]
> Certifique-se de que o Portal de Gestão de Dados pode aceder a **ALL** the [name node server]:[porta do nó de nome] e [servidores de nó de dados]:[porta do nó de dados] do cluster Hadoop. O padrão [porta do nó de nome] é 50070, e o padrão [porta do nó de dados] é 50075.

Enquanto pode instalar o portal na mesma máquina no local ou no Azure VM que o HDFS, recomendamos que instale o portal numa máquina separada/Azure IaaS VM. Ter porta de entrada numa máquina separada reduz a contenção de recursos e melhora o desempenho. Quando instalar o portal numa máquina separada, a máquina deverá poder aceder à máquina com o HDFS.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados a partir de uma fonte HDFS utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal Azure,** **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados HDFS, consulte o [exemplo da JSON: Copiar dados do HDFS no local para](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) a secção Azure Blob deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Fábrica de Dados específicas do HDFS:

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
Um serviço ligado liga uma loja de dados a uma fábrica de dados. Cria um serviço ligado de **Hdfs** tipo para ligar um HDFS no local à sua fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao HDFS.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **Hdfs** |Sim |
| url |URL para o HDFS |Sim |
| authenticationType |Anónimo, ou Windows. <br><br> Para utilizar a **autenticação Kerberos** para o conector HDFS, consulte [esta secção](#use-kerberos-authentication-for-hdfs-connector) para configurar o seu ambiente no local em conformidade. |Sim |
| userName |Nome de utilizador para autenticação do Windows. Para autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para autenticação do Windows. |Sim (para autenticação do Windows) |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar ao HDFS. |Sim |
| credenta encriptado |[Saída new-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) da credencial de acesso. |Não |

### <a name="using-anonymous-authentication"></a>Utilização da autenticação anónima

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Utilização da autenticação do Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção typeProperties para conjunto de dados do tipo **FileShare** (que inclui dataset HDFS) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Use o personagem de fuga ' \ ' para caracteres especiais na corda. Por exemplo: para a subpasta\\\\pasta da pasta,especificar subpasta da\\\\pasta e para a pasta d:\samplefolder, especificar d: pasta de amostras.<br/><br/>Pode combinar esta propriedade com **partiçãoBy** para ter caminhos de pastas baseados em datas de início/fim de fatias. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPath** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt`(por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| divididoBy |a divisãoBy pode ser usada para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Exemplo: pastaPath parametrizado para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> nome de ficheiro e ficheiroSO filtro não pode ser utilizado simultaneamente.

### <a name="using-partionedby-property"></a>Usando propriedade partionedBy
Conforme mencionado na secção anterior, pode especificar uma pasta dinâmicaPath e nome de ficheiro para dados de séries de tempo com a propriedade **divididaBy,** [funções data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Para saber mais sobre conjuntos de dados de séries de tempo, agendamento e fatias, consulte [Criar Conjuntos](data-factory-create-datasets.md)de Dados, [Agendar & Execução](data-factory-scheduling-and-execution.md)e Criar Artigos de [Pipelines.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Amostra 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo {Slice} é substituído pelo valor da variável sliceStart do sistema data Factory no formato (YYYYMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. A pastaCaminho é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Amostra 2:

```JSON
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

Considerando que as propriedades disponíveis na secção typeProperties da atividade variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

Para a Atividade de Cópia, quando a fonte é do tipo **FileSystemSource** as seguintes propriedades estão disponíveis na secção typeProperties:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdade, Falso (padrão) |Não |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Consulte [os formatos de Arquivo e compressão no](data-factory-supported-file-and-compression-formats.md) artigo da Azure Data Factory sobre detalhes.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemplo jSON: Copiar dados do HDFS no local para Azure Blob
Esta amostra mostra como copiar dados de um HDFS no local para o Armazenamento de Blob Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.  

A amostra fornece definições JSON para as seguintes entidades data Factory. Pode utilizar estas definições para criar um pipeline para copiar dados de HDFS para Armazenamento De Blob Azure utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Um serviço de tipo [OnPremisesHdfs](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de um HDFS no local para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, criar a porta de entrada de gestão de dados. As instruções nos [dados em movimento entre os locais no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem.

**Serviço ligado ao HDFS:** Este exemplo utiliza a autenticação do Windows. Consulte a secção de [serviço ligada ao HDFS](#linked-service-properties) para obter diferentes tipos de autenticação que possa utilizar.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Serviço ligado ao Armazenamento Azure:**

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

Conjunto de dados de **entrada HDFS:** Este conjunto de dados refere-se à pasta HDFS DataTransfer/UnitTest/. O pipeline copia todos os ficheiros desta pasta para o destino.

Definição "externa": "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Conjunto de dados de saída de Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Uma atividade de cópia num oleoduto com fonte do Sistema de Ficheiros e pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **FileSystemSource** e o tipo **de pia** está definido para **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilize a autenticação Kerberos para conector HDFS
Existem duas opções para configurar o ambiente no local de modo a utilizar a Autenticação Kerberos no conector HDFS. Pode escolher o que melhor se encaixa no seu caso.
* Opção 1: [Junte-se à máquina de gateway no reino de Kerberos](#kerberos-join-realm)
* Opção 2: Permitir a confiança mútua entre o domínio do Windows e o reino de [Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opção 1: Junte-se à máquina de gateway no reino de Kerberos

#### <a name="requirement"></a>Exigência:

* A máquina de porta de entrada precisa de se juntar ao reino kerberos e não pode aderir a nenhum domínio windows.

#### <a name="how-to-configure"></a>Como configurar:

**Na máquina de porta de entrada:**

1.  Execute o utilitário **Ksetup** para configurar o servidor e reino Da KDC Kerberos.

    A máquina deve ser configurada como membro de um grupo de trabalho, uma vez que um reino kerberos é diferente de um domínio Windows. Isto pode ser conseguido definindo o reino kerberos e adicionando um servidor KDC da seguinte forma. Substitua *REALM.COM* pelo seu reino, conforme necessário.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reinicie** a máquina depois de executar estes 2 comandos.

2.  Verifique a configuração com o comando **Ksetup.** A saída deve ser como:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Na Fábrica de Dados Azure:**

* Configure o conector HDFS utilizando a **autenticação do Windows** juntamente com o nome principal da Kerberos e a palavra-passe para se ligar à fonte de dados hdfs. Verifique a secção de [propriedades do Serviço Ligado HDFS](#linked-service-properties) nos detalhes da configuração.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opção 2: Permitir a confiança mútua entre o domínio do Windows e o reino de Kerberos

#### <a name="requirement"></a>Exigência:
*   A máquina de porta de entrada deve juntar-se a um domínio Windows.
*   Precisa de autorização para atualizar as definições do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar:

> [!NOTE]
> Substitua REALM.COM e AD.COM no seguinte tutorial pelo seu próprio domínio e controlador de domínio, conforme necessário.

**No servidor KDC:**

1. Editar a configuração KDC no ficheiro **krb5.conf** para permitir que o KDC confie no Domínio do Windows referindo-se ao seguinte modelo de configuração. Por predefinição, a configuração está localizada em **/etc/krb5.conf**.

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

2. Prepare um principal chamado **krbtgt/REALM.COM\@AD.COM** no servidor KDC com o seguinte comando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Em **hadoop.security.auth_to_local** ficheiro de configuração `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`do serviço HDFS, adicione .

**No controlador de domínio:**

1.  Executar os seguintes comandos **Ksetup** para adicionar uma entrada no reino:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabeleça a confiança do Domínio do Windows para o Reino kerberos. [palavra-passe] é a palavra-passe para o principal **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione algoritmo de encriptação usado em Kerberos.

    1. Vá ao Gestor de Servidores > Gestão de Políticas de Grupo > Domínio > Objetos de Política de Grupo > Política de Domínio Padrão ou Ativo, e Editar.

    2. Na janela popup do Editor de **Gestão** de Políticas do Grupo, vá às políticas de configuração de computador > > Definições do Windows > Definições de Segurança > Políticas Locais > Opções de Segurança e configurar a **segurança da rede: Configure os tipos de encriptação permitidos para Kerberos**.

    3. Selecione o algoritmo de encriptação que pretende utilizar quando estiver ligado ao KDC. Normalmente, pode simplesmente selecionar todas as opções.

        ![Tipos de encriptação config para Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Utilize o comando **Ksetup** para especificar o algoritmo de encriptação a ser utilizado no REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e o principal Kerberos, de forma a utilizar o principal Kerberos no Domínio do Windows.

    1. Inicie as ferramentas administrativas > **Utilizadores e Computadores de Diretório Ativo.**

    2. Configure funcionalidades avançadas clicando em **Visualizar** > **Funcionalidades Avançadas**.

    3. Localize a conta para a qual pretende criar mapeamentos e clique à direita para ver **Mapeamentos** de nome sem > clique no separador **Nomes Kerberos.**

    4. Adicione um diretor do reino.

        ![Identidade de segurança do mapa](media/data-factory-hdfs-connector/map-security-identity.png)

**Na máquina de porta de entrada:**

* Executar os seguintes comandos **Ksetup** para adicionar uma entrada no reino.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Na Fábrica de Dados Azure:**

* Configure o conector HDFS utilizando a **autenticação do Windows** juntamente com a sua Conta de Domínio ou o Principal Kerberos para se ligar à fonte de dados hdfs. Verifique a secção de [propriedades do Serviço Ligado HDFS](#linked-service-properties) nos detalhes da configuração.

> [!NOTE]
> Para mapear colunas desde o conjunto de dados de origem até colunas a partir de conjunto de dados de sumidouro, consulte [colunas de conjunto de dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .


## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
