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
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424903"
---
# <a name="overview-query-data-in-storage"></a>Visão geral: Dados de consulta no armazenamento

Esta secção contém consultas de amostra que pode utilizar para experimentar o recurso SQL on-demand (pré-visualização) dentro do Azure Synapse Analytics.
Os ficheiros suportados atualmente são: 
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

Antes de utilizar as amostras incluídas mais tarde neste artigo, tem dois passos:

- Crie uma base de dados para os seus pontos de vista (caso pretenda utilizar pontos de vista)
- Criar credenciais a utilizar pela SQL a pedido para aceder aos ficheiros armazenados

### <a name="create-database"></a>Criar base de dados

Precisa de uma base de dados para criar vistas. Você usará esta base de dados para algumas das consultas de amostra nesta documentação.

> [!NOTE]
> As bases de dados são usadas apenas para visualizar metadados, não para dados reais.  Escreva o nome da base de dados que utiliza, e vai precisar dele mais tarde.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Criar credenciais

Tens de criar credenciais antes de fazer consultas. Esta credencial será utilizada pelo serviço on-demand sQL para aceder aos ficheiros armazenados.

> [!NOTE]
> Para executar com sucesso Como faz êms nesta secção, tem de usar token SAS.
>
> Para começar a utilizar tokens SAS tem de largar a Identidade de Utilizador que é explicada no [seguinte artigo](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL on-demand por padrão usa sempre a passagem do AAD.

Para obter mais informações sobre como gerir o controlo de acesso ao armazenamento, verifique este [link](develop-storage-files-storage-access-control.md).

> [!WARNING]
> Você precisa criar credenciais para uma conta de armazenamento que está localizada na sua região de endpoint. Embora a SQL on-demand possa aceder a armazenamentos de diferentes regiões, ter armazenamento e ponto final na mesma região proporcionará uma melhor experiência de desempenho.

Para criar credenciais para contentores CSV, JSON e Parquet, execute o código abaixo:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
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

## <a name="validation"></a>Validação

Execute as seguintes três consultas e verifique se as credenciais são criadas corretamente.

> [!NOTE]
> Todos os URIs nas consultas de amostra utilizam uma conta de armazenamento localizada na região do Azure do Norte da Europa. Certifique-se de que criou a credencial apropriada. Faça a consulta abaixo e certifique-se de que a conta de armazenamento está listada.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Se não encontrar a credencial apropriada, verifique a [configuração pela primeira vez.](#first-time-setup)

### <a name="sample-query"></a>Consulta de exemplo

O último passo de validação é executar a seguinte consulta:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

A consulta acima deve devolver este número: **8945574**.

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para continuar com os seguintes artigos como fazer:

- [Consulta single CSV arquivo](query-single-csv-file.md)

- [Questiões e vários ficheiros CSV](query-folders-multiple-csv-files.md)

- [Consulta de ficheiros específicos](query-specific-files.md)

- [Arquivos De Consulta Parquet](query-parquet-files.md)

- [Tipos aninhados de Parquet de consulta](query-parquet-nested-types.md)

- [Consulta de ficheiros JSON](query-json-files.md)

- [Criar e usar vistas](create-use-views.md)
