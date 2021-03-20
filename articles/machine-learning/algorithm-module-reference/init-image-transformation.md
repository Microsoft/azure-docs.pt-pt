---
title: Init Imagem Transformação de Imagem Transformação
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo init Image Transformation no Azure Machine Learning designer para inicializar a transformação de imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421282"
---
# <a name="init-image-transformation"></a>Inicializar a Transformação da Imagem

Este artigo descreve como usar o módulo **de transformação de imagem Init** no designer de Aprendizagem automática Azure, para inicializar a transformação da imagem para especificar como quer que a imagem seja transformada.

## <a name="how-to-configure-init-image-transformation"></a>Como configurar a transformação da imagem init

1.  Adicione o módulo **init Image Transformation** ao seu pipeline no designer. 

2.  Para **redimensionar**, especifique se deve redimensionar a imagem PIL de entrada para o tamanho dado. Se escolher 'Verdadeiro', pode especificar o tamanho da imagem de saída desejada em **Tamanho**, por predefinição 256. 

3.  Para **a colheita do centro**, especifique se deve cortar a imagem PIL dada no centro. Se escolher 'True', pode especificar o tamanho da imagem de saída desejada da cultura em **tamanho de colheita,** por defeito 224.  

4.  Para **pad**, especifique se deve remar a imagem PIL dada em todos os lados com o valor do almofada 0. Se escolher 'True', pode especificar o enchimento (quantos pixéis adicionar) em cada borda em **Estofo**.

5.  Para **o nervosismo da cor**, especifique se deve alterar aleatoriamente o brilho, o contraste e a saturação de uma imagem.

6.  Para **a escala de cinza,** especifique se deve converter a imagem em escala de cinza.

7.  Para **a cultura de redimensionamento aleatório**, especifique se deve cortar a imagem PIL dada para o tamanho e o aspeto aleatórios. É efetuada uma cultura de tamanho aleatório (variando entre 0,08 e 1,0) do tamanho original e uma relação de aspeto aleatório (variam entre 3/4 e 4/3) da relação de aspeto original. Esta colheita é finalmente redimensionada para o tamanho dado.
    Isto é comumente usado na formação das redes de incepção. Se escolher 'True', pode especificar o tamanho de saída esperado de cada borda em **tamanho Aleatório,** por padrão 256.

8.  Para **a colheita aleatória**, especifique se deve cortar a imagem PIL dada num local aleatório. Se escolher 'True', pode especificar o tamanho de saída pretendido da cultura em **tamanho de cultura aleatório,** por defeito 224.

9.  Para **um giro horizontal aleatório**, especifique se deve virar horizontalmente a imagem PIL dada aleatoriamente com probabilidade 0.5.

10.  Para **um giro vertical aleatório,** especifique se deve virar verticalmente a imagem PIL dada aleatoriamente com probabilidade 0.5.

11.  Para **a rotação aleatória,** especifique se deve rodar a imagem por ângulo. Se escolher 'Verdadeiro', pode especificar em intervalo de graus definindo graus de **rotação aleatórios**, o que significa (-graus, +graus), por predefinição 0.

12.  Para **affine aleatório**, especifique se deve a transformação aleatória da imagem mantendo o centro invariante. Se escolher 'Verdadeiro', pode especificar em intervalos de graus para selecionar em graus aleatórios de **affine**, o que significa (-graus, +graus), por predefinição 0.

13.  Para **a escala de cinza aleatória**, especifique se deve converter aleatoriamente a imagem em escala de cinza com probabilidade 0.1.

14.  Para **a perspetiva aleatória**, especifique se deve realizar a transformação de Perspetiva da imagem PIL dada aleatoriamente com probabilidade 0.5.


16.  Ligue-se ao módulo [de Transformação de Imagem,](apply-image-transformation.md) para aplicar a transformação especificada acima no conjunto de dados de imagem de entrada.

17. Envie o oleoduto.

## <a name="results"></a>Resultados

Após a transformação concluída, pode encontrar imagens transformadas na saída do módulo de Transformação de [Imagem Aplicada.](apply-image-transformation.md)


## <a name="technical-notes"></a>Notas técnicas  

Consulte [https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html) para mais informações sobre a transformação da imagem.

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Name                    | Intervalo   | Tipo    | Predefinição | Description                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Redimensionar                  | Qualquer     | Booleano | Verdadeiro    | Redimensione a imagem PIL de entrada para o tamanho dado |
| Tamanho                    | >=1     | Número inteiro | 256     | Especificar o tamanho de saída pretendido          |
| Cultura central             | Qualquer     | Booleano | Verdadeiro    | Culturas a imagem PIL dada no centro  |
| Tamanho da cultura               | >=1     | Número inteiro | 224     | Especificar o tamanho de saída desejado da cultura |
| Pad                     | Qualquer     | Booleano | Falso   | Pad a imagem PIL dada em todos os lados com o valor "pad" dado |
| Preenchimento                 | >=0     | Número inteiro | 0       | Estofamento em cada fronteira                   |
| Nervosismo de cor            | Qualquer     | Booleano | Falso   | Alterar aleatoriamente o brilho, contraste e saturação de uma imagem |
| Escala de cinza               | Qualquer     | Booleano | Falso   | Converter imagem em escala cinzenta               |
| Cultura de redimensionado aleatório     | Qualquer     | Booleano | Falso   | Cultura a imagem PIL dada para tamanho aleatório e relação de aspeto |
| Tamanho aleatório             | >=1     | Número inteiro | 256     | Tamanho de saída esperado de cada borda        |
| Cultura aleatória             | Qualquer     | Booleano | Falso   | Corte a imagem PIL dada em um local aleatório |
| Tamanho de cultura aleatória        | >=1     | Número inteiro | 224     | Tamanho de saída desejado da cultura          |
| Flip horizontal aleatório  | Qualquer     | Booleano | Verdadeiro    | Horizontalmente vire a imagem PIL dada aleatoriamente com uma dada probabilidade |
| Flip vertical aleatório    | Qualquer     | Booleano | Falso   | Verticalmente vire a imagem PIL dada aleatoriamente com uma dada probabilidade |
| Rotação aleatória         | Qualquer     | Booleano | Falso   | Rode a imagem por ângulo                |
| Graus de rotação aleatória | [0,180] | Número inteiro | 0       | Gama de graus para selecionar a partir de          |
| Affine aleatório           | Qualquer     | Booleano | Falso   | Transformação aleatória afina da imagem mantendo o centro invariante |
| Graus affine aleatórios   | [0,180] | Número inteiro | 0       | Gama de graus para selecionar a partir de          |
| Escala cinzenta aleatória        | Qualquer     | Booleano | Falso   | Converter aleatoriamente a imagem em escala cinzenta com probabilidade 0.1 |
| Perspetiva aleatória      | Qualquer     | Booleano | Falso   | Realiza a transformação da perspetiva da imagem PIL dada aleatoriamente com probabilidade 0.5 |
| Apagamento aleatório          | Qualquer     | Booleano | Falso   | Seleciona aleatoriamente uma região de retângulo numa imagem e apaga os seus pixels com probabilidade 0,5 |

###  <a name="output"></a>Saída  

| Nome                        | Tipo                    | Description                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Transformação de imagem de saída | Diretório de Transformação | Transformação de imagem de saída que pode ser ligada ao módulo **de Transformação de Imagem Aplicada.** |

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 