---
title: 'Clustering K-means: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de clustering K-means no Azure Machine Learning para treinar modelos de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 6e2fa96584570e5837c4367c8be4701b7398fb0f
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152215"
---
# <a name="module-k-means-clustering"></a>Módulo: clustering K-means

Este artigo descreve como usar o módulo *de clustering K-Means* em Azure Machine Learning designer (pré-visualização) para criar um modelo de clustering de k-means não treinado. 
 
K-means é um dos algoritmos de aprendizagem mais simples e mais conhecidos *sem supervisão.* Você pode usar o algoritmo para uma variedade de tarefas de aprendizado de máquina, como: 

* [Deteção de dados anormais.](https://msdn.microsoft.com/magazine/jj891054.aspx)
* Documentos de texto em cluster.
* Analisar conjuntos de valores antes de usar outros métodos de classificação ou regressão. 

Para criar um modelo de clustering, você:

* Adicione este módulo ao seu pipeline.
* Conectar um conjunto de uma.
* Defina parâmetros, como o número de clusters que você espera, a métrica de distância a ser usada na criação de clusters e assim por diante. 
  
Depois de configurar os hiperparâmetros do módulo, liga o modelo não treinado ao Modelo de Agrupamento de [Comboios](train-clustering-model.md). Como o algoritmo K-means é um método de aprendizado não supervisionado, uma coluna de rótulo é opcional. 

+ Se seus dados incluírem um rótulo, você poderá usar os valores de rótulo para orientar a seleção dos clusters e otimizar o modelo. 

+ Se os dados não tiverem nenhum rótulo, o algoritmo criará clusters que representam as categorias possíveis, com base apenas nos dados.  

##  <a name="understand-k-means-clustering"></a>Entender o clustering de K-means
 
Em geral, o clustering usa técnicas iterativas para agrupar casos em um conjunto de um em clusters que possuem características semelhantes. Esses agrupamentos são úteis para explorar dados, identificar anomalias nos dados e, eventualmente, para fazer previsões. Os modelos de clustering também podem ajudá-lo a identificar relações em um conjunto de um DataSet que você não pode derivar logicamente por meio de navegação ou observação simples. Por esses motivos, o clustering é geralmente usado nas primeiras fases das tarefas de aprendizado de máquina, para explorar os dados e descobrir correlações inesperadas.  
  
 Quando configurar um modelo de agrupamento utilizando o método K-means, deve especificar um número de destino *k* que indique o número de *centrosides* que deseja no modelo. O centróide é um ponto representativo de cada cluster. O algoritmo K-means atribui cada ponto de dados de entrada a um dos clusters minimizando a soma de quadrados dentro do cluster. 
 
Quando ele processa os dados de treinamento, o algoritmo K-means começa com um conjunto inicial de centróides escolhidos aleatoriamente. As centróides servem como pontos de partida para os clusters e aplicam o algoritmo de Lloyd para refinar seus locais iterativamente. O algoritmo K-means interrompe a criação e refinação de clusters quando ele atende a uma ou mais destas condições:  
  
-   Os centróides estabilizam, o que significa que as atribuições de cluster para pontos individuais não são mais alteradas e o algoritmo convergiu em uma solução.  
  
-   O algoritmo concluiu a execução do número especificado de iterações.  
  
 Depois de completar a fase de treino, utiliza o módulo De Atribuição de [Dados para Clusters](assign-data-to-clusters.md) para atribuir novos casos a um dos clusters que encontrou utilizando o algoritmo K-means. Você executa a atribuição de cluster computando a distância entre o novo caso e o centróide de cada cluster. Cada novo caso é atribuído ao cluster com o centróide mais próximo.  

## <a name="configure-the-k-means-clustering-module"></a>Configurar o módulo de clustering K-means
  
1.  Adicione o módulo **de agrupamento K-Means** ao seu pipeline.  
  
2.  Para especificar como pretende que o modelo seja treinado, selecione a opção **modo Criar.**  
  
    -   **Parâmetro único**: Se souber os parâmetros exatos que pretende utilizar no modelo de agrupamento, pode fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **número de centrosides,** escreva o número de clusters com os que pretende que o algoritmo comece.  
  
     Não há garantia de que o modelo produza exatamente esse número de clusters. O algoritmo começa com esse número de pontos de dados e itera para localizar a configuração ideal.  
  
4.  As propriedades **A inicialização** é usada para especificar o algoritmo que é usado para definir a configuração inicial do cluster.  
  
    -   **Primeiro N**: Alguns números iniciais de pontos de dados são escolhidos a partir do conjunto de dados e utilizados como os meios iniciais. 
    
         Este método também é chamado de *método Forgy*.  
  
    -   **Random**: O algoritmo coloca aleatoriamente um ponto de dados num cluster e, em seguida, calcula a média inicial de ser o centroide dos pontos atribuídos aleatoriamente do cluster. 

         Este método também é chamado de método *de partição aleatória.*  
  
    -   **K-Means++** : Este é o método padrão para inicializar clusters.  
  
         O algoritmo **K-means++** foi proposto em 2007 por David Arthur e Sergei Vassilvitskii para evitar o mau agrupamento pelo algoritmo padrão k-means. **K-means++** melhora os meios K padrão utilizando um método diferente para escolher os centros de cluster iniciais.  
  
    
5.  Para **sementes aleatórias,** digite opcionalmente um valor a utilizar como semente para a inicialização do cluster. Esse valor pode ter um efeito significativo na seleção de cluster.  
  
6.  Para **Métrica,** escolha a função a utilizar para medir a distância entre os vetores de cluster, ou entre novos pontos de dados e o centroid escolhido aleatoriamente. O Azure Machine Learning dá suporte às seguintes métricas de distância do cluster:  
  
    -   **Euclideano**: A distância euclidiana é comumente usada como medida de dispersão de aglomerados para agrupamento si- K. Essa métrica é preferida porque minimiza a distância média entre pontos e as centróides.
  
7.  Para **iterações,** digite o número de vezes que o algoritmo deve iterar sobre os dados de treino antes de finalizar a seleção de centrosides.  
  
     Você pode ajustar esse parâmetro para equilibrar a precisão em relação ao tempo de treinamento.  
  
8.  Para **o modo de etiqueta de atribuição,** escolha uma opção que especifique como uma coluna de etiqueta, se estiver presente no conjunto de dados, deve ser manuseada.  
  
     Como o clustering K-means é um método de aprendizado de máquina não supervisionado, os rótulos são opcionais. No entanto, se o conjunto de seus conjuntos de seus já tiver uma coluna de rótulo, você poderá usar esses valores para orientar a seleção dos clusters ou pode especificar que os valores sejam ignorados.  
  
    -   **Ignore**a coluna do rótulo : Os valores na coluna de etiquetas são ignorados e não são utilizados na construção do modelo.
  
    -   **Preencher valores em falta**: Os valores da coluna de etiquetasão utilizados como características para ajudar a construir os clusters. Se alguma linha estiver sem um rótulo, o valor será imputados usando outros recursos.  
  
    -   **Sobreposição do mais próximo ao centro**: Os valores da coluna de etiqueta são substituídos por valores de etiqueta previstos, utilizando o rótulo do ponto mais próximo do atual centroid.  

8.  Selecione a opção **Normalize** se pretender normalizar as funcionalidades antes do treino.
  
     Se aplicar a normalização, antes do treino, os pontos de dados são normalizados para `[0,1]` pela MinMaxNormalizer.

10. Treine o modelo.  
  
    -   Se definir o **modo Criar** modo de treinador para um parâmetro **único,** adicione um conjunto de dados marcado e treine o modelo utilizando o módulo modelo de [clustering](train-clustering-model.md) de comboios.  
  
### <a name="results"></a>Resultados

Depois de concluir a configuração e o treinamento do modelo, você tem um modelo que pode ser usado para gerar pontuações. No entanto, há várias maneiras de treinar o modelo e várias maneiras de exibir e usar os resultados: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Capturar um instantâneo do modelo em seu espaço de trabalho

Se utilizou o módulo modelo de [clustering](train-clustering-model.md) de comboios:

1. Selecione o módulo modelo de clustering de **comboios** e abra o painel certo.

2. Selecione o separador **Saídas.** Selecione o ícone do conjunto de **dados Do Registo** para guardar uma cópia do modelo treinado.

O modelo salvo representa os dados de treinamento no momento em que você salvou o modelo. Se você atualizar mais tarde os dados de treinamento usados no pipeline, ele não atualizará o modelo salvo. 

#### <a name="see-the-clustering-result-dataset"></a>Consulte o conjunto de conjuntos de resultados de clustering 

Se utilizou o módulo modelo de [clustering](train-clustering-model.md) de comboios:

1. Clique no módulo modelo de **clustering** de comboios.

2. **Selecione Visualizar**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Dicas para gerar o melhor modelo de clustering  

Sabe-se que o processo de *sementer* que é usado durante o agrupamento pode afetar significativamente o modelo. A propagação significa o posicionamento inicial de pontos em possíveis centróides.
 
Por exemplo, se o conjunto de dados contiver muitas exceções e uma exceção for escolhida para propagar os clusters, nenhum outro ponto de dados se ajustará bem com esse cluster, e o cluster poderá ser um singleton. Ou seja, ele pode ter apenas um ponto.  
  
Você pode evitar esse problema de duas maneiras:  
  
-   Altere o número de centróides e experimente vários valores de semente.  
  
-   Crie vários modelos, variando a métrica ou Iterando mais.  
  
Em geral, com modelos de clustering, é possível que qualquer configuração específica resulte em um conjunto de clusters otimizado localmente. Em outras palavras, o conjunto de clusters retornado pelo modelo atende apenas aos pontos de dados atuais e não é generalizado para outros dados. Se você usar uma configuração inicial diferente, o método K-means poderá encontrar uma configuração diferente e superior. 
