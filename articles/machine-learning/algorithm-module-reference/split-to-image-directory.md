---
title: Diretório de imagem dividido
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo De Modelo de Imagem de Pontuação em Azure Machine Learning para gerar previsões usando um modelo de imagem treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 9673b3260425fd9244c635beaf77d367a14cac54
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450837"
---
# <a name="split-image-directory"></a>Diretório de imagem dividido

Este tópico descreve como usar o módulo de Diretório de Imagem Dividida no Azure Machine Learning designer (pré-visualização), para dividir as imagens de um diretório de imagem em dois conjuntos distintos.

Este módulo é particularmente útil quando é necessário separar os dados de imagem em conjuntos de treino e teste. 

## <a name="how-to-configure-split-image-directory"></a>Como configurar o Diretório de Imagem Split

1. Adicione o módulo **de Diretório de Imagem Dividido** ao seu oleoduto. Pode encontrar este módulo na categoria 'Visão de Computador/Transformação de Dados de Imagem'.

2. Conecte-o ao módulo do qual a saída é diretório de imagem.

3. **Inserir Fração de imagens na primeira saída** para especificar a percentagem de dados a colocar na divisão esquerda, por defeito 0.9.


## <a name="technical-notes"></a>Notas técnicas

### <a name="expected-inputs"></a>Entradas esperadas

| Nome                  | Tipo           | Descrição              |
| --------------------- | -------------- | ------------------------ |
| Diretório de imagem de entrada | ImageDirectory | Diretório de imagem para dividir |

### <a name="module-parameters"></a>Parâmetros do módulo

| Nome                                   | Tipo  | Intervalo | Opcional | Descrição                            | Predefinição |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fração de imagens na primeira saída | Float | 0-1   | Necessário | Fração de imagens na primeira saída | 0.9     |

### <a name="outputs"></a>Saídas

| Nome                    | Tipo           | Descrição                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Diretório de imagem de saída1 | ImageDirectory | Diretório de imagem que contém imagens selecionadas |
| Diretório de imagem de saída2 | ImageDirectory | Diretório de imagem que contém todas as outras imagens |

## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

