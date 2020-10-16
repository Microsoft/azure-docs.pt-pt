---
title: Mover dados do HDFS no local
description: Saiba como mover dados de HDFS no local usando a Azure Data Factory.
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
ms.openlocfilehash: e3f158bb4e8208d00fdfbc44b4afaf067183b6d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087321"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover dados do HDFS no local usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-hdfs-connector.md)
> * [Versão 2 (versão atual)](../connector-hdfs.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector HDFS em V2](../connector-hdfs.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de um HDFS no local. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados do HDFS para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de um HDFS no local para outras lojas de dados, mas não para transferir dados de outras lojas de dados para um HDFS no local.

> [!NOTE]
> A Copy Activity não apaga o ficheiro de origem depois de ter sido copiado com sucesso para o destino. Se precisar de eliminar o ficheiro de origem após uma cópia bem sucedida, crie uma atividade personalizada para eliminar o ficheiro e utilizar a atividade no pipeline. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Habilitação da conectividade
O serviço Data Factory suporta a ligação ao HDFS no local utilizando o Gateway de Gestão de Dados. Consulte [dados em movimento entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para saber sobre o Gateway de Gestão de Dados e instruções passo a passo sobre a configuração do gateway. Utilize o gateway para ligar ao HDFS mesmo que esteja hospedado num Azure IaaS VM.

> [!NOTE]
> Certifique-se de que o Gateway de Gestão de Dados pode aceder a **ALL** o [servidor de nó de nome]:[porta de nó de nome] e [servidores de nó de dados]:[porta de nó de dados] do cluster Hadoop. O predefinitivo [porta de nó de nome] é 50070, e o padrão [porta de nó de dados] é 50075.

Enquanto pode instalar gateway na mesma máquina no local ou no Azure VM que o HDFS, recomendamos que instale o gateway numa máquina separada/Azure IaaS VM. Ter porta de entrada numa máquina separada reduz a contenção de recursos e melhora o desempenho. Quando instalar o gateway numa máquina separada, a máquina deverá ser capaz de aceder à máquina com o HDFS.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma fonte HDFS utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal Azure,** **Visual Studio**, **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados HDFS, consulte [o exemplo JSON: Copiar dados das secções HDFS para Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas do HDFS:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
Um serviço ligado liga uma loja de dados a uma fábrica de dados. Cria um serviço de **tipo Hdfs** ligado para ligar um HDFS no local à sua fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao HDFS.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **Hdfs** |Sim |
| url |URL para o HDFS |Sim |
| authenticationType |Anónimo, ou Windows. <br><br> Para utilizar **a autenticação Kerberos** para o conector HDFS, consulte [esta secção](#use-kerberos-authentication-for-hdfs-connector) para configurar o ambiente no local em conformidade. |Sim |
| userName |Nome de utilizador para autenticação do Windows. Para a autenticação de Kerberos, especifique `<username>@<domain>.com` . |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para autenticação do Windows. |Sim (para autenticação do Windows) |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar ao HDFS. |Sim |
| criptografadoCredential |[Saída new-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) da credencial de acesso. |Não |

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
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção typeProperties para conjunto de dados do tipo **FileShare** (que inclui conjunto de dados HDFS) tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Utilize o personagem de fuga ' \ ' para caracteres especiais na corda. Por exemplo: para a sub-dobragem pasta\, especifique \\ \\ a sub-dobradeira e para d:\samplefolder, especificar d: \\ \\ amostragem.<br/><br/>Pode combinar esta propriedade com **partição Para** ter caminhos de pasta baseados em intervalos de datas de início/fim da fatia. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPata** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o data de ficheiro não for especificado para um conjunto de dados de saída, o nome do ficheiro gerado estará no seguinte formato: <br/><br/>`Data.<Guid>.txt` (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode ser usado para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Exemplo: pastaPametante parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2**e **ZipDeflate**. Os níveis suportados são: **Ideal** e **Mais rápido**. Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> o nome de ficheiro e o ficheiroFiltro não podem ser utilizados simultaneamente.

### <a name="using-partionedby-property"></a>Usando propriedade partionedBy
Como mencionado na secção anterior, pode especificar uma pasta dinâmicaPath e nome de ficheiro para dados de séries de tempo com a propriedade **partitionedBy,** [funções de Data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Para saber mais sobre conjuntos de dados, agendamentos e fatias de séries de tempo, consulte [Criar Conjuntos de Dados,](data-factory-create-datasets.md) [Agendar & Execução](data-factory-scheduling-and-execution.md)e [Criar Artigos de Pipelines.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Amostra 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo {Slice} é substituído pelo valor da variável do sistema de data factory SliceStart no formato (YYYYMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. A pastaApata é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

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
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas por propriedades de pastaPapa e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na secção de tipos de atividade variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Para a Atividade de Cópia, quando a fonte é do tipo **FileSystemSource,** as seguintes propriedades estão disponíveis na secção typeProperties:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. |Verdadeiro, Falso (padrão) |Não |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Consulte [os formatos de arquivo e compressão no artigo da Azure Data Factory](data-factory-supported-file-and-compression-formats.md) sobre detalhes.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemplo JSON: Copiar dados de HDFS para Azure Blob
Esta amostra mostra como copiar dados de um HDFS no local para o armazenamento de blob Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.  

A amostra fornece definições JSON para as seguintes entidades da Data Factory. Pode utilizar estas definições para criar um pipeline para copiar dados de HDFS para Azure Blob Storage utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Um serviço ligado do tipo [OnPremisesHdfs](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de um HDFS no local para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, crie o portal de gestão de dados. As instruções nos [dados móveis entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

**Serviço ligado ao HDFS:** Este exemplo utiliza a autenticação do Windows. Consulte a secção [de serviços ligadas ao HDFS](#linked-service-properties) para diferentes tipos de autenticação que pode utilizar.

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

**Serviço ligado a Azure Storage:**

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

**Conjunto de dados de entrada HDFS:** Este conjunto de dados refere-se à pasta HDFS DataTransfer/UnitTest/. O pipeline copia todos os ficheiros desta pasta para o destino.

Definição "externa": "verdadeiro" informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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

O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **FileSystemSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **de consulta** seleciona os dados na hora passada para copiar.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilize a autenticação Kerberos para o conector HDFS
Existem duas opções para configurar o ambiente no local de modo a utilizar a autenticação Kerberos no conector HDFS. Pode escolher o que melhor se encaixa no seu caso.
* Opção 1: [Junte-se à máquina de gateway no reino de Kerberos](#kerberos-join-realm)
* Opção 2: [Permitir a confiança mútua entre o domínio windows e o reino de Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opção 1: Junte-se à máquina de gateway no reino de Kerberos

#### <a name="requirement"></a>Requisito:

* A máquina de gateway precisa de se juntar ao reino de Kerberos e não pode juntar-se a nenhum domínio windows.

#### <a name="how-to-configure"></a>Como configurar:

**Na máquina de gateway:**

1.  Executar o utilitário **Ksetup** para configurar o servidor e o reino do Kerberos KDC.

    A máquina deve ser configurada como membro de um grupo de trabalho, uma vez que um reino Kerberos é diferente de um domínio Windows. Isto pode ser conseguido definindo o reino de Kerberos e adicionando um servidor KDC da seguinte forma. Substitua *REALM.COM* pelo seu próprio reino, se necessário.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    **Reinicie** a máquina depois de executar estes 2 comandos.

2.  Verifique a configuração com o comando **Ksetup.** A saída deve ser como:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
        ```

**In Azure Data Factory:**

* Configure the HDFS connector using **Windows authentication** together with your Kerberos principal name and password to connect to the HDFS data source. Check [HDFS Linked Service properties](#linked-service-properties) section on configuration details.

### <a name="kerberos-mutual-trust"></a>Option 2: Enable mutual trust between Windows domain and Kerberos realm

#### Requirement:
*   The gateway machine must join a Windows domain.
*   You need permission to update the domain controller's settings.

#### How to configure:

> [!NOTE]
> Replace REALM.COM and AD.COM in the following tutorial with your own respective realm and domain controller as needed.

**On KDC server:**

1. Edit the KDC configuration in **krb5.conf** file to let KDC trust Windows Domain referring to the following configuration template. By default, the configuration is located at **/etc/krb5.conf**.

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

   **Reinicie** o serviço KDC após a configuração.

2. Prepare um principal chamado **krbtgt/REALM.COM \@ AD.COM** no servidor KDC com o seguinte comando:

   ```cmd
   Kadmin> addprinc krbtgt/REALM.COM@AD.COM
   ```

3. No **hadoop.security.auth_to_local** ficheiro de configuração do serviço HDFS, adicione `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**No controlador de domínio:**

1.  Executar os seguintes comandos **Ksetup** para adicionar uma entrada de reino:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Estabeleça a confiança do Windows Domain para o Reino de Kerberos. [palavra-passe] é a palavra-passe para o **principal krbtgt/REALM.COM \@ AD.COM**.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]
    ```

3.  Selecione algoritmo de encriptação usado em Kerberos.

    1. Vá ao Gestor de Servidor > Gestão de Políticas de Grupo > Objetos de Política de Grupo > Objetos de Política de Grupo > Política de Domínio Padrão ou Ativo e Edite.

    2. Na janela popup do **Editor de Gestão de Políticas de Grupo,** aceda a Políticas de Configuração de Computador > Políticas > Definições de Segurança do Windows > Definições de Segurança > Políticas Locais > Opções de Segurança e configufique **a segurança da rede: Configurar tipos de encriptação permitidos para Kerberos**.

    3. Selecione o algoritmo de encriptação que pretende utilizar quando ligar ao KDC. Normalmente, pode simplesmente selecionar todas as opções.

        ![Tipos de encriptação Config para Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Utilize o comando **Ksetup** para especificar o algoritmo de encriptação a utilizar no REALM específico.

       ```cmd
       C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
       ```

4.  Crie o mapeamento entre a conta de domínio e kerberos principal, de modo a utilizar kerberos principal no Domínio do Windows.

    1. Inicie as ferramentas administrativas > **Utilizadores e Computadores de Diretório Ativo**.

    2. Configure funcionalidades avançadas clicando **em Ver**  >  **Funcionalidades Avançadas**.

    3. Localize a conta à qual pretende criar mapeamentos e clique no botão direito para ver **Os Mapeamentos de Nome** > clique no separador **Nomes Kerberos.**

    4. Adicione um diretor do reino.

        ![Identidade de segurança do mapa](media/data-factory-hdfs-connector/map-security-identity.png)

**Na máquina de gateway:**

* Executar os seguintes comandos **Ksetup** para adicionar uma entrada de reino.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Na Fábrica de Dados Azure:**

* Configure o conector HDFS utilizando a **autenticação do Windows** juntamente com a sua Conta de Domínio ou Kerberos Principal para ligar à fonte de dados HDFS. Consulte a secção [de propriedades do Serviço Linked HDFS](#linked-service-properties) nos detalhes da configuração.

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
