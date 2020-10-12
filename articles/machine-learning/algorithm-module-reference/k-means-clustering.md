---
title: 'K-Significa Clustering: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de clustering K-Means na Aprendizagem da Máquina Azure para treinar modelos de agrupamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/04/2020
ms.openlocfilehash: 97cadfb8f5004cfd2701335172d4416c64f05259
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907874"
---
# <a name="module-k-means-clustering"></a>Módulo: K-Significa Clustering

Este artigo descreve como usar o módulo *de clustering K-Means* no designer de aprendizagem automática Azure para criar um modelo de clustering K-means destreinado. 
 
K-means é um dos algoritmos de aprendizagem mais simples e conhecidos *sem supervisão.* Pode utilizar o algoritmo para uma variedade de tarefas de aprendizagem automática, tais como: 

* [Detetando dados anormais.](https://msdn.microsoft.com/magazine/jj891054.aspx)
* Agrupamento de documentos de texto.
* Analisar conjuntos de dados antes de utilizar outros métodos de classificação ou regressão. 

Para criar um modelo de agrupamento, você:

* Adicione este módulo ao seu oleoduto.
* Ligue um conjunto de dados.
* Definir parâmetros, como o número de clusters que você espera, a métrica de distância a usar na criação dos clusters, e assim por diante. 
  
Depois de configurar os hiperparmetros do módulo, liga o modelo não treinado ao [Modelo de Agrupamento de Comboios.](train-clustering-model.md) Como o algoritmo K-significa é um método de aprendizagem não supervisionado, uma coluna de etiqueta é opcional. 

+ Se os seus dados incluir uma etiqueta, pode utilizar os valores da etiqueta para orientar a seleção dos clusters e otimizar o modelo. 

+ Se os seus dados não tão bem marcadores, o algoritmo cria clusters que representam possíveis categorias, com base apenas nos dados.  

##  <a name="understand-k-means-clustering"></a>Compreenda o agrupamento K-significa
 
Em geral, o agrupamento utiliza técnicas iterativas para agrupar casos num conjunto de dados em clusters que possuem características semelhantes. Estes agrupamentos são úteis para explorar dados, identificar anomalias nos dados e, eventualmente, para fazer previsões. Os modelos de agrupamento também podem ajudá-lo a identificar relações num conjunto de dados que pode não derivar logicamente através da navegação ou da simples observação. Por estas razões, o agrupamento é frequentemente usado nas fases iniciais das tarefas de machine learning, para explorar os dados e descobrir correlações inesperadas.  
  
 Ao configurar um modelo de agrupamento utilizando o método K-significa, deve especificar um número de alvo *k* que indique o número de *centrosids* que deseja no modelo. O centroid é um ponto que é representativo de cada cluster. O algoritmo K-significa atribuir cada ponto de dados de entrada a um dos clusters minimizando a soma do cluster de quadrados. 
 
Quando processa os dados de treino, o algoritmo K-significa começa com um conjunto inicial de centrosids escolhidos aleatoriamente. Os centrosids servem como pontos de partida para os clusters, e aplicam o algoritmo de Lloyd para refinar iterativamente as suas localizações. O algoritmo K-significa que para de construir e refinar clusters quando satisfaz uma ou mais destas condições:  
  
-   Os centrosids estabilizam, o que significa que as atribuições de cluster para pontos individuais já não mudam e o algoritmo convergiu para uma solução.  
  
-   O algoritmo completou a execução do número especificado de iterações.  
  
 Depois de completar a fase de treino, utiliza o módulo [Desatribuição de Dados a Clusters](assign-data-to-clusters.md) para atribuir novos casos a um dos clusters que encontrou usando o algoritmo K-significa. Realiza-se a atribuição de cluster calculando a distância entre o novo caso e o centroíid de cada cluster. Cada novo caso é atribuído ao cluster com o centroid mais próximo.  

## <a name="configure-the-k-means-clustering-module"></a>Configure o módulo de agrupamento K-Means
  
1.  Adicione o módulo **de agrupamento K-Significa** ao seu oleoduto.  
  
2.  Para especificar como pretende que o modelo seja treinado, selecione a opção **Modo De Formação Criar.**  
  
    -   **Parâmetro único**: Se conhecer os parâmetros exatos que pretende utilizar no modelo de agrupamento, pode fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **o número de centrosids,** digite o número de clusters com que o algoritmo comece.  
  
     O modelo não é garantido para produzir exatamente este número de aglomerados. O algoritmo começa com este número de pontos de dados e iterates para encontrar a configuração ideal. Pode consultar o [código fonte de sklearn](https://github.com/scikit-learn/scikit-learn/blob/fd237278e/sklearn/cluster/_kmeans.py#L1069).
  
4.  As propriedades **A inicialização** é usada para especificar o algoritmo que é usado para definir a configuração inicial do cluster.  
  
    -   **Primeiro N**: Alguns números iniciais de pontos de dados são escolhidos a partir do conjunto de dados e utilizados como meios iniciais. 
    
         Este método também é chamado *de método Forgy.*  
  
    -   **Aleatório**: O algoritmo coloca aleatoriamente um ponto de dados num cluster e, em seguida, calcula a média inicial para ser o centroíid dos pontos atribuídos aleatoriamente do cluster. 

         Este método também é chamado de método *de partição aleatória.*  
  
    -   **K-Means++** Este é o método padrão para inicializar agrupamentos.  
  
         O algoritmo **K-means++** foi proposto em 2007 por David Arthur e Sergei Vassilvitskii para evitar o mau agrupamento pelo algoritmo k-means padrão. **K-means++** melhora em k-means padrão usando um método diferente para escolher os centros de cluster iniciais.  
  
    
5.  Para **sementes de número aleatório,** digite opcionalmente um valor a utilizar como semente para a inicialização do cluster. Este valor pode ter um efeito significativo na seleção do cluster.  
  
6.  Para **Métrica**, escolha a função a utilizar para medir a distância entre os vetores de cluster, ou entre novos pontos de dados e o centroid escolhido aleatoriamente. A Azure Machine Learning suporta as seguintes métricas de distância do cluster:  
  
    -   **Euclideano**: A distância euclidana é comumente usada como uma medida de dispersão de aglomerados para o agrupamento de meios K. Esta métrica é preferida porque minimiza a distância média entre os pontos e os centrosids.
  
7.  Para **iterações,** digite o número de vezes que o algoritmo deve iterar sobre os dados de treino antes de finalizar a seleção de centrosids.  
  
     Pode ajustar este parâmetro para equilibrar a precisão em termos de tempo de treino.  
  
8.  Para **o modo de etiqueta de atribuição**, escolha uma opção que especifique como uma coluna de etiqueta, se estiver presente no conjunto de dados, deve ser manuseada.  
  
     Como o agrupamento K-significa que o agrupamento é um método de aprendizagem automática não supervisionado, os rótulos são opcionais. No entanto, se o seu conjunto de dados já tiver uma coluna de etiquetas, pode utilizar esses valores para orientar a seleção dos clusters, ou pode especificar que os valores serão ignorados.  
  
    -   **Ignore a coluna da etiqueta**: Os valores na coluna da etiqueta são ignorados e não são utilizados na construção do modelo.
  
    -   **Preencha os valores em falta**: Os valores da coluna de etiqueta são utilizados como características para ajudar a construir os clusters. Se faltar alguma etiqueta, o valor é imputado utilizando outras funcionalidades.  
  
    -   **Sobrepor-se do mais próximo ao centro**: Os valores da coluna de etiqueta são substituídos por valores de etiquetas previstos, utilizando a etiqueta do ponto mais próximo do centroid atual.  

8.  Selecione a opção **de normalização das funcionalidades** se quiser normalizar as funcionalidades antes do treino.
  
     Se aplicar a normalização, antes do treino, os pontos de dados são normalizados `[0,1]` por MinMaxNormalizer.

10. Treine o modelo.  
  
    -   Se definir **Criar modo de treinador** a único **parâmetro,** adicione um conjunto de dados marcado e treine o modelo utilizando o módulo [Modelo de Agrupamento de Comboios.](train-clustering-model.md)  
  
## <a name="results"></a>Results

Depois de terminar de configurar e treinar o modelo, tem um modelo que pode usar para gerar pontuações. No entanto, existem múltiplas formas de treinar o modelo, e várias formas de ver e usar os resultados: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Capture uma imagem do modelo no seu espaço de trabalho

Se usou o módulo [modelo de agrupamento de comboios:](train-clustering-model.md)

1. Selecione o módulo **modelo de agrupamento de comboios** e abra o painel direito.

2. Selecione o separador **Saídas.** Selecione o ícone **do conjunto de dados registo** para guardar uma cópia do modelo treinado.

O modelo guardado representa os dados de treino no momento em que guardou o modelo. Se atualizar mais tarde os dados de treino utilizados no pipeline, este não atualiza o modelo guardado. 

### <a name="see-the-clustering-result-dataset"></a>Consulte o conjunto de dados de resultados de clustering 

Se usou o módulo [modelo de agrupamento de comboios:](train-clustering-model.md)

1. Clique com o botão direito no módulo **modelo de agrupamento de comboios.**

2. **Selecione Visualizar**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Dicas para gerar o melhor modelo de clustering  

Sabe-se que o processo *de sementeira* que é usado durante o agrupamento pode afetar significativamente o modelo. Sementeira significa a colocação inicial de pontos em potenciais centrosids.
 
Por exemplo, se o conjunto de dados contiver muitos outliers, e um outlier é escolhido para semear os clusters, nenhum outro ponto de dados se encaixaria bem com esse cluster, e o cluster poderia ser um singleton. Ou seja, pode ter apenas um ponto.  
  
Pode evitar este problema de várias maneiras:  
  
-   Mude o número de centrosids e experimente vários valores de sementes.  
  
-   Crie vários modelos, variando a métrica ou itando mais.  
  
Em geral, com modelos de clustering, é possível que qualquer configuração dada resulte num conjunto de clusters otimizado localmente. Por outras palavras, o conjunto de clusters que é devolvido pelo modelo adequa-se apenas aos pontos de dados atuais e não é generalizável a outros dados. Se utilizar uma configuração inicial diferente, o método K-significa pode encontrar uma configuração diferente, superior. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
