---
title: Valores de Clip
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo valores de clipes em Azure Machine Learning para detectar exceções e recortar ou substituir seus valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: ca35af997834a897572555e752597026b5159712
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314458"
---
# <a name="clip-values"></a>Valores de Clip

Este artigo descreve um módulo do designer de Azure Machine Learning.

Use o módulo valores de clipe para identificar e, opcionalmente, substituir valores de dados que estão acima ou abaixo de um limite especificado com uma média, uma constante ou outro valor substituto.  

Você conecta o módulo a um conjunto de um que tem os números que deseja recortar, escolhe as colunas com as quais trabalhar e, em seguida, define um limite ou intervalo de valores e um método de substituição. O módulo pode gerar apenas os resultados ou os valores alterados anexados ao conjunto de banco de um original.

## <a name="how-to-configure-clip-values"></a>Como configurar valores de clipe

Antes de começar, identifique as colunas que você deseja recortar e o método a ser usado. Recomendamos que você teste qualquer método de recorte em um pequeno subconjunto de dados primeiro.

O módulo aplica os mesmos critérios e método de substituição a **todas as** colunas que você incluir na seleção. Portanto, certifique-se de excluir colunas que você não deseja alterar.

Se você precisar aplicar métodos de recorte ou diferentes critérios a algumas colunas, deverá usar uma nova instância de **valores de clipe** para cada conjunto de colunas semelhantes.

1.  Adicione o módulo **valores de clipes** ao seu pipeline e conecte-o ao conjunto de um que você deseja modificar. Você pode encontrar esse módulo em **transformação de dados**, na categoria **escala e redução** . 
  
1.  Em **lista de colunas**, use o seletor de coluna para escolher as colunas às quais os **valores de clipe** serão aplicados.  
  
1.  Para o **conjunto de limites**, escolha uma das opções a seguir na lista suspensa. Essas opções determinam como você define os limites superior e inferior para valores aceitáveis versus valores que devem ser recortados.  
  
    - **ClipPeaks**: quando você corta valores por picos, você especifica apenas um limite superior. Valores maiores que esse valor de limite são substituídos.
  
    -  **ClipSubpeaks**: quando você corta valores por subpicos, você especifica apenas um limite inferior. Valores menores que esse valor de limite são substituídos.  
  
    - **ClipPeaksAndSubpeaks**: quando você corta valores por picos e subpicos, você pode especificar os limites superior e inferior. Os valores que estão fora desse intervalo são substituídos. Os valores que correspondem aos valores de limite não são alterados.
  
1.  Dependendo da sua seleção na etapa anterior, você pode definir os seguintes valores de limite: 

    + **Limite inferior**: exibido somente se você escolher **ClipSubPeaks**
    + **Limite superior**: exibido somente se você escolher **ClipPeaks**
    + **Limite**: exibido somente se você escolher **ClipPeaksAndSubPeaks**

    Para cada tipo de limite, escolha **constante** ou **percentil**.

1. Se você selecionar **constante**, digite o valor máximo ou mínimo na caixa de texto. Por exemplo, suponha que você saiba que o valor 999 foi usado como um valor de espaço reservado. Você pode escolher **constante** para o limite superior e digitar 999 no **valor constante para o limite superior**.
  
1. Se você escolher **percentil**, restringirá os valores de coluna a um intervalo percentual. 

    Por exemplo, suponha que você deseja manter apenas os valores no intervalo de 10-80 percentil e substituir todos os outros. Você deve escolher **percentil**e, em seguida, digitar 10 para o **valor de percentil para o limite inferior**e digitar 80 para o **valor de percentil para o limite superior**. 

    Consulte a seção sobre [percentuais](#examples-for-clipping-using-percentiles) para ver alguns exemplos de como usar intervalos de percentil.  
  
1.  Defina um valor de substituição.

    Os números que correspondem exatamente aos limites que você especificou são considerados dentro do intervalo de valores permitido e, portanto, não são substituídos. Todos os números que estão fora do intervalo especificado são substituídos pelo valor de substituição. 
  
    + **Valor substituto para picos**: define o valor a ser substituído por todos os valores de coluna que são maiores que o limite especificado.  
    + **Valor de substituição para subpicos**: define o valor a ser usado como um substituto para todos os valores de coluna que são menores que o limite especificado.  
    + Se você usar a opção **ClipPeaksAndSubpeaks** , poderá especificar valores de substituição separados para os valores recortados superior e inferior.  

    Há suporte para os seguintes valores de substituição:  
  
    -   **Limite**: substitui valores recortados pelo valor de limite especificado.  
  
    -   **Média**: substitui os valores cortados pela média dos valores de coluna. A média é calculada antes que os valores sejam recortados.  
  
    -   **Mediana**: substitui os valores recortados pela mediana dos valores da coluna. A mediana é computada antes que os valores sejam recortados.   
  
    -   **Ausente**. Substitui os valores recortados pelo valor ausente (vazio).  
  
1.  **Adicionar colunas de indicador**: Selecione esta opção se desejar gerar uma nova coluna que informa se a operação de recorte especificada foi aplicada aos dados nessa linha. Essa opção é útil quando você está testando um novo conjunto de valores de recorte e substituição.  
  
1. **Substituir sinalizador**: indica como você deseja que os novos valores sejam gerados. Por padrão, **os valores de clipes** constroem uma nova coluna com os valores de pico recortados para o limite desejado. Novos valores substituem a coluna original.  
  
    Para manter a coluna original e adicionar uma nova coluna com os valores recortados, desmarque essa opção.  
  
1.  Executar o pipeline.  
  
    Clique com o botão direito do mouse no módulo **valores de clipe** e selecione **Visualizar** ou selecione o módulo e alterne para a guia **saídas** no painel direito, clique no ícone de histograma nas **saídas de porta**para examinar os valores e verificar se a operação de recorte atende às suas expectativas.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Exemplos de recorte usando percentils

Para entender como o recorte por percentils funciona, considere um conjunto de registros com 10 linhas, que têm uma instância de cada um dos valores de 1-10.  
  
- Se você estiver usando o percentil como o limite superior, no valor do 90 º percentil, 90 por cento de todos os valores no conjunto de um deve ser menor que esse valor.  
  
- Se você estiver usando o percentil como o limite inferior, no valor do décimo percentil, 10 por cento de todos os valores no conjunto de um deve ser menor que esse valor.  
  
1.  Para o **conjunto de limites**, escolha **ClipPeaksAndSubPeaks**.  
  
1.  Para **limite superior**, escolha **percentil**e para **número percentil**, digite 90.  
  
1.  Para o **valor de substituição superior**, escolha **valor ausente**.  
  
1.  Para o **limite inferior**, escolha **percentil**e, para **número percentual**, digite 10.  
  
1.  Para **valor de substituição inferior**, escolha **valor ausente**.  
  
1.  Desmarque a opção **sinalizador de substituição**e selecione a opção **adicionar coluna do indicador**.  
  
Agora, experimente o mesmo pipeline usando 60 como o limite superior do percentil e 30 como o limite inferior do percentil e use o valor do limite como o valor de substituição. A tabela a seguir compara esses dois resultados:  
  
1.  Substituir por ausente; Limite superior = 90; Limite inferior = 20  
  
1.  Substituir por limite; Percentual superior = 60; Percentual inferior = 40  
  
|Dados originais|Substituir por ausente|Substituir pelo limite|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|VERDADEIRO<br /><br /> VERDADEIRO<br /><br /> 3, FALSO<br /><br /> 4, FALSO<br /><br /> 5, FALSO<br /><br /> 6, FALSO<br /><br /> 7, FALSO<br /><br /> 8, FALSO<br /><br /> 9, FALSO<br /><br /> VERDADEIRO|4, VERDADEIRO<br /><br /> 4, VERDADEIRO<br /><br /> 4, VERDADEIRO<br /><br /> 4, VERDADEIRO<br /><br /> 5, FALSO<br /><br /> 6, FALSO<br /><br /> 7, VERDADEIRO<br /><br /> 7, VERDADEIRO<br /><br /> 7, VERDADEIRO<br /><br /> 7, VERDADEIRO| 
 
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
