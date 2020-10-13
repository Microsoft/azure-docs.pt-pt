---
title: Formato do Common Data Model
description: Transformar dados utilizando o sistema de metadados do Modelo de Dados Comuns
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: daperlov
ms.openlocfilehash: 483e26cf4044b909c8d7923cfd74bd6fcf871e2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905310"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Formato comum do modelo de dados na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O sistema de metadados Common Data Model (CDM) permite que os dados e o seu significado sejam facilmente partilhados entre aplicações e processos de negócio. Para saber mais, consulte a visão geral [do Modelo de Dados Comum.](https://docs.microsoft.com/common-data-model/)

Na Azure Data Factory, os utilizadores podem transformar dados de entidades de CDM em ambos os formulários model.jse manifesto armazenados na [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) usando fluxos de dados de mapeamento. Também pode afundar dados em formato CDM utilizando referências de entidades CDM que irão aterrar os seus dados em formato CSV ou Parquet em pastas divididas. 

> [!NOTE]
> O conector de formato Common Data Model (CDM) para fluxos de dados ADF está atualmente disponível como pré-visualização pública.

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

O Modelo Comum de Dados está disponível como um [conjunto de dados inline no](data-flow-source.md#inline-datasets) mapeamento de fluxos de dados como uma fonte e um lavatório.

> [!NOTE]
> Ao escrever entidades de CDM, deve ter uma definição de entidade cdm existente (esquema de metadados) já definida como referência. O sumidouro de fluxo de dados ADF irá ler o ficheiro da entidade CDM e importar o esquema para a pia para mapeamento de campo.

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte de MDL. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Formato deve ser `cdm` | sim | `cdm` | formato |
| Formato de metadados | Onde a entidade se refere aos dados está localizada. Se utilizar a versão 1.0 do CDM, escolha manifesto. Se utilizar uma versão CDM antes do 1.0, escolha model.jsligado. | Sim | `'manifest'` ou `'model'` | manifestoType |
| Localização da raiz: recipiente | Nome do recipiente da pasta CDM | sim | Cadeia | sistema de ficheiros |
| Localização da raiz: caminho da pasta | Localização da pasta raiz da pasta CDM | sim | Cadeia | folderPath |
| Arquivo manifesto: Caminho da entidade | Caminho da pasta da entidade dentro da pasta raiz | não | Cadeia | entidadePata |
| Arquivo manifesto: Nome manifesto | Nome do ficheiro manifesto. O valor predefinido é 'predefinido'  | Não | String | manifestoName |
| Filtrar por última modificação | Opte por filtrar ficheiros com base na última alteração que foram alterados | não | Timestamp | modificado Depois <br> modificadoSForo antes | 
| Serviço ligado a Schema | O serviço ligado onde o corpus está localizado | Sim, se usar manifesto | `'adlsgen2'` ou `'github'` | corpusStore | 
| Recipiente de referência de entidade | Container corpus está em | Sim, se usar manifesto e corpus na ADLS Gen2 | Cadeia | adlsgen2_fileSystem |
| Repositório de referência de entidade | Nome do repositório do GitHub | Sim, se usar manifesto e corpus no GitHub | Cadeia | github_repository |
| Ramo de referência de entidade | Ramo de repositório GitHub | Sim, se usar manifesto e corpus no GitHub | Cadeia |  github_branch |
| Pasta Corpus | a localização raiz do corpus | Sim, se usar manifesto | Cadeia | corpusPath |
| Entidade corpus | Caminho para referência de entidade | sim | Cadeia | entidade |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="sink-settings"></a>Configurações do lavatório

* Aponte para o ficheiro de referência da entidade CDM que contém a definição da entidade que gostaria de escrever.

![configurações de entidades](media/data-flow/common-data-model-111.png "Referência de entidade")

* Defina o caminho de partição e o formato dos ficheiros de saída que pretende que a ADF utilize para escrever as suas entidades.

![formato de entidade](media/data-flow/common-data-model-222.png "Formato de entidade")

* Desa estação de ficheiro de saída e a localização e nome do ficheiro manifesto.

![localização cdm](media/data-flow/common-data-model-333.png "Localização do CDM")


#### <a name="import-schema"></a>Esquema de importação

O CDM só está disponível como conjunto de dados inline e, por padrão, não tem um esquema associado. Para obter metadados de coluna, clique no botão **de esquema de importação** no **separador Projeção.** Isto permitir-lhe-á fazer referência aos nomes das colunas e tipos de dados especificados pelo corpus. Para importar o esquema, uma [sessão de depuramento de fluxo de dados](concepts-data-flow-debug-mode.md) deve estar ativa e você deve ter um ficheiro de definição de entidade CDM existente para apontar.

Ao mapear colunas de fluxo de dados para propriedades de entidades na transformação do lavatório, clique no separador "Mapeamento" e selecione "Import Schema". A ADF irá ler a referência da entidade que apontou nas suas opções de Pia, permitindo-lhe mapear para o esquema de CDM alvo.

![Definições de pia cdm](media/data-flow/common-data-model-444.png "Mapeamento de CDM")

> [!NOTE]
>  Ao utilizar model.jsno tipo de origem que se origina a partir de fluxos de dados power BI ou Power Platform, poderá encontrar erros "corpus path is null or empty" da transformação da fonte. Isto deve-se, provavelmente, a problemas de formatação do caminho de localização da partição no model.jsarquivado. Para corrigir isto, siga estes passos: 

1. Abra a model.jsem arquivo num editor de texto
2. Encontre as divisórias. Propriedade de localização 
3. Mude o "blob.core.windows.net" para "dfs.core.windows.net"
4. Fixar qualquer codificação "%2F" no URL para "/"
 

### <a name="cdm-source-data-flow-script-example"></a>Exemplo de script de fluxo de dados de fonte de CDM

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Propriedades de pia

A tabela abaixo lista as propriedades suportadas por um lavatório CDM. Pode editar estas propriedades no **separador Definições.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Formato deve ser `cdm` | sim | `cdm` | formato |
| Localização da raiz: recipiente | Nome do recipiente da pasta CDM | sim | Cadeia | sistema de ficheiros |
| Localização da raiz: caminho da pasta | Localização da pasta raiz da pasta CDM | sim | Cadeia | folderPath |
| Arquivo manifesto: Caminho da entidade | Caminho da pasta da entidade dentro da pasta raiz | não | Cadeia | entidadePata |
| Arquivo manifesto: Nome manifesto | Nome do ficheiro manifesto. O valor predefinido é 'predefinido' | Não | String | manifestoName |
| Serviço ligado a Schema | O serviço ligado onde o corpus está localizado | sim | `'adlsgen2'` ou `'github'` | corpusStore | 
| Recipiente de referência de entidade | Container corpus está em | Sim, se corpus na ADLS Gen2 | Cadeia | adlsgen2_fileSystem |
| Repositório de referência de entidade | Nome do repositório do GitHub | Sim, se corpus em GitHub | Cadeia | github_repository |
| Ramo de referência de entidade | Ramo de repositório GitHub | Sim, se corpus em GitHub | Cadeia |  github_branch |
| Pasta Corpus | a localização raiz do corpus | sim | Cadeia | corpusPath |
| Entidade corpus | Caminho para referência de entidade | sim | Cadeia | entidade |
| Caminho da partição | Local onde a partição será escrita | não | Cadeia | partitionPath |
| Limpe a pasta | Se a pasta de destino for apurada antes de escrever | não | `true` ou `false` | truncato |
| Tipo de formato | Escolha especificar o formato parquet | não | `parquet` se especificado | subformat |
| Delimitador de colunas | Se escrever ao DelimitedText, como delimitar colunas | Sim, se escrever ao DelimitedText | Cadeia | columnDelimiter |
| Primeira linha como cabeçalho | Se utilizar o DelimitedText, se os nomes das colunas são adicionados como cabeçalho | não | `true` ou `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>Exemplo de script de fluxo de dados de cdM

O script de fluxo de dados associado é:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Passos seguintes

Crie uma [transformação de fonte](data-flow-source.md) no fluxo de dados de mapeamento.
