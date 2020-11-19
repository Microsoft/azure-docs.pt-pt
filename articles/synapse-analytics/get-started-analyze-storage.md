---
title: 'Tutorial: Começar a analisar dados em contas de Armazenamento'
description: Neste tutorial, você vai aprender a analisar dados localizados numa conta de armazenamento.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e3fbd1868cc1216cb7b9d02b2aa8e690af33952
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917686"
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
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Analisar dados numa conta de armazenamento

Pode analisar os dados na sua conta ADLS Gen2 padrão do seu espaço de trabalho ou pode ligar uma conta de armazenamento ADLS Gen2 ou Blob ao seu espaço de trabalho através de "**Gerir**" > "**Serviços Ligados**" > "**Novo**" (Os passos abaixo referem-se à conta primária da ADLS Gen2).

1. No Synapse Studio, vá ao centro **de dados** e, em seguida, selecione **Linked**.
1. Vá às **contas de armazenamento**  >  **myworkspace (Primário - contosolake)**.
1. Selecione **utilizadores (Primário)**. Devia ver a pasta **NYCTaxi.** No interior deverá ver duas pastas chamadas **PassengerCountStats_csvformat** e **PassengerCountStats_parquetformat**.
1. Abra a pasta **PassengerCountStats_parquetformat.** Lá dentro, verá um arquivo de parquet com um nome como `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Clique à direita **em .parquet** e, em seguida, selecione **novo caderno**. Cria um caderno que tem uma célula como esta:

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

    Na janela do script, o campo **'Ligar para'** está definido para **a piscina SQL sem servidor**.

1. Execute o script.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Orquestrar atividades com oleodutos](get-started-pipelines.md)
