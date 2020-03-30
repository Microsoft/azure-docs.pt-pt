---
title: Valores de Clip
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Clip Values em Aprendizagem automática Azure para detetar outliers e clipe ou substituir os seus valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 6466cea9fe04bb308a670cb03fd3de5314758142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456612"
---
# <a name="clip-values"></a>Valores de Clip

Este artigo descreve um módulo de designer de Machine Learning Azure (pré-visualização).

Utilize o módulo Clip Values para identificar e substituir opcionalmente valores de dados acima ou abaixo de um limiar especificado por um valor médio, constante ou outro valor substituto.  

Liga-se o módulo a um conjunto de dados que tem os números que pretende fixar, escolhe as colunas para trabalhar e, em seguida, estabelece um limiar ou intervalo de valores, e um método de substituição. O módulo pode ser de saída apenas dos resultados, ou os valores alterados anexados ao conjunto de dados original.

## <a name="how-to-configure-clip-values"></a>Como configurar valores de clipe

Antes de começar, identifique as colunas que pretende cortar e o método a utilizar. Recomendamos que teste primeiro qualquer método de recorte num pequeno subconjunto de dados.

O módulo aplica os mesmos critérios e método de substituição a **todas as** colunas que incluir na seleção. Portanto, não se esqueça de excluir colunas que não quer mudar.

Se precisar de aplicar métodos de recorte ou critérios diferentes em algumas colunas, deve utilizar uma nova instância de Valores de **Clippara** cada conjunto de colunas semelhantes.

1.  Adicione o módulo **Clip Values** ao seu pipeline e ligue-o ao conjunto de dados que pretende modificar. Pode encontrar este módulo na categoria De processamento de **dados,** na **categoria Escala e Redução.** 
  
1.  Na **lista das colunas,** utilize o Seletor de Colunas para escolher as colunas às quais serão **aplicados valores** de clip.  
  
1.  Para **o conjunto de limiares,** escolha uma das seguintes opções da lista de abandono. Estas opções determinam como definir os limites superior estoirantes e inferiores para valores aceitáveis vs. valores que devem ser cortados.  
  
    - **ClipPeaks**: Quando corta os valores por picos, especifica apenas um limite superior. São substituídos valores superiores ao valor limite.
  
    -  **ClipSubpeaks**: Quando corta os valores por subpicos, especifica apenas um limite inferior. Valores inferiores a esse valor limite são substituídos.  
  
    - **ClipPeaksAndSubpeaks**: Quando corta valores por picos e subpicos, pode especificar os limites superiores e inferiores. Os valores que estão fora desse alcance são substituídos. Valores que correspondam aos valores de fronteira não são alterados.
  
1.  Dependendo da sua seleção no passo anterior, pode definir os seguintes valores limiares: 

    + **Limiar inferior**: Apresentado apenas se escolher **ClipSubPeaks**
    + **Limiar superior**: Apresentado apenas se escolher **ClipPeaks**
    + **Limiar**: Apresentado apenas se escolher **ClipPeaksAndSubPeaks**

    Para cada tipo de limiar, escolha **Constant** ou **Percentil**.

1. Se selecionar **Constant,** escreva o valor máximo ou mínimo na caixa de texto. Por exemplo, assuma que sabe que o valor 999 foi usado como um valor de espaço reservado. Pode escolher **Constant** para o limiar superior, e tipo 999 em **valor constante para o limiar superior**.
  
1. Se escolher **percentil,** limita os valores da coluna a uma gama de percentil. 

    Por exemplo, assuma que quer manter apenas os valores na gama de percentil de 10-80, e substituir todos os outros. Escolheria **Percentil**, e depois escreveria 10 para **o valor percentil para um limiar mais baixo,** e escreveria 80 para o **valor percentil para o limiar superior**. 

    Consulte a secção de [percentículos](#examples-for-clipping-using-percentiles) para obter alguns exemplos de como usar intervalos de percentil.  
  
1.  Defina um valor substituto.

    Os números que correspondem exatamente aos limites que especificou são considerados dentro da gama permitida de valores, pelo que não são substituídos. Todos os números que se inserem fora do intervalo especificado são substituídos pelo valor de substituição. 
  
    + **Valor de substituição dos picos**: Define o valor para substituir todos os valores da coluna que sejam superiores ao limiar especificado.  
    + **Valor de substituição dos subpicos**: Define o valor a utilizar como substituto de todos os valores da coluna inferiores ao limiar especificado.  
    + Se utilizar a opção **ClipPeaksAndSubpeaks,** pode especificar valores de substituição separados para os valores de corte superior e inferior.  

    Suportam-se os seguintes valores de substituição:  
  
    -   **Limiar**: Substitui os valores cortados pelo valor limiar especificado.  
  
    -   **Média**: Substitui os valores recortados pela média dos valores da coluna. A média é calculada antes de os valores serem cortados.  
  
    -   **Mediano**: Substitui valores recortados pela mediana dos valores da coluna. A mediana é calculada antes de os valores serem cortados.   
  
    -   **Desaparecido.** Substitui os valores cortados pelo valor (vazio) em falta.  
  
1.  **Adicione colunas indicadoras**: Selecione esta opção se pretender gerar uma nova coluna que lhe diga se a operação de recorte especificada aplicada ou não aos dados dessa linha. Esta opção é útil quando está a testar um novo conjunto de valores de recorte e substituição.  
  
1. **Sobrepor a bandeira**: Indique como pretende que os novos valores sejam gerados. Por padrão, **a Clip Values** constrói uma nova coluna com os valores de pico cortados ao limiar desejado. Novos valores sobreporem a coluna original.  
  
    Para manter a coluna original e adicionar uma nova coluna com os valores recortados, desmarque esta opção.  
  
1.  Submeta o oleoduto.  
  
    Clique no módulo **Clip Values** e selecione **Visualizar** ou selecionar o módulo e mudar para o separador **Saídas** no painel direito, clique no ícone histograma nas **saídas da Porta,** para rever os valores e certificar-se de que a operação de recorte correspondeu às suas expectativas.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Exemplos para recortar usando percentiles

Para entender como funciona o recorte por percentículos, considere um conjunto de dados com 10 linhas, que têm um caso cada um dos valores 1-10.  
  
- Se estiver a usar o percentil como limiar superior, pelo valor para o percentil 90, 90% de todos os valores no conjunto de dados devem ser inferiores a esse valor.  
  
- Se estiver a usar o percentil como limiar inferior, pelo valor do percentil 10, 10% de todos os valores no conjunto de dados devem ser inferiores a esse valor.  
  
1.  Para **o conjunto de limiares,** escolha **ClipPeaks AndSubPeaks**.  
  
1.  Para **o limiar superior,** escolha **Percentil,** e para **o número percentil,** tipo 90.  
  
1.  Para **o valor de substituição superior,** escolha O Valor Em **Falta**.  
  
1.  Para **um limiar inferior,** escolha **Percentil,** e para **o número percentil,** tipo 10.  
  
1.  Para **um menor valor de substituição,** escolha O Valor Em **Falta**.  
  
1.  Desmarque a **opção Sobreescrever a bandeira**, e selecione a opção, Adicione a **coluna indicadora**.  
  
Agora tente o mesmo gasoduto usando 60 como o limiar de percentil superior e 30 como limiar de percentil mais baixo, e use o valor limiar como o valor de substituição. O quadro seguinte compara estes dois resultados:  
  
1.  Substituir por falta; Limiar superior = 90; Limiar inferior = 20  
  
1.  Substituir por limiar; Percentil superior = 60; Percentil inferior = 40  
  
|Dados originais|Substitua por falta|Substituir por limiar|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSO<br /><br /> 4, FALSO<br /><br /> 5, FALSO<br /><br /> 6, FALSO<br /><br /> 7, FALSO<br /><br /> 8, FALSO<br /><br /> 9, FALSO<br /><br /> TRUE|4, VERDADE<br /><br /> 4, VERDADE<br /><br /> 4, VERDADE<br /><br /> 4, VERDADE<br /><br /> 5, FALSO<br /><br /> 6, FALSO<br /><br /> 7, VERDADE<br /><br /> 7, VERDADE<br /><br /> 7, VERDADE<br /><br /> 7, VERDADE| 
 
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
