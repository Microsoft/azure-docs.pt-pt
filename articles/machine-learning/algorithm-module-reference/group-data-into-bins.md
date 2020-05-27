---
title: Dados do grupo em bins
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar os Dados do Grupo no módulo Bins para agrupar números ou alterar a distribuição de dados contínuos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: b33aa3d2ab22fc986100d6141dd03d5547ef1862
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853828"
---
# <a name="group-data-into-bins"></a>Dados do grupo em bins

Este artigo descreve como usar os Dados do Grupo no módulo [Bins](group-data-into-bins.md) em Azure Machine Learning designer (pré-visualização), para números de grupo ou alterar a distribuição de dados contínuos.

O módulo [De dados do grupo em Bins](group-data-into-bins.md) suporta múltiplas opções para a fixação de dados. Pode personalizar a forma como as bordas do caixote do lixo são definidas e como os valores são repartidos nos caixotes. Por exemplo, pode:  

+ Digite manualmente uma série de valores para servir como os limites do caixote do lixo.  
+ Atribuir valores aos caixotes utilizando *quantiles,* ou percentil.  
+ Forçar uma distribuição uniforme de valores nos caixotes.  

### <a name="more-about-binning-and-grouping"></a>Mais sobre a fixação e agrupamento

*A fixação* ou agrupam dados (por vezes *chamados de quantificação)* é uma ferramenta importante na preparação de dados numéricos para a aprendizagem automática, e é útil em cenários como estes:

+ Uma coluna de números contínuos tem demasiados valores únicos para modelar eficazmente, por isso atribui automaticamente ou manualmente os valores a grupos, para criar um conjunto menor de gamas discretas.

+ Substitua uma coluna de números por valores categóricos que representem gamas específicas.

    Por exemplo, é possível que queira agrupar valores numa coluna etária especificando gamas personalizadas, tais como 1-15, 16-22, 23-30, e assim por diante, para a demografia do utilizador.

+ Um conjunto de dados tem alguns valores extremos, todos bem fora do intervalo esperado, e estes valores têm uma influência desmesurada no modelo treinado. Para mitigar o enviesamento no modelo, pode transformar os dados numa distribuição uniforme, utilizando o método quantiles.

    Com este método, o módulo De dados do [grupo em Bins](group-data-into-bins.md) determina as localizações ideais do caixote do lixo e as larguras do caixote do lixo para garantir que aproximadamente o mesmo número de amostras caia em cada contentor. Então, dependendo do método de normalização que escolher, os valores nos caixotes são transformados em percentiles ou mapeados para um número de lixo.

### <a name="examples-of-binning"></a>Exemplos de fixação

O diagrama seguinte mostra a distribuição de valores numéricos antes e depois de se ligar ao método **dos quantiles.** Note que, em comparação com os dados brutos à esquerda, os dados foram bloqueados e transformados numa escala normal unitária.  

"Um exemplo pode ser encontrado a partir do resultado desta execução do https://ml.azure.com/visualinterface/authoring/Normal/87270db9-4651-448e-bd28-8ef7428084dc?wsid=%2Fsubscriptions%2Fe9b2ec51-5c94-4fa8-809a-dc1e695e4896%2Fresourcegroups%2Fmodule-ws-rg%2Fworkspaces%2Fmodule-prerelease-119&flight=cm&tid=72f988bf-86f1-41af-91ab-2d7cd011db47&smtendpoint=https%3A%2F%2Fsmt-test1.azureml-test.net gasoduto:

Como há tantas formas de agrupar dados, todos personalizáveis, recomendamos que experimente diferentes métodos e valores. 

## <a name="how-to-configure-group-data-into-bins"></a>Como configurar dados do grupo em bins

1. Adicione o módulo de dados do **grupo em bins** ao seu pipeline em Designer (pré-visualização). Pode encontrar este módulo na categoria **De transformação**de Dados.

2. Ligue o conjunto de dados que tem dados numéricos ao lixo.  A quantificação só pode ser aplicada a colunas que contenham dados numéricos. 

    Se o conjunto de dados contiver colunas não numéricas, utilize as [Colunas Select no](select-columns-in-dataset.md) módulo Dataset para selecionar um subconjunto de colunas para trabalhar.

3. Especifique o modo de fixação. O modo de fixação determina outros parâmetros, por isso certifique-se de selecionar primeiro a opção do **modo Binning.** São suportados os seguintes tipos de fixação:

    **Quantiles**: O método quantilo atribui valores a caixotes com base em percentil. Quantiles também é conhecido como igual ajuste de altura.

    **Largura igual**: Com esta opção, deve especificar o número total de caixas. Os valores da coluna de dados são colocados nos caixotes de modo a que cada caixote tenha o mesmo intervalo entre os valores de partida e de fim. Como resultado, alguns caixotes podem ter mais valores se os dados forem agrupados em torno de um determinado ponto.

    **Bordas personalizadas**: Pode especificar os valores que iniciam cada caixote. O valor da borda é sempre o limite inferior do caixote do lixo.  Por exemplo, assuma que quer agrupar valores em dois caixotes, um com valores superiores a 0, e outro com valores inferiores ou iguais a 0. Neste caso, para as bordas do caixote do lixo, você escreveria 0 na **lista separada da Vírcula de bordas de caixotes**do lixo . A saída do módulo seria de 1 e 2, indicando o índice de caixa para cada valor de linha. Por favor, note que a lista de valor separada da vírvia deve estar em ordem ascendente, tais como 1, 3, 5, 7.

4. **Número de caixas**: Se estiver a utilizar os modos de fixação **de Quantiles**, e **equal width,** utilize esta opção para especificar quantas caixas, ou *quantiles,* pretende criar.

5. Para que as **Colunas sejam de saque,** utilize o Seletor de Colunas para escolher as colunas que têm os valores que pretende desmarcar. As colunas devem ser um tipo de dados numéricos.

    A mesma regra de fixação é aplicada a todas as colunas aplicáveis que escolher. Portanto, se precisar de colocar algumas colunas utilizando um método diferente, utilize uma instância separada de Dados de [Grupo em bins](group-data-into-bins.md) para cada conjunto de colunas.

    > [!WARNING]
    > Se escolher uma coluna que não seja um tipo permitido, gera-se um erro de tempo de execução. O módulo devolve um erro assim que encontra qualquer coluna de um tipo proibido. Se tiver um erro, reveja todas as colunas selecionadas. O erro não enumera todas as colunas inválidas.

6. Para o **modo de saída,** indique como pretende obter os valores quantificados.

      + **Apêndice**: Cria uma nova coluna com os valores e apêndices que estão na tabela de entrada.

      + **Inplace**: Substitui os valores originais com os novos valores no conjunto de dados.

      + **ResultadoSApenas:** Devolve apenas as colunas de resultados.

7. Se selecionar o modo de fixação **quantiles,** utilize a opção de **normalização quântica** para determinar como os valores são normalizados antes de se classificar em quantiles. Note que normalizar os valores transforma os valores, mas não afeta o número final de caixotes.

    Os seguintes tipos de normalização são suportados:

    + **Por cento**: Os valores são normalizados dentro do intervalo [0,100]

    + **PQuantile**: Os valores são normalizados dentro do intervalo [0,1]

    + **QuantileIndex**: Os valores são normalizados dentro do intervalo [1,número de caixotes]

8. Se escolher a opção **Custom Edges,** digite uma lista separada de números à comma para usar como *bordas* de lixo na lista de texto separada da **Comma + Comma.**  Os valores marcam o ponto que divide os caixotes, portanto, se escrever um valor de borda de caixote, dois caixotes serão gerados; se escrever dois valores de borda de caixote, serão gerados três caixotes, etc.

    Os valores devem ser classificados para que os caixotes sejam criados, dos mais baixos aos mais altos.

10. **Colunas de etiquetas como categóricas:** Selecione esta opção para indicar que as colunas quantificadas devem ser manuseadas como variáveis categóricas.

11. Submeta o oleoduto.

### <a name="results"></a>Resultados

O módulo [De dados do grupo em Bins](group-data-into-bins.md) devolve um conjunto de dados no qual cada elemento foi bloqueado de acordo com o modo especificado. 

Também devolve uma **transformação de Binning,** que é uma função que pode ser passada para o módulo [de Transformação de Aplicação](apply-transformation.md) para deter novas amostras de dados usando o mesmo modo de fixação e parâmetros.  

> [!TIP]
> Lembre-se, se utilizar os seus dados de treino, deve utilizar o mesmo método de fixação nos dados que utiliza para testar e fazer a previsão. Isto inclui o método de fixação, as localizações do caixote do lixo e as larguras do caixote do lixo. 
> 
> Para garantir que os dados são sempre transformados utilizando o mesmo método de fixação, recomendamos que guarde transformações de dados úteis e, em seguida, aplique-os a outros conjuntos de dados, utilizando o módulo [de Transformação de Aplicação.](apply-transformation.md)

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
