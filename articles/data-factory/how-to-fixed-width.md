---
title: Processar arquivos de texto de comprimento fixo com fluxos de dados de mapeamento em Azure Data Factory
description: Saiba como processar arquivos de texto de comprimento fixo em Azure Data Factory usando fluxos de dados de mapeamento.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210679"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Processar arquivos de texto de comprimento fixo usando fluxos de dados de mapeamento de Data Factory

Data Factory mapeamento de fluxos de dados dá suporte a dados de transformação de arquivos de texto de largura fixa. Você definirá um conjunto de uma para um arquivo de texto sem um delimitador e, em seguida, configurará divisões de subcadeias com base na posição ordinal.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Ir para **+ novo pipeline** para iniciar um novo pipeline

2. Adicionar uma atividade de fluxo de dados que será usada para processar arquivos de largura fixa

  ![Pipeline de largura fixa](media/data-flow/fwpipe.png)

3. Na atividade fluxo de dados, selecione novo mapeamento fluxo de dados

4. Adicionar uma transformação de origem, coluna derivada, selecionar e transformação de coletor

  ![Fluxo de dados de largura fixa](media/data-flow/fw2.png)

5. Configurar a transformação de origem para usar um novo conjunto de um que será do tipo texto delimitado

6. Não definir nenhum delimitador de coluna e nenhum cabeçalho

Vamos simplesmente definir pontos de partida de campo e comprimentos para este conteúdo de arquivo:

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

7. Na guia projeção da transformação origem, você deverá ver uma coluna de cadeia de caracteres chamada "column_1"

8. Agora, na coluna derivada, crie uma nova coluna

9. Forneceremos nomes simples de colunas como Col1

10. Em seguida, no construtor de expressões, digite:

  ```substring(Column_1,1,4)```

  ![coluna derivada](media/data-flow/fwderivedcol1.png)

10. Repita isso para todas as colunas que você precisa analisar

12. Clique na guia inspecionar para ver as novas colunas que serão geradas

  ![Quanto](media/data-flow/fwinspect.png)

13. Use a transformação selecionar para remover qualquer uma das colunas que não serão necessárias para a transformação

  ![selecionar transformação](media/data-flow/fwselect.png)

14. Por fim, use o coletor para gerar os dados em uma pasta:

  ![coletor de largura fixa](media/data-flow/fwsink.png)

  Esta será a aparência da saída:

  ![saída de largura fixa](media/data-flow/fxdoutput.png)

  Os dados de largura fixa agora são divididos com quatro caracteres cada e atribuídos a Col1, Col2, Col3, COL4,... Com base no exemplo acima, estou dividindo os dados em 4 colunas

## <a name="next-steps"></a>Passos Seguintes

* Compilar o restante da lógica de fluxo de dados usando transformações de fluxo de dados de mapeamento [](concepts-data-flow-overview.md)
