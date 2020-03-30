---
title: Copiar dados para/de um sistema de ficheiros utilizando a Azure Data Factory
description: Saiba como copiar dados de e para um sistema de ficheiros no local utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d298c83c0c1a0f33f28644e2e467ad5035300221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265938"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Copiar dados de e para um sistema de ficheiros no local utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-file-system-connector.md)
> * [Versão 2 (versão atual)](../connector-file-system.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector do Sistema de Ficheiros em V2](../connector-file-system.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de/para um sistema de ficheiros no local. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **de um sistema de ficheiros no local** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para um sistema de ficheiros no local:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> A Atividade de Cópia não elimina o ficheiro fonte depois de ter sido copiado com sucesso para o destino. Se necessitar de eliminar o ficheiro fonte após uma cópia bem sucedida, crie uma atividade personalizada para apagar o ficheiro e utilizar a atividade no pipeline.

## <a name="enabling-connectivity"></a>Permitindo a conectividade
Data Factory suporta a ligação de e para um sistema de ficheiros no local através do Gateway de **Gestão**de Dados . Deve instalar o Portal de Gestão de Dados no seu ambiente no local para que o serviço Data Factory se conectem a qualquer loja de dados apoiada no local, incluindo o sistema de ficheiros. Para conhecer o Gateway de Gestão de Dados e para obter instruções passo a passo sobre a configuração do gateway, consulte [Mover dados entre fontes no local e a nuvem com Gateway](data-factory-move-data-between-onprem-and-cloud.md)de Gestão de Dados . Para além do Portal de Gestão de Dados, não é necessário instalar outros ficheiros binários para comunicar de e para um sistema de ficheiros no local. Tem de instalar e utilizar o Portal de Gestão de Dados, mesmo que o sistema de ficheiros esteja em VM Azure IaaS. Para obter informações detalhadas sobre o portal, consulte Data [Management Gateway](data-factory-data-management-gateway.md).

Para utilizar uma partilha de ficheiros Linux, instale [o Samba](https://www.samba.org/) no seu servidor Linux e instale o Portal de Gestão de Dados num servidor Windows. A instalação de Gateway de Gestão de Dados num servidor Linux não é suportada.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para um sistema de ficheiros utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para um sistema de ficheiros no local, cria dois serviços ligados para ligar o seu sistema de ficheiros no local e a conta de armazenamento Azure à sua fábrica de dados. Para propriedades de serviço saem específicas de um sistema de ficheiros no local, consulte a secção de [propriedades de serviço ligadas.](#linked-service-properties)
3. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente e a pasta blob que contém os dados de entrada. E cria outro conjunto de dados para especificar a pasta e o nome do ficheiro (opcional) no seu sistema de ficheiros. Para propriedades de conjunto de dados específicas para o sistema de ficheiros no local, consulte a secção de propriedades do conjunto de [dados.](#dataset-properties)
4. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, utiliza o BlobSource como fonte e o FileSystemSink como um lavatório para a atividade da cópia. Da mesma forma, se estiver a copiar do sistema de ficheiros no local para o Armazenamento De Blob Azure, utiliza fileSystemSource e BlobSink na atividade da cópia. Para propriedades de copy activity específicas para o sistema de ficheiros no local, consulte a secção de propriedades de atividade de [cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para amostras com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de/para um sistema de ficheiros, consulte a secção de [exemplos jSON](#json-examples-for-copying-data-to-and-from-file-system) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades data Factory específicas para sistema de ficheiros:

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
Pode ligar um sistema de ficheiros no local a uma fábrica de dados Azure com o serviço ligado ao **Servidor de Ficheiros On-Premises.** A tabela seguinte fornece descrições para elementos JSON específicos do serviço ligado ao Servidor de Ficheiros No Local.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |Certifique-se de que a propriedade do tipo está definida para **onPremisesFileServer**. |Sim |
| anfitrião |Especifica o caminho raiz da pasta que pretende copiar. Use o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte as definições de serviço ligados à [amostra e definições](#sample-linked-service-and-dataset-definitions) de conjunto de dados, por exemplo. |Sim |
| usado |Especifique a identificação do utilizador que tem acesso ao servidor. |Não (se escolher o Credential encriptado) |
| palavra-passe |Especifique a palavra-passe para o utilizador (userid). |Não (se escolher o Credential encriptado |
| credenta encriptado |Especifique as credenciais encriptadas que pode obter executando o cmdlet New-AzDataFactoryEncryptValue. |Não (se optar por especificar userid e palavra-passe em texto simples) |
| nome gateway |Especifica o nome do portal que a Data Factory deve utilizar para se ligar ao servidor de ficheiros no local. |Sim |


### <a name="sample-linked-service-and-dataset-definitions"></a>Definições de serviço ligados à amostra e conjunto de dados
| Cenário | Anfitrião na definição de serviço ligado | pastaCaminho na definição de conjunto de dados |
| --- | --- | --- |
| Pasta local na máquina Gateway de Gestão de Dados: <br/><br/>Exemplos: D:\\ \* ou D:\folder\subpasta\\\* |D:\\ \\ (para data Management Gateway 2.0 e versões posteriores) <br/><br/> localhost (para versões anteriores do que Gateway 2.0 de Gestão de Dados) |. ou\\\\subpasta de pasta (para data Management Gateway 2.0 e versões posteriores) \\ \\ <br/><br/>D:\\ \\ ou\\\\D: subpasta de pasta\\\\(para versão gateway abaixo de 2.0) |
| Pasta partilhada remota: <br/><br/>Exemplos: \\ \\\\myserver\\ \* \\ \\share\\ou\\\\myserver share folder subfolder\\\* |\\\\\\\\myserver\\\\partilhar |. ou\\subpasta de pasta\\ \\ \\ |

>[!NOTE]
>Ao ser autor através da UI, não é necessário`\\`inserir o duplo backslash ( ) para escapar como faz através da JSON, especifique um único backslash.

### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: Usar o nome de utilizador e a palavra-passe em texto simples

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Exemplo: Utilização de um cedcessão encriptado

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Criar conjuntos](data-factory-create-datasets.md)de dados . Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntode dados.

A secção typeProperties é diferente para cada tipo de conjunto de dados. Fornece informações como a localização e o formato dos dados na loja de dados. A secção typeProperties para o conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Especifica o subcaminho para a pasta. Use o personagem de fuga para\' personagens especiais na corda. O filtro Wildcard não é suportado. Consulte as definições de serviço ligados à [amostra e definições](#sample-linked-service-and-dataset-definitions) de conjunto de dados, por exemplo.<br/><br/>Pode combinar esta propriedade com **partiçãoBy** para ter caminhos de pastas baseados em datas de início/fim de fatias. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPath** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o nome do **ficheiro** não é especificado para um conjunto de dados de saída e preservar a **hierarquia** não é especificado no sumidouro de atividade, o nome do ficheiro gerado está no seguinte formato: <br/><br/>`Data.<Guid>.txt`(Exemplo: Dados.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a utilizar para selecionar um subconjunto de ficheiros na pastaPath em vez de todos os ficheiros. <br/><br/>Os valores `*` permitidos são: (múltiplos caracteres) e `?` (personagem único).<br/><br/>Exemplo 1: "fileFilter": "*.log"<br/>Exemplo 2: "fileFilter": 2014-1-?. txt "<br/><br/>Note que o filtro de ficheiros é aplicável para um conjunto de dados de FileShare de entrada. |Não |
| divididoBy |Pode utilizar o divididoBy para especificar uma pasta dinâmicaPath/fileName para dados da série de tempo. Um exemplo é a pastaPath parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. ver [formatos de ficheiros e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Não é possível utilizar o nome de ficheiro e o ficheiroFilter simultaneamente.

### <a name="using-partitionedby-property"></a>Usando propriedade divididaBy
Conforme mencionado na secção anterior, pode especificar uma pasta dinâmicaPath e nome de ficheiro para dados de séries de tempo com a propriedade **divididaBy,** [funções data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Para compreender mais detalhes sobre conjuntos de dados, agendamento e fatias da série de tempo, consulte [Criar conjuntos](data-factory-create-datasets.md)de dados, [agendamento e execução](data-factory-scheduling-and-execution.md)e [criar oleodutos.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Amostra 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {Slice} é substituído pelo valor da variável sliceStart do sistema data Factory no formato (YYYYMMDDHH). SliceStart refere-se à hora de início da fatia. A pastaCaminho é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

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

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que as propriedades da pastaPath e fileName usam.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, conjuntos de dados de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades. Considerando que as propriedades disponíveis na secção **typeProperties** da atividade variam com cada tipo de atividade.

Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias. Se estiver a mover dados de um sistema de ficheiros no local, delineie o tipo de origem na atividade de cópia para **FileSystemSource**. Da mesma forma, se estiver a mover dados para um sistema de ficheiros no local, delineie o tipo de pia na atividade de cópia para **FileSystemSink**. Esta secção fornece uma lista de propriedades suportadas por FileSystemSource e FileSystemSink.

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdade, Falso (padrão) |Não |

**FileSystemSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copiarComportamento |Define o comportamento da cópia quando a fonte é BlobSource ou FileSystem. |**PreserveHierarchy:** Preserva a hierarquia dos ficheiros na pasta-alvo. Ou seja, o caminho relativo do ficheiro fonte para a pasta fonte é o mesmo que o caminho relativo do ficheiro alvo para a pasta-alvo.<br/><br/>**Hierarquia do Achatamento:** Todos os ficheiros da pasta fonte são criados no primeiro nível da pasta alvo. Os ficheiros-alvo são criados com um nome autogerado.<br/><br/>**Ficheiros de fusão:** Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro/nome blob for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, trata-se de um nome de ficheiro gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>exemplos recursivos e copyBehavior
Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores para as propriedades recursivas e copyBehavior.

| valor recursivo | cópiaValor de comportamento | Comportamento resultante |
| --- | --- | --- |
| true |preservar Hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura,<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 |
| true |achatar a hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura,<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta 1 com a seguinte estrutura,<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + Ficheiro 5 conteúdos são fundidos num só ficheiro com um nome de ficheiro gerado automaticamente. |
| false |preservar Hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura,<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com File3, File4 e File5 não é captada. |
| false |achatar a hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura,<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Subpasta1 com File3, File4 e File5 não é captada. |
| false |mergeFiles |Para uma pasta de origem Pasta 1 com a seguinte estrutura,<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos File1 + File2 são fundidos num só ficheiro com um nome de ficheiro gerado automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/><br/>Subpasta1 com File3, File4 e File5 não é captada. |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Consulte [os formatos de Arquivo e compressão no](data-factory-supported-file-and-compression-formats.md) artigo da Azure Data Factory sobre detalhes.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Exemplos jSON para copiar dados de e para o sistema de ficheiros
Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados de e para um sistema de ficheiros no local e armazenamento de Blob Azure. No entanto, pode copiar dados *diretamente* de qualquer uma das fontes para qualquer um dos lavatórios listados em [fontes e pias suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a Copy Activity na Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exemplo: Copiar dados de um sistema de ficheiros no local para armazenamento de Blob Azure
Esta amostra mostra como copiar dados de um sistema de ficheiros no local para o armazenamento de Azure Blob. A amostra tem as seguintes entidades data Factory:

* Um serviço ligado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra seguinte copia dados da série time de um sistema de ficheiros no local para o armazenamento de Azure Blob a cada hora. As propriedades JSON que são utilizadas nestas amostras são descritas nas secções após as amostras.

Como primeiro passo, criar o Portal de Gestão de Dados de acordo com as instruções em [Mover dados entre fontes no local e a nuvem com Gateway](data-factory-move-data-between-onprem-and-cloud.md)de Gestão de Dados .

**Serviço ligado ao servidor de ficheiros no local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Recomendamos a utilização da propriedade **Credential encriptada,** em vez das propriedades **utilizadas** e **passwords.** Consulte o serviço ligado ao Servidor de [Ficheiros](#linked-service-properties) para obter detalhes sobre este serviço ligado.

**Serviço ligado ao Armazenamento Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de dados do sistema de ficheiros no local:**

Os dados são recolhidos de um novo ficheiro a cada hora. As propriedades do folderPath e do fileName são determinadas com base na hora de início da fatia.

A `"external": "true"` definição informa a Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída de armazenamento Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza as partes do ano, mês, dia e hora da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Uma atividade de cópia num oleoduto com fonte do Sistema de Ficheiros e pia Blob:**

O pipeline contém uma atividade de cópia configurada para utilizar os conjuntos de dados de entrada e saída, e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **FileSystemSource**, e o tipo **de pia** é definido para **BlobSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exemplo: Copiar dados da Base de Dados Azure SQL para um sistema de ficheiros no local
A amostra que se segue mostra:

* Um serviço ligado do tipo [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Um serviço ligado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um conjunto de dados de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Um conjunto de dados de saída do tipo [FileShare](#dataset-properties).
* Um pipeline com uma atividade de cópia que utiliza [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [FileSystemSink](#copy-activity-properties).

A amostra copia dados da série de tempo de uma tabela Azure SQL para um sistema de ficheiros no local a cada hora. As propriedades JSON que são utilizadas nestas amostras são descritas em secções após as amostras.

**Serviço ligado à base de dados Azure SQL:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```

**Serviço ligado ao servidor de ficheiros no local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Recomendamos a utilização da propriedade **Credential encriptada** em vez de utilizar as propriedades **utilizadas** e **passwords.** Consulte o serviço ligado ao Sistema de [Ficheiros](#linked-service-properties) para obter detalhes sobre este serviço ligado.

**Conjunto de dados de entrada Azure SQL:**

A amostra pressupõe que criou uma tabela "MyTable" no Azure SQL, e contém uma coluna chamada "timestampcolumn" para dados da série time.

A ``“external”: ”true”`` definição informa a Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída do sistema de ficheiros no local:**

Os dados são copiados para um novo ficheiro a cada hora. As pastasPath e fileName para a bolha são determinadas com base na hora de início da fatia.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Uma atividade de cópia num oleoduto com fonte SQL e sumidouro do Sistema de Ficheiros:**

O pipeline contém uma atividade de cópia configurada para utilizar os conjuntos de dados de entrada e saída, e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **SqlSource**, e o tipo **de pia** está definido para **FileSystemSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora para copiar.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Também pode mapear colunas desde o conjunto de dados de origem até colunas a partir do conjunto de dados do sumidouro na definição de atividade da cópia. Para mais detalhes, consulte [mapeiar colunas de conjuntos](data-factory-map-columns.md)de dados na Azure Data Factory .

## <a name="performance-and-tuning"></a>Desempenho e otimização
 Para conhecer os factores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Azure e várias formas de o otimizar, consulte o desempenho da Atividade de Cópia e o guia de [afinação.](data-factory-copy-activity-performance.md)
