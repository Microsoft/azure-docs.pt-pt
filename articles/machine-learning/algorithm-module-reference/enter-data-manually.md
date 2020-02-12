---
title: 'Inserir dados manualmente: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo inserir dados manualmente em Azure Machine Learning para criar um pequeno conjunto digitando valores. O conjunto de linhas pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 61c3dde49d4cd298fb3a0f19dc1d8c00e54977af
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152351"
---
# <a name="enter-data-manually-module"></a>Inserir dados manualmente no módulo

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um pequeno conjunto de pequena digitando valores. O conjunto de linhas pode ter várias colunas.
  
Esse módulo pode ser útil em cenários como estes:  
  
- Gerando um pequeno conjunto de valores para teste  
  
- Criando uma lista curta de rótulos
  
- Digitando uma lista de nomes de coluna para inserir em um conjunto de um DataSet

## <a name="enter-data-manually"></a>Introduzir os Dados Manualmente 
  
1.  Adicione o módulo [De dados de entrada manualmente](./enter-data-manually.md) ao seu pipeline. Pode encontrar este módulo na categoria **de Entrada e Saída** de Dados no Azure Machine Learning. 
  
2.  Para **dataFormat,** selecione uma das seguintes opções. Essas opções determinam como os dados que você fornece devem ser analisados. Os requisitos para cada formato diferem muito, portanto, não deixe de ler os tópicos relacionados.  
  
    -   **ARFF.** O formato de arquivo de relação de atributo, usado pelo weka.   
  
    -   **CSV.** Formato de valores separados por vírgula. Para mais informações, consulte [Converter para CSV](./convert-to-csv.md).  
  
    -   **Svmlight.** Um formato usado por Vowpal wabbit e outras estruturas de aprendizado de máquina.  
  
    -   **TSV.** Formato de valores separados por tabulação.

     Se você escolher um formato e não fornecer dados que atendam às especificações de formato, ocorrerá um erro em tempo de execução.
  
3.  Clique na caixa de texto **Data** para começar a introduzir dados. Os formatos a seguir exigem atenção especial:  
  
    - **CSV**: Para criar múltiplas colunas, colar em texto separado de vírgula ou digitar várias colunas utilizando vírgulas entre campos.
  
        Se selecionar a opção **HasHeader,** pode utilizar a primeira linha de valores como a direção da coluna.  
  
        Se você desmarcar essa opção, as colunas Names, Col1, Col2 e assim por diante serão usadas. Pode adicionar ou alterar nomes de colunas mais tarde utilizando [metadados de edição](./edit-metadata.md).  
  
    - **TSV**: Para criar múltiplas colunas, colar em texto separado de separados por separado, ou escrever várias colunas usando separadores entre campos.  
  
        Se selecionar a opção **HasHeader,** pode utilizar a primeira linha de valores como a direção da coluna.  
  
        Se você desmarcar essa opção, as colunas Names, Col1, Col2 e assim por diante serão usadas. Pode adicionar ou alterar nomes de colunas mais tarde utilizando [metadados de edição](./edit-metadata.md).  
  
    -   **ARFF**: Colar num ficheiro de formato ARFF existente. Se você estiver digitando valores diretamente, certifique-se de adicionar o cabeçalho opcional e os campos de atributo necessários no início dos dados. 
    
        Por exemplo, as linhas de cabeçalho e de atributo a seguir podem ser adicionadas a uma lista simples. A direção da coluna seria `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Digite ou cola em valores utilizando o formato SVMLight.  
  
        Por exemplo, o exemplo a seguir representa as primeiras linhas do conjunto de linha de doação de sangue, no formato SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Quando executa manualmente o módulo [Enter Data,](./enter-data-manually.md) estas linhas são convertidas para um conjunto de dados de colunas e valores de índice da seguinte forma:  
  
        |Col1|Col2|Col3|Col4|Etiquetas|  
        |-|-|-|-|-|  
        |0, 16|0, 4|0,999961|0, 784|1|  
        |0|0, 4|0,999955|0, 8615|1|  
  
4.  Pressione ENTER após cada linha para iniciar uma nova linha.  
  
     **Certifique-se de que pressiona ENTER após a última fila.** 
     
     Se você pressionar ENTER várias vezes para adicionar várias linhas à direita vazias, a linha final vazia será removida, mas outras linhas vazias serão tratadas como valores ausentes.  
  
     Se você criar linhas com valores ausentes, você sempre poderá filtrá-los mais tarde.  
  
5.  Conecte a porta de saída a outros módulos e execute o pipeline.  
  
     Para ver o conjunto de dados, clique no módulo e **selecione Visualize**.  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 