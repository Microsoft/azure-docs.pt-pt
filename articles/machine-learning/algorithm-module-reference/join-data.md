---
title: 'Dados de junção: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo unir dados de junção em Azure Machine Learning para mesclar conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: a154874f4f1026b36da473ba18788fc804609ac9
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152232"
---
# <a name="join-data"></a>Associar dados

Este artigo descreve como usar o módulo **Join Data** em Azure Machine Learning designer (pré-visualização) para fundir dois conjuntos de dados usando uma operação de adesão ao estilo de base de dados.  

## <a name="how-to-configure-join-data"></a>Como configurar dados de junção

Para executar uma junção em dois conjuntos de valores, eles devem estar relacionados por uma coluna de chave. Também há suporte para chaves compostas usando várias colunas. 

1. Adicione os conjuntos de dados que pretende combinar e, em seguida, arraste o módulo **Join Data** para o seu pipeline. 

    Pode encontrar o módulo na categoria **de Transformação** de Dados, em **Manipulação.**

1. Ligue os conjuntos de dados ao módulo **Join Data.** 
 
1. Selecione seletor de **colunas de lançamento** para escolher as colunas de teclas. Lembre-se de escolher colunas para as entradas à esquerda e à direita.

    Para uma única chave:

    Selecione uma única coluna de chave para ambas as entradas.
    
    Para uma chave composta:

    Selecione todas as colunas de chave da entrada esquerda e a entrada direita na mesma ordem. O módulo **Join Data** juntar-se-á às tabelas quando todas as colunas-chave coincidirem. Verifique a opção Permitir duplicados e preservar a **ordem da coluna na seleção** se a ordem da coluna não for a mesma que a tabela original. 

    ![seletor de coluna](media/module/join-data-column-selector.png)


1. Selecione a opção **'Caso Dejogo'** se pretender preservar a sensibilidade da caixa numa coluna de texto. 
   
1. Utilize a lista de dropdown do **tipo Join** para especificar como os conjuntos de dados devem ser combinados.  
  
    * **Interior Join**: Uma *união interior* é a operação de adesão mais comum. Ele retorna as linhas combinadas somente quando os valores das colunas de chave correspondem.  
  
    * **Left Outer Join**: Uma *junta exterior esquerda* volta a juntar-se às linhas para todas as linhas da mesa esquerda. Quando uma linha na tabela esquerda não tem linhas correspondentes na tabela direita, a linha retornada contém valores ausentes para todas as colunas que vêm da tabela direita. Você também pode especificar um valor de substituição para valores ausentes.  
  
    * **Full Outer Join**: Uma *junta exterior completa* devolve todas as linhas da mesa esquerda **(tabela 1**) e da mesa direita **(tabela 2).**  
  
         Para cada uma das linhas em uma das tabelas que não têm linhas correspondentes no outro, o resultado inclui uma linha que contém valores ausentes.  
  
    * **Semi-join esquerdo**: Uma *semi-união esquerda* devolve apenas os valores da mesa esquerda quando os valores das colunas-chave correspondem.  

1. Para a opção **Mantenha as colunas de teclas certas na tabela unida:**

    * Selecione esta opção para exibir as chaves de ambas as tabelas de entrada.
    * Anular seleção para retornar apenas as colunas de chave da entrada à esquerda.

1. Executar o pipeline.

1. Para ver os resultados, clique no Clique direito nos **Dados de Juntar** e selecione **Visualizar**.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 