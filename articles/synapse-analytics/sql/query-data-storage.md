---
title: Visão geral - Dados de consulta no armazenamento utilizando SQL on-demand (pré-visualização)
description: Esta secção contém consultas de amostra que pode utilizar para experimentar o recurso SQL on-demand (pré-visualização) dentro do Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118274"
---
# <a name="overview-query-data-in-storage"></a>Visão geral: Dados de consulta no armazenamento

Esta secção contém consultas de amostra que pode utilizar para experimentar o recurso SQL on-demand (pré-visualização) dentro do Azure Synapse Analytics.
Os formatos de ficheiro suportados atualmente são:  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Pré-requisitos

As ferramentas necessárias para emitir consultas:

- Cliente SQL à sua escolha:
    - Estúdio Azure Synapse (pré-visualização)
    - Azure Data Studio
    - SQL Server Management Studio

Além disso, os parâmetros são os seguintes:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço final do serviço sql on-demand    | Será usado como o nome do servidor.                                   |
| Região de ponto final do serviço sql a pedido     | Será utilizado para determinar o armazenamento utilizado nas amostras. |
| Nome de utilizador e senha para acesso ao ponto final | Será usado para aceder ao ponto final.                               |
| A base de dados que vai usar para criar pontos de vista     | Esta base de dados será utilizada como ponto de partida para as amostras.       |

## <a name="first-time-setup"></a>Configuração pela primeira vez

O seu primeiro passo é **criar uma base de dados** onde executará as consultas. Em seguida, inicialize os objetos executando o script de [configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são usados para ler dados nestas amostras.

> [!NOTE]
> As bases de dados são usadas apenas para visualizar metadados, não para dados reais.  Escreva o nome da base de dados que utiliza, e vai precisar dele mais tarde.

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>Fornecido dados de demonstração

Os dados da demonstração contêm os seguintes conjuntos de dados:

- Táxi nyc - Yellow Taxi Trip Records - parte do conjunto de dados público de NYC
  - Formato CSV
  - Formato Parquet
- Conjunto de dados da população
  - Formato CSV
- Experimente ficheiros Parquet com colunas aninhadas
  - Formato Parquet
- Livros JSON
  - Formato JSON

| Folder path                                                  | Descrição                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Pasta dos pais para dados em formato CSV                         |
| /csv/população/<br />/csv/população-unix/<br />/csv/população-unix-hdr/<br />/csv/população-unix-hdr-escape<br />/csv/população-unix-hdr-quoted | Pastas com ficheiros de dados da População em diferentes formatos CSV. |
| /csv/táxi/                                                   | Pasta com ficheiros de dados públicos de NYC no formato CSV              |
| /parquet/                                                    | Pasta dos pais para dados em formato Parquet                     |
| /parquet/táxi                                                | Ficheiros de dados públicos de NYC em formato Parquet, divididos por ano, e mês usando o esquema de partição Hive/Hadoop. |
| /parquet/aninhado/                                             | Experimente ficheiros Parquet com colunas aninhadas                     |
| /json/                                                       | Pasta dos pais para dados em formato JSON                        |
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

## <a name="next-steps"></a>Próximos passos

Está agora pronto para continuar com os seguintes artigos como fazer:

- [Consulta single CSV arquivo](query-single-csv-file.md)

- [Questiões e vários ficheiros CSV](query-folders-multiple-csv-files.md)

- [Consulta de ficheiros específicos](query-specific-files.md)

- [Consultar ficheiros de Parquet](query-parquet-files.md)

- [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md)

- [Consultar ficheiros JSON](query-json-files.md)

- [Criar e usar vistas](create-use-views.md)
