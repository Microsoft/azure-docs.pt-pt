---
title: 'Adicione linhas: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de adicionar linhas no serviço Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028759"
---
# <a name="add-rows-module"></a>Adicionar o módulo de linhas

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para concatenar dois conjuntos de dados. Concatenação, as linhas do segundo conjunto de dados são adicionadas ao final do primeiro conjunto de dados.  
  
Concatenação de linhas é útil em cenários como os seguintes:  
  
+ Tiver gerado uma série de estatísticas de avaliação, e desejar combiná-los numa tabela para geração de relatórios mais fácil.  
  
+ Tem vindo a trabalhar com conjuntos de dados diferentes, e pode querer combinar os conjuntos de dados para criar um conjunto de dados final.  

## <a name="how-to-use-add-rows"></a>Como utilizar Adicionar linhas  

Para concatenar as linhas de dois conjuntos de dados, as linhas tem de ter exatamente o mesmo esquema. Isso significa, o mesmo número de colunas e o mesmo tipo de dados nas colunas.

1.  Arrastar o **adicionar linhas** módulo na sua experimentação, pode encontrá-lo sob **transformação de dados**, no **Manipulate** categoria.

2. Ligue-se os conjuntos de dados para as duas portas de entrada. O conjunto de dados que pretende anexar deverá estar ligado para a segunda porta (direita). 
  
3.  Execute a experimentação. O número de linhas no conjunto de dados de saída deve ser igual a soma das linhas de ambos os conjuntos de dados de entrada.

    Se adicionar o mesmo conjunto de dados para as duas entradas do **adicionar linhas** módulo, o conjunto de dados é duplicado. 

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 