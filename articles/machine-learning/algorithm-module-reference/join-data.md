---
title: 'Junte-se a dados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar a associação ao módulo de associar dados no serviço Azure Machine Learning para intercalar os conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e814f5ea4bd47ceb0697e860c946039ce39ae1f
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518017"
---
# <a name="join-data"></a>Associar dados

Este artigo descreve como utilizar o **associar dados** módulo na interface de visual de serviço do Azure Machine Learning para intercalar dois conjuntos de dados através de uma operação de associação de estilo da base de dados.  

## <a name="how-to-configure-join-data"></a>Como configurar dados de associação

Para executar uma associação em dois conjuntos de dados, eles devem estar relacionados por uma coluna chave. As chaves compostas com várias colunas também são suportadas. 

1. Adicionar os conjuntos de dados que pretende combinar e, em seguida, arraste o **associar dados** módulo na sua experimentação. 

    Pode encontrar o módulo no **transformação de dados** categoria, em **manipulação**.

1. Ligar os conjuntos de dados para o **associar dados** módulo. 
 
1. Selecione **iniciar Seletor de colunas** para escolher colunas chave. Lembre-se de escolher colunas para entradas esquerdas e direita.

    Para uma única chave:

    Selecione uma única coluna de chave para ambas as entradas.
    
    Para uma chave composta:

    Selecione todas as colunas chave de entrada à esquerda e direita entrada na mesma ordem. O **associar dados** módulo se irão associar as tabelas ao correspondem a todas as colunas chave. Marque a opção **permite duplicatas e preservar a ordem das colunas na seleção** se a ordem da coluna não é igual a tabela original. 

    ![Seletor de colunas](media/module/join-data-column-selector.png)


1. Selecione o **maiúsculas/minúsculas** opção se quiser preservar as maiúsculas e minúsculas numa associação de coluna de texto. 
   
1. Utilize o **tipo de associação** lista pendente para especificar a forma como os conjuntos de dados devem ser combinados.  
  
    * **Associação interna**: Uma *associação interna* é a operação de associação mais comuns. Devolve as linhas combinadas apenas quando os valores das colunas chave corresponderem.  
  
    * **LEFT associação externa**: R *associação externa à esquerda* devolve associado linhas para todas as linhas da tabela da esquerda. Quando uma linha na tabela esquerda tem não existem linhas correspondentes na tabela da direita, a linha retornada contém valores em falta para todas as colunas que vêm da tabela da direita. Também pode especificar um valor de substituição para valores em falta.  
  
    * **Total de associação externa**: R *União externa completa* devolve todas as linhas da tabela da esquerda (**table1**) e da tabela da direita (**table2**).  
  
         Para cada uma das linhas na tabela de qualquer um dos que têm não existem linhas correspondentes no outro, o resultado inclui uma linha que contém valores em falta.  
  
    * **LEFT Join via de atualizações**: Uma *left join semiestruturados* devolve apenas os valores da tabela da esquerda, quando os valores das colunas chave correspondem.  

1. Para a opção **manter as colunas chaves direita na tabela associado ao**:

    * Selecione esta opção para exibir as chaves das duas tabelas de entrada.
    * Anular seleção para devolver apenas as colunas de chave de entrada da esquerda.

1. Execute a experimentação, ou selecione o módulo de dados de associação e selecionado **executar selecionado** para efetuar a associação.

1. Para ver os resultados, clique com botão direito a **dados de associação** > **conjunto de dados de resultados** > **Visualize**.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 