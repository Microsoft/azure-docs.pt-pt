---
title: 'Juntar Dados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo DeDerdes no Azure Machine Learning designer para fundir dois conjuntos de dados juntos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c23dca40d50c5837bd9ff45bc3c3d7fb2581685b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420755"
---
# <a name="join-data"></a>Associar dados

Este artigo descreve como usar o módulo **DeDerdes** no Azure Machine Learning designer para fundir dois conjuntos de dados usando uma operação de junção estilo base de dados.  

## <a name="how-to-configure-join-data"></a>Como configurar os Dados de Junção

Para realizar uma junção em dois conjuntos de dados, devem estar relacionados por uma coluna-chave. As teclas compostas que utilizam várias colunas também são suportadas. 

1. Adicione os conjuntos de dados que pretende combinar e, em seguida, arraste o módulo **'Unir Dados'** para o seu pipeline. 

    Pode encontrar o módulo na categoria **de Transformação de Dados,** em **Manipulação.**

1. Ligue os conjuntos de dados ao módulo **'Unir Dados'.** 
 
1. Selecione **Seletor de colunas de lançamento** para escolher colunas-chave. Lembre-se de escolher colunas para as entradas esquerda e direita.

    Para uma única chave:

    Selecione uma única coluna-chave para ambas as entradas.
    
    Para uma chave composta:

    Selecione todas as colunas-chave da entrada esquerda e da entrada direita na mesma ordem. O módulo **'Unir Dados'** juntar-se-á às tabelas quando todas as colunas-chave corresponderem. Verifique a opção Deixe duplicar e preservar a ordem da **coluna na seleção** se a ordem da coluna não for a mesma da tabela original. 

    ![seletor de colunas](media/module/join-data-column-selector.png)


1. Selecione a opção **caso 'Match'** se pretender preservar a sensibilidade ao caso numa coluna de texto. 
   
1. Utilize a lista de dropdown **do tipo 'Juntar',** para especificar como os conjuntos de dados devem ser combinados.  
  
    * **Interior :** Uma *junção interior* é a operação de junção mais comum. Só devolve as linhas combinadas quando os valores das colunas-chave coincidem.  
  
    * **Left Outer Join**: Um *lado esquerdo junta-se* a linhas unidas para todas as linhas da mesa esquerda. Quando uma linha na mesa esquerda não tem linhas correspondentes na mesa direita, a fila retornado contém valores em falta para todas as colunas que vêm da mesa direita. Também pode especificar um valor de substituição para valores em falta.  
  
    * **Full Outer Join**: Uma *junção exterior completa* retorna todas as linhas da mesa esquerda **(mesa1**) e da mesa direita **(tabela2**).  
  
         Para cada uma das linhas de cada mesa que não têm linhas correspondentes na outra, o resultado inclui uma linha contendo valores em falta.  
  
    * **Semi-unir à esquerda**: Uma *semi-junção esquerda* retorna apenas os valores da mesa esquerda quando os valores das colunas-chave coincidem.  

1. Para a opção **Mantenha as colunas-chave certas na tabela unida:**

    * Selecione esta opção para visualizar as teclas de ambas as tabelas de entrada.
    * Desmarcar apenas para retornar as colunas-chave da entrada esquerda.

1. Envie o oleoduto.

1. Para ver os resultados, clique com o botão direito para **'Unir Dados'** e **selecione Visualizar**.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 