---
title: 'Clustering de K-means: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de clustering K-means no serviço de Azure Machine Learning para treinar modelos de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 7de2978fee80cf3acd078ef8ee5f235fab21713e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128706"
---
# <a name="module-k-means-clustering"></a>Modulo Clustering K-Means

Este artigo descreve como usar o módulo de *clustering k-* means no Azure Machine Learning Studio para criar um modelo de clustering k-means não treinado. 
 
K-means é um dos mais simples e mais conhecidos algoritmos de aprendizado não *supervisionados* . Você pode usar o algoritmo para uma variedade de tarefas de aprendizado de máquina, como: 

* [Detectando dados anormais](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Documentos de texto em cluster.
* Analisar conjuntos de valores antes de usar outros métodos de classificação ou regressão. 

Para criar um modelo de clustering, você:

* Adicione este módulo ao seu experimento.
* Conectar um conjunto de uma.
* Defina parâmetros, como o número de clusters que você espera, a métrica de distância a ser usada na criação de clusters e assim por diante. 
  
Depois de configurar os hiperparâmetros do módulo, conecte o modelo não treinado ao [modelo treinar clustering](train-clustering-model.md). Como o algoritmo K-means é um método de aprendizado não supervisionado, uma coluna de rótulo é opcional. 

+ Se seus dados incluírem um rótulo, você poderá usar os valores de rótulo para orientar a seleção dos clusters e otimizar o modelo. 

+ Se os dados não tiverem nenhum rótulo, o algoritmo criará clusters que representam as categorias possíveis, com base apenas nos dados.  

##  <a name="understand-k-means-clustering"></a>Entender o clustering de K-means
 
Em geral, o clustering usa técnicas iterativas para agrupar casos em um conjunto de um em clusters que possuem características semelhantes. Esses agrupamentos são úteis para explorar dados, identificar anomalias nos dados e, eventualmente, para fazer previsões. Os modelos de clustering também podem ajudá-lo a identificar relações em um conjunto de um DataSet que você não pode derivar logicamente por meio de navegação ou observação simples. Por esses motivos, o clustering é geralmente usado nas primeiras fases das tarefas de aprendizado de máquina, para explorar os dados e descobrir correlações inesperadas.  
  
 Ao configurar um modelo de clustering usando o método K-means, você deve especificar um número de destino *K* que indica o número de *centróides* que você deseja no modelo. O centróide é um ponto representativo de cada cluster. O algoritmo K-means atribui cada ponto de dados de entrada a um dos clusters minimizando a soma de quadrados dentro do cluster. 
 
Quando ele processa os dados de treinamento, o algoritmo K-means começa com um conjunto inicial de centróides escolhidos aleatoriamente. As centróides servem como pontos de partida para os clusters e aplicam o algoritmo de Lloyd para refinar seus locais iterativamente. O algoritmo K-means interrompe a criação e refinação de clusters quando ele atende a uma ou mais destas condições:  
  
-   Os centróides estabilizam, o que significa que as atribuições de cluster para pontos individuais não são mais alteradas e o algoritmo convergiu em uma solução.  
  
-   O algoritmo concluiu a execução do número especificado de iterações.  
  
 Depois de concluir a fase de treinamento, use o módulo [atribuir dados aos clusters](assign-data-to-clusters.md) para atribuir novos casos a um dos clusters encontrados usando o algoritmo K-means. Você executa a atribuição de cluster computando a distância entre o novo caso e o centróide de cada cluster. Cada novo caso é atribuído ao cluster com o centróide mais próximo.  

## <a name="configure-the-k-means-clustering-module"></a>Configurar o módulo de clustering K-means
  
1.  Adicione o módulo de **clustering K-** means ao seu experimento.  
  
2.  Para especificar como você deseja que o modelo seja treinado, selecione a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: Se você souber os parâmetros exatos que deseja usar no modelo de clustering, poderá fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **número de centróides**, digite o número de clusters para o qual você deseja que o algoritmo comece.  
  
     Não há garantia de que o modelo produza exatamente esse número de clusters. O algoritmo começa com esse número de pontos de dados e itera para localizar a configuração ideal.  
  
4.  A **inicialização** de propriedades é usada para especificar o algoritmo que é usado para definir a configuração de cluster inicial.  
  
    -   **Primeiro N**: Um número inicial de pontos de dados é escolhido do DataSet e usado como o meio inicial. 
    
         Esse método também é chamado de *método forjado*.  
  
    -   **Aleatório**: O algoritmo coloca aleatoriamente um ponto de dados em um cluster e, em seguida, computa a média inicial para ser o centróide dos pontos atribuídos aleatoriamente do cluster. 

         Esse método também é chamado de método de *partição aleatória* .  
  
    -   **K-Means++** : Esse é o método padrão para inicializar clusters.  
  
         O algoritmo **k-means + +** foi proposto em 2007 por David Arthur e Sergei Vassilvitskii para evitar clustering ruim pelo algoritmo K-means padrão. O **k-means + +** melhora na k-means padrão usando um método diferente para escolher os centros de cluster iniciais.  
  
    
5.  Para **semente de número aleatório**, opcionalmente, digite um valor a ser usado como semente para a inicialização do cluster. Esse valor pode ter um efeito significativo na seleção de cluster.  
  
6.  Para **métrica**, escolha a função a ser usada para medir a distância entre os vetores de cluster ou entre os novos pontos de dados e os centróides escolhidos aleatoriamente. O Azure Machine Learning dá suporte às seguintes métricas de distância do cluster:  
  
    -   **Euclidiana**: A distância euclidiana é normalmente usada como uma medida de dispersão de cluster para o clustering de K-means. Essa métrica é preferida porque minimiza a distância média entre pontos e as centróides.
  
7.  Para **iterações**, digite o número de vezes que o algoritmo deve iterar sobre os dados de treinamento antes de finalizar a seleção de centróides.  
  
     Você pode ajustar esse parâmetro para equilibrar a precisão em relação ao tempo de treinamento.  
  
8.  Para o **modo atribuir rótulo**, escolha uma opção que especifique como uma coluna de rótulo, se estiver presente no conjunto de um, deve ser tratada.  
  
     Como o clustering K-means é um método de aprendizado de máquina não supervisionado, os rótulos são opcionais. No entanto, se o conjunto de seus conjuntos de seus já tiver uma coluna de rótulo, você poderá usar esses valores para orientar a seleção dos clusters ou pode especificar que os valores sejam ignorados.  
  
    -   **Ignorar coluna de rótulo**: Os valores na coluna rótulo são ignorados e não são usados na criação do modelo.
  
    -   **Preencha os valores ausentes**: Os valores de coluna de rótulo são usados como recursos para ajudar a criar os clusters. Se alguma linha estiver sem um rótulo, o valor será imputados usando outros recursos.  
  
    -   **Substituir do mais próximo ao centro**: Os valores de coluna de rótulo são substituídos por valores de rótulo previstos, usando o rótulo do ponto mais próximo do centróide atual.  

8.  Selecione a opção **normalizar recursos** se desejar normalizar os recursos antes do treinamento.
  
     Se você aplicar a normalização, antes do treinamento, os pontos de dados serão `[0,1]` normalizados para por MinMaxNormalizer.

10. Treine o modelo.  
  
    -   Se você definir **criar modo de instrutor** como um **único parâmetro**, adicione um conjunto de um DataSet marcado e treine o modelo usando o módulo [treinar modelo](train-clustering-model.md) de clustering.  
  
### <a name="results"></a>Resultados

Depois de concluir a configuração e o treinamento do modelo, você tem um modelo que pode ser usado para gerar pontuações. No entanto, há várias maneiras de treinar o modelo e várias maneiras de exibir e usar os resultados: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Capturar um instantâneo do modelo em seu espaço de trabalho

Se você usou o módulo [treinar modelo](train-clustering-model.md) de clustering:

1. Clique com o botão direito do mouse no módulo **treinar modelo** de clustering.

2. Selecione **modelo treinado**e, em seguida, selecione **salvar como modelo treinado**.

O modelo salvo representa os dados de treinamento no momento em que você salvou o modelo. Se você atualizar mais tarde os dados de treinamento usados no experimento, ele não atualizará o modelo salvo. 

#### <a name="see-the-clustering-result-dataset"></a>Consulte o conjunto de conjuntos de resultados de clustering 

Se você usou o módulo [treinar modelo](train-clustering-model.md) de clustering:

1. Clique com o botão direito do mouse no módulo **treinar modelo** de clustering.

2. Selecione **os resultados DataSet**e, em seguida, selecione **Visualizar**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Dicas para gerar o melhor modelo de clustering  

É conhecido que o processo de *propagação* usado durante o clustering pode afetar significativamente o modelo. A propagação significa o posicionamento inicial de pontos em possíveis centróides.
 
Por exemplo, se o conjunto de dados contiver muitas exceções e uma exceção for escolhida para propagar os clusters, nenhum outro ponto de dados se ajustará bem com esse cluster, e o cluster poderá ser um singleton. Ou seja, ele pode ter apenas um ponto.  
  
Você pode evitar esse problema de duas maneiras:  
  
-   Altere o número de centróides e experimente vários valores de semente.  
  
-   Crie vários modelos, variando a métrica ou Iterando mais.  
  
Em geral, com modelos de clustering, é possível que qualquer configuração específica resulte em um conjunto de clusters otimizado localmente. Em outras palavras, o conjunto de clusters retornado pelo modelo atende apenas aos pontos de dados atuais e não é generalizado para outros dados. Se você usar uma configuração inicial diferente, o método K-means poderá encontrar uma configuração diferente, talvez superior. 
