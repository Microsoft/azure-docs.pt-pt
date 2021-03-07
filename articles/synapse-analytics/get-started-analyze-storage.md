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
ms.date: 12/31/2020
ms.openlocfilehash: 71ba3d99ceee89464dafdf5bf4c16e70df146bef
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426081"
---
# <a name="analyze-data-in-a-storage-account"></a>Analisar dados numa conta de armazenamento

Neste tutorial, você vai aprender a analisar dados localizados numa conta de armazenamento.

## <a name="overview"></a>Descrição Geral

Até agora, cobrimos cenários onde os dados residem em bases de dados no espaço de trabalho. Agora vamos mostrar-lhe como trabalhar com ficheiros em contas de armazenamento. Neste cenário, vamos usar a conta de armazenamento primário do espaço de trabalho e do contentor que especificamos ao criar o espaço de trabalho.

* O nome da conta de armazenamento: **contosolake**
* O nome do recipiente na conta de armazenamento: **utilizadores**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Crie ficheiros CSV e Parquet na sua conta de armazenamento

Executar o seguinte código num caderno numa nova célula de código. Cria um ficheiro CSV e um ficheiro parquet na conta de armazenamento.

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
1. Vá ao **Azure data Storage Gen2**  >  **myworkspace (Primary - contosolake)**.
1. Selecione **utilizadores (Primário)**. Devia ver a pasta **NYCTaxi.** No interior deverá ver duas pastas chamadas **PassengerCountStats_csvformat** e **PassengerCountStats_parquetformat**.
1. Abra a pasta **PassengerCountStats_parquetformat.** Lá dentro, verá um arquivo de parquet com um nome como `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Clique com o botão **direito .parquet,** em seguida, selecione **Novo caderno** e, em seguida, selecione Carregar **para DataFrame**. Um novo caderno é criado com uma célula como esta:

    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    display(df.limit(10))
    ```

1. Anexar à piscina Spark chamada **Spark1**. Execute a célula.
1. Clique de volta na pasta do **utilizadores.** Clique com o botão direito novamente no ficheiro **.parquet** e, em seguida, selecione **Novo script SQL**  >  **SELECT TOP 100 linhas**. Cria um script SQL como este:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    Na janela do script, **certifique-se** de que o campo **De Ligação** está definido para a piscina SQL sem servidor incorporada.

1. Execute o script.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Orquestrar atividades com oleodutos](get-started-pipelines.md)
