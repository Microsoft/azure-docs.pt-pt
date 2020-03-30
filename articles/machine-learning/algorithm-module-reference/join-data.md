---
title: 'Juntar dados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Join Data em Azure Machine Learning para fundir conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 38606f424e38fc68519181f485b5b698d0705d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477583"
---
# <a name="join-data"></a>Associar dados

Este artigo descreve como usar o módulo **Join Data** em Azure Machine Learning designer (pré-visualização) para fundir dois conjuntos de dados usando uma operação de adesão ao estilo de base de dados.  

## <a name="how-to-configure-join-data"></a>Como configurar dados de adesão

Para realizar uma adesão em dois conjuntos de dados, estes devem estar relacionados com uma coluna chave. As teclas compostas que utilizam várias colunas também são suportadas. 

1. Adicione os conjuntos de dados que pretende combinar e, em seguida, arraste o módulo **Join Data** para o seu pipeline. 

    Pode encontrar o módulo na categoria **de Transformação** de Dados, em **Manipulação.**

1. Ligue os conjuntos de dados ao módulo **Join Data.** 
 
1. Selecione seletor de **colunas de lançamento** para escolher as colunas de teclas. Lembre-se de escolher colunas para as inputs esquerda e direita.

    Para uma única chave:

    Selecione uma única coluna de teclas para ambas as inputs.
    
    Para uma chave composta:

    Selecione todas as colunas chave da entrada esquerda e direita na mesma ordem. O módulo **Join Data** juntar-se-á às tabelas quando todas as colunas-chave coincidirem. Verifique a opção Permitir duplicados e preservar a **ordem da coluna na seleção** se a ordem da coluna não for a mesma que a tabela original. 

    ![selecionador de colunas](media/module/join-data-column-selector.png)


1. Selecione a opção **'Caso Dejogo'** se pretender preservar a sensibilidade da caixa numa coluna de texto. 
   
1. Utilize a lista de dropdown do **tipo Join** para especificar como os conjuntos de dados devem ser combinados.  
  
    * **Interior Join**: Uma *união interior* é a operação de adesão mais comum. Só devolve as linhas combinadas quando os valores das colunas-chave correspondem.  
  
    * **Left Outer Join**: Uma *junta exterior esquerda* volta a juntar-se às linhas para todas as linhas da mesa esquerda. Quando uma linha na mesa esquerda não tem linhas correspondentes na mesa direita, a linha devolvida contém valores em falta para todas as colunas que vêm da mesa direita. Também pode especificar um valor de substituição para valores em falta.  
  
    * **Full Outer Join**: Uma *junta exterior completa* devolve todas as linhas da mesa esquerda **(tabela 1**) e da mesa direita **(tabela 2).**  
  
         Para cada uma das linhas em cada mesa que não têm linhas correspondentes na outra, o resultado inclui uma linha contendo valores em falta.  
  
    * **Semi-join esquerdo**: Uma *semi-união esquerda* devolve apenas os valores da mesa esquerda quando os valores das colunas-chave correspondem.  

1. Para a opção **Mantenha as colunas de teclas certas na tabela unida:**

    * Selecione esta opção para visualizar as teclas de ambas as tabelas de entrada.
    * Desmarque apenas para devolver as colunas chave da entrada esquerda.

1. Submeta o oleoduto.

1. Para ver os resultados, clique no Clique direito nos **Dados de Juntar** e selecione **Visualizar**.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 