---
title: 'Tutorial: Começar a analisar dados em contas de Armazenamento'
description: Neste tutorial, você vai aprender a analisar dados localizados numa conta de armazenamento.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 0b2978335596dd256aa69007245cb86b35a75417
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090578"
---
# <a name="analyze-data-in-a-storage-account"></a>Analisar dados numa conta de armazenamento

Neste tutorial, você vai aprender a analisar dados localizados numa conta de armazenamento.

## <a name="overview"></a>Descrição geral

Até agora, cobrimos cenários onde os dados residem em bases de dados no espaço de trabalho. Agora vamos mostrar-lhe como trabalhar com ficheiros em contas de armazenamento. Neste cenário, vamos usar a conta de armazenamento primário do espaço de trabalho e do contentor que especificamos ao criar o espaço de trabalho.

* O nome da conta de armazenamento: **contosolake**
* O nome do recipiente na conta de armazenamento: **utilizadores**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Crie ficheiros CSV e Parquet na sua conta de armazenamento

Execute o seguinte código num caderno. Cria um ficheiro CSV e um ficheiro parquet na conta de armazenamento.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analisar dados numa conta de armazenamento

1. No Synapse Studio, vá ao centro **de dados** e, em seguida, selecione **Linked**.
1. Vá às **contas de armazenamento**  >  **myworkspace (Primário - contosolake)**.
1. Selecione **utilizadores (Primário)**. Devia ver a pasta **NYCTaxi.** No interior deverá ver duas pastas chamadas **PassengerCountStats.csv** e **PassengerCountStats.parquet**.
1. Abra a pasta **PassengerCountStats.parquet.** Lá dentro, verá um arquivo de parquet com um nome como `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Clique à direita **em .parquet**e, em seguida, selecione **novo caderno**. Cria um caderno que tem uma célula como esta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Execute a célula.
1. Clique com o botão direito no ficheiro parquet no interior e, em seguida, selecione **Novo script SQL**  >  **SELECT TOP 100 linhas**. Cria um script SQL como este:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    Na janela do script, o **campo 'Ligar para'** está definido para **SQL on demand**.

1. Execute o script.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Orquestrar atividades com oleodutos](get-started-pipelines.md)
