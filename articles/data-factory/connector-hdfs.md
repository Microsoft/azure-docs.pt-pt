---
title: Copiar dados do HDFS utilizando a Azure Data Factory
description: Saiba como copiar dados de uma nuvem ou no local fonte HDFS para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 09c39c41b2d31f88fe2b19d8f20cd19e182c9214
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417270"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Copiar dados do HDFS utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-hdfs-connector.md)
> * [Versão atual](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do servidor HDFS. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector HDFS é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Especificamente, este conector HDFS suporta:

- Copiar ficheiros utilizando o **Windows** (Kerberos) ou a autenticação **Anónima.**
- Copiar ficheiros utilizando o protocolo **webhdfs** ou suporte **DistCp incorporado.**
- Copiar ficheiros como estão ou analisar/gerar ficheiros com os [formatos de ficheiros suportados e os códigos](supported-file-formats-and-compression-codecs.md)de compressão .

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Certifique-se de que o Tempo de Execução de Integração pode aceder a **ALL** o [servidor do nó de nome]:[porta do nó de nome] e [servidores de nó de dados]:[porta do nó de dados] do cluster Hadoop. O padrão [porta do nó de nome] é 50070, e o padrão [porta do nó de dados] é 50075.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas ao HDFS.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao HDFS:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Hdfs**. | Sim |
| url |URL para o HDFS |Sim |
| authenticationType | Os valores permitidos são: **Anónimos,** ou **Windows**. <br><br> Para utilizar a **autenticação Kerberos** para o conector HDFS, consulte [esta secção](#use-kerberos-authentication-for-hdfs-connector) para configurar o seu ambiente no local em conformidade. |Sim |
| userName |Nome de utilizador para autenticação do Windows. Para autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para autenticação do Windows. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim (para autenticação do Windows) |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

**Exemplo: utilização de autenticação anónima**

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas `location` para HDFS em definições no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade `location` do tipo em conjunto de dados deve ser definida para **HdfsLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se pretender utilizar o wildcard para filtrar a pasta, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro sob a pasta dadaPath. Se pretender utilizar ficheiros wildcard para filtrar ficheiros, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte hdfs.

### <a name="hdfs-as-source"></a>HDFS como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas `storeSettings` para HDFS em definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade `storeSettings` do tipo em baixo deve ser definida para **HdfsReadSettings**. | Sim                                           |
| recursivo                | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando se recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres wildcard para filtrar as pastas de origem. <br>Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro. <br>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| nome wildcardFile         | O nome do ficheiro com caracteres wildcard sob a pasta dadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro.  Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Sim, `fileName` se não for especificado no conjunto de dados |
| alteradoDatetimeStart    | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados. | Não                                            |
| alteradoDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| distcpSettings | Grupo de propriedades ao utilizar HDFS DistCp. | Não |
| recursosManagerEndpoint | O ponto final do Gestor de Recursos de Fios | Sim, se usar DistCp |
| tempScriptPath | Um caminho de pasta usado para armazenar o script de comando DistCp temporário. O ficheiro script é gerado pela Data Factory e será removido após o trabalho da Cópia ter terminado. | Sim, se usar DistCp |
| distcpOptions | Opções adicionais fornecidas ao comando DistCp. | Não |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                            |

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

Esta secção descreve o comportamento resultante do caminho da pasta e nome de ficheiro com filtros wildcard.

| folderPath | fileName             | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em **negrito** são recuperados) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (vazio, utilização por defeito) | false     | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (vazio, utilização por defeito) | true      | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Utilize o DistCp para copiar dados do HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) é uma ferramenta de linha de comando nativa hadoop para fazer cópia distribuída num cluster Hadoop. Quando executar um comando Distcp, ele primeiro listará todos os ficheiros a copiar, criará vários trabalhos mapnográficos no cluster Hadoop, e cada trabalho do Mapa fará cópias binárias de origem a afundar.

Suporte de atividade de cópia utilizando distCp para copiar ficheiros como está em Azure Blob (incluindo [cópia encenada)](copy-activity-performance.md)ou Azure Data Lake Store, nesse caso pode alavancar totalmente a potência do seu cluster em vez de executar no Tempo de Integração Auto-hospedado. Fornecerá uma melhor cópia de entrada, especialmente se o seu cluster for muito poderoso. Com base na sua configuração na Azure Data Factory, a atividade de cópia constrói automaticamente um comando distcp, submete-se ao seu cluster Hadoop e monitoriza o estado da cópia.

### <a name="prerequisites"></a>Pré-requisitos

Para utilizar o DistCp para copiar ficheiros como é de HDFS a Azure Blob (incluindo cópia encenada) ou Azure Data Lake Store, certifique-se de que o seu cluster Hadoop satisfaz os requisitos abaixo dos requisitos:

1. Os serviços MapReduce e Yarn estão ativados.
2. A versão de fios é 2.5 ou superior.
3. O servidor HDFS está integrado na sua loja de dados alvo - Azure Blob ou Azure Data Lake Store:

    - O Sistema de Ficheiros Azure Blob é suportado de forma nativa desde Hadoop 2.7. Basta especificar o caminho do frasco em Hadoop env config.
    - O Sistema de Ficheiros azure Data Lake Store está embalado a partir de Hadoop 3.0.0-alpha1. Se o seu cluster Hadoop for inferior a essa versão, precisa de importar manualmente pacotes de frascos relacionados com ADLS (azure-datalake-store.jar) para o cluster a partir [daqui](https://hadoop.apache.org/releases.html), e especificar o caminho do frasco em Hadoop env config.

4. Prepare uma pasta temporária no HDFS. Esta pasta temporária é usada para armazenar o script de concha DistCp, para que ocupe o espaço de nível KB.
5. Certifique-se de que a conta de utilizador fornecida no HDFS Linked Service tem permissão para a) submeter a aplicação em Fios; b) ter a permissão para criar subpastas, ler/escrever ficheiros por baixo da pasta acima da temperatura.

### <a name="configurations"></a>Configurações

Consulte as configurações e exemplos relacionados com distCp no [HDFS como](#hdfs-as-source) secção fonte.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilize a autenticação Kerberos para conector HDFS

Existem duas opções para configurar o ambiente no local de modo a utilizar a Autenticação Kerberos no conector HDFS. Pode escolher o que melhor se encaixa no seu caso.
* Opção 1: Junte-se à máquina de corrida de [integração auto-hospedada no reino de Kerberos](#kerberos-join-realm)
* Opção 2: Permitir a confiança mútua entre o domínio do Windows e o reino de [Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opção 1: Junte-se à máquina de corrida de integração auto-hospedada no reino de Kerberos

#### <a name="requirements"></a>Requisitos

* A máquina de corrida de integração auto-hospedada precisa de se juntar ao reino kerberos e não pode aderir a nenhum domínio windows.

#### <a name="how-to-configure"></a>Como configurar

**Na máquina de tempo de execução de integração auto-hospedada:**

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

#### <a name="requirements"></a>Requisitos

*   A máquina de tempo de execução de integração auto-hospedada deve juntar-se a um domínio Windows.
*   Precisa de autorização para atualizar as definições do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar

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

        ![Tipos de encriptação config para Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Utilize o comando **Ksetup** para especificar o algoritmo de encriptação a ser utilizado no REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e o principal Kerberos, de forma a utilizar o principal Kerberos no Domínio do Windows.

    1. Inicie as ferramentas administrativas > **Utilizadores e Computadores de Diretório Ativo.**

    2. Configure funcionalidades avançadas clicando em **Visualizar** > **Funcionalidades Avançadas**.

    3. Localize a conta para a qual pretende criar mapeamentos e clique à direita para ver **Mapeamentos** de nome sem > clique no separador **Nomes Kerberos.**

    4. Adicione um diretor do reino.

        ![Identidade de segurança do mapa](media/connector-hdfs/map-security-identity.png)

**Na máquina de tempo de execução de integração auto-hospedada:**

* Executar os seguintes comandos **Ksetup** para adicionar uma entrada no reino.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Na Fábrica de Dados Azure:**

* Configure o conector HDFS utilizando a **autenticação do Windows** juntamente com a sua Conta de Domínio ou o Principal Kerberos para se ligar à fonte de dados hdfs. Verifique a secção de [propriedades do Serviço Ligado HDFS](#linked-service-properties) nos detalhes da configuração.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os seguintes modelos ainda são suportados como é para retrocompatibilidade. É-lhe sugerido que utilize o novo modelo mencionado nas secções acima, e a UI de autoria da ADF mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **FileShare** |Sim |
| folderPath | Caminho para a pasta. O filtro Wildcard é suportado, `*` os wildcards permitidos `?` são: (corresponde a zero ou mais caracteres) e (corresponde a zero ou personagem individual); usar `^` para escapar se o seu nome de arquivo real tiver wildcard ou este char de fuga dentro. <br/><br/>Exemplos: pasta/subpasta/, ver mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). |Sim |
| fileName |  **Nome ou filtro wildcard** para os ficheiros(s) sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os `*` wildcards permitidos são: (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou personagem individual).<br/>- Exemplo 1:`"fileName": "*.csv"`<br/>- Exemplo 2:`"fileName": "???20180427.txt"`<br/>Utilize `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga no seu interior. |Não |
| alteradoDatetimeStart | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> Esteja ciente de que o desempenho global do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAs, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| Não |
| alteradoDatetimeEnd | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> Esteja ciente de que o desempenho global do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAs, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| Não |
| formato | Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ficheiros com um formato específico, os seguintes tipos de formato de ficheiro são suportados: **TextFormat**, **JsonFormat**, **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Formato Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [Formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para mais informações, consulte [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)de compressão .<br/>Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**.<br/>Os níveis suportados são: **Optimal** e **Fastest**. |Não |

>[!TIP]
>Para copiar todos os ficheiros sob uma pasta, especifique apenas **pastaPath.**<br>Para copiar um único ficheiro com um nome dado, especifique **a pastaPath** com peça de pasta e nome de **ficheiro** com nome de ficheiro.<br>Para copiar um subconjunto de ficheiros sob uma pasta, especifique **a pastaPath** com peça de pasta e nome de **ficheiro** com filtro wildcard.

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de fonte de fonte de atividade de cópia legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **HdfsSource** |Sim |
| recursivo | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando a recursiva for definida como verdadeira e a pia for uma loja baseada em ficheiros, a pasta/subpasta vazia não será copiada/criada na pia.<br/>Os valores permitidos são: **verdadeiros** (padrão), **falsos** | Não |
| distcpSettings | Grupo de propriedades ao utilizar HDFS DistCp. | Não |
| recursosManagerEndpoint | O ponto final do Gestor de Recursos de Fios | Sim, se usar DistCp |
| tempScriptPath | Um caminho de pasta usado para armazenar o script de comando DistCp temporário. O ficheiro script é gerado pela Data Factory e será removido após o trabalho da Cópia ter terminado. | Sim, se usar DistCp |
| distcpOptions | Opções adicionais fornecidas ao comando DistCp. | Não |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

**Exemplo: Fonte HDFS na atividade de cópia utilizando DistCp**

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
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
