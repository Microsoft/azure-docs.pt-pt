---
title: Formato Delta na Azure Data Factory
description: Transforme e mova dados de um lago delta usando o formato delta
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: daperlov
ms.openlocfilehash: 3e1c5f3b360960779dd58c8c05b25885df81d2e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276519"
---
# <a name="delta-format-in-azure-data-factory"></a>Formato Delta na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo destaca como copiar dados de e para um lago delta armazenado em [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) ou [Azure Blob Storage](connector-azure-blob-storage.md) usando o formato delta. Este conector está disponível como um [conjunto de dados inline](data-flow-source.md#inline-datasets) no mapeamento de fluxos de dados como uma fonte e um lavatório.

> [!NOTE]
> O conector de formato delta para mapeamento de fluxos de dados está atualmente disponível como pré-visualização pública.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Este conector está disponível como um [conjunto de dados inline](data-flow-source.md#inline-datasets) no mapeamento de fluxos de dados como uma fonte e um lavatório.

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte delta. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Formato deve ser `delta` | sim | `delta` | formato |
| Sistema de ficheiros | O sistema de contentores/arquivos do lago delta | sim | Cadeia | sistema de ficheiros |
| Folder path | O direto do lago delta | sim | Cadeia | folderPath |
| Tipo de compressão | O tipo de compressão da tabela delta | não | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressãoType |
| Nível de compressão | Escolha se a compressão se completa o mais rapidamente possível ou se o ficheiro resultante deve ser comprimido da melhor forma. | necessário se `compressedType` for especificado. | `Optimal` ou `Fastest` | compressãoLevel |
| Viagem no tempo | Escolha se deve consultar um instantâneo mais antigo de uma tabela delta | não | Consulta por timetamp: Timetamp <br> Consulta por versão: Inteiro | timetampAsOf <br> versõesAsOf |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Esquema de importação

A Delta só está disponível como conjunto de dados inline e, por padrão, não tem um esquema associado. Para obter metadados de coluna, clique no botão **de esquema de importação** no **separador Projeção.** Isto permitir-lhe-á fazer referência aos nomes das colunas e tipos de dados especificados pelo corpus. Para importar o esquema, uma [sessão de depuramento de fluxo de dados](concepts-data-flow-debug-mode.md) deve estar ativa e você deve ter um ficheiro de definição de entidade CDM existente para apontar.
 

### <a name="delta-source-script-example"></a>Exemplo de script de fonte delta

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Propriedades de pia

A tabela abaixo lista as propriedades suportadas por um lavatório delta. Pode editar estas propriedades no **separador Definições.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Formato deve ser `delta` | sim | `delta` | formato |
| Sistema de ficheiros | O sistema de contentores/arquivos do lago delta | sim | Cadeia | sistema de ficheiros |
| Folder path | O direto do lago delta | sim | Cadeia | folderPath |
| Tipo de compressão | O tipo de compressão da tabela delta | não | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressãoType |
| Nível de compressão | Escolha se a compressão se completa o mais rapidamente possível ou se o ficheiro resultante deve ser comprimido da melhor forma. | necessário se `compressedType` for especificado. | `Optimal` ou `Fastest` | compressãoLevel |
| Limpeza | Especifique o limiar de retenção em horas para versões mais antigas da tabela. Um valor de 0 ou menos incumprimentos para 30 dias | sim | Número inteiro | vácuo |
| Método de atualização | Especifique quais as operações de atualização permitidas no lago delta. Para métodos que não são inseridos, uma transformação de linha alterante anterior é necessária para marcar linhas. | sim | `true` ou `false` | deletable <br> inserível <br> atualizável <br> upsertable |

### <a name="delta-sink-script-example"></a>Exemplo de script de pia delta

O script de fluxo de dados associado é:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Limitações conhecidas

Ao escrever para uma pia delta, há uma limitação conhecida onde o número de linhas escritas não será retorno na saída de monitorização.

## <a name="next-steps"></a>Passos seguintes

* Crie uma [transformação de fonte](data-flow-source.md) no fluxo de dados de mapeamento.
* Crie uma [transformação de pia](data-flow-sink.md) no fluxo de dados de mapeamento.
* Crie uma [transformação de linha de alteração](data-flow-alter-row.md) para marcar linhas como inserir, atualizar, intensificar ou apagar.
