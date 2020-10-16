---
title: 'Dados do grupo em bins: referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo De Dados do Grupo em Bins para agrupar números ou alterar a distribuição de dados contínuos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/13/2020
ms.openlocfilehash: 392cb9b4c2ded1b98b79ce8dcd780ac59e96b78a
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998477"
---
# <a name="group-data-into-bins-module"></a>Dados do grupo no módulo Bins

Este artigo descreve como utilizar o módulo De Dados do Grupo em Bins no designer de Machine Learning Azure, para agrupar números ou alterar a distribuição de dados contínuos.

O módulo Des de Grupo em Bins suporta múltiplas opções para a recolha de dados. Pode personalizar como as bordas do caixote estão definidas e como os valores são distribuídos nos caixotes. Por exemplo, pode:  

+ Digite manualmente uma série de valores para servir como limites do caixote do lixo.  
+ Atribua valores aos caixotes utilizando *quânticos,* ou classificações percentil.  
+ Forçar uma distribuição uniforme dos valores nos caixotes.  

## <a name="more-about-binning-and-grouping"></a>Mais sobre o binário e o agrupamento

*A binning* ou agrupar dados (por vezes *chamados de quantificação)* é uma ferramenta importante na preparação de dados numéricos para a aprendizagem automática. É útil em cenários como estes:

+ Uma coluna de números contínuos tem demasiados valores únicos para modelar eficazmente. Assim, atribua automaticamente ou manualmente os valores aos grupos, para criar um conjunto menor de gamas discretas.

+ Pretende substituir uma coluna de números por valores categóricos que representem intervalos específicos.

    Por exemplo, pode querer agrupar valores numa coluna etária especificando gamas personalizadas, tais como 1-15, 16-22, 23-30, e assim por diante para a demografia do utilizador.

+ Um conjunto de dados tem alguns valores extremos, todos bem fora do alcance esperado, e estes valores têm uma influência desmesurada no modelo treinado. Para mitigar o enviesamento no modelo, pode transformar os dados numa distribuição uniforme utilizando o método quantiles.

    Com este método, o módulo Dese de Grupo em Bins determina as localizações ideais do caixote do lixo e as larguras do caixote do lixo para garantir que aproximadamente o mesmo número de amostras caia em cada caixote. Em seguida, dependendo do método de normalização que escolher, os valores nas caixas são transformados em percentiles ou mapeados para um número de lixo.

### <a name="examples-of-binning"></a>Exemplos de vinculação

O diagrama seguinte mostra a distribuição de valores numéricos antes e depois de se ligar ao método *quantiles.* Note que, em comparação com os dados brutos à esquerda, os dados foram ligados e transformados numa escala unit-normal.  

> [!div class="mx-imgBorder"]
> ![Visualização do resultado](media/module/group-data-into-bins-result-example.png)

Como há tantas formas de agrupar dados, todos personalizáveis, recomendamos que experimente com diferentes métodos e valores. 

## <a name="how-to-configure-group-data-into-bins"></a>Como configurar dados do grupo em bins

1. Adicione o módulo **de dados de grupo em bins** ao seu pipeline no designer. Pode encontrar este módulo na categoria Transformação de **Dados.**

2. Ligue o conjunto de dados que tem dados numéricos ao caixote do lixo. A quantificação só pode ser aplicada a colunas que contenham dados numéricos. 

    Se o conjunto de dados contiver colunas não numéricas, utilize as [Colunas Selecionadas no](select-columns-in-dataset.md) módulo Dataset para selecionar um subconjunto de colunas para trabalhar.

3. Especifique o modo de ligação. O modo de ligação determina outros parâmetros, por isso certifique-se de selecionar primeiro a opção **modo De Ligação.** São suportados os seguintes tipos de vinculação:

    - **Quantiles**: O método quântil atribui valores a caixotes com base em fileiras percentil. Este método também é conhecido como igual binário de altura.

    - **Largura igual**: Com esta opção, deve especificar o número total de caixotes. Os valores da coluna de dados são colocados nas caixas de modo a que cada caixa tenha o mesmo intervalo entre valores de arranque e final. Como resultado, alguns caixotes podem ter mais valores se os dados forem agrupados em torno de um determinado ponto.

    - **Arestas personalizadas**: Pode especificar os valores que começam cada caixote. O valor da borda é sempre o limite inferior do caixote. 
    
      Por exemplo, assuma que quer agrupar valores em dois caixotes. Um terá valores superiores a 0, e um terá valores inferiores ou iguais a 0. Neste caso, para as bordas do caixote do lixo, introduz-se **0** na **lista de bordas de contentores separadas pela Vírgula.** A saída do módulo será 1 e 2, indicando o índice de caixa para cada valor de linha. Note que a lista de valor separada por vírgula deve estar numa ordem ascendente, tais como 1, 3, 5, 7.
    
    > [!Note]
    > O modo *Entropy MDL* é definido em Studio (clássico) e não há nenhum pacote de código aberto correspondente que possa ser alavancado para suportar no Designer ainda.        

4. Se estiver a utilizar os modos de ligação **Quantiles** e **Equal Width,** utilize a opção **Número de caixotes** para especificar quantos caixotes, ou *quânticos,* pretende criar.

5. Para **que as colunas se descodem,** utilize o seletor de colunas para escolher as colunas que têm os valores que pretende utilizar. As colunas devem ser um tipo de dados numéricos.

    A mesma regra de vinculação é aplicada a todas as colunas aplicáveis que escolher. Se precisar de colocar algumas colunas utilizando um método diferente, utilize uma instância separada dos Dados do Grupo no módulo Bins para cada conjunto de colunas.

    > [!WARNING]
    > Se escolher uma coluna que não seja um tipo permitido, é gerado um erro de tempo de execução. O módulo retorna um erro assim que encontra qualquer coluna de um tipo não permitido. Se tiver um erro, reveja todas as colunas selecionadas. O erro não lista todas as colunas inválidas.

6. Para **o modo saída,** indique como pretende descoduar os valores quantificizados:

    + **Apêndice**: Cria uma nova coluna com os valores ligados e anexa-a à tabela de entrada.

    + **Inplace**: Substitui os valores originais pelos novos valores no conjunto de dados.

    + **ResultadoSOnly**: Devolve apenas as colunas de resultados.

7. Se selecionar o modo de ligação **quantiles,** utilize a opção de **normalização quântil** para determinar como os valores são normalizados antes de classificar em quantiles. Note que a normalização dos valores transforma os valores mas não afeta o número final de caixotes.

    São suportados os seguintes tipos de normalização:

    + **Por cento**: Os valores são normalizados dentro do intervalo [0,100].

    + **PQuantile**: Os valores estão normalizados dentro do intervalo [0,1].

    + **QuantileIndex**: Os valores são normalizados dentro do intervalo [1,número de caixotes].

8. Se escolher a opção **Bordas Personalizadas, insira** uma lista de *números separados* por vírgula para usar como bordas de lixo na **lista separada da Vírgula da** caixa de texto das bordas do caixote do lixo. 

    Os valores marcam o ponto que divide os caixotes. Por exemplo, se introduzir um valor de borda de um caixote, dois caixotes serão gerados. Se introduzir dois valores de borda do caixote do lixo, serão gerados três caixotes.

    Os valores devem ser classificados na ordem que os caixotes são criados, do mais baixo ao mais alto.

10. Selecione as colunas Tag como opção **categórica** para indicar que as colunas quânticas devem ser manuseadas como variáveis categóricas.

11. Envie o oleoduto.

## <a name="results"></a>Results

O módulo Dese de Grupo em Bins devolve um conjunto de dados no qual cada elemento foi ligado de acordo com o modo especificado. 

Também devolve uma *transformação de binário.* Esta função pode ser transmitida ao módulo [De Transformação Aplicada](apply-transformation.md) para repor novas amostras de dados utilizando o mesmo modo de ligação e parâmetros.  

> [!TIP]
> Se utilizar a vinculação nos seus dados de treino, deve utilizar o mesmo método de vinculação em dados que utiliza para testar e previsão. Também deve utilizar os mesmos locais do caixote do lixo e larguras do lixo. 
> 
> Para garantir que os dados são sempre transformados utilizando o mesmo método de binário, recomendamos que guarde transformações de dados úteis. Em seguida, aplique-os a outros conjuntos de dados utilizando o módulo [De Transformação De Aplicar.](apply-transformation.md)

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
