---
title: Dividir o Diretório da Imagem
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de Diretório de Imagem Dividida no Azure Machine Learning designer (pré-visualização), para dividir as imagens de um diretório de imagem em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: fe5ba25904298fe1a394a4b01d6bdacc72d599c9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448686"
---
# <a name="split-image-directory"></a>Dividir o Diretório da Imagem

Este tópico descreve como usar o módulo de Diretório de Imagem Dividida no Azure Machine Learning designer, para dividir as imagens de um diretório de imagem em dois conjuntos distintos.

Este módulo é particularmente útil quando é necessário separar os dados de imagem em conjuntos de treino e teste. 

## <a name="how-to-configure-split-image-directory"></a>Como configurar o Diretório de Imagem Split

1. Adicione o módulo **de Diretório de Imagem Dividido** ao seu oleoduto. Pode encontrar este módulo na categoria 'Visão de Computador/Transformação de Dados de Imagem'.

2. Conecte-o ao módulo do qual a saída é diretório de imagem.

3. **Inserir Fração de imagens na primeira saída** para especificar a percentagem de dados a colocar na divisão esquerda, por defeito 0.9. Se o resultado da fração não for inteiro, o módulo utiliza o menor número de inteiros.


## <a name="technical-notes"></a>Notas técnicas

### <a name="expected-inputs"></a>Entradas esperadas

| Nome                  | Tipo           | Descrição              |
| --------------------- | -------------- | ------------------------ |
| Diretório de imagem de entrada | ImageDirectory | Diretório de imagem para dividir |

### <a name="module-parameters"></a>Parâmetros do módulo

| Nome                                   | Tipo  | Intervalo | Opcional | Descrição                            | Predefinição |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fração de imagens na primeira saída | Float | 0-1   | Necessário | Fração de imagens na primeira saída | 0,9     |

### <a name="outputs"></a>Saídas

| Nome                    | Tipo           | Descrição                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Diretório de imagem de saída1 | ImageDirectory | Diretório de imagem que contém imagens selecionadas |
| Diretório de imagem de saída2 | ImageDirectory | Diretório de imagem que contém todas as outras imagens |

## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

