---
title: 'Clustering de K-Means: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de Clustering de K-Means no serviço do Azure Machine Learning para criar modelos de clusters.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e9b7c8f2cf86245322679198b84b50d2c5edce8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464672"
---
# <a name="module-k-means-clustering"></a>Módulo: Clustering K-Means

Este artigo descreve como utilizar o *Clustering de K-Means* módulo no Azure Machine Learning Studio para criar um modelo de clustering de K-means inoportuno. 
 
K-means é a mais simples e mais conhecido *supervisionados* algoritmos de aprendizagem. Pode usar o algoritmo para uma variedade de machine learning tarefas, tais como: 

* [Detectando dados anormais](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Clustering de documentos de texto.
* Analisar conjuntos de dados antes de utilizar outros métodos de classificação ou regressão. 

Para criar um modelo de clustering,:

* Adicione este módulo à sua experimentação.
* Ligar um conjunto de dados.
* Definir parâmetros, como o número de clusters de se esperar, a métrica de distância para utilizar na criação de clusters e assim por diante. 
  
Depois de configurar os módulo hiperparâmetros, ligue-se o modelo inoportuno para o [Train Clustering Model](train-clustering-model.md). Uma vez que o algoritmo K-means é um método de aprendizado não supervisionado, uma coluna de etiqueta é opcional. 

+ Se seus dados incluem uma etiqueta, pode utilizar os valores de etiqueta a orientar a seleção dos clusters e otimizar o modelo. 

+ Se os dados não tiverem nenhum rótulo, o algoritmo cria clusters que representa as categorias possíveis, com base apenas nos dados.  

##  <a name="understand-k-means-clustering"></a>Compreender o clustering de K-means
 
Em geral, clustering utiliza técnicas iterativas para casos de grupo num conjunto de dados em clusters que possuem características semelhantes. Esses agrupamentos são úteis para explorar dados, identificar anomalias nos dados e, eventualmente, para fazer previsões. Clustering modelos também pode ajudar a identificar as relações num conjunto de dados que não pode derivar logicamente pela observação de navegação ou simple. Por esses motivos, clustering é frequentemente utilizado nas fases iniciais de machine learning tarefas, para explorar os dados e descobrir correlações inesperadas.  
  
 Quando configurar um modelo de clustering usando o método de K-means, tem de especificar um número de destino *k* que indica o número de *centróides* mesmo no modelo. O centroide é um ponto que seja representativo da cada cluster. O algoritmo K-means atribui cada ponto de dados de entrada para um dos clusters, minimizando a soma de dentro do cluster de quadrados. 
 
Quando processa os dados de treinamento, o algoritmo K-means começa com um conjunto inicial de centróides escolhidos aleatoriamente. Centróides servem como pontos de partida para os clusters e aplicam-se algoritmo de Lloyd iterativamente refinar seus locais. O algoritmo K-means para criar e refinar os clusters quando ele cumpre um ou mais dos seguintes condições:  
  
-   Os centroides estabilizar, o que significa que já não altere as atribuições de cluster para os pontos individuais e o algoritmo foi convergido com uma solução.  
  
-   O algoritmo foi concluído com o número especificado de iterações.  
  
 Depois de concluir a fase de treinamento, utilize o [dados de atribuir a Clusters](assign-data-to-clusters.md) módulo para atribuir novos casos a um dos clusters que encontra utilizando o algoritmo K-means. Realizar atribuição de cluster por computar a distância entre o novo incidente e o centroide de cada cluster. Cada novo incidente está atribuído ao cluster com o centroide mais próximo.  

## <a name="configure-the-k-means-clustering-module"></a>Configurar o módulo de Clustering de K-Means
  
1.  Adicionar a **Clustering de K-Means** módulo à sua experimentação.  
  
2.  Para especificar como pretende que o modelo preparado, selecione o **modo de instrutor de criação** opção.  
  
    -   **Único parâmetro**: Se souber os parâmetros exatos de que pretende utilizar no modelo de clustering, pode fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **número de Centróides**, escreva o número de clusters que pretende que o algoritmo de começar.  
  
     O modelo não é garantido para produzir exatamente este número de clusters. O algoritmo começa com este número de pontos de dados e faz a iteração para encontrar a configuração ideal.  
  
4.  As propriedades **inicialização** é utilizado para especificar o algoritmo que é utilizado para definir a configuração de cluster inicial.  
  
    -   **N primeiros**: Um número inicial de pontos de dados é escolhido a partir do conjunto de dados e utilizado como uma forma inicial. 
    
         Esse método também é chamado de *método Forgy*.  
  
    -   **Aleatório**: O algoritmo coloca aleatoriamente um ponto de dados num cluster e, em seguida, calcula a média inicial para ser o centroide de cluster de aleatoriamente atribuídos pontos. 

         Esse método também é chamado de *partição aleatória* método.  
  
    -   **K-Means++**: Este é o método predefinido para inicializar clusters.  
  
         O **K-means + +** algoritmo foi proposto no 2007, por David Arthur e Sergei Vassilvitskii para evitar o clustering ruim pelo algoritmo K-means padrão. **K-means + +** aprimora padrão K-means utilizando um método diferente para a escolha de centros de cluster inicial.  
  
    
5.  Para **seed número aleatório**, opcionalmente, escreva um valor a utilizar como a semente para a inicialização de cluster. Este valor pode ter um impacto significativo na seleção de cluster.  
  
6.  Para **métrica**, escolha a função a utilizar para medir a distância entre os vetores de cluster ou entre novos pontos de dados e o centroide escolhido aleatoriamente. O Azure Machine Learning suporta as seguintes métricas de distância do cluster:  
  
    -   **Euclidiana**: A distância Euclidiana é geralmente utilizada como uma medida de dispersão de cluster para clustering de K-means. Esta métrica é preferida porque minimiza a distância média entre pontos e as de centróides.
  
7.  Para **iterações**, escreva o número de vezes que o algoritmo deve iterar sobre os dados de treinamento antes de ele finaliza a seleção de centróides.  
  
     Pode ajustar este parâmetro para precisão do saldo relativamente ao tempo de treinamento.  
  
8.  Para **modo de etiqueta de atribuir**, escolha uma opção que especifica como uma coluna de etiqueta, se estiver presente no conjunto de dados, deve ser tratada.  
  
     Como o clustering de K-means é uma método de aprendizagem não supervisionada, as etiquetas são opcionais. No entanto, se o conjunto de dados já tem uma coluna de etiqueta, pode utilizar esses valores para orientar a seleção dos clusters, ou pode especificar que os valores ignorados.  
  
    -   **Ignorar a coluna de etiqueta**: Os valores da coluna de etiqueta são ignorados e não são usados na criação do modelo.
  
    -   **Preencher valores em falta**: Os valores de coluna de etiqueta são utilizados como recursos para ajudar a criar os clusters. Se nenhuma linha está em falta uma etiqueta, o valor é imputed através de outros recursos.  
  
    -   **Substituir mais próximo ao centro de**: Os valores de coluna de etiqueta são substituídos por valores de etiqueta prevista, utilizar a etiqueta do ponto de que mais se aproxima o centroide atual.  

8.  Selecione o **normalizar funcionalidades** opção se pretende normalizar funcionalidades antes de treinamento.
  
     Se aplicar a normalização, antes de treinamento, os pontos de dados são normalizados para `[0,1]` por MinMaxNormalizer.

10. Prepare o modelo.  
  
    -   Se definir **modo de instrutor de criação** para **único parâmetro**, adicione um conjunto de dados marcado e preparar o modelo utilizando o [treinar o modelo de agrupamento](train-clustering-model.md) módulo.  
  
### <a name="results"></a>Resultados

Depois de terminar a configuração e o modelo de formação, tem um modelo que pode utilizar para gerar as pontuações. No entanto, existem várias formas para preparar o modelo e várias formas de ver e utilizar os resultados: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Capturar um instantâneo do modelo na sua área de trabalho

Se tiver utilizado o [Train Clustering Model](train-clustering-model.md) módulo:

1. Com o botão direito a **Train Clustering Model** módulo.

2. Selecione **modelo Trained**e, em seguida, selecione **guardar como modelo preparado**.

O modelo guardado representa os dados de treinamento no momento em que guardou o modelo. Se atualizar os dados de treinamento usados na experimentação mais tarde, não atualiza o modelo guardado. 

#### <a name="see-the-clustering-result-dataset"></a>Ver o conjunto de dados de resultado clustering 

Se tiver utilizado o [Train Clustering Model](train-clustering-model.md) módulo:

1. Com o botão direito a **Train Clustering Model** módulo.

2. Selecione **conjunto de dados de resultados**e, em seguida, selecione **Visualize**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Dicas para gerar o melhor clustering do modelo  

Sabe-se que o *seeding* processo que é utilizada durante o clustering pode afetar significativamente o modelo. Seeding significa que a colocação inicial de pontos em potenciais de centróides.
 
Por exemplo, se o conjunto de dados contém valores atípicos muitos e exceção é escolhida para efetuar o seeding os clusters, não houver outros pontos de dados seriam encaixa bem com esse cluster e o cluster pode ser um singleton. Ou seja, ele pode ter apenas um ponto.  
  
Pode evitar esse problema de duas formas:  
  
-   Alterar o número de centroides e tente vários valores de seed.  
  
-   Crie vários modelos, variando a métrica ou fazendo a iteração mais.  
  
Em geral, com o clustering de modelos, é possível que qualquer determinada configuração resultará num conjunto otimizado localmente de clusters. Em outras palavras, o conjunto de clusters, que é devolvido pelo modelo adequado para apenas os pontos de dados atual e não generalizable a outros dados. Se utilizar uma configuração inicial diferente, o método de K-means talvez uma configuração diferente, talvez superior. 
