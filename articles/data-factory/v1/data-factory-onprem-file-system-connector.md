---
title: Copiar dados de/para um sistema de arquivos usando Azure Data Factory
description: Saiba como copiar dados de e para um sistema de arquivos local usando Azure Data Factory.
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
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895666"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Copiar dados de e para um sistema de arquivos local usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-file-system-connector.md)
> * [Versão 2 (versão atual)](../connector-file-system.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do sistema de arquivos na v2](../connector-file-system.md).


Este artigo explica como usar a atividade de cópia no Azure Data Factory para copiar dados de/para um sistema de arquivos local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Você pode copiar dados **de um sistema de arquivos local** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes repositórios de dados **para um sistema de arquivos local**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> A atividade de cópia não exclui o arquivo de origem depois que ele é copiado com êxito para o destino. Se você precisar excluir o arquivo de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir o arquivo e use a atividade no pipeline.

## <a name="enabling-connectivity"></a>Habilitando a conectividade
O Data Factory dá suporte à conexão de e para um sistema de arquivos local por meio do **Gateway de gerenciamento de dados**. Você deve instalar o gateway de Gerenciamento de Dados no seu ambiente local para que o serviço de Data Factory se conecte a qualquer armazenamento de dados local com suporte, incluindo o sistema de arquivos. Para saber mais sobre Gerenciamento de Dados gateway e instruções passo a passo sobre como configurar o gateway, consulte [mover dados entre fontes locais e a nuvem com gerenciamento de dados gateway](data-factory-move-data-between-onprem-and-cloud.md). Além do gateway de Gerenciamento de Dados, nenhum outro arquivo binário precisa ser instalado para se comunicar de e para um sistema de arquivos local. Você deve instalar e usar o gateway Gerenciamento de Dados mesmo se o sistema de arquivos estiver na VM IaaS do Azure. Para obter informações detalhadas sobre o gateway, consulte [Gerenciamento de dados gateway](data-factory-data-management-gateway.md).

Para usar um compartilhamento de arquivos do Linux, instale o [samba](https://www.samba.org/) em seu servidor Linux e instale gerenciamento de dados gateway em um Windows Server. Não há suporte para a instalação do gateway de Gerenciamento de Dados em um servidor Linux.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de/para um sistema de arquivos usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines.
2. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory. Por exemplo, se você estiver copiando dados de um armazenamento de BLOBs do Azure para um sistema de arquivos local, crie dois serviços vinculados para vincular seu sistema de arquivos local e a conta de armazenamento do Azure ao seu data factory. Para propriedades do serviço vinculado que são específicas para um sistema de arquivos local, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .
3. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de BLOBs e a pasta que contém o dado de entrada. E, você cria outro conjunto de um para especificar o nome da pasta e do arquivo (opcional) no sistema de arquivos. Para propriedades do conjunto de informações que são específicas do sistema de arquivos local, consulte a seção [Propriedades do conjunto](#dataset-properties) de informações.
4. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. No exemplo mencionado anteriormente, você usa o Blobname como uma origem e FileSystemSink como um coletor para a atividade de cópia. Da mesma forma, se você estiver copiando do sistema de arquivos local para o armazenamento de BLOBs do Azure, use o BlobSink e o Microsoft na atividade de cópia. Para propriedades da atividade de cópia que são específicas para o sistema de arquivos local, consulte a seção [Propriedades da atividade de cópia](#copy-activity-properties) . Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para seu armazenamento de dados.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter exemplos com definições de JSON para Data Factory entidades usadas para copiar dados de/para um sistema de arquivos, consulte a seção [exemplos de JSON](#json-examples-for-copying-data-to-and-from-file-system) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas ao sistema de arquivos:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Você pode vincular um sistema de arquivos local a um data factory do Azure com o serviço vinculado do **servidor de arquivos local** . A tabela a seguir fornece descrições para elementos JSON que são específicos para o serviço vinculado do servidor de arquivos local.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |Verifique se a propriedade Type está definida como **OnPremisesFileServer**. |Sim |
| anfitrião |Especifica o caminho de raiz da pasta que pretende copiar. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Ver [exemplo ligado as definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos. |Sim |
| ID de utilizador |Especifica o ID de utilizador que tem acesso ao servidor. |Não (se você escolher encryptedCredential) |
| palavra-passe |Especifique a palavra-passe para o utilizador (ID de utilizador). |Não (se você escolher encryptedCredential |
| encryptedCredential |Especifique as credenciais criptografadas que você pode obter executando o cmdlet New-AzDataFactoryEncryptValue. |Não (se você optar por especificar userid e password em texto sem formatação) |
| gatewayName |Especifica o nome do gateway que Data Factory deve usar para se conectar ao servidor de arquivos local. |Sim |


### <a name="sample-linked-service-and-dataset-definitions"></a>Serviço ligado e as definições do conjunto de dados de exemplo
| Cenário | Host na definição de serviço vinculado | folderPath na definição de DataSet |
| --- | --- | --- |
| Pasta local no computador do gateway de Gerenciamento de Dados: <br/><br/>Exemplos: D:\\\* ou D:\folder\subfolder\\\\* |D:\\\\ (para Gerenciamento de Dados gateway 2,0 e versões posteriores) <br/><br/> localhost (para versões anteriores do que Gerenciamento de Dados gateway 2,0) |.\\\\ ou pasta\\\\subpasta (para Gerenciamento de Dados gateway 2,0 e versões posteriores) <br/><br/>D:\\\\ ou D:\\pasta \\\\subpasta \\(para a versão do gateway abaixo de 2,0) |
| Pasta remota partilhada: <br/><br/>Exemplos: \\\\meuservidor\\compartilhamento\\\* ou \\\\MyServer\\compartilhamento\\pasta\\\\subpasta \\* |\\\\\\\\meuservidor\\compartilhamento de \\ |.\\\\ ou pasta\\subpasta \\ |

>[!NOTE]
>Durante a criação de através da interface do Usuário, não precisa de barra invertida duplo de entrada (`\\`) para o escape tal como faz via JSON, especifique a barra invertida única.

### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: usando nome de usuário e senha em texto sem formatação

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

### <a name="example-using-encryptedcredential"></a>Exemplo: usando encryptedcredential

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [criando conjuntos](data-factory-create-datasets.md)de os. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A seção typeproperties é diferente para cada tipo de conjunto de texto. Ele fornece informações como o local e o formato dos dados no armazenamento de dados. A seção typeproperties do conjunto de um do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Especifica o subcaminho para a pasta. Use o caractere de escape '\' para caracteres especiais na cadeia de caracteres. Não é suportado o filtro de carateres universais. Ver [exemplo ligado as definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pasta com base em data/hora de início/término da fatia. |Sim |
| fileName |Especifique o nome do arquivo no **FolderPath** se você quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar nenhum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando **filename** não for especificado para um conjunto de resultados de saída e **preserveHierarchy** não for especificado no coletor de atividade, o nome do arquivo gerado estará no seguinte formato: <br/><br/>`Data.<Guid>.txt` (exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos. <br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (caractere único).<br/><br/>Exemplo 1: "FileFilter": "*. log"<br/>Exemplo 2: "FileFilter": 2014-1-?. localizado<br/><br/>Observe que FileFilter é aplicável a um conjunto de dados de FileShare de entrada. |Não |
| partitionedBy |Você pode usar partitionedBy para especificar um folderPath/fileName dinâmico para dados de série temporal. Um exemplo é folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| compression | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Você não pode usar fileName e FileFilter simultaneamente.

### <a name="using-partitionedby-property"></a>Usando a propriedade partitionedBy
Conforme mencionado na seção anterior, você pode especificar um folderPath e um nome de arquivo dinâmico para dados de série temporal com a propriedade **partitionedBy** , [funções de data Factory e as variáveis de sistema](data-factory-functions-variables.md).

Para entender mais detalhes sobre conjuntos de data de série temporal, agendamento e fatias, confira [criando conjuntos](data-factory-create-datasets.md)de, [agendando e execução](data-factory-scheduling-and-execution.md)e [criando pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exemplo 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {Slice} é substituído pelo valor da variável de sistema Data Factory SliceStart no formato (AAAAMMDDHH). SliceStart refere-se à hora de início da fatia. FolderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

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

Neste exemplo, ano, mês, dia e hora de SliceStart são extraídos em variáveis separadas que as propriedades folderPath e fileName usam.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, conjuntos de dados de entrada e saída e políticas estão disponíveis para todos os tipos de atividades. Enquanto que as propriedades disponíveis na seção **typeproperties** da atividade variam de acordo com cada tipo de atividade.

Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores. Se você estiver movendo dados de um sistema de arquivos local, defina o tipo de origem na atividade de cópia como **FileSystemName**. Da mesma forma, se você estiver movendo dados para um sistema de arquivos local, defina o tipo de coletor na atividade de cópia como **FileSystemSink**. Esta seção fornece uma lista das propriedades com suporte por FileSystemName e FileSystemSink.

O **FileSystemName** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |True, false (padrão) |Não |

O **FileSystemSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BLOB ou FileSystem. |**PreserveHierarchy:** Preserva a hierarquia de arquivos na pasta de destino. Ou seja, o caminho relativo do arquivo de origem para a pasta de origem é o mesmo que o caminho relativo do arquivo de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** Todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles:** Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/nome do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, será um nome de arquivo gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores para as propriedades recursiva e copyBehavior.

| valor recursivo | valor de copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura que a origem:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de file1 + arquivo2 + arquivo3 + Arquivo4 + arquivo 5 é mesclado em um arquivo com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de file1 + arquivo2 é mesclado em um arquivo com um nome de arquivo gerado automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para file1<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e compactação com suporte
Consulte [formatos de arquivo e compactação no artigo Azure data Factory](data-factory-supported-file-and-compression-formats.md) sobre detalhes.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Exemplos de JSON para copiar dados de e para o sistema de arquivos
Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para um sistema de arquivos local e o armazenamento de BLOBs do Azure. No entanto, você pode copiar dados *diretamente* de qualquer uma das fontes para qualquer um dos coletores listados em [fontes com suporte e coletores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exemplo: copiar dados de um sistema de arquivos local para o armazenamento de BLOBs do Azure
Este exemplo mostra como copiar dados de um sistema de arquivos local para o armazenamento de BLOBs do Azure. O exemplo tem as seguintes entidades de Data Factory:

* Um serviço vinculado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [FileShare](#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa o [FileSystemName](#copy-activity-properties) e o [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo a seguir copia dados de série temporal de um sistema de arquivos local para o armazenamento de BLOBs do Azure a cada hora. As propriedades JSON que são usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure Gerenciamento de Dados gateway de acordo com as instruções em [mover dados entre fontes locais e a nuvem com gerenciamento de dados gateway](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado do servidor de arquivos local:**

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

É recomendável usar a propriedade **encryptedCredential** em vez disso, as propriedades **userid** e **password** . Consulte [serviço vinculado do servidor de arquivos](#linked-service-properties) para obter detalhes sobre esse serviço vinculado.

**Serviço vinculado do armazenamento do Azure:**

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

**Conjunto de dados de entrada do sistema de arquivos local:**

Os dados são coletados de um novo arquivo a cada hora. As propriedades folderPath e fileName são determinadas com base na hora de início da fatia.

Definir `"external": "true"` informa Data Factory que o conjunto de os é externo à data factory e não é produzido por uma atividade no data factory.

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

**Conjunto de resultados do armazenamento de BLOBs do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes de ano, mês, dia e hora da hora de início.

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

**Uma atividade de cópia em um pipeline com origem do sistema de arquivos e coletor de blob:**

O pipeline contém uma atividade de cópia que é configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **FileSystemName**e o tipo de **coletor** é definido como **BlobSink**.

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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exemplo: copiar dados do banco de dados SQL do Azure para um sistema de arquivos local
O exemplo a seguir mostra:

* Um serviço vinculado do tipo [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Um serviço vinculado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um conjunto de dados de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Um conjunto de uma saída do tipo [FileShare](#dataset-properties).
* Um pipeline com uma atividade de cópia que usa [sqlsource](data-factory-azure-sql-connector.md#copy-activity-properties) e [FileSystemSink](#copy-activity-properties).

O exemplo copia dados de série temporal de uma tabela SQL do Azure para um sistema de arquivos local a cada hora. As propriedades JSON que são usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do banco de dados SQL do Azure:**

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

**Serviço vinculado do servidor de arquivos local:**

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

É recomendável usar a propriedade **encryptedCredential** em vez de usar as propriedades **userid** e **password** . Consulte [serviço vinculado do sistema de arquivos](#linked-service-properties) para obter detalhes sobre esse serviço vinculado.

**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL do Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Definir ``“external”: ”true”`` informa Data Factory que o conjunto de os é externo à data factory e não é produzido por uma atividade no data factory.

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

**Conjunto de entrada do sistema de arquivos local:**

Os dados são copiados para um novo arquivo a cada hora. FolderPath e fileName para o blob são determinados com base na hora de início da fatia.

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

**Uma atividade de cópia em um pipeline com fonte SQL e coletor de sistema de arquivos:**

O pipeline contém uma atividade de cópia que é configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **sqlsource**e o tipo de **coletor** é definido como **FileSystemSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

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

Você também pode mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta da atividade de cópia. Para obter detalhes, consulte [mapeando colunas do conjunto de informações em Azure data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
 Para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure Data Factory e várias maneiras de otimizá-lo, consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).
