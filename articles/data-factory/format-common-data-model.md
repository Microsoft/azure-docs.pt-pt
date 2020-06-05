---
title: Formato comum do modelo de dados
description: Transformar dados utilizando o sistema de metadados do Modelo de Dados Comuns
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 2faa14ef7724bb9cccfe425569539f5ef0621a28
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435395"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Formato comum do modelo de dados na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O sistema de metadados Common Data Model (CDM) permite que os dados e o seu significado sejam facilmente partilhados entre aplicações e processos de negócio. Para saber mais, consulte a visão geral [do Modelo de Dados Comum.](https://docs.microsoft.com/common-data-model/)

Na Azure Data Factory, os utilizadores podem transformar-se de e para entidades de CDM armazenadas na [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) utilizando fluxos de dados de mapeamento.

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

O Modelo Comum de Dados está disponível como um [conjunto de dados inline no](data-flow-source.md#inline-datasets) mapeamento de fluxos de dados como uma fonte e um lavatório.

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte de MDL.

| Name | Description | Necessário | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | O é sempre`cdm` | sim | `cdm` | formato |
| Formato de metadados | Onde a entidade se refere aos dados está localizada. Se utilizar a versão 1.0 do CDM, escolha manifesto. Se utilizar uma versão CDM antes do 1.0, escolha model.json. | Yes | `'manifest'` ou `'model'` | manifestoType |
| Localização da raiz: recipiente | Nome do recipiente da pasta CDM | sim | String | sistema de ficheiros |
| Localização da raiz: caminho da pasta | Localização da pasta raiz da pasta CDM | sim | String | folderPath |
| Arquivo manifesto: Caminho da entidade | Caminho da pasta da entidade dentro da pasta raiz | não | String | entidadePata |
| Arquivo manifesto: Nome manifesto | Nome do manifesto  | Sim, se usar manifesto | String | manifestoName |
| Filtrar por última modificação | Opte por filtrar ficheiros com base na última alteração que foram alterados | não | Carimbo de data/hora | modificado Depois <br> modificadoSForo antes | 
| Serviço ligado a Schema | O serviço ligado onde o corpus está localizado | Sim, se usar manifesto | `'adlsgen2'` ou `'github'` | corpusStore | 
| Recipiente de referência de entidade | Container corpus está em | Sim, se usar manifesto e corpus na ADLS Gen2 | String | adlsgen2_fileSystem |
| Repositório de referência de entidade | Nome do repositório do GitHub | Sim, se usar manifesto e corpus no GitHub | String | github_repository |
| Ramo de referência de entidade | Ramo de repositório GitHub | Sim, se usar manifesto e corpus no GitHub | String |  github_branch |
| Pasta Corpus | a localização raiz do corpus | Sim, se usar manifesto | String | corpusPath |
| Entidade corpus | Caminho para referência de entidade | sim | String | entidade |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

![Fonte de CDM](media/format-common-data-model/data-flow-source.png)

### <a name="sink-properties"></a>Propriedades de pia

A tabela abaixo lista as propriedades suportadas por um lavatório CDM.

| Name | Description | Necessário | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | O é sempre`cdm` | sim | `cdm` | formato |
| Localização da raiz: recipiente | Nome do recipiente da pasta CDM | sim | String | sistema de ficheiros |
| Localização da raiz: caminho da pasta | Localização da pasta raiz da pasta CDM | sim | String | folderPath |
| Arquivo manifesto: Caminho da entidade | Caminho da pasta da entidade dentro da pasta raiz | não | String | entidadePata |
| Arquivo manifesto: Nome manifesto | Nome do manifesto  | sim | String | manifestoName |
| Serviço ligado a Schema | O serviço ligado onde o corpus está localizado | sim | `'adlsgen2'` ou `'github'` | corpusStore | 
| Recipiente de referência de entidade | Container corpus está em | Sim, se corpus na ADLS Gen2 | String | adlsgen2_fileSystem |
| Repositório de referência de entidade | Nome do repositório do GitHub | Sim, se corpus em GitHub | String | github_repository |
| Ramo de referência de entidade | Ramo de repositório GitHub | Sim, se corpus em GitHub | String |  github_branch |
| Pasta Corpus | a localização raiz do corpus | sim | String | corpusPath |
| Entidade corpus | Caminho para referência de entidade | sim | String | entidade |
| Caminho da partição | Local onde a partição será escrita | não | String | partitionPath |
| Limpe a pasta | Se a pasta de destino for apurada antes de escrever | não | `true` ou `false` | truncato |
| Tipo de formato | Escolha especificar o formato parquet | não | `parquet`se especificado | subformat |
| Delimitador de colunas | Se escrever ao DelimitedText, como delimitar colunas | Sim, se escrever ao DelimitedText | String | columnDelimiter |
| Primeira linha como cabeçalho | Se utilizar o DelimitedText, se os nomes das colunas são adicionados como cabeçalho | não | `true` ou `false` | columnNamesAsHeader |

![Fonte de CDM](media/format-common-data-model/data-flow-sink.png)

## <a name="next-steps"></a>Passos seguintes

Crie uma [transformação de fonte](data-flow-source.md) no fluxo de dados de mapeamento.