---
title: 'Insira os dados manualmente: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo inserir dados manualmente no serviço de Azure Machine Learning para criar um pequeno DataSet digitando valores. O conjunto de linhas pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d7e6cede12f5a348f59db83b31b19c89266dfdf7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128843"
---
# <a name="enter-data-manually-module"></a>Inserir dados manualmente no módulo

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para criar um pequeno conjunto de pequena digitando valores. O conjunto de linhas pode ter várias colunas.
  
Esse módulo pode ser útil em cenários como estes:  
  
- Gerando um pequeno conjunto de valores para teste  
  
- Criando uma lista curta de rótulos
  
- Digitando uma lista de nomes de coluna para inserir em um conjunto de um DataSet

## <a name="enter-data-manually"></a>Introduzir os Dados Manualmente 
  
1.  Adicione o módulo [inserir dados manualmente](./enter-data-manually.md) ao seu experimento. Você pode encontrar esse módulo na categoria **entrada e saída de dados** em Azure Machine Learning. 
  
2.  Em **formato**de DataFormat, selecione uma das opções a seguir. Essas opções determinam como os dados que você fornece devem ser analisados. Os requisitos para cada formato diferem muito, portanto, não deixe de ler os tópicos relacionados.  
  
    -   **ARFF**. O formato de arquivo de relação de atributo, usado pelo weka.   
  
    -   **CSV**. Formato de valores separados por vírgula. Para obter mais informações, consulte [converter em CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Um formato usado por Vowpal wabbit e outras estruturas de aprendizado de máquina.  
  
    -   **TSV**. Formato de valores separados por tabulação.

     Se você escolher um formato e não fornecer dados que atendam às especificações de formato, ocorrerá um erro em tempo de execução.
  
3.  Clique dentro da caixa de texto **dados** para começar a inserir dados. Os formatos a seguir exigem atenção especial:  
  
    - **CSV**:  Para criar várias colunas, Cole o texto separado por vírgulas ou digite várias colunas usando vírgulas entre os campos.
  
        Se você selecionar a opção **HasHeader** , poderá usar a primeira linha de valores como o título de coluna.  
  
        Se você desmarcar essa opção, as colunas Names, Col1, Col2 e assim por diante serão usadas. Você pode adicionar ou alterar nomes de colunas posteriormente usando [Editar metadados](./edit-metadata.md).  
  
    - **TSV**: Para criar várias colunas, cole em texto separado por tabulações ou digite várias colunas usando guias entre campos.  
  
        Se você selecionar a opção **HasHeader** , poderá usar a primeira linha de valores como o título de coluna.  
  
        Se você desmarcar essa opção, as colunas Names, Col1, Col2 e assim por diante serão usadas. Você pode adicionar ou alterar nomes de colunas posteriormente usando [Editar metadados](./edit-metadata.md).  
  
    -   **ARFF**:  Cole um arquivo de formato ARFF existente. Se você estiver digitando valores diretamente, certifique-se de adicionar o cabeçalho opcional e os campos de atributo necessários no início dos dados. 
    
        Por exemplo, as linhas de cabeçalho e de atributo a seguir podem ser adicionadas a uma lista simples. O título da coluna seria `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Digite ou cole valores usando o formato SVMLight.  
  
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
  
5.  Clique com o botão direito do mouse no módulo e selecione **executar selecionado** para analisar os dados e carregá-los em seu espaço de trabalho como um conjunto.  
  
     Para exibir o conjunto de resultados, clique na porta de saída e selecione **Visualizar**.  
## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 