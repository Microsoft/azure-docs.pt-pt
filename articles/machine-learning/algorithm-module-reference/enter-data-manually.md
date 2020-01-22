---
title: 'Inserir dados manualmente: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo inserir dados manualmente em Azure Machine Learning para criar um pequeno conjunto digitando valores. O conjunto de linhas pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 509b82596ce4819152fcd8d0bc202df186a20095
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314849"
---
# <a name="enter-data-manually-module"></a>Inserir dados manualmente no módulo

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um pequeno conjunto de pequena digitando valores. O conjunto de linhas pode ter várias colunas.
  
Esse módulo pode ser útil em cenários como estes:  
  
- Gerando um pequeno conjunto de valores para teste  
  
- Criando uma lista curta de rótulos
  
- Digitando uma lista de nomes de coluna para inserir em um conjunto de um DataSet

## <a name="enter-data-manually"></a>Introduzir os Dados Manualmente 
  
1.  Adicione o módulo [inserir dados manualmente](./enter-data-manually.md) ao seu pipeline. Você pode encontrar esse módulo na categoria **entrada e saída de dados** em Azure Machine Learning. 
  
2.  Em **formato de DataFormat**, selecione uma das opções a seguir. Essas opções determinam como os dados que você fornece devem ser analisados. Os requisitos para cada formato diferem muito, portanto, não deixe de ler os tópicos relacionados.  
  
    -   **ARFF**. O formato de arquivo de relação de atributo, usado pelo weka.   
  
    -   **CSV**. Formato de valores separados por vírgula. Para obter mais informações, consulte [converter em CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Um formato usado por Vowpal wabbit e outras estruturas de aprendizado de máquina.  
  
    -   **TSV**. Formato de valores separados por tabulação.

     Se você escolher um formato e não fornecer dados que atendam às especificações de formato, ocorrerá um erro em tempo de execução.
  
3.  Clique dentro da caixa de texto **dados** para começar a inserir dados. Os formatos a seguir exigem atenção especial:  
  
    - **CSV**: para criar várias colunas, Cole o texto separado por vírgulas ou digite várias colunas usando vírgulas entre os campos.
  
        Se você selecionar a opção **HasHeader** , poderá usar a primeira linha de valores como o título de coluna.  
  
        Se você desmarcar essa opção, as colunas Names, Col1, Col2 e assim por diante serão usadas. Você pode adicionar ou alterar nomes de colunas posteriormente usando [Editar metadados](./edit-metadata.md).  
  
    - **TSV**: para criar várias colunas, cole em texto separado por tabulações ou digite várias colunas usando guias entre campos.  
  
        Se você selecionar a opção **HasHeader** , poderá usar a primeira linha de valores como o título de coluna.  
  
        Se você desmarcar essa opção, as colunas Names, Col1, Col2 e assim por diante serão usadas. Você pode adicionar ou alterar nomes de colunas posteriormente usando [Editar metadados](./edit-metadata.md).  
  
    -   **ARFF**: colar um arquivo de formato ARFF existente. Se você estiver digitando valores diretamente, certifique-se de adicionar o cabeçalho opcional e os campos de atributo necessários no início dos dados. 
    
        Por exemplo, as linhas de cabeçalho e de atributo a seguir podem ser adicionadas a uma lista simples. O título da coluna seria `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: digite ou cole valores usando o formato SVMLight.  
  
        Por exemplo, o exemplo a seguir representa as primeiras linhas do conjunto de linha de doação de sangue, no formato SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Quando você executa o módulo [inserir dados manualmente](./enter-data-manually.md) , essas linhas são convertidas em um conjunto de valores de colunas e de índice da seguinte maneira:  
  
        |Col1|Col2|Col3|Col4|Etiquetas|  
        |-|-|-|-|-|  
        |0, 16|0, 4|0,999961|0, 784|1|  
        |0|0, 4|0,999955|0, 8615|1|  
  
4.  Pressione ENTER após cada linha para iniciar uma nova linha.  
  
     **Certifique-se de pressionar ENTER após a linha final.** 
     
     Se você pressionar ENTER várias vezes para adicionar várias linhas à direita vazias, a linha final vazia será removida, mas outras linhas vazias serão tratadas como valores ausentes.  
  
     Se você criar linhas com valores ausentes, você sempre poderá filtrá-los mais tarde.  
  
5.  Conecte a porta de saída a outros módulos e execute o pipeline.  
  
     Para exibir o conjunto de um, clique com o botão direito do mouse no módulo e selecione **Visualizar**.  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 