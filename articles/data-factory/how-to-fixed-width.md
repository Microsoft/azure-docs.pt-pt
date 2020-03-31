---
title: Processar ficheiros de texto de comprimento fixo com fluxos de dados de mapeamento na Fábrica de Dados do Azure
description: Saiba como processar ficheiros de texto de comprimento fixo na Azure Data Factory utilizando fluxos de dados de mapeamento.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d6885e9b30cc71bda822a29574c4d574f2b020a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387053"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Processar ficheiros de texto de comprimento fixo utilizando fluxos de dados de mapeamento da Fábrica de Dados

Ao utilizar fluxos de dados de mapeamento na Microsoft Azure Data Factory, pode transformar dados de ficheiros de texto de largura fixa. Na seguinte tarefa, definiremos um conjunto de dados para um ficheiro de texto sem um delimitador e, em seguida, configuraremos divisões de subcadeias com base na posição ordinal.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **+Novo Pipeline** para criar um novo oleoduto.

2. Adicione uma atividade de fluxo de dados, que será utilizada para o processamento de ficheiros de largura fixa:

    ![Gasoduto de largura fixa](media/data-flow/fwpipe.png)

3. Na atividade de fluxo de dados, selecione Novo fluxo de **dados de mapeamento**.

4. Adicione uma origem, coluna derivada, selete e transformação de afundar:

    ![Fluxo de dados de largura fixa](media/data-flow/fw2.png)

5. Configure a transformação da Fonte para utilizar um novo conjunto de dados, que será do tipo Texto Delimitado.

6. Não coloque nenhum delimitador de coluna ou cabeçalhos.

   Agora vamos definir pontos de partida de campo e comprimentos para o conteúdo deste ficheiro:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. No separador **de projeção** da sua transformação de Origem, deve ver uma coluna de cordas chamada *Column_1*.

8. Na coluna Derivada, crie uma nova coluna.

9. Daremos às colunas nomes simples como *col1.*

10. No construtor de expressão, digite o seguinte:

    ```substring(Column_1,1,4)```

    ![coluna derivada](media/data-flow/fwderivedcol1.png)

11. Repita o passo 10 para todas as colunas necessárias para analisar.

12. Selecione o separador **Inspecionar** para ver as novas colunas que serão geradas:

    ![inspecionar](media/data-flow/fwinspect.png)

13. Utilize a transformação Select para remover qualquer uma das colunas que não precisa para a transformação:

    ![selecionar transformação](media/data-flow/fwselect.png)

14. Utilize o Sink para forrindo os dados para uma pasta:

    ![pia de largura fixa](media/data-flow/fwsink.png)

    Aqui está o aspeto da saída:

    ![saída de largura fixa](media/data-flow/fxdoutput.png)

  Os dados de largura fixa são agora divididos, com quatro caracteres cada e atribuídos a Col1, Col2, Col3, Col4, e assim por diante. Com base no exemplo anterior, os dados são divididos em quatro colunas.

## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando transformações de [fluxos](concepts-data-flow-overview.md)de dados de mapeamento.
