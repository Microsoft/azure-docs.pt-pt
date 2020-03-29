---
title: 'K-Significa Clustering: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de agrupamento K-Means no Azure Machine Learning para treinar modelos de agrupamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 9606768288cc74afc24491149eb471944f45e2dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921169"
---
# <a name="module-k-means-clustering"></a>Módulo: Clustering K-Means

Este artigo descreve como usar o módulo *de clustering K-Means* em Azure Machine Learning designer (pré-visualização) para criar um modelo de clustering de k-means não treinado. 
 
K-means é um dos algoritmos de aprendizagem mais simples e mais conhecidos *sem supervisão.* Você pode usar o algoritmo para uma variedade de tarefas de aprendizagem automática, tais como: 

* [Deteção de dados anormais.](https://msdn.microsoft.com/magazine/jj891054.aspx)
* Agrupar documentos de texto.
* Analisar conjuntos de dados antes de utilizar outros métodos de classificação ou regressão. 

Para criar um modelo de agrupamento, você:

* Adicione este módulo ao seu oleoduto.
* Ligue um conjunto de dados.
* Defina parâmetros, como o número de clusters que espera, a métrica da distância a utilizar na criação dos clusters, e assim por diante. 
  
Depois de configurar os hiperparâmetros do módulo, liga o modelo não treinado ao Modelo de Agrupamento de [Comboios](train-clustering-model.md). Como o algoritmo K-significa é um método de aprendizagem não supervisionado, uma coluna de etiquetas é opcional. 

+ Se os seus dados incluirem uma etiqueta, pode utilizar os valores de etiqueta para orientar a seleção dos clusters e otimizar o modelo. 

+ Se os seus dados não têm etiqueta, o algoritmo cria clusters que representam possíveis categorias, com base apenas nos dados.  

##  <a name="understand-k-means-clustering"></a>Compreender o agrupamento k-significa
 
Em geral, o agrupamento utiliza técnicas iterativas para agrupar casos num conjunto de dados em clusters que possuem características semelhantes. Estes agrupamentos são úteis para explorar dados, identificar anomalias nos dados e, eventualmente, fazer previsões. Os modelos de agrupamento também podem ajudá-lo a identificar relações num conjunto de dados que você pode não derivar logicamente através da navegação ou de observação simples. Por estas razões, o agrupamento é frequentemente usado nas fases iniciais das tarefas de aprendizagem automática, para explorar os dados e descobrir correlações inesperadas.  
  
 Quando configurar um modelo de agrupamento utilizando o método K-means, deve especificar um número de destino *k* que indique o número de *centrosides* que deseja no modelo. O centroid é um ponto que é representativo de cada cluster. O algoritmo K-significa que cada dados que chegam apontam para um dos clusters minimizando a soma dentro do cluster dos quadrados. 
 
Quando processa os dados de treino, o algoritmo K-means começa com um conjunto inicial de centrosides escolhidos aleatoriamente. Os centrosides servem como pontos de partida para os clusters, e aplicam o algoritmo de Lloyd para refinar iterativamente as suas localizações. O algoritmo K-significa que para de construir e refinar clusters quando cumpre uma ou mais destas condições:  
  
-   Os centrosides estabilizam, o que significa que as atribuições de cluster para pontos individuais já não mudam e o algoritmo convergiu para uma solução.  
  
-   O algoritmo completou o número especificado de iterações.  
  
 Depois de completar a fase de treino, utiliza o módulo De Atribuição de [Dados para Clusters](assign-data-to-clusters.md) para atribuir novos casos a um dos clusters que encontrou utilizando o algoritmo K-means. Executa a atribuição de cluster sintetizando a distância entre o novo caso e o centroid de cada cluster. Cada novo caso é atribuído ao cluster com o centroid mais próximo.  

## <a name="configure-the-k-means-clustering-module"></a>Configure o módulo de agrupamento K-Means
  
1.  Adicione o módulo **de agrupamento K-Means** ao seu pipeline.  
  
2.  Para especificar como pretende que o modelo seja treinado, selecione a opção **modo Criar.**  
  
    -   **Parâmetro único**: Se souber os parâmetros exatos que pretende utilizar no modelo de agrupamento, pode fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **número de centrosides,** escreva o número de clusters com os que pretende que o algoritmo comece.  
  
     O modelo não é garantido para produzir exatamente este número de clusters. O algoritmo começa com este número de pontos de dados e iterates para encontrar a configuração ideal.  
  
4.  As propriedades **A inicialização** é usada para especificar o algoritmo que é usado para definir a configuração inicial do cluster.  
  
    -   **Primeiro N**: Alguns números iniciais de pontos de dados são escolhidos a partir do conjunto de dados e utilizados como os meios iniciais. 
    
         Este método também é chamado de *método Forgy*.  
  
    -   **Random**: O algoritmo coloca aleatoriamente um ponto de dados num cluster e, em seguida, calcula a média inicial de ser o centroide dos pontos atribuídos aleatoriamente do cluster. 

         Este método também é chamado de método *de partição aleatória.*  
  
    -   **K-Means++**: Este é o método padrão para inicializar clusters.  
  
         O algoritmo **K-means++** foi proposto em 2007 por David Arthur e Sergei Vassilvitskii para evitar o mau agrupamento pelo algoritmo padrão k-means. **K-means++** melhora os meios K padrão utilizando um método diferente para escolher os centros de cluster iniciais.  
  
    
5.  Para **sementes aleatórias,** digite opcionalmente um valor a utilizar como semente para a inicialização do cluster. Este valor pode ter um efeito significativo na seleção do cluster.  
  
6.  Para **Métrica,** escolha a função a utilizar para medir a distância entre os vetores de cluster, ou entre novos pontos de dados e o centroid escolhido aleatoriamente. O Azure Machine Learning suporta as seguintes métricas de distância de cluster:  
  
    -   **Euclideano**: A distância euclidiana é comumente usada como medida de dispersão de aglomerados para agrupamento si- K. Esta métrica é preferida porque minimiza a distância média entre os pontos e os centrosides.
  
7.  Para **iterações,** digite o número de vezes que o algoritmo deve iterar sobre os dados de treino antes de finalizar a seleção de centrosides.  
  
     Pode ajustar este parâmetro para equilibrar a precisão em relação ao tempo de treino.  
  
8.  Para **o modo de etiqueta de atribuição,** escolha uma opção que especifique como uma coluna de etiqueta, se estiver presente no conjunto de dados, deve ser manuseada.  
  
     Como o agrupamento k-significa que o agrupamento é um método de aprendizagem automática não supervisionado, os rótulos são opcionais. No entanto, se o seu conjunto de dados já tiver uma coluna de etiquetas, pode utilizar esses valores para orientar a seleção dos clusters, ou pode especificar que os valores são ignorados.  
  
    -   **Ignore**a coluna do rótulo : Os valores na coluna de etiquetas são ignorados e não são utilizados na construção do modelo.
  
    -   **Preencher valores em falta**: Os valores da coluna de etiquetasão utilizados como características para ajudar a construir os clusters. Se faltar em nenhuma etiqueta, o valor é imputado utilizando outras funcionalidades.  
  
    -   **Sobreposição do mais próximo ao centro**: Os valores da coluna de etiqueta são substituídos por valores de etiqueta previstos, utilizando o rótulo do ponto mais próximo do atual centroid.  

8.  Selecione a opção **Normalize** se pretender normalizar as funcionalidades antes do treino.
  
     Se aplicar a normalização, antes do treino, `[0,1]` os pontos de dados são normalizados pelo MinMaxNormalizer.

10. Treine o modelo.  
  
    -   Se definir o **modo Criar** modo de treinador para um parâmetro **único,** adicione um conjunto de dados marcado e treine o modelo utilizando o módulo modelo de [clustering](train-clustering-model.md) de comboios.  
  
## <a name="results"></a>Resultados

Depois de terminar de configurar e treinar o modelo, tem um modelo que pode usar para gerar pontuações. No entanto, existem múltiplas formas de treinar o modelo, e várias formas de ver e usar os resultados: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Capture uma foto do modelo no seu espaço de trabalho

Se utilizou o módulo modelo de [clustering](train-clustering-model.md) de comboios:

1. Selecione o módulo modelo de clustering de **comboios** e abra o painel certo.

2. Selecione o separador **Saídas.** Selecione o ícone do conjunto de **dados Do Registo** para guardar uma cópia do modelo treinado.

O modelo guardado representa os dados de treino no momento em que guardou o modelo. Se atualizar mais tarde os dados de treino utilizados no pipeline, não atualiza o modelo guardado. 

### <a name="see-the-clustering-result-dataset"></a>Consulte o conjunto de dados do resultado do agrupamento 

Se utilizou o módulo modelo de [clustering](train-clustering-model.md) de comboios:

1. Clique no módulo modelo de **clustering** de comboios.

2. **Selecione Visualizar**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Dicas para gerar o melhor modelo de agrupamento  

Sabe-se que o processo de *sementer* que é usado durante o agrupamento pode afetar significativamente o modelo. Sementeing significa a colocação inicial de pontos em potenciais centrosides.
 
Por exemplo, se o conjunto de dados contiver muitos outliers, e um outlier for escolhido para semear os clusters, nenhum outro ponto de dados se encaixaria bem com esse cluster, e o cluster poderia ser um singleton. Ou seja, pode ter apenas um ponto.  
  
Pode evitar este problema de várias maneiras:  
  
-   Altere o número de centrosides e experimente vários valores de sementes.  
  
-   Crie vários modelos, variando a métrica ou iterando mais.  
  
Em geral, com modelos de agrupamento, é possível que qualquer configuração determinada resulte num conjunto de clusters otimizado localmente. Por outras palavras, o conjunto de clusters que é devolvido pelo modelo adequa-se apenas aos pontos de dados atuais e não é generalizável para outros dados. Se utilizar uma configuração inicial diferente, o método K-means pode encontrar uma configuração diferente, superior. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
