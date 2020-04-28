---
title: Executar consultas da Base Apache em Azure HDInsight com Apache Phoenix
description: Aprenda a usar Apache Zeppelin para executar consultas da Base Apache com Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72392244"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Use Apache Zeppelin para executar consultas Apache Phoenix sobre Apache HBase em Azure HDInsight

Apache Phoenix é uma camada de base de dados relacional massivamente paralela construída em HBase. A Phoenix permite-lhe usar sQL como consultas sobre HBase. A Phoenix utiliza os condutores JDBC por baixo para permitir criar, eliminar, alterar tabelas, índices, visualizações e sequências de SQL.  Também pode utilizar a Phoenix para atualizar linhas individualmente e a granel. Phoenix usa uma compilação nativa NOSQL em vez de usar mapReduce para compilar consultas, permitindo a criação de aplicações de baixa latência em cima de HBase.

O Apache Zeppelin é um portátil baseado na web de código aberto que lhe permite criar documentos colaborativos baseados em dados usando análises de dados interativos e idiomas como SQL e Scala. Ajuda os desenvolvedores de dados & os cientistas de dados desenvolverem, organizarem, executarem e partilharem código para manipulação de dados. Permite visualizar resultados sem se referir à linha de comando ou precisar dos detalhes do cluster.

Os utilizadores do HDInsight podem usar o Apache Zeppelin para consultar as tabelas Phoenix. O Apache Zeppelin está integrado com o cluster HDInsight e não existem passos adicionais para usá-lo. Basta criar um Caderno Zeppelin com intérprete JDBC e começar a escrever as suas consultas Phoenix SQL

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Apache HBase no HDInsight. Ver [Começar com Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Crie uma Nota Apache Zeppelin

1. Substitua-a `CLUSTERNAME` com o nome `https://CLUSTERNAME.azurehdinsight.net/zeppelin`do seu cluster no seguinte URL . Em seguida, introduza o URL num navegador web. Introduza o nome de utilizador e a palavra-passe do seu cluster.

1. A partir da página Zeppelin, selecione **Criar nova nota**.

    ![HDInsight Interactive Query Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. A partir do novo diálogo de **nota Criar,** digitar ou selecionar os seguintes valores:

    - Nota Nome: Introduza um nome para a nota.
    - Intérprete predefinido: Selecione **jdbc** da lista de lançamentos.

    Em seguida, selecione **Criar Nota**.

1. Certifique-se de que o cabeçalho do caderno mostra um estado ligado. É denotado por um ponto verde no canto superior direito.

    ![Estatuto do caderno Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Estatuto do caderno Zeppelin")

1. Crie uma tabela HBase. Introduza o seguinte comando e, em seguida, prima **Shift + Enter**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    A declaração **%jdbc (phoenix)** na primeira linha diz ao caderno para utilizar o intérprete Phoenix JDBC.

1. Ver tabelas criadas.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Insira os valores na tabela.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Consulta a mesa.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Apagar um disco.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Larga a mesa.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Passos seguintes

- [Apache Phoenix agora suporta Zeppelin em Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Gramática Apache Phoenix](https://phoenix.apache.org/language/index.html)
