---
title: Copiar dados do HDFS com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de uma origem HDFS na cloud ou no local para os arquivos de dados de sink suportado com uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: jingwang
ms.openlocfilehash: d0f22360a36105e5bfb877d7478b58c37fcc4353
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213785"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Copiar dados do HDFS com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-hdfs-connector.md)
> * [Versão atual](connector-hdfs.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do HDFS. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do HDFS para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector HDFS suporta:

- Copiar ficheiros usando **Windows** (Kerberos) ou **anónimo** autenticação.
- Copiar ficheiros usando **webhdfs** protocolo ou **DistCp incorporado** suportar.
- Copiar ficheiros como-é ou análise/gerar arquivos com o [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de um HDFS que não está acessível ao público, terá de configurar um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](concepts-integration-runtime.md) artigo para saber mais detalhes.

> [!NOTE]
> Certifique-se com o Runtime de integração pode acessar **todos os** [server de nó nome]: [nome de porta de nó] e [servidores de nó de dados]: [porta do nó de dados] do cluster de Hadoop. A predefinição [porta do nó de nome] é 50070 e padrão [porta do nó de dados] é 50075.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas no HDFS.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do HDFS:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **Hdfs**. | Sim |
| url |URL para o HDFS |Sim |
| authenticationType | Valores permitidos são: **Anónimo**, ou **Windows**. <br><br> Para utilizar **a autenticação Kerberos** para o conector do HDFS, consulte [nesta secção](#use-kerberos-authentication-for-hdfs-connector) para configurar o seu ambiente no local em conformidade. |Sim |
| userName |Autenticação de nome de utilizador para Windows. Para a autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para a autenticação do Windows. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim (para autenticação do Windows) |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

**Exemplo: utilizar a autenticação anónima**

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

**Exemplo: utilizar a autenticação do Windows**

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do HDFS.

Para copiar dados do HDFS, defina a propriedade de tipo de conjunto de dados para **partilha de ficheiros**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados deve ser definida como: **FileShare** |Sim |
| folderPath | Caminho para a pasta. Filtro de carateres universais é suportado, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de ficheiro real tem carateres universais ou esse caractere de escape dentro de escape. <br/><br/>Exemplos: rootfolder/subpasta /, veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). |Sim |
| fileName |  **Filtro de nome ou o caráter universal** para o ficheiro ou ficheiros sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros na pasta. <br/><br/>Para o filtro, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape. |Não |
| modifiedDatetimeStart | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| Formato | Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não (apenas para o cenário de cópia binária) |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Ideal** e **mais rápida**. |Não |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique **folderPath** apenas.<br>Para copiar um único ficheiro com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com o nome de ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro de carateres universais.

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

### <a name="folder-and-file-filter-examples"></a>Pasta e exemplos de filtro de ficheiro

Esta secção descreve o comportamento resultante o nome de ficheiro e caminho de pasta com filtros de caráter universal.

| folderPath | fileName | recursiva | Resultado de estrutura e o filtro de pasta de origem (arquivos no **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, utilizar a predefinição) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vazio, utilizar a predefinição) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem HDFS.

### <a name="hdfs-as-source"></a>HDFS como origem

Para copiar dados do HDFS, defina o tipo de origem na atividade de cópia para **HdfsSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **HdfsSource** |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção quando recursiva é definida como true e de sink é baseada em ficheiros de arquivo, vazia pasta/subutilização-folder não serão copiados/criado no sink.<br/>Valores permitidos são: **true** (predefinição), **FALSO** | Não |
| distcpSettings | Grupo de propriedades quando utilizar o DistCp do HDFS. | Não |
| resourceManagerEndpoint | O ponto de extremidade de ResourceManager do Yarn | Sim, se utilizar o DistCp |
| tempScriptPath | Um caminho de pasta utilizado para armazenar o script de comando DistCp temp. O ficheiro de script é gerado por fábrica de dados e será removido após a conclusão da tarefa de cópia. | Sim, se utilizar o DistCp |
| distcpOptions | Opções adicionais fornecidas para o comando DistCp. | Não |

**Exemplo: Origem de HDFS na atividade de cópia com o descarregamento**

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

Saiba mais sobre como utilizar o DistCp para copiar dados do HDFS com eficiência a partir da secção seguinte.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Utilizar o DistCp para copiar dados do HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) é uma Hadoop de ferramenta de linha de comandos nativa para fazer a cópia distribuída num cluster do Hadoop. Quando executar um comando Distcp, ele primeiro listará todos os arquivos copiados, criar várias tarefas de mapa no cluster do Hadoop, e cada tarefa de mapa irá executar a cópia de binária de origem para o sink.

Copie a utilizar o DistCp para copiar ficheiros como o suporte de atividade-é em BLOBs do Azure (incluindo [cópia faseada](copy-activity-performance.md) ou do Azure Data Lake Store, caso em que ele pode aproveitar totalmente power do seu cluster em vez de em execução no Runtime de integração autoalojado . Irá fornecer um melhor débito de cópia especialmente se o cluster é muito poderoso. Com base na sua configuração no Azure Data Factory, atividade de cópia automaticamente construir um comando distcp, enviar para o seu cluster do Hadoop e monitorizar o estado de cópia.

### <a name="prerequsites"></a>Prerequsites

Utilizar o DistCp para copiar os ficheiros como-é do HDFS para BLOBs do Azure (incluindo a cópia faseada) ou do Azure Data Lake Store, certifique-se o cluster de Hadoop cumpre abaixo requisitos:

1. Serviços de MapReduce e o Yarn estão ativados.
2. Versão do yarn é 2.5 ou superior.
3. Servidor HDFS é integrada com o seu armazenamento de dados de destino - BLOBs do Azure ou do Azure Data Lake Store:

    - Sistema de ficheiros Blob do Azure é suportado nativamente desde 2.7 do Hadoop. Só precisa de especificar o caminho de jar na configuração de env do Hadoop.
    - Sistema de ficheiros do Azure Data Lake Store é empacotado a partir de 3.0.0-alpha1 do Hadoop. Se o seu cluster do Hadoop é inferior essa versão, terá de importar manualmente o ADLS relacionados com o jar pacotes (azure-datalake-store.jar) em cluster a partir de [aqui](https://hadoop.apache.org/releases.html)e especifique o caminho de jar na configuração de env do Hadoop.

4. Prepare-se uma pasta temporária no HDFS. Esta pasta temporária é utilizada para armazenar o script de shell DistCp, para que ele será ocupam o espaço de nível de KB.
5. Certifique-se a conta de utilizador fornecida no serviço ligado do HDFS tem permissão para um) submeter a aplicação no Yarn; b) tem permissão para criar a subpasta, ler/escrever ficheiros em acima pasta temporária.

### <a name="configurations"></a>Configurações

Segue-se um exemplo de configuração de atividade de cópia para copiar dados do HDFS para BLOBs do Azure, utilizar o DistCp:

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilizar a autenticação Kerberos para o conector do HDFS

Existem duas opções para configurar o ambiente no local de modo a utilizar a autenticação Kerberos no conector HDFS. Pode escolher que o melhor se adequa a seu caso.
* Opção 1: [Junte-se a máquina do Integration Runtime autoalojado no Kerberos realm](#kerberos-join-realm)
* Opção 2: [Ativar a fidedignidade bidirecional entre o domínio do Windows e o Kerberos realm](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: Junte-se a máquina do Integration Runtime autoalojado no Kerberos realm

#### <a name="requirements"></a>Requisitos

* A máquina de Runtime de integração autoalojado tem de associar o realm de Kerberos e não é possível associar a qualquer domínio Windows.

#### <a name="how-to-configure"></a>Como configurar

**No computador do Runtime de integração autoalojado:**

1.  Executar o **Ksetup** utilitário para configurar o servidor de KDC do Kerberos e realm.

    A máquina tem de ser configurada como membro do grupo de trabalho, uma vez que um realm de Kerberos é diferente de um domínio Windows. Isso pode ser conseguido ao definir o realm de Kerberos e adicionar um servidor KDC da seguinte forma. Substitua *REALM.COM* com seu respectivo realm conforme necessário.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reiniciar** o computador depois de executar estes 2 comandos.

2.  Certifique-se a configuração com o **Ksetup** comando. O resultado deve ser, como:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Na fábrica de dados do Azure:**

* Configure a através do conector HDFS **autenticação do Windows** juntamente com seu nome principal Kerberos e a palavra-passe para ligar à origem de dados de HDFS. Verifique [propriedades de serviço ligado do HDFS](#linked-service-properties) seção sobre detalhes de configuração.

### <a name="kerberos-mutual-trust"></a>Opção 2: Ativar a fidedignidade bidirecional entre o domínio do Windows e o Kerberos realm

#### <a name="requirements"></a>Requisitos

*   A máquina de Runtime de integração autoalojado tem de associar um domínio Windows.
*   Necessita de permissão para atualizar as definições do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial seguinte com seu próprio realm respectivo e o controlador de domínio, conforme necessário.

**No servidor do KDC:**

1.  Editar a configuração de KDC no **krb5.conf** arquivo para permitir que o KDC confiar no domínio do Windows que faça referência ao modelo de configuração seguintes. Por predefinição, a configuração está localizada em **/etc/krb5.conf**.

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

  **Reiniciar** o serviço KDC após a configuração.

2.  Preparar um principal com o nome **krbtgt/REALM.COM@AD.COM** no servidor KDC com o seguinte comando:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  Na **hadoop.security.auth_to_local** configuração do serviço HDFS do ficheiro, adicione `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**No controlador de domínio:**

1.  Execute o seguinte **Ksetup** comandos para adicionar uma entrada de realm:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabelece confiança de domínio do Windows para o Realm de Kerberos. [senha] é a palavra-passe para o principal **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione o algoritmo de encriptação utilizado no Kerberos.

    1. Aceda ao Gestor de servidor > Gestão de política de grupo > domínio > objetos de política de grupo > padrão ou política de domínio Active Directory e editar.

    2. Na **Editor de gerenciamento de diretiva de grupo** janela de pop-up, aceda a configuração do computador > políticas > definições do Windows > definições de segurança > Políticas locais > Opções de segurança e configurar **rede segurança: Configurar tipos de encriptação permitidos para Kerberos**.

    3. Selecione o algoritmo de encriptação que pretende utilizar quando ligar ao KDC. Normalmente, pode simplesmente selecionar todas as opções.

        ![Tipos de encriptação de configuração de Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Uso **Ksetup** comando para especificar o algoritmo de encriptação a utilizar sobre o REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e o principal de Kerberos, para poder utilizar o principal de Kerberos no domínio do Windows.

    1. Iniciar as ferramentas administrativas > **Active Directory Users and Computers**.

    2. Configurar funcionalidades avançadas clicando **View** > **funcionalidades avançadas**.

    3. Localize a conta à qual pretende criar mapeamentos e, com o botão direito para ver **mapeamentos de nome** > clique em **Kerberos nomes** separador.

    4. Adicione um principal de território.

        ![Identidade de segurança do mapa](media/connector-hdfs/map-security-identity.png)

**No computador do Runtime de integração autoalojado:**

* Execute o seguinte **Ksetup** comandos para adicionar uma entrada de realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Na fábrica de dados do Azure:**

* Configure a através do conector HDFS **autenticação do Windows** juntamente com a sua conta de domínio ou Principal de Kerberos para ligar à origem de dados de HDFS. Verifique [propriedades de serviço ligado do HDFS](#linked-service-properties) seção sobre detalhes de configuração.


## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
