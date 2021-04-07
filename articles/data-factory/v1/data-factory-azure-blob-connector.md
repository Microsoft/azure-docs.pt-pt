---
title: Copiar dados de/para o armazenamento de Azure Blob
description: 'Saiba como copiar dados blob na Azure Data Factory. Utilize a nossa amostra: Como copiar dados de e para a Azure Blob Storage e Azure SQL Database.'
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f1343f900e12bff09c0436ca52d8b091fe48a181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393552"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Copiar dados de ou para a Azure Blob Storage usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-blob-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-blob-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector de armazenamento Azure Blob em V2](../connector-azure-blob-storage.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para o Armazenamento Azure Blob. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

## <a name="overview"></a>Descrição Geral
Pode copiar dados de qualquer loja de dados de origem suportada para o Azure Blob Storage ou do Azure Blob Storage para qualquer loja de dados de lavatórios suportados. A tabela seguinte fornece uma lista de lojas de dados suportadas como fontes ou sumidouros pela atividade de cópia. Por exemplo, pode mover dados de uma base de dados **do** SQL Server ou de uma base de dados na Base de Dados Azure SQL **para** um armazenamento de bolhas Azure. E, você pode copiar dados do armazenamento **de** blob Azure **para** Azure Synapse Analytics ou uma coleção de DB Azure Cosmos.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do Azure Blob Storage** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para o Azure Blob Storage:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> A Copy Activity suporta a cópia de/para contas gerais de armazenamento Azure e armazenamento de Blob Quente/Cool. A atividade suporta **a leitura de blocos, apêndices ou bolhas de página,** mas suporta **escrever apenas para bloquear bolhas**. O Azure Premium Storage não é suportado como uma pia porque é apoiado por bolhas de página.
>
> A Copy Activity não apaga os dados da fonte após a cópia com sucesso dos dados para o destino. Se precisar de eliminar dados de origem após uma cópia bem sucedida, crie uma [atividade personalizada](data-factory-use-custom-activities.md) para eliminar os dados e utilizar a atividade no pipeline. Por exemplo, consulte a [amostra de mancha ou pasta Delete no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para um Azure Blob Storage utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Este artigo tem uma [passagem](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) para criar um pipeline para copiar dados de um local de armazenamento de blob Azure para outro local de armazenamento de Azure Blob. Para obter um tutorial sobre a criação de um pipeline para copiar dados de um Azure Blob Storage para Azure SQL Database, consulte [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para Azure SQL Database, cria dois serviços ligados para ligar a sua conta de armazenamento Azure e a Base de Dados Azure SQL à sua fábrica de dados. Para propriedades de serviço ligadas específicas ao Azure Blob Storage, consulte a secção [de propriedades de serviços ligadas.](#linked-service-properties)
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente blob e a pasta que contém os dados de entrada. E cria outro conjunto de dados para especificar a tabela SQL na Base de Dados Azure SQL que detém os dados copiados do armazenamento do blob. Para obter propriedades de conjunto de dados específicas do Azure Blob Storage, consulte a secção [de propriedades do conjunto de dados.](#dataset-properties)
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa BlobSource como uma fonte e SqlSink como um lavatório para a atividade da cópia. Da mesma forma, se estiver a copiar da Base de Dados Azure SQL para o Azure Blob Storage, utilize o SqlSource e o BlobSink na atividade da cópia. Para obter propriedades de atividade de cópia específicas do Azure Blob Storage, consulte a secção [de propriedades da atividade da cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON.  Para amostras com definições JSON para entidades da Data Factory que são usadas para copiar dados de/para um Armazenamento Azure Blob, consulte a secção de [exemplos JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas do Azure Blob Storage.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
Existem dois tipos de serviços ligados que pode utilizar para ligar um Azure Storage a uma fábrica de dados Azure. São: Serviço ligado **a AzureStorage** e serviço ligado **AzureStorageSas.** O serviço Azure Storage ligado fornece à fábrica de dados acesso global ao Azure Storage. Enquanto que o serviço ligado ao Azure Storage SAS (Shared Access Signature) fornece à fábrica de dados acesso restrito/limitado ao armazenamento Azure. Não existem outras diferenças entre estes dois serviços ligados. Escolha o serviço ligado que se adequa às suas necessidades. As seguintes secções fornecem mais detalhes sobre estes dois serviços ligados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para especificar um conjunto de dados para representar dados de entrada ou saída num Azure Blob Storage, define a propriedade tipo do conjunto de dados para: **AzureBlob**. Defina a propriedade **linkedServiceName** do conjunto de dados com o nome do serviço ligado ao Azure Storage ou Azure Storage SAS.  As propriedades do tipo do conjunto de dados especificam o **recipiente blob** e a **pasta** no armazenamento do blob.

Para obter uma lista completa das secções JSON & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A fábrica de dados suporta os seguintes valores de tipo baseados em CLS .NET para fornecer informações de tipo em "estrutura" para fontes de dados de schema-on-read como a mancha Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. A Data Factory realiza automaticamente conversões de tipo ao mover dados de uma loja de dados de origem para uma loja de dados de lavatórios.

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização, formato, etc., dos dados na loja de dados. A secção typeProperties para conjunto de dados do tipo **AzureBlob** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para o recipiente e pasta no armazenamento do blob. Exemplo: myblobcontainer\myblobfolder\ |Yes |
| fileName |O nome da bolha. fileName é opcional e sensível a casos.<br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo Copy) funciona na Bolha específica.<br/><br/>Quando o data de ficheiroName não é especificado, copy inclui todas as Blobs na pastaPa para o conjunto de dados de entrada.<br/><br/>Quando **o fileName** não é especificado para um conjunto de dados de saída e **a preservaçãoHierarquia** não é especificado na pia de atividade, o nome do ficheiro gerado estaria no seguinte formato: `Data.<Guid>.txt` (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy é uma propriedade opcional. Pode usá-lo para especificar uma pasta dinâmicaPath e nome de ficheiro para dados da série de tempo. Por exemplo, a pastaPath pode ser parametrizada para cada hora de dados. Consulte a [secção de propriedade 'Utilizar divisórias'](#using-partitionedby-property) para obter mais detalhes e exemplos. |No |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |No |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate**. Os níveis suportados são: **Ideal** e **Mais rápido**. Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

### <a name="using-partitionedby-property"></a>Usando propriedade partitionedBy
Como mencionado na secção anterior, pode especificar uma pasta dinâmicaPath e nome de ficheiro para dados de séries de tempo com a propriedade **partitionedBy,** [funções de Data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Para obter mais informações sobre conjuntos de dados, agendamentos e fatias de séries de tempo, consulte [criar conjuntos de dados](data-factory-create-datasets.md) e agendar artigos [de execução &.](data-factory-scheduling-and-execution.md)

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {Slice} é substituído pelo valor da variável do sistema de data factory SliceStart no formato (YYYYMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. A pastaApata é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Exemplo 2

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
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, conjuntos de dados de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades. Enquanto que as propriedades disponíveis na secção **de tipos de atividade** variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias. Se estiver a mover dados de um Azure Blob Storage, descreva o tipo de origem na atividade da cópia para **BlobSource**. Da mesma forma, se estiver a transferir dados para um Azure Blob Storage, define o tipo de pia na atividade da cópia para **BlobSink**. Esta secção fornece uma lista de propriedades suportadas por BlobSource e BlobSink.

**BlobSource** suporta as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. |Verdadeiro (valor predefinido), Falso |No |

**BlobSink** suporta a seguinte secção **de propriedades tipoProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyOportundo |Define o comportamento da cópia quando a fonte é BlobSource ou FileSystem. |<b>PreserveHierarchy</b>: preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro alvo para a pasta alvo.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível de pasta alvo. Os ficheiros-alvo têm nome gerado automaticamente. <br/><br/><b>MergeFiles</b>: funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro/blob for especificado, o nome do ficheiro fundido será o nome especificado; caso contrário, seria nome de ficheiro gerado automaticamente. |No |

**BlobSource** também suporta estas duas propriedades para retrocompatibilidade.

* **treatEmptyAsNull**: Especifica se deve tratar a cadeia nula ou vazia como valor nulo.
* **skipHeaderLineCount** - Especifica quantas linhas precisam de ser ignoradas. Só é aplicável quando o conjunto de dados de entrada estiver a utilizar o TextFormat.

Da mesma forma, **blobSink** suporta a seguinte propriedade para retrocompatibilidade.

* **blobWriterAddHeader**: Especifica se deve adicionar um cabeçalho de definições de coluna enquanto escreve para um conjunto de dados de saída.

Os conjuntos de dados suportam agora as seguintes propriedades que implementam a mesma funcionalidade: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

A tabela seguinte fornece orientações sobre a utilização das novas propriedades do conjunto de dados no lugar destas propriedades de fonte de bolha/pia.

| Propriedade de Atividade de Cópia | Propriedade Dataset |
|:--- |:--- |
| skipHeaderLineCount no BlobSource |skipLineCount e firstRowAsHeader. As linhas são ignoradas primeiro e, em seguida, a primeira linha é lida como um cabeçalho. |
| treatEmptyAsNull em BlobSource |treatEmptyAsNull no conjunto de dados de entrada |
| blobWriterAddHeader em BlobSink |firstRowAsHeader no conjunto de dados de saída |

Consulte a secção [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) para obter informações detalhadas sobre estas propriedades.

### <a name="recursive-and-copybehavior-examples"></a>recursivo e copySA examples de comportamento
Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores recursivos e de cópias.

| recursivo | copyOportundo | Comportamento resultante |
| --- | --- | --- |
| true |preservarHierarquia |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a mesma estrutura que a fonte<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |achatamento |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |fusõesFilias |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + O conteúdo do Ficheiro 5 é fundido num ficheiro com nome de ficheiro gerado automaticamente |
| false |preservarHierarquia |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |
| false |achatamento |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |
| false |fusõesFilias |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos do Ficheiro1 + Ficheiro2 são fundidos num único ficheiro com o nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Walkthrough: Use copy Wizard para copiar dados de/para o armazenamento de blob
Vamos ver como copiar rapidamente dados de/para um armazenamento de bolhas Azure. Nesta passagem, tanto as lojas de dados de origem como de destino do tipo: Azure Blob Storage. O gasoduto desta passagem copia os dados de uma pasta para outra pasta no mesmo recipiente blob. Este walkthrough é intencionalmente simples de mostrar-lhe as definições ou propriedades ao usar o Blob Storage como fonte ou pia.

### <a name="prerequisites"></a>Pré-requisitos
1. Crie uma Conta de **Armazenamento Azure** de uso geral se ainda não tiver uma. Você usa o armazenamento de bolhas como loja de dados **de origem** e **destino** nesta passagem. se não tiver uma conta de armazenamento Azure, consulte o artigo de [conta de armazenamento Criar um](../../storage/common/storage-account-create.md) artigo de conta de armazenamento para etapas para criar uma.
2. Crie um recipiente blob chamado **adfblobconnector** na conta de armazenamento.
4. Crie uma pasta chamada **entrada** no recipiente **de adfblobconnector.**
5. Crie um ficheiro nomeado **emp.txt** com o seguinte conteúdo e faça o upload para a pasta **de entrada** utilizando ferramentas como [o Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Criar a fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique **em Criar um recurso** a partir do canto superior esquerdo, clique em Inteligência + **análise,** e clique em **Data Factory**.
3. No novo painel de **fábrica de dados:**  
    1. Introduza **o nome ADFBlobConnectorDF** **.** O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: `*Data factory name "ADFBlobConnectorDF" is not available` , altere o nome da fábrica de dados (por exemplo, o seu nomeADFBlobConnectorDF) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
    2. Selecione a sua **subscrição Azure**.
    3. Para o Grupo de Recursos, selecione **Utilização existente** para selecionar um grupo de recursos existente (ou) selecione **Criar novo** para introduzir um nome para um grupo de recursos.
    4. Selecione uma **localização** para a fábrica de dados.
    5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.
    6. Clique em **Criar**.
3. Após a criação estar concluída, vê a lâmina da **Data Factory** como mostrado na seguinte imagem: Data Factory  ![ home page](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Assistente de Cópia
1. Na página inicial da Data Factory, clique no azulejo de **dados copy** para lançar **copy data Wizard** num separado.  

    > [!NOTE]
    > Se vir que o navegador está preso em "Autorizar...", **desative/desmarque cookies de terceiros e** a definição de dados do site (ou) mantenha-o ativo e crie uma exceção para **login.microsoftonline.com** e tente lançar novamente o assistente.
2. Na página **Propriedades**:
    1. Introduza **copyPipeline** para **o nome de tarefa**. O nome da tarefa é o nome do oleoduto na sua fábrica de dados.
    2. Introduza uma **descrição** para a tarefa (opcional).
    3. Para **cadência de tarefas ou agendamento de tarefas,** mantenha a **Execução regularmente na** opção de agendamento. Se pretender executar esta tarefa apenas uma vez em vez de executar repetidamente num horário, selecione **Executar uma vez agora**. Se selecionar, **executar uma vez agora** a opção, é criado um pipeline [único.](data-factory-create-pipelines.md#onetime-pipeline)
    4. Mantenha as definições para **padrão recorrente**. Esta tarefa é executado diariamente entre os tempos de início e de fim especificados no passo seguinte.
    5. Alterar a **data de início** para **04/21/2017**.
    6. Altere a **data de fim** para **04/25/2017**. Pode querer digitar a data em vez de navegar pelo calendário.
    8. Clique em **Seguinte**.
        ![Página Ferramenta Copiar – Propriedades](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na página **Arquivo de dados de origem**, clique no mosaico **Blob Storage do Azure**. Utilize esta página para especificar o arquivo de dados de origem para a tarefa de cópia. Pode utilizar um serviço ligado do arquivo de dados existente (ou) especificar um novo arquivo de dados. Para utilizar um serviço ligado existente, selecionaria **A PARTIR DE SERVIÇOS LIGADOS EXISTENTES** e selecionaria o serviço ligado à direita.
    ![Página Ferramenta Copiar – Arquivo de dados de origem](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de blobs do Azure**:
    1. Mantenha o nome gerado automaticamente para **o nome de ligação**. O nome da ligação é o nome do serviço de tipo ligado: Azure Storage.
    2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
    3. Selecione a sua subscrição Azure ou mantenha **Selecione tudo** para **a subscrição Azure**.
    4. Selecione uma **Conta de armazenamento do Azure** na lista das contas de armazenamento do Azure disponíveis na subscrição selecionada. Também pode optar por introduzir manualmente as definições da conta de armazenamento selecionando a opção **Enter manualmente** para o **método de seleção de Conta.**
    5. Clique em **Seguinte**.  
        ![Ferramenta Copiar – Especificar a conta de armazenamento de blobs do Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na página **Escolher o ficheiro ou pasta de entrada**:
    1. **Adfblobcontainer** de duplo clique.
    2. Selecione **a entrada** e clique em **Escolher**. Nesta passagem de passagem, selecione a pasta de entrada. Em vez disso, pode selecionar o ficheiro emp.txt na pasta.
        ![Ferramenta de cópia - Escolha o ficheiro de entrada ou a pasta 1](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na página **Escolha o ficheiro de entrada ou a** página de pastas:
    1. Confirme se o **ficheiro ou a pasta** está definido para **adfblobconnector/entrada**. Se os ficheiros estiverem em sub-pastas, por exemplo, 2017/04/01, 2017/04/02, e assim por diante, insira adfblobconnector/entrada/{ano}/{mês}/{dia} para ficheiro ou pasta. Quando premir o TAB da caixa de texto, vê três listas de espera para selecionar formatos para o ano (yyyy), mês (MM) e dia (dd).
    2. Não definir **o ficheiro Copy recursivamente**. Selecione esta opção para atravessar novamente através de pastas para que os ficheiros sejam copiados para o destino.
    3. Não faça a opção **de cópia binária.** Selecione esta opção para executar uma cópia binária do ficheiro de origem para o destino. Não selecione para este walkthrough para que possa ver mais opções nas páginas seguintes.
    4. Confirme se o **tipo de compressão** está definido para **Nenhum**. Selecione um valor para esta opção se os seus ficheiros de origem forem comprimidos num dos formatos suportados.
    5. Clique em **Seguinte**.
    ![Ferramenta de cópia - Escolha o ficheiro de entrada ou a pasta 2](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na página **Definições do formato de ficheiro**, pode ver os delimitadores e o esquema que é detetado automaticamente pelo assistente ao analisar o ficheiro.
    1. Confirme as seguintes opções:  
        a. O **formato de ficheiro** está definido para o formato **Text**. Pode ver todos os formatos suportados na lista de suspensos. Por exemplo: JSON, Avro, ORC, Parquet.
       b. O **delimiter da coluna** está definido para `Comma (,)` . Pode ver os outros delimiters de coluna suportados pela Data Factory na lista de suspensos. Também pode especificar um delimiter personalizado.
       c. O **delimiter de linha** está definido para `Carriage Return + Line feed (\r\n)` . Pode ver os delimiters da outra linha suportados pela Data Factory na lista de suspensos. Também pode especificar um delimiter personalizado.
       d. A **contagem de linhas de salto** está definida para **0**. Se quiser que algumas linhas sejam ignoradas no topo do ficheiro, insira o número aqui.
       e. A **primeira linha de dados contém nomes de colunas** não está definida. Se os ficheiros de origem contiverem nomes de colunas na primeira linha, selecione esta opção.
       f. O **valor da coluna vazia como opção nulo** é definido.
    2. Expanda **as definições avançadas** para ver a opção avançada disponível.
    3. Na parte inferior da página, consulte a **pré-visualização** dos dados do ficheiro emp.txt.
    4. Clique no **separador SCHEMA** na parte inferior para ver o esquema que o assistente de cópia inferiu ao olhar para os dados no ficheiro de origem.
    5. Clique em **Seguinte** depois de rever os delimitadores e pré-visualizar os dados.
    ![Ferramenta Copiar – Definições do formato de ficheiro](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na página de **loja de dados destino**, selecione **Azure Blob Storage**, e clique em **Seguinte**. Está a utilizar o Azure Blob Storage como lojas de dados de origem e destino nesta passagem.  
    ![Copy Tool - selecione loja de dados de destino](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na especificação da página **de armazenamento Azure Blob:**  
    1. Introduza **O AzureStorageLinkedService** para o campo **de nomes de ligação.**
    2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
    3. Selecione a sua **subscrição Azure**.
    4. Selecione a sua conta de armazenamento Azure.
    5. Clique em **Seguinte**.
10. Na página Escolha o ficheiro de saída ou a página **de pasta:**  
    1. especificar **o caminho da pasta** como **adfblobconnector/output/{year}/{month}/{day}**. Insira **o TAB**.
    1. Para o **ano,** selecione **yyyy**.
    1. Para o **mês,** confirme que está programado para **MM**.
    1. Para o **dia,** confirme que está programado para **dd**.
    1. Confirme se o **tipo de compressão** está definido para **Nenhum**.
    1. Confirme que o comportamento da **cópia** está definido para **ficheiros de fusão**. Se o ficheiro de saída com o mesmo nome já existir, o novo conteúdo é adicionado ao mesmo ficheiro no final.
    1. Clique em **Seguinte**.
       ![Ferramenta de cópia - Escolha o ficheiro de saída ou a pasta](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na página de definições do **formato do Ficheiro,** reveja as definições e clique em **Seguinte**. Uma das opções adicionais aqui é adicionar um cabeçalho ao ficheiro de saída. Se selecionar essa opção, é adicionada uma linha de cabeçalho com os nomes das colunas do esquema da fonte. Pode renomear os nomes das colunas predefinindo ao visualizar o esquema para a fonte. Por exemplo, pode alterar a primeira coluna para Primeiro Nome e segunda coluna para Apelido. Em seguida, o ficheiro de saída é gerado com um cabeçalho com estes nomes como nomes de colunas.
    ![Ferramenta de Cópia - Definições de formato de ficheiro para destino](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na página de **definições de Desempenho,** confirme que **as unidades de nuvem** e **cópias paralelas** estão definidas para **Auto**, e clique em Seguinte. Para obter mais informações sobre estas definições, consulte [o desempenho da atividade do Copy e o guia de afinação](data-factory-copy-activity-performance.md#parallel-copy).
    ![Ferramenta de cópia - Definições de desempenho](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na página **Resumo,** reveja todas as definições (propriedades de tarefas, definições para origem e destino e definições de cópia) e clique em **Seguinte**.
    ![Ferramenta de Cópia - Página de resumo](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Reveja as informações na página **Resumo** e clique em **Concluir**. O assistente cria dois serviços ligados, dois conjuntos de dados (entrada e saída) e um pipeline na fábrica de dados (a partir da qual iniciou o Assistente de Cópia).
    ![Ferramenta de cópia - Página de implantação](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorize o gasoduto (tarefa de cópia)

1. Clique no link `Click here to monitor copy pipeline` na página **'Implantação'.**
2. Deverá ver a **aplicação Monitor and Manage** num separador.  ![Monitore e Gere app](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Altere a hora **de início** na parte superior para o `04/19/2017` tempo **final** para `04/27/2017` , e, em seguida, clique em **Aplicar**.
4. Deverá ver cinco janelas de atividade na lista **ACTIVITY WINDOWS.** Os **tempos do WindowStart** devem cobrir todos os dias desde o início do gasoduto até aos tempos finais do gasoduto.
5. Clique no botão **Refresh** para a lista **DE JANELAS DE ATIVIDADE** algumas vezes até ver o estado de todas as janelas de atividade definidas como Pronto.
6. Verifique se os ficheiros de saída são gerados na pasta de saída do contentor de adfblobconnector. Deve ver a seguinte estrutura de pasta na pasta de saída:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Para obter informações detalhadas sobre a monitorização e gestão das fábricas de dados, consulte [Monitor e gerencie](data-factory-monitor-manage-app.md) o artigo do pipeline data Factory.

### <a name="data-factory-entities"></a>Entidades da Data Factory
Agora, volte ao separador com a página inicial da Data Factory. Note que existem dois serviços ligados, dois conjuntos de dados e um oleoduto na sua fábrica de dados agora.

![Página inicial da Data Factory com entidades](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Clique **em Autor e implemente** para lançar Data Factory Editor.

![Editor do Data Factory](media/data-factory-azure-blob-connector/data-factory-editor.png)

Deverá consultar as seguintes entidades da Data Factory na sua fábrica de dados:

- Dois serviços ligados. Um para a fonte e o outro para o destino. Ambos os serviços ligados referem-se à mesma conta de Armazenamento Azure nesta passagem.
- Dois conjuntos de dados. Um conjunto de dados de entrada e um conjunto de dados de saída. Nesta passagem, ambos usam o mesmo recipiente blob, mas referem-se a diferentes pastas (entrada e saída).
- Um oleoduto. O gasoduto contém uma atividade de cópia que utiliza uma fonte de bolha e uma pia blob para copiar dados de um local de bolha de Azure para outro local de bolha de Azure.

As seguintes secções fornecem mais informações sobre estas entidades.

#### <a name="linked-services"></a>Serviços ligados
Devia ver dois serviços ligados. Um para a fonte e o outro para o destino. Nesta passagem, ambas as definições parecem iguais, exceto para os nomes. O **tipo** de serviço ligado está definido para **AzureStorage**. A propriedade mais importante da definição de serviço ligada é a **ligaçãoString,** que é usada pela Data Factory para ligar à sua conta de Armazenamento Azure em tempo de execução. Ignore a propriedade hubName na definição.

##### <a name="source-blob-storage-linked-service"></a>Serviço ligado ao armazenamento de blob de origem
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Serviço de armazenamento de blob de destino ligado

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Para obter mais informações sobre o serviço ligado ao Armazenamento Azure, consulte a secção [de propriedades de serviços Linked.](#linked-service-properties)

#### <a name="datasets"></a>Conjuntos de dados
Existem dois conjuntos de dados: um conjunto de dados de entrada e um conjunto de dados de saída. O tipo de conjunto de dados é definido para **AzureBlob** para ambos.

O conjunto de dados de entrada aponta para a pasta de **entrada** do recipiente de bolhas de **adfblobconnector.** A propriedade **externa** é definida como **verdadeira** para este conjunto de dados, uma vez que os dados não são produzidos pelo pipeline com a atividade de cópia que toma este conjunto de dados como uma entrada.

O conjunto de dados de saída aponta para a pasta de **saída** do mesmo recipiente blob. O conjunto de dados de saída também utiliza o ano, mês e dia da variável do sistema **SliceStart** para avaliar dinamicamente o caminho para o ficheiro de saída. Para obter uma lista de funções e variáveis do sistema suportadas pela Data Factory, consulte [as funções da Data Factory e as variáveis do sistema](data-factory-functions-variables.md). A propriedade **externa** é definida como **falsa** (valor predefinido) porque este conjunto de dados é produzido pelo pipeline.

Para obter mais informações sobre propriedades suportadas por Azure Blob, consulte a secção [de propriedades do Dataset.](#dataset-properties)

##### <a name="input-dataset"></a>Conjunto de dados de entrada

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Conjunto de dados de saída

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pipeline
O oleoduto tem apenas uma atividade. O **tipo** de atividade está definido para **Copy**. Nas propriedades tipo para a atividade, existem duas secções, uma para fonte e outra para pia. O tipo de fonte é definido para **BlobSource** uma vez que a atividade está copiando dados de um armazenamento de bolhas. O tipo de pia é definido para **BlobSink** como a atividade que copia dados para um armazenamento de bolhas. A atividade de cópia toma o InputDataset-z4y como entrada e OutputDataset-z4y como saída.

Para obter mais informações sobre propriedades suportadas por BlobSource e BlobSink, consulte a secção [de propriedades de atividade de Copy.](#copy-activity-properties)

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Exemplos JSON para copiar dados de e para o Blob Storage
Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para Azure Blob Storage e Azure SQL Database. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados usando a Atividade de Cópia na Fábrica de Dados Azure.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Exemplo JSON: Copiar dados do Blob Storage para a Base de Dados SQL
A seguinte amostra mostra:

1. Um serviço ligado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Um [oleoduto](data-factory-create-pipelines.md) com uma atividade Copy que utiliza [BlobSource](#copy-activity-properties) e [SqlSink.](data-factory-azure-sql-connector.md#copy-activity-properties)

A amostra copia os dados da série de tempo de uma bolha Azure para uma mesa Azure SQL de hora em hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado Azure SQL:**

```json
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
**Serviço ligado a Azure Storage:**

```json
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
A Azure Data Factory suporta dois tipos de serviços ligados ao Azure Storage: **AzureStorage** e **AzureStorageSas**. Para a primeira, especifique a cadeia de ligação que inclui a chave de conta e para a posterior, especifica a Assinatura de Acesso Partilhado (SAS) Uri. Consulte a secção [Serviços Ligados](#linked-service-properties) para mais detalhes.

**Conjunto de dados de entrada Azure Blob:**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza a parte do ano, mês e dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. Definição "externa": definição "verdadeira" informa a Data Factory que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
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
**Conjunto de dados de saída Azure SQL:**

A amostra copia dados para uma tabela chamada "MyTable" na Base de Dados Azure SQL. Crie a tabela na sua base de dados SQL com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Uma atividade de cópia num oleoduto com fonte Blob e pia SQL:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON Exemplo: Copiar dados do Azure SQL para Azure Blob
A seguinte amostra mostra:

1. Um serviço ligado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade copy que utiliza [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [BlobSink.](#copy-activity-properties)

A amostra copia os dados da série de tempo de uma tabela Azure SQL para uma bolha Azure por hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado Azure SQL:**

```json
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
**Serviço ligado a Azure Storage:**

```json
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
A Azure Data Factory suporta dois tipos de serviços ligados ao Azure Storage: **AzureStorage** e **AzureStorageSas**. Para a primeira, especifique a cadeia de ligação que inclui a chave de conta e para a posterior, especifica a Assinatura de Acesso Partilhado (SAS) Uri. Consulte a secção [Serviços Ligados](#linked-service-properties) para mais detalhes.

**Conjunto de dados de entrada Azure SQL:**

A amostra pressupõe que criou uma tabela "MyTable" em Azure SQL e contém uma coluna chamada "timetampcolumn" para dados da série de tempo.

Definição "externa": "verdadeiro" informa o serviço Data Factory que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```json
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

**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
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

**Uma atividade de cópia num oleoduto com fonte SQL e pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **SqlSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na hora passada para copiar.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
