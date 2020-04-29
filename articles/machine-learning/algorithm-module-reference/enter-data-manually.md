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
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79367521"
---
# <a name="enter-data-manually-module"></a>Introduza manualmente o módulo de dados

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize o módulo **De Introduzir dados manualmente** para criar um pequeno conjunto de dados digitando valores. O conjunto de dados pode ter várias colunas.
  
Este módulo pode ser útil em cenários como:  
  
- Gerando um pequeno conjunto de valores para testes.  
- Criando uma pequena lista de rótulos.  
- Digitando uma lista de nomes de colunas para inserir num conjunto de dados.

## <a name="create-a-dataset"></a>Criar um conjunto de dados 
  
1. Adicione o módulo [De dados de entrada manualmente](./enter-data-manually.md) ao seu pipeline. Pode encontrar este módulo na categoria **de Entrada e Saída** de Dados no Azure Machine Learning. 
  
1. Para **dataFormat,** selecione uma das seguintes opções. Estas opções determinam como os dados que fornece devem ser analisados. Os requisitos para cada formato diferem muito, por isso não se esqueça de ler os tópicos relacionados.  
  
   - **ARFF**: Formato de ficheiro de relação de atributo utilizado pela Weka.   
   - **CSV**: Formato de valores separados comma. Para mais informações, consulte [Converter para CSV](./convert-to-csv.md).    
   - **SVMLight**: Formato utilizado pela Vowpal Wabbit e outros quadros de aprendizagem automática.    
   - **TSV**: Formato de valores separados por separados por separados por separado.

   Se escolher um formato e não fornecer dados que satisfaçam as especificações do formato, ocorre um erro de tempo de execução.
  
1. Clique na caixa de texto **Data** para começar a introduzir dados. Os seguintes formatos requerem atenção especial:  
  
   - **CSV**: Para criar múltiplas colunas, colar em texto separado de vírgula ou digitar várias colunas utilizando vírgulas entre campos.
  
     Se selecionar a opção **HasHeader,** pode utilizar a primeira linha de valores como a direção da coluna.  
  
     Se desseleccionar esta opção, são utilizados os nomes das colunas (Col1, Col2 e assim por diante). Pode adicionar ou alterar os nomes das colunas mais tarde utilizando os [Metadados de Edição](./edit-metadata.md).  
  
   - **TSV**: Para criar múltiplas colunas, colar em texto separado de separados por separado, ou escrever várias colunas utilizando separadores entre campos.  
  
     Se selecionar a opção **HasHeader,** pode utilizar a primeira linha de valores como a direção da coluna.  
  
     Se desseleccionar esta opção, são utilizados os nomes das colunas (Col1, Col2 e assim por diante). Pode adicionar ou alterar os nomes das colunas mais tarde utilizando os [Metadados de Edição](./edit-metadata.md).  
  
   - **ARFF**: Colar num ficheiro de formato ARFF existente. Se estiver a digitar os valores diretamente, certifique-se de adicionar o cabeçalho opcional e os campos de atributos necessários no início dos dados. 

     Por exemplo, as seguintes linhas de cabeçalho e atributopodem ser adicionadas a uma lista simples. O título da `SampleText`coluna seria. Note que o tipo String não é suportado.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Digite ou cola em valores utilizando o formato SVMLight.  
  
     Por exemplo, a amostra que se segue representa o primeiro par de linhas do conjunto de dados da dádiva de sangue, em formato SVMLight:  
  
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
  
1. Selecione a tecla Enter após cada linha, para iniciar uma nova linha.      
     
   Se selecionar Introduzir várias vezes para adicionar várias linhas vazias, as linhas vazias serão removidas ou aparadas.  
  
   Se criar filas com valores em falta, pode sempre filtrar mais tarde.  
  
1. Ligue a porta de saída a outros módulos e execute o gasoduto.  
  
   Para ver o conjunto de dados, clique no módulo e **selecione Visualize**.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 