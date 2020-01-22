---
title: 'Dados de junção: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo unir dados de junção em Azure Machine Learning para mesclar conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 11/19/2019
ms.openlocfilehash: c984b38498085567441fb0c4f965602d437589e5
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311500"
---
# <a name="join-data"></a>Associar dados

Este artigo descreve como usar o módulo **ingressar dados** no designer de Azure Machine Learning para mesclar dois DataSets usando uma operação de junção de estilo de banco de dado.  

## <a name="how-to-configure-join-data"></a>Como configurar dados de junção

Para executar uma junção em dois conjuntos de valores, eles devem estar relacionados por uma coluna de chave. Também há suporte para chaves compostas usando várias colunas. 

1. Adicione os conjuntos de dados que você deseja combinar e, em seguida, arraste o módulo de **dado de junção** para seu pipeline. 

    Você pode encontrar o módulo na categoria **transformação de dados** , em **manipulação**.

1. Conecte os conjuntos de dados ao módulo **unir data** . 
 
1. Selecione **Iniciar seletor de coluna** para escolher coluna (s) de chave. Lembre-se de escolher colunas para as entradas à esquerda e à direita.

    Para uma única chave:

    Selecione uma única coluna de chave para ambas as entradas.
    
    Para uma chave composta:

    Selecione todas as colunas de chave da entrada esquerda e a entrada direita na mesma ordem. O módulo de **dados de junção** unirá as tabelas quando todas as colunas de chave forem correspondentes. Marque a opção **permitir duplicatas e preservar a ordem das colunas na seleção** se a ordem da coluna não for igual à tabela original. 

    ![seletor de coluna](media/module/join-data-column-selector.png)


1. Selecione a opção **corresponder caso** para preservar a diferenciação de maiúsculas e minúsculas em uma junção de coluna de texto. 
   
1. Use a lista suspensa **tipo de junção** para especificar como os conjuntos de valores devem ser combinados.  
  
    * **Junção interna**: uma *junção interna* é a operação de junção mais comum. Ele retorna as linhas combinadas somente quando os valores das colunas de chave correspondem.  
  
    * **Junção externa esquerda**: uma *junção externa esquerda* retorna linhas Unidas para todas as linhas da tabela esquerda. Quando uma linha na tabela esquerda não tem linhas correspondentes na tabela direita, a linha retornada contém valores ausentes para todas as colunas que vêm da tabela direita. Você também pode especificar um valor de substituição para valores ausentes.  
  
    * **Junção externa completa**: uma *junção externa completa* retorna todas as linhas da tabela esquerda (**Table1**) e da tabela direita (**Table2**).  
  
         Para cada uma das linhas em uma das tabelas que não têm linhas correspondentes no outro, o resultado inclui uma linha que contém valores ausentes.  
  
    * **Semijunção à esquerda**: uma *semijunção à esquerda* retorna somente os valores da tabela esquerda quando os valores das colunas de chave correspondem.  

1. Para a opção, **Mantenha as colunas de chave direita na tabela unida**:

    * Selecione esta opção para exibir as chaves de ambas as tabelas de entrada.
    * Anular seleção para retornar apenas as colunas de chave da entrada à esquerda.

1. Executar o pipeline.

1. Para exibir os resultados, clique com o botão direito do mouse nos **dados de junção** e selecione **Visualizar**.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 