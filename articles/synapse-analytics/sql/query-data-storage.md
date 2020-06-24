---
title: Visão geral - Dados de consulta no armazenamento utilizando SQL on demand (pré-visualização)
description: Esta secção contém consultas de amostra que pode usar para experimentar o recurso SQL on-demand (pré-visualização) dentro do Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d42dac0899c5ab9c4f817df40095dd08891fb8e7
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85207707"
---
# <a name="overview-query-data-in-storage"></a>Visão geral: Dados de consulta no armazenamento

Esta secção contém consultas de amostra que pode usar para experimentar o recurso SQL on-demand (pré-visualização) dentro do Azure Synapse Analytics.
Os formatos de ficheiros atualmente suportados são:  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Pré-requisitos

As ferramentas que precisa para emitir consultas:

- Cliente SQL à sua escolha:
    - Azure Synapse Studio (pré-visualização)
    - Azure Data Studio
    - SQL Server Management Studio

Além disso, os parâmetros são os seguintes:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço de ponto final de serviço a pedido da SQL    | Será usado como o nome do servidor.                                   |
| SqL região de ponto final de serviço a pedido     | Será utilizado para determinar o armazenamento utilizado nas amostras. |
| Nome de utilizador e senha para acesso ao ponto final | Será usado para aceder ao ponto final.                               |
| A base de dados que vai usar para criar vistas     | Esta base de dados será utilizada como ponto de partida para as amostras.       |

## <a name="first-time-setup"></a>Configuração pela primeira vez

O seu primeiro passo é **criar uma base de dados** onde executará as consultas. Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são usados para ler dados nestas amostras.

> [!NOTE]
> As bases de dados são utilizadas apenas para visualização de metadados, não para dados reais.  Anote o nome da base de dados que usa, vai precisar mais tarde.

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>Dados de demonstração fornecidos

Os dados de demonstração contêm os seguintes conjuntos de dados:

- NYC Taxi - Yellow Taxi Trip Records - parte do conjunto de dados público nyc
  - Formato CSV
  - Formato Parquet
- Conjunto de dados da população
  - Formato CSV
- Amostra de arquivos Parquet com colunas aninhadas
  - Formato Parquet
- Livros JSON
  - Formato JSON

| Folder path                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Pasta-mãe para dados em formato CSV                         |
| /csv/população/<br />/csv/população-unix/<br />/csv/população unix-hdr/<br />/csv/população-unix-hdr-escape<br />/csv/população-unix-hdr-citado | Pastas com ficheiros de dados da População em diferentes formatos CSV. |
| /csv/táxi/                                                   | Pasta com ficheiros de dados públicos de NYC em formato CSV              |
| /parquet/                                                    | Pasta-mãe para dados em formato Parquet                     |
| /parquet/táxi                                                | Ficheiros de dados públicos de NYC no formato Parquet, divididos por ano e mês usando o esquema de partição Hive/Hadoop. |
| /parquet/aninhado/                                             | Amostra de arquivos Parquet com colunas aninhadas                     |
| /json/                                                       | Pasta-mãe para dados em formato JSON                        |
| /json/livros/                                                 | Ficheiros JSON com dados de livros                                   |

### <a name="sample-query"></a>Consulta de exemplo

O último passo de validação é executar a seguinte consulta:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

A consulta acima deve devolver este número: **8945574**.

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para continuar com os seguintes artigos:

- [Consulta único ficheiro CSV](query-single-csv-file.md)

- [Pastas de consulta e vários ficheiros CSV](query-folders-multiple-csv-files.md)

- [Consulta de ficheiros específicos](query-specific-files.md)

- [Consultar ficheiros de Parquet](query-parquet-files.md)

- [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md)

- [Consultar ficheiros JSON](query-json-files.md)

- [Criar e utilizar vistas](create-use-views.md)
