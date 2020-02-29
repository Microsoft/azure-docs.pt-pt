---
title: 'Introduza dados manualmente: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Enter Data Manualmente em Azure Machine Learning para criar um pequeno conjunto de dados digitando valores. O conjunto de dados pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 27c359a594cec66708647d5a648c7e8414c0d92c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917480"
---
# <a name="enter-data-manually-module"></a>Introduza manualmente o módulo de dados

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um pequeno conjunto de dados digitando valores. O conjunto de dados pode ter várias colunas.
  
Este módulo pode ser útil em cenários como estes:  
  
- Gerando um pequeno conjunto de valores para testes  
  
- Criação de uma pequena lista de rótulos
  
- Digitar uma lista de nomes de colunas para inserir num conjunto de dados

## <a name="enter-data-manually"></a>Introduzir os Dados Manualmente 
  
1.  Adicione o módulo [De dados de entrada manualmente](./enter-data-manually.md) ao seu pipeline. Pode encontrar este módulo na categoria **de Entrada e Saída** de Dados no Azure Machine Learning. 
  
2.  Para **dataFormat,** selecione uma das seguintes opções. Estas opções determinam como os dados que fornece devem ser analisados. Os requisitos para cada formato diferem muito, por isso não se esqueça de ler os tópicos relacionados.  
  
    -   **ARFF**: O formato de ficheiro de relação de atributo, utilizado pela Weka.   
  
    -   **CSV**: Formato de valores separados comma. Para mais informações, consulte [Converter para CSV](./convert-to-csv.md).  
  
    -   **SVMLight**: Um formato utilizado pela Vowpal Wabbit e outros quadros de aprendizagem automática.  
  
    -   **TSV**: Formato de valores separados por separados por separados por separado.

     Se escolher um formato e não fornecer dados que satisfaçam as especificações do formato, ocorre um erro de tempo de execução.
  
3.  Clique na caixa de texto **Data** para começar a introduzir dados. Os seguintes formatos requerem atenção especial:  
  
    - **CSV**: Para criar múltiplas colunas, colar em texto separado de vírgula ou digitar várias colunas utilizando vírgulas entre campos.
  
        Se selecionar a opção **HasHeader,** pode utilizar a primeira linha de valores como a direção da coluna.  
  
        Se desseleccionar esta opção, são utilizados os nomes das colunas, Col1, Col2, e assim por diante. Pode adicionar ou alterar nomes de colunas mais tarde utilizando [metadados de edição](./edit-metadata.md).  
  
    - **TSV**: Para criar múltiplas colunas, colar em texto separado de separados por separado, ou escrever várias colunas usando separadores entre campos.  
  
        Se selecionar a opção **HasHeader,** pode utilizar a primeira linha de valores como a direção da coluna.  
  
        Se desseleccionar esta opção, são utilizados os nomes das colunas, Col1, Col2, e assim por diante. Pode adicionar ou alterar nomes de colunas mais tarde utilizando [metadados de edição](./edit-metadata.md).  
  
    -   **ARFF**: Colar num ficheiro de formato ARFF existente. Se estiver a digitar os valores diretamente, certifique-se de adicionar o cabeçalho opcional e os campos de atributos necessários no início dos dados. 
    
        Por exemplo, as seguintes linhas de cabeçalho e atributos poderiam ser adicionadas a uma lista simples. A direção da coluna seria `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Digite ou cola em valores utilizando o formato SVMLight.  
  
        Por exemplo, a amostra que se segue representa as primeiras linhas do conjunto de dados da dádiva de sangue, no formato SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Quando executa manualmente o módulo [Enter Data,](./enter-data-manually.md) estas linhas são convertidas para um conjunto de dados de colunas e valores de índice da seguinte forma:  
  
        |Col1|Col2|Col3|Col4|Etiquetas|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Pressione ENTER após cada linha, para iniciar uma nova linha.  
  
     **Certifique-se de que pressiona ENTER após a última fila.** 
     
     Se premir enter várias vezes para adicionar várias linhas vazias, a última linha vazia é removida aparada, mas outras filas vazias são tratadas como valores em falta.  
  
     Se criar filas com valores em falta, pode sempre filtrar mais tarde.  
  
5.  Ligue a porta de saída a outros módulos e execute o gasoduto.  
  
     Para ver o conjunto de dados, clique no módulo e **selecione Visualize**.  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 