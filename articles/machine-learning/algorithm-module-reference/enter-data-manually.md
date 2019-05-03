---
title: 'Introduza dados manualmente: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo introduza dados manualmente no serviço Azure Machine Learning para criar um pequeno conjunto de dados ao escrever valores. O conjunto de dados pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028609"
---
# <a name="enter-data-manually-module"></a>Introduza o módulo de dados manualmente

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um pequeno conjunto de dados ao escrever valores. O conjunto de dados pode ter várias colunas.
  
Este módulo pode ser útil em cenários como os seguintes:  
  
- Gerar um pequeno conjunto de valores para fins de teste  
  
- Criar uma lista de etiquetas
  
- Escrever uma lista de nomes de coluna para inserir num conjunto de dados

## <a name="enter-data-manually"></a>Introduzir dados manualmente 
  
1.  Adicionar a [introduza dados manualmente](./enter-data-manually.md) módulo à sua experimentação. Pode encontrar este módulo na **dados de entrada e saída** categoria no Azure Machine Learning. 
  
2.  Para **DataFormat**, selecione uma das seguintes opções. Estas opções determinam como os dados que fornece devem ser analisados. Os requisitos para cada formato sejam muito diferentes, por isso, certifique-se de que leia os tópicos relacionados.  
  
    -   **ARFF**. O relação de atributo formato de arquivo, utilizado pelo Weka.   
  
    -   **CSV**. Formato de valores separados por vírgulas. Para obter mais informações, consulte [converter para CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Um formato usado pelo Vowpal Wabbit e outras arquiteturas de machine learning.  
  
    -   **TSV**. Formato de valores separados por tabulações.

     Se escolher um formato e não fornecem dados que satisfaz as especificações de formato, ocorre um erro de tempo de execução.
  
3.  Clique no interior da **dados** caixa de texto para começar a inserir dados. Os seguintes formatos necessitam de atenção especial:  
  
    - **CSV**:  Para criar várias colunas, colar texto separado por vírgulas ou escreva usando vírgulas entre os campos de várias colunas.
  
        Se selecionar a **HasHeader** opção, pode usar a primeira linha de valores como o cabeçalho da coluna.  
  
        Se desmarcar esta opção, os nomes de colunas, Col1, Col2 e assim por diante, são utilizados. Pode adicionar ou alterar colunas nomes posteriormente utilizando [Editar metadados](./edit-metadata.md).  
  
    - **TSV**: Para criar várias colunas, colar texto separado por tabulações ou escreva várias colunas, utilizando os separadores entre campos.  
  
        Se selecionar a **HasHeader** opção, pode usar a primeira linha de valores como o cabeçalho da coluna.  
  
        Se desmarcar esta opção, os nomes de colunas, Col1, Col2 e assim por diante, são utilizados. Pode adicionar ou alterar colunas nomes posteriormente utilizando [Editar metadados](./edit-metadata.md).  
  
    -   **ARFF**:  Cole um ficheiro de formato ARFF existente. Se escrever valores diretamente, certifique-se de que adicionar os campos de atributo necessário e o cabeçalho opcional no início dos dados. 
    
        Por exemplo, as seguintes linhas de cabeçalho e o atributo foi possível adicionar a uma lista simple. O cabeçalho da coluna seria `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Escreva ou cole valores usando o formato de SVMLight.  
  
        Por exemplo, o exemplo a seguir representa as primeiras linhas de alguns do conjunto de dados doação de sangue, no formato de SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Quando executa o [introduza dados manualmente](./enter-data-manually.md) módulo, estas linhas são convertidas num conjunto de dados das colunas e indexar os valores da seguinte forma:  
  
        |Col1|Col2|Col3|Col4|Etiquetas|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Prima ENTER após cada linha, para iniciar uma nova linha.  
  
     **Certifique-se de que prima ENTER após a linha final.** 
     
     Se pressionar ENTER várias vezes para adicionar vários vazio à direita linhas, a linha vazia final é removida reduzida, mas outras linhas vazias são tratadas como valores em falta.  
  
     Se criar linhas com valores em falta, pode sempre filtrá-los mais tarde.  
  
5.  O módulo com o botão direito e selecione **executar seleção** para analisar os dados e carregá-lo em sua área de trabalho como um conjunto de dados.  
  
     Para ver o conjunto de dados, clique na porta de saída e selecione **Visualize**.  
## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 