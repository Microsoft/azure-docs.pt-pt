---
title: Copiar dados de/para um sistema de ficheiros utilizando a Azure Data Factory
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85847610"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Copiar dados de e para um sistema de ficheiros no local utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-file-system-connector.md)
> * [Versão 2 (versão atual)](../connector-file-system.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector do Sistema de Ficheiros em V2](../connector-file-system.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de/para um sistema de ficheiros no local. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados de um sistema de ficheiros no local para as **seguintes lojas de** dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para um sistema de ficheiros no local:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> A Copy Activity não apaga o ficheiro de origem depois de ter sido copiado com sucesso para o destino. Se precisar de eliminar o ficheiro de origem após uma cópia bem sucedida, crie uma atividade personalizada para eliminar o ficheiro e utilizar a atividade no pipeline.

## <a name="enabling-connectivity"></a>Habilitação da conectividade
A Data Factory suporta a ligação de e para um sistema de ficheiros no local através do **Data Management Gateway**. Tem de instalar o Data Management Gateway no seu ambiente no local para que o serviço data Factory se conecte a qualquer loja de dados suportada no local, incluindo o sistema de ficheiros. Para saber mais sobre o Gateway de Gestão de Dados e para obter instruções passo a passo sobre a configuração do gateway, consulte [mover dados entre as fontes no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). Para além do Data Management Gateway, não é necessário instalar outros ficheiros binários para comunicar de e para um sistema de ficheiros no local. Tem de instalar e utilizar o Gateway de Gestão de Dados mesmo que o sistema de ficheiros esteja no Azure IaaS VM. Para obter informações detalhadas sobre o gateway, consulte [o Data Management Gateway](data-factory-data-management-gateway.md).

Para utilizar uma partilha de ficheiros Linux, [instale o Samba](https://www.samba.org/) no seu servidor Linux e instale o Data Management Gateway num servidor Windows. A instalação do Gateway de Gestão de Dados num servidor Linux não é suportada.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para um sistema de ficheiros utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para um sistema de ficheiros no local, cria dois serviços ligados para ligar o seu sistema de ficheiros no local e a conta de armazenamento Azure à sua fábrica de dados. Para propriedades de serviço ligadas específicas a um sistema de ficheiros no local, consulte a secção [de propriedades de serviços ligadas.](#linked-service-properties)
3. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente blob e a pasta que contém os dados de entrada. E cria outro conjunto de dados para especificar a pasta e o nome do ficheiro (opcional) no seu sistema de ficheiros. Para propriedades de conjunto de dados específicas do sistema de ficheiros no local, consulte a secção [de propriedades do conjunto de dados.](#dataset-properties)
4. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, utiliza o BlobSource como fonte e o FileSystemSink como pia para a atividade da cópia. Da mesma forma, se estiver a copiar do sistema de ficheiros no local para o Azure Blob Storage, utilize o FileSystemSource e o BlobSink na atividade de cópia. Para propriedades de atividade de cópia específicas do sistema de ficheiros no local, consulte a secção [de propriedades da atividade de cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON.  Para amostras com definições JSON para entidades da Data Factory que são usadas para copiar dados de/para um sistema de ficheiros, consulte a secção de [exemplos JSON](#json-examples-for-copying-data-to-and-from-file-system) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas para o sistema de ficheiros:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
Pode ligar um sistema de ficheiros no local a uma fábrica de dados Azure com o serviço de **ficheiros "Servidor de Ficheiros" "No local".** A tabela seguinte fornece descrições para elementos JSON específicos do serviço de ficheiros no local.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |Certifique-se de que a propriedade tipo está definida para **OnPremisesFileServer**. |Sim |
| anfitrião |Especifica o caminho da raiz da pasta que pretende copiar. Utilize o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte por exemplo [o serviço ligado à amostra e definições de conjunto de dados.](#sample-linked-service-and-dataset-definitions) |Sim |
| userid |Especifique o ID do utilizador que tem acesso ao servidor. |Não (se escolher o Criptografial) |
| palavra-passe |Especifique a palavra-passe para o utilizador (userid). |Não (se escolher o Criptografial |
| criptografadoCredential |Especifique as credenciais encriptadas que pode obter executando o New-AzDataFactoryEncryptValue cmdlet. |Não (se optar por especificar userid e palavra-passe em texto simples) |
| gatewayName |Especifica o nome do gateway que a Data Factory deve utilizar para ligar ao servidor de ficheiros no local. |Sim |


### <a name="sample-linked-service-and-dataset-definitions"></a>Definições de serviço e conjunto de dados ligados à amostra
| Cenário | Hospedagem na definição de serviço ligado | fase de pasta na definição de conjunto de dados |
| --- | --- | --- |
| Pasta local na máquina Data Management Gateway: <br/><br/>Exemplos: D: \\ \* ou D:\pasta\sub-dobra\\\* |D: \\ \\ (para a Gestão de Dados Gateway 2.0 e versões posteriores) <br/><br/> local (para versões anteriores do que data Management Gateway 2.0) |.\\\\ sub-dobragem ou pasta \\ \\ (para a gestão de dados Gateway 2.0 e versões posteriores) <br/><br/>D: \\ \\ ou D: \\ \\ \\ \\ sub-dobragem de pasta (para a versão gateway abaixo de 2.0) |
| Pasta partilhada remotamente: <br/><br/>Exemplos: \\ \\ \\ myserver share ou \\ \* \\ \\ \\ myserver share pasta \\ \\ subfolder\\\* |\\\\\\\\partilha de myserver \\ \\ |.\\\\ ou \\ \\ sub-dobragem de pasta |

>[!NOTE]
>Ao autorizar via UI, não precisa de inserir um duplo backslash `\\` () para escapar como faz através de JSON, especificar uma única contrala detrás.

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

### <a name="example-using-encryptedcredential"></a>Exemplo: Utilização de credenciais encriptadas

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
Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A secção typeProperties é diferente para cada tipo de conjunto de dados. Fornece informações como a localização e formato dos dados na loja de dados. A secção typeProperties para o conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Especifica o subpato à pasta. Use o personagem de fuga \' para caracteres especiais na corda. O filtro Wildcard não é suportado. Consulte por exemplo [o serviço ligado à amostra e definições de conjunto de dados.](#sample-linked-service-and-dataset-definitions)<br/><br/>Pode combinar esta propriedade com **partição Para** ter caminhos de pasta baseados em intervalos de datas de início/fim da fatia. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPata** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando **o nome de ficheiro** não é especificado para um conjunto de dados de saída e **preservarAequia** não é especificado na pia de atividade, o nome do ficheiro gerado está no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| ficheiroFiltro |Especifique um filtro a ser utilizado para selecionar um subconjunto de ficheiros na pastaPalho em vez de todos os ficheiros. <br/><br/>Os valores permitidos são: `*` (múltiplos caracteres) e `?` (único carácter).<br/><br/>Exemplo 1: "fileFilter": "*.log"<br/>Exemplo 2: "fileFilter": 2014-1-?. txt "<br/><br/>Note que o ficheiroFiltro é aplicável para um conjunto de dados de FileShare de entrada. |Não |
| partitionedBy |Pode utilizar partitionedBy para especificar uma pasta dinâmicaPa/nome de ficheiros Para dados da série de tempo. Um exemplo é pastaPamota parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2**e **ZipDeflate**. Os níveis suportados são: **Ideal** e **Mais rápido**. ver [formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Não é possível utilizar o ficheiroName e o ficheiroFiltro simultaneamente.

### <a name="using-partitionedby-property"></a>Usando propriedade partitionedBy
Como mencionado na secção anterior, pode especificar uma pasta dinâmicaPath e nome de ficheiro para dados de séries de tempo com a propriedade **partitionedBy,** [funções de Data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Para obter mais detalhes sobre conjuntos de dados, agendamentos e fatias de séries temporênciais, consulte [criar conjuntos de dados,](data-factory-create-datasets.md) [agendamento e execução,](data-factory-scheduling-and-execution.md)e [criar oleodutos.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Amostra 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {Slice} é substituído pelo valor da variável do sistema de data factory SliceStart no formato (YYYYMMDDHH). SliceStart refere-se à hora de início da fatia. A pastaApata é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

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

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que as propriedades de pastaPath e fileName usam.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, conjuntos de dados de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades. Enquanto que as propriedades disponíveis na secção **de tipos de atividade** variam com cada tipo de atividade.

Para a atividade copy, variam dependendo dos tipos de fontes e pias. Se estiver a mover dados de um sistema de ficheiros no local, define o tipo de origem na atividade de cópia para **FileSystemSource**. Da mesma forma, se estiver a transferir dados para um sistema de ficheiros no local, define o tipo de pia na atividade da cópia para **FileSystemSink**. Esta secção fornece uma lista de propriedades suportadas por FileSystemSource e FileSystemSink.

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. |Verdadeiro, Falso (padrão) |Não |

**FileSystemSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyOportundo |Define o comportamento da cópia quando a fonte é BlobSource ou FileSystem. |**PreservarHierarquia:** Preserva a hierarquia do ficheiro na pasta alvo. Ou seja, o percurso relativo do ficheiro de origem para a pasta de origem é o mesmo que o caminho relativo do ficheiro-alvo para a pasta alvo.<br/><br/>**Achatamento da Achata:** Todos os ficheiros da pasta de origem são criados no primeiro nível de pasta alvo. Os ficheiros-alvo são criados com um nome autogerado.<br/><br/>**Filtros de fusão:** Combina todos os ficheiros da pasta de origem a um ficheiro. Se o nome do ficheiro/nome blob for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>recursivo e copySA examples de comportamento
Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores para as propriedades recursivas e cópiasBehavior.

| valor recursivo | copyO valor do comportamento do comportamento | Comportamento resultante |
| --- | --- | --- |
| true |preservarHierarquia |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |achatamento |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |fusõesFilias |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + O conteúdo do Ficheiro 5 é fundido num ficheiro com um nome de ficheiro gerado automaticamente. |
| false |preservarHierarquia |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A subfolder1 com File3, File4 e File5 não é recolhida. |
| false |achatamento |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>A subfolder1 com File3, File4 e File5 não é recolhida. |
| false |fusõesFilias |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos do Ficheiro1 + Ficheiro2 são fundidos num único ficheiro com um nome de ficheiro gerado automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/><br/>A subfolder1 com File3, File4 e File5 não é recolhida. |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Consulte [os formatos de arquivo e compressão no artigo da Azure Data Factory](data-factory-supported-file-and-compression-formats.md) sobre detalhes.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Exemplos JSON para copiar dados de e para o sistema de ficheiros
Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para um sistema de ficheiros no local e armazenamento Azure Blob. No entanto, pode copiar dados *diretamente* de qualquer uma das fontes para qualquer uma das pias listadas em [fontes e pias suportadas,](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a Copy Activity na Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exemplo: Copiar dados de um sistema de ficheiros no local para o armazenamento da Azure Blob
Esta amostra mostra como copiar dados de um sistema de ficheiros no local para o armazenamento de Azure Blob. A amostra tem as seguintes entidades da Data Factory:

* Um serviço ligado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra que se segue copia os dados da série de tempo de um sistema de ficheiros no local para o armazenamento de Azure Blob a cada hora. As propriedades JSON que são utilizadas nestas amostras são descritas nas secções após as amostras.

Como primeiro passo, crie o Data Management Gateway de acordo com as instruções em [Mover dados entre fontes no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

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

Recomendamos a utilização da propriedade **criptografada DomEdial,** em vez das propriedades **userid** e **password.** Consulte [o serviço de serviço do Servidor de Ficheiros para](#linked-service-properties) obter mais informações sobre este serviço ligado.

**Serviço ligado a Azure Storage:**

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

**Conjunto de dados de entrada do sistema de ficheiros no local:**

Os dados são recolhidos de um novo ficheiro a cada hora. As propriedades de pastaaaatas e ficheirosastomas são determinadas com base na hora de início da fatia.

A Definição `"external": "true"` informa a Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza as partes do ano, mês, dia e hora da hora de início.

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

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saída, e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **FileSystemSource,** e o tipo **de pia** é definido para **BlobSink**.

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
A seguinte amostra mostra:

* Um serviço ligado do tipo [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Um serviço ligado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um conjunto de dados de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Um conjunto de dados de saída do tipo [FileShare](#dataset-properties).
* Um pipeline com uma atividade de cópia que utiliza [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [FileSystemSink.](#copy-activity-properties)

A amostra copia dados de séries temporinhas de uma tabela Azure SQL para um sistema de ficheiros no local a cada hora. As propriedades JSON que são utilizadas nestas amostras são descritas em secções após as amostras.

**Serviço de base de dados Azure SQL ligado:**

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

Recomendamos a utilização da propriedade **criptografada** Em vez de utilizar as propriedades **userid** e **password.** Consulte [o serviço de ficheiros ligado ao Sistema](#linked-service-properties) de Ficheiros para obter mais informações sobre este serviço ligado.

**Conjunto de dados de entrada Azure SQL:**

A amostra assume que criou uma tabela "MyTable" em Azure SQL, e contém uma coluna chamada "timetampcolumn" para dados da série temporal.

A Definição ``“external”: ”true”`` informa a Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são copiados para um novo ficheiro a cada hora. A pastaApata e o nome de ficheiros O nome da bolha é determinado com base na hora de início da fatia.

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

**Uma atividade de cópia num oleoduto com fonte SQL e lavatório do Sistema de Ficheiros:**

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saída, e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **SqlSource,** e o tipo **de pia** está definido para **FileSystemSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na hora passada para copiar.

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

Também pode mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia na definição de atividade de cópia. Para mais informações, consulte [as colunas mapping dataset na Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
 Para conhecer os factores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo, consulte o [desempenho da Atividade de Cópia e o guia de afinação](data-factory-copy-activity-performance.md).
