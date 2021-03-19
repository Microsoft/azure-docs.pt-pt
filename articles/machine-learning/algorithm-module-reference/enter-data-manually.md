---
title: 'Introduza dados manualmente: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Entrar Manualmente em Azure Machine Learning para criar um pequeno conjunto de dados através de valores de dactilografia. O conjunto de dados pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3bff9a91f06649487560faef3ab554b2a3d56af3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90908074"
---
# <a name="enter-data-manually-module"></a>Introduza o módulo manual de dados

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize o módulo **'Inserir Manualmente' para** criar um pequeno conjunto de dados digitando valores. O conjunto de dados pode ter várias colunas.
  
Este módulo pode ser útil em cenários como:  
  
- Gerando um pequeno conjunto de valores para testes.  
- Criando uma pequena lista de rótulos.  
- Digitando uma lista de nomes de colunas para inserir num conjunto de dados.

## <a name="create-a-dataset"></a>Criar um conjunto de dados 
  
1. Adicione o módulo [de dados de entrada manualmente](./enter-data-manually.md) ao seu oleoduto. Pode encontrar este módulo na categoria entrada e saída de **dados** em Azure Machine Learning. 
  
1. Para **DataFormat,** selecione uma das seguintes opções. Estas opções determinam como os dados que fornece devem ser analisados. Os requisitos para cada formato diferem muito, por isso não se esqueça de ler os tópicos relacionados.  
  
   - **ARFF**: Formato de ficheiro de relação de atributos utilizado pela Weka.   
   - **CSV**: Formato de valores separados por vírgula. Para mais informações, consulte [Converter em CSV](./convert-to-csv.md).    
   - **SVMLight**: Formato utilizado pela Vowpal Wabbit e outras estruturas de aprendizagem automática.    
   - **TSV**: Formato de valores separados por separados por separados por separados.

   Se escolher um formato e não fornecer dados que satisfaçam as especificações do formato, ocorre um erro de tempo de execução.
  
1. Clique dentro da caixa de texto **de Dados** para começar a introduzir dados. Os seguintes formatos requerem uma atenção especial:  
  
   - **CSV**: Para criar várias colunas, cole em texto separado de vírgula ou digite várias colunas utilizando vírgulas entre campos.
  
     Se selecionar a opção **HasHeader,** pode utilizar a primeira linha de valores como título de coluna.  
  
     Se desmarcar esta opção, são utilizados os nomes das colunas (Col1, Col2, etc.). Pode adicionar ou alterar nomes de colunas mais tarde utilizando [Metadados de Edição](./edit-metadata.md).  
  
   - **TSV**: Para criar várias colunas, cole em texto separado do separado por separados do separado por separados do separador ou digite várias colunas utilizando separadores entre campos.  
  
     Se selecionar a opção **HasHeader,** pode utilizar a primeira linha de valores como título de coluna.  
  
     Se desmarcar esta opção, são utilizados os nomes das colunas (Col1, Col2, etc.). Pode adicionar ou alterar nomes de colunas mais tarde utilizando [Metadados de Edição](./edit-metadata.md).  
  
   - **ARFF**: Cole num ficheiro de formato ARFF existente. Se estiver a digitar valores diretamente, certifique-se de adicionar o cabeçalho opcional e os campos de atributos necessários no início dos dados. 

     Por exemplo, as seguintes linhas de cabeçalho e atributos podem ser adicionadas a uma lista simples. O título da coluna `SampleText` seria. Note que o tipo de corda não está suportado.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Digite ou cole em valores utilizando o formato SVMLight.  
  
     Por exemplo, a amostra a seguir representa o primeiro par de linhas do conjunto de dados de doação de sangue, no formato SVMLight:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Quando executam o módulo ['Inserir Manualmente',](./enter-data-manually.md) estas linhas são convertidas para um conjunto de dados de colunas e valores de índice da seguinte forma:  
  
     |Col1|Col2|Col3|Col4|Etiquetas|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. Selecione a tecla 'Entrar' depois de cada linha, para iniciar uma nova linha.      
     
   Se selecionar Insira várias vezes para adicionar várias linhas de fuga vazias, as linhas vazias serão removidas ou aparadas.  
  
   Se criar linhas com valores em falta, pode sempre filtrá-las mais tarde.  
  
1. Ligue a porta de saída a outros módulos e esgoe o gasoduto.  
  
   Para ver o conjunto de dados, clique no módulo com direito e **selecione Visualize**.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 