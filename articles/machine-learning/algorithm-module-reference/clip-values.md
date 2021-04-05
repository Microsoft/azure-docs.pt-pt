---
title: Valores de Clip
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Valores de Clip em Azure Machine Learning para detetar os outliers e o clip ou substituir os seus valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 99fb41542dff28997438881abad71da11e927a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96014895"
---
# <a name="clip-values"></a>Valores de Clip

Este artigo descreve um módulo de Azure Machine Learning designer.

Utilize o módulo Valores de Clip para identificar e substituir opcionalmente valores de dados acima ou abaixo de um limiar especificado por um valor médio, constante ou outro valor de substituição.  

Liga o módulo a um conjunto de dados que tem os números que pretende cortar, escolhe as colunas para trabalhar e, em seguida, define um limiar ou intervalo de valores e um método de substituição. O módulo pode obter apenas os resultados, ou os valores alterados anexados ao conjunto de dados originais.

## <a name="how-to-configure-clip-values"></a>Como configurar valores de clipe

Antes de começar, identifique as colunas que pretende cortar e o método a utilizar. Recomendamos que teste primeiro qualquer método de recorte num pequeno subconjunto de dados.

O módulo aplica os mesmos critérios e método de substituição a **todas as** colunas que inclui na seleção. Portanto, certifique-se de excluir colunas que não quer mudar.

Se precisar de aplicar métodos de reprodução ou critérios diferentes em algumas colunas, deve utilizar uma nova instância de **Valores** de Clip para cada conjunto de colunas semelhantes.

1.  Adicione o módulo **Valores de Clip** ao seu pipeline e conecte-o ao conjunto de dados que pretende modificar. Pode encontrar este módulo na categoria **De Transformação de Dados,** na categoria **Escala e Redução.** 
  
1.  Na **Lista de colunas,** utilize o Seletor de Colunas para escolher as colunas às quais serão **aplicados valores de clipe.**  
  
1.  Para **definir limiares,** escolha uma das seguintes opções da lista de abandono. Estas opções determinam como define os limites superiores e inferiores para valores aceitáveis vs. valores que devem ser cortados.  
  
    - **ClipPeaks**: Quando se cortam valores por picos, especificam apenas um limite superior. São substituídos valores superiores a esse valor limite.
  
    -  **ClipSubpeaks**: Quando se cortam valores por subpeaks, especifica apenas um limite inferior. São substituídos valores inferiores a esse valor limite.  
  
    - **ClipPeaksAndSubpeaks**: Quando se cortam valores por picos e subpeaks, pode especificar tanto os limites superiores como os inferiores. Os valores que estão fora desse alcance são substituídos. Os valores que correspondem aos valores de fronteira não são alterados.
  
1.  Dependendo da sua seleção na etapa anterior, pode definir os seguintes valores-limiar: 

    + **Limiar inferior**: Exibido apenas se escolher **ClipSubPeaks**
    + **Limiar superior**: Exibido apenas se escolher **ClipPeaks**
    + **Limiar**: Exibido apenas se escolher **ClipPeaksAndSubPeaks**

    Para cada tipo de limiar, escolha **Constant** ou **Percentil**.

1. Se selecionar **Constant,** digite o valor máximo ou mínimo na caixa de texto. Por exemplo, assuma que sabe que o valor 999 foi usado como um valor reservado. Pode escolher **Constant** para o limiar superior e digitar 999 em **valor constante para o limiar superior**.
  
1. Se escolher **o Percentil,** limita os valores da coluna a um intervalo percentil. 

    Por exemplo, assuma que deseja manter apenas os valores na gama de percentil de 10-80, e substituir todos os outros. Você escolheria **Percentil**, e depois tipo 10 para **o valor percentil para o limiar mais baixo**, e tipo 80 para o valor **percentil para o limiar superior**. 

    Consulte a secção sobre [percentils](#examples-for-clipping-using-percentiles) para obter alguns exemplos de como utilizar gamas percentil.  
  
1.  Defina um valor substituto.

    Os números que correspondem exatamente aos limites especificados são considerados dentro da gama de valores permitida, pelo que não são substituídos. Todos os números que se encontram fora do intervalo especificado são substituídos pelo valor de substituição. 
  
    + **Valor de substituição para picos**: Define o valor a substituir por todos os valores da coluna que sejam superiores ao limiar especificado.  
    + **Valor de substituição para subpeaks**: Define o valor a utilizar como substituto de todos os valores da coluna que sejam inferiores ao limiar especificado.  
    + Se utilizar a opção **ClipPeaksAndSubpeaks,** pode especificar valores de substituição separados para os valores superiores e inferiores.  

    São suportados os seguintes valores de substituição:  
  
    -   **Limiar**: Substitui os valores cortados pelo valor limiar especificado.  
  
    -   **Média**: Substitui valores cortados com a média dos valores da coluna. A média é calculada antes que os valores sejam cortados.  
  
    -   **Mediana**: Substitui os valores cortados pela mediana dos valores da coluna. A mediana é calculada antes de os valores serem cortados.   
  
    -   **Desaparecido.** Substitui os valores cortados pelo valor em falta (vazio).  
  
1.  **Adicione colunas indicadoras**: Selecione esta opção se pretender gerar uma nova coluna que lhe diga se a operação de clipping especificada é ou não aplicada aos dados dessa linha. Esta opção é útil quando está a testar um novo conjunto de valores de clipping e substituição.  
  
1. **Bandeira de substituição**: Indique como pretende que os novos valores sejam gerados. Por predefinição, **os Valores de Clip** constrói uma nova coluna com os valores de pico cortados ao limiar pretendido. Novos valores substituem a coluna original.  
  
    Para manter a coluna original e adicionar uma nova coluna com os valores cortados, desmarca esta opção.  
  
1.  Envie o oleoduto.  
  
    Clique com o botão direito no módulo **Valores de Clipe** e selecione **Visualize** ou selecione o módulo e mude para o separador **Saídas** no painel direito, clique no ícone histograma nas saídas da Porta , para rever os **valores** e certifique-se de que a operação de clipping corresponde às suas expectativas.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Exemplos para recortes usando percentiles

Para entender como funciona o clipping por percentais, considere um conjunto de dados com 10 linhas, que têm um exemplo cada um dos valores 1-10.  
  
- Se estiver a usar o percentil como limiar superior, pelo valor do percentil 90, 90% de todos os valores do conjunto de dados devem ser inferiores a esse valor.  
  
- Se estiver a usar o percentil como o limiar mais baixo, pelo valor do percentil 10, 10% de todos os valores do conjunto de dados devem ser inferiores a esse valor.  
  
1.  Para **definir limiares,** escolha **ClipPeaksAndSubPeaks**.  
  
1.  Para **o limiar superior**, escolha **Percentil**, e para o número **percentil**, tipo 90.  
  
1.  Para **o valor de substituição superior,** escolha Valor Em **Falta.**  
  
1.  Para **o limiar mais baixo**, escolha **Percentil**, e para o número **percentil**, tipo 10.  
  
1.  Para **um valor de substituição inferior,** escolha Valor Em **Falta**.  
  
1.  Desseleccionar a **opção Substitua a bandeira**, e selecione a opção, **Adicione a coluna indicadora**.  
  
Agora experimente o mesmo oleoduto usando 60 como o limiar percentil superior e 30 como o limiar percentil inferior, e use o valor limiar como o valor de substituição. A tabela a seguir compara estes dois resultados:  
  
1.  Substitua-o por falta; Limiar superior = 90; Limiar inferior = 20  
  
1.  Substituir por limiar; Percentil superior = 60; Percentil inferior = 40  
  
|Dados originais|Substitua por falta|Substituir por limiar|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3.<br /><br /> 4, FALSO<br /><br /> 5, FALSO<br /><br /> 6.<br /><br /> 7.<br /><br /> 8, FALSO<br /><br /> 9, FALSO<br /><br /> TRUE|4, VERDADEIRO<br /><br /> 4, VERDADEIRO<br /><br /> 4, VERDADEIRO<br /><br /> 4, VERDADEIRO<br /><br /> 5, FALSO<br /><br /> 6.<br /><br /> 7, VERDADEIRO<br /><br /> 7, VERDADEIRO<br /><br /> 7, VERDADEIRO<br /><br /> 7, VERDADEIRO| 
 
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
