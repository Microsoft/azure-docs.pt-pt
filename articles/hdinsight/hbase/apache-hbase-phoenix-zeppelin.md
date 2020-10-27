---
title: Executar consultas da Base Apache em Azure HDInsight com Apache Phoenix
description: Aprenda a usar Apache Zeppelin para executar consultas da Base Apache com Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: e85226e7a2544cda01f40a1c1be6be32f11d9dfc
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542799"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Use Apache Zeppelin para executar apaches Phoenix consultas sobre Apache HBase em Azure HDInsight

Apache Phoenix é uma fonte aberta, massivamente paralela camada de base de dados relacional construída em HBase. A Phoenix permite-lhe utilizar SQL como consultas sobre a HBase. A Phoenix utiliza controladores JDBC por baixo para lhe permitir criar, eliminar, alterar tabelas, índices, visualizações e sequências.  Também pode utilizar phoenix para atualizar linhas individualmente e a granel. Phoenix usa uma compilação nativa NOSQL em vez de usar MapReduce para compilar consultas, permitindo a criação de aplicações de baixa latência em cima da HBase.

O Apache Zeppelin é um notebook baseado na web de código aberto que permite criar documentos colaborativos e orientados para dados usando análises interativas de dados e idiomas como SQL e Scala. Ajuda os desenvolvedores de dados & os cientistas de dados desenvolverem, organizarem, executarem e partilharem código para manipulação de dados. Permite visualizar resultados sem se referir à linha de comando ou precisar dos detalhes do cluster.

Os utilizadores do HDInsight podem usar o Apache Zeppelin para consultar as tabelas Phoenix. O Apache Zeppelin está integrado com o cluster HDInsight e não existem passos adicionais para usá-lo. Basta criar um Caderno Zeppelin com intérprete JDBC e começar a escrever as suas consultas Phoenix SQL

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado Apache HBase em HDInsight. Ver [Começar com Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Criar uma Nota De Zeppelin Apache

1. `CLUSTERNAME`Substitua-o pelo nome do seu cluster no seguinte URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Em seguida, insira o URL num navegador web. Insira o nome de utilizador e a palavra-passe do seu cluster.

1. Na página de Zeppelin, **selecione Criar nova nota.**

    ![HDInsight Interactive Query zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. A partir do **diálogo de notas nova,** escreva ou selecione os seguintes valores:

    - Nota: Introduza um nome para a nota.
    - Intérprete predefinido: Selecione **jdbc** da lista de drop-down.

    Em seguida, **selecione Criar Nota** .

1. Certifique-se de que o cabeçalho do caderno mostra um estado ligado. É denotado por um ponto verde no canto superior direito.

    ![Estado do caderno zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Estado do caderno zeppelin")

1. Crie uma tabela HBase. Introduza o seguinte comando e, em seguida, prima **Shift + Enter** :

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    A declaração **de %jdbc (phoenix)** na primeira linha diz ao caderno para usar o intérprete Phoenix JDBC.

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

- [Apache Phoenix agora apoia Zeppelin em Azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Gramática de Apache Phoenix](https://phoenix.apache.org/language/index.html)