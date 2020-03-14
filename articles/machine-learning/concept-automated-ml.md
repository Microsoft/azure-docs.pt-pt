---
title: O que é automatizado ML / AutoML
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning pode escolher automaticamente um algoritmo para si e gerar um modelo para economizar tempo utilizando os parâmetros e critérios que fornece para selecionar o melhor algoritmo para o seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: 501158ffa8d05bc34dd39c21680012b1f3308def
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283891"
---
# <a name="what-is-automated-machine-learning"></a>O que é automatizado aprendizagem automática?

O machine learning automatizado, também referido como ML automatizado, é o processo de automatização das tarefas iterativas e de desenvolvimento do modelo de aprendizagem automática. Permite que cientistas de dados, analistas e desenvolvedores construam modelos ML com alta escala, eficiência e produtividade, mantendo a qualidade do modelo. O ML automatizado baseia-se numa descoberta da nossa [divisão de Pesquisa microsoft.](https://arxiv.org/abs/1705.05355)

O desenvolvimento do modelo tradicional de aprendizagem automática é intensivo em recursos, requerendo conhecimentos e tempo significativos de domínio para produzir e comparar dezenas de modelos. Aplique ML automatizado quando quiser que o Azure Machine Learning treine e sintetetete um modelo para si utilizando a métrica-alvo que especifica. O serviço então iterates através de algoritmos ML emparelhados com seleções de recursos, onde cada iteração produz um modelo com uma pontuação de treino. Quanto maior for a pontuação, melhor o modelo é considerado para "encaixar" os seus dados.

Com machine learning automatizado, você vai acelerar o tempo que leva para obter modelos ML prontos para a produção com grande facilidade e eficiência.

## <a name="when-to-use-automated-ml"></a>Quando utilizar ML automatizado

O ML automatizado democratiza o processo de desenvolvimento do modelo de aprendizagem automática e capacita os seus utilizadores, independentemente da sua experiência em ciência de dados, a identificarem um pipeline de aprendizagem automática de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores em todas as indústrias podem usar ML automatizado para:

+ Implementar soluções de aprendizagem automática sem um vasto conhecimento de programação
+ Poupe tempo e recursos
+ Alavancar as melhores práticas da ciência dos dados
+ Proporcionar uma resolução ágil de problemas

A tabela que se segue lista os casos comuns de utilização automática de ML. 

Classificação| Previsão de séries temporais | Regressão
---|---|---
[Deteção de Fraudes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Previsão de Vendas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Previsão do Desempenho do CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Previsão de Marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Previsão da Procura](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Classificação de Dados do Newsgroup](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Previsão de Produção de Bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="how-automated-ml-works"></a>Como funciona o ML automatizado

Utilizando o **Azure Machine Learning,** pode projetar e executar as suas experiências automatizadas de treino ML com estes passos:

1. **Identificar o problema ml** a resolver: classificação, previsão ou regressão

1. **Especificar a fonte e o formato dos dados de formação rotulados:** Matrizes numpy ou quadro de dados pandas

1. **Configure o alvo da computação para a formação**de modelos, como o seu [computador local, as Computações de Aprendizagem automática Azure, os VMs remotos ou os Databricks Azure.](how-to-set-up-training-targets.md)  Aprenda sobre formação automatizada [num recurso remoto.](how-to-auto-train-remote.md)

1. **Configure os parâmetros automatizados** de aprendizagem automática de máquinas que determinam quantas iterações em diferentes modelos, configurações de hiperparâmetros, pré-processamento/caracterização avançada, e que métricas olhar para a determinação do melhor modelo.  Pode configurar as definições para experiências de treino automático no [estúdio Azure Machine Learning,](https://ml.azure.com)ou [com o SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Submeta o treino.**

  ![Aprendizagem automática de máquinas](./media/concept-automated-ml/automl-concept-diagram2.png)

Durante o treino, o Azure Machine Learning cria uma série de oleodutos paralelos que experimentam diferentes algoritmos e parâmetros. Vai parar assim que atingir os critérios de saída definidos na experiência.

Também pode inspecionar as informações de execução [registadas, que contêm métricas recolhidas](how-to-understand-automated-ml.md) durante a execução. O ensaio de treino produz um objeto serializado Python (ficheiro`.pkl`) que contém o modelo e o pré-processamento de dados.

Enquanto a construção de modelos é automatizada, também pode [aprender o quão importantes ou relevantes são as funcionalidades](how-to-configure-auto-train.md#explain) para os modelos gerados.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pré-processamento

Em todas as experiências automatizadas de aprendizagem automática de máquinas, os seus dados são pré-processados utilizando os métodos predefinidos e opcionalmente através de um pré-processamento avançado.

> [!NOTE]
> Os passos automatizados de pré-processamento de aprendizagem automática (normalização de recursos, manuseamento de dados em falta, conversão de texto para numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treino são aplicadas automaticamente aos seus dados de entrada.

### <a name="automatic-preprocessing-standard"></a>Pré-processamento automático (padrão)

Em todas as experiências automatizadas de aprendizagem automática de máquinas, os seus dados são automaticamente dimensionados ou normalizados para ajudar os algoritmos a ter um bom desempenho.  Durante o treino de modelos, uma das seguintes técnicas de escala ou normalização será aplicada a cada modelo.

|Escalada&nbsp;&&nbsp;normalização| Descrição |
| ------------- | ------------- |
| [Invólucro standardScale](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronize as funcionalidades removendo a média e a escala para a variação da unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma as características escalando cada característica pelo mínimo e máximo da coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Escala cada característica pelo seu valor absoluto máximo |
| [RobustSescalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Este Scaler apresenta-se pela sua gama quântica |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução da dimensionalidade linear usando singular valor decomposição dos dados para projetá-lo para um espaço dimensional inferior |
| [Invólucro TruncadoSVD](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Este transformador realiza a redução linear da dimensionalidade através de decomposição de valor singular truncado (SVD). Ao contrário do PCA, este estimador não centra os dados antes de calcular a decomposição de valor singular, o que significa que pode funcionar com matrizes scipy.sparse eficientemente |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada amostra (isto é, cada linha da matriz de dados) com pelo menos um componente não zero é redimensionada independentemente de outras amostras de modo que a sua norma (l1 ou l2) seja igual a uma |

### <a name="advanced-preprocessing-optional-featurization"></a>Pré-processamento avançado: caracterização opcional

Estão também disponíveis pré-processamento e funcionalidades avançados adicionais, tais como guarda-dados, codificação e transformações. [Saiba mais sobre o que está incluído.](how-to-use-automated-ml-for-ml-models.md#featurization) Ativar esta definição com:

+ Estúdio Azure Machine Learning: Ativar **a funcionalidade automática** na secção de **configuração adicional do View** com estes [passos](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: Especificar `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` para a [aula de`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

## <a name="prevent-over-fitting"></a>Evitar excesso de adaptação

O excesso de adaptação na aprendizagem automática ocorre quando um modelo se encaixa demasiado bem nos dados de treino, e como resultado não pode prever com precisão em dados de teste invisíveis. Por outras palavras, o modelo simplesmente memorizou padrões específicos e ruído nos dados de treino, mas não é suficientemente flexível para fazer previsões sobre dados reais. Nos casos mais flagrantes, um modelo sobre-equipado assumirá que as combinações de valor da funcionalidade observadas durante o treino resultarão sempre na mesma saída para o alvo. 

A melhor maneira de evitar o excesso de adaptação é seguir as melhores práticas ml, incluindo:

* Usando mais dados de formação e eliminando o enviesamento estatístico
* Prevenção de fugas de alvo
* Usando menos funcionalidades
* **Regularização e otimização de hiperparâmetros**
* **Limitações de complexidade do modelo**
* **Validação cruzada**

No contexto do ML automatizado, os três primeiros itens acima são **as melhores práticas que implementa.** Os três últimos itens arrojados são **as melhores práticas implementadas ml automatizadas** por padrão para proteger contra o excesso de encaixe. Em definições que não sejam ML automatizadas, todas as seis melhores práticas valem a pena seguir para evitar modelos demasiado adequados.

### <a name="best-practices-you-implement"></a>As melhores práticas que implementa

Usar **mais dados** é a forma mais simples e possível de prevenir o excesso de adaptação, e como um bónus adicionado normalmente aumenta a precisão. Quando se utiliza mais dados, torna-se mais difícil para o modelo memorizar padrões exatos, e é forçado a alcançar soluções mais flexíveis para acomodar mais condições. Também é importante reconhecer **o enviesamento estatístico,** para garantir que os seus dados de formação não incluem padrões isolados que não existirão nos dados de previsão ao vivo. Este cenário pode ser difícil de resolver, porque pode não haver excesso de adaptação entre o seu comboio e os conjuntos de teste, mas pode haver um presente demasiado adequado quando comparado com os dados de teste ao vivo.

A fuga de alvos é um problema semelhante, onde pode não ver o excesso de adaptação entre os conjuntos de comboios/testes, mas aparece na hora da previsão. A fuga de destino ocorre quando o seu modelo "faz batota" durante o treino, tendo acesso a dados que normalmente não deveria ter no momento da previsão. Por exemplo, se o seu problema é prever na segunda-feira qual será o preço da mercadoria na sexta-feira, mas uma das suas características incluiu acidentalmente dados a partir das quintas-feiras, que seriam dados que o modelo não terá na hora da previsão, uma vez que não pode ver o futuro. A fuga de alvo é um erro fácil de perder, mas é frequentemente caracterizada por uma precisão anormalmente elevada para o seu problema. Se está a tentar prever o preço das ações e treinou um modelo com uma precisão de 95%, é provável que haja fugas de destino algures nas suas características.

A remoção de funcionalidades também pode ajudar na sobremontagem, impedindo que o modelo tenha demasiados campos para memorizar padrões específicos, fazendo com que seja mais flexível. Pode ser difícil medir quantitativamente, mas se conseguir remover as funcionalidades e reter a mesma precisão, provavelmente tornou o modelo mais flexível e reduziu o risco de excesso de adaptação.

### <a name="best-practices-automated-ml-implements"></a>Implementações automáticas de ML de boas práticas

Regularização é o processo de minimizar uma função de custo para penalizar modelos complexos e sobre-instalados. Existem diferentes tipos de funções de regularização, mas em geral todos penalizam o tamanho, variação e complexidade do modelo. Ml automatizado usa L1 (Lasso), L2 (Ridge) e ElasticNet (L1 e L2 simultaneamente) em diferentes combinações com diferentes configurações de hiperparâmetros de modelo que controlam o excesso de encaixe. Em termos simples, ml automatizado variará quanto um modelo é regulado e escolherá o melhor resultado.

O ML automatizado também implementa limitações explícitas de complexidade do modelo para evitar o excesso de adaptação. Na maioria dos casos, esta implementação é especificamente para algoritmos de árvores de decisão ou floresta, onde a profundidade máxima individual das árvores é limitada, e o número total de árvores usadas em técnicas florestais ou de conjunto são limitados.

A validação cruzada (CV) é o processo de tomar muitos subconjuntos dos seus dados de treino completos e treinar um modelo em cada subconjunto. A ideia é que um modelo possa ter "sorte" e ter uma grande precisão com um subconjunto, mas ao usar muitos subconjuntos o modelo não conseguirá sempre esta alta precisão. Ao fazer CV, fornece um conjunto de dados de validação, especifique as suas dobras cv (número de subconjuntos) e ml automatizado treinará o seu modelo e afinará hiperparâmetros para minimizar o erro no seu conjunto de validação. Uma dobra de CV pode ser demasiado ajustada, mas ao usar muitos deles reduz a probabilidade de o seu modelo final estar demasiado apto. A contrapartida é que o CV resulta em tempos de treino mais longos e, portanto, maior custo, porque em vez de treinar um modelo uma vez, treina-o uma vez para *cada* subconjunto de CV.

> [!NOTE]
> A validação cruzada não é ativada por defeito; deve ser configurado em definições automáticas de ML. No entanto, após a configuração do CV e de um conjunto de dados de validação ter sido fornecido, o processo é automatizado para si.

### <a name="identifying-over-fitting"></a>Identificação excessiva

Considere os seguintes modelos treinados e as respetivas precisões de comboio e teste.

| Modelo | Precisão do comboio | Precisão do teste |
|-------|----------------|---------------|
| Uma | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Considerando o modelo **A,** existe um equívoco comum de que se a precisão do teste em dados invisíveis for inferior à precisão do treino, o modelo está sobre-montado. No entanto, a precisão do teste deve ser sempre menor do que a precisão do treino, e a distinção para o excesso de ajuste vs. adequadamente ajustado resume-se a *quão* menos precisa. 

Ao comparar os modelos **A** e **B,** o modelo **A** é um modelo melhor porque tem uma maior precisão de teste, e embora a precisão do teste seja ligeiramente mais baixa em 95%, não é uma diferença significativa que sugira que o excesso de adaptação está presente. Não escolheria o modelo **B** simplesmente porque as precisões do comboio e do teste estão mais próximas.

O modelo **C** representa um caso claro de excesso de adaptação; a precisão do treino é muito alta, mas a precisão do teste não é tão alta. Esta distinção é subjetiva, mas vem do conhecimento do seu problema e dos seus dados, e que magnitudes de erro são aceitáveis. 

## <a name="classification--regression"></a>Classificação e regressão

Classificação e regressão são os tipos mais comuns de tarefas de aprendizagem automática. Ambos são tipos de aprendizagem supervisionada em que os modelos aprendem usando dados de formação, e aplicam essas aprendizagens a novos dados. O Azure Machine Learning oferece recursos especificamente para estas tarefas, tais como recursos de texto de rede neural profunda para classificação. Saiba mais sobre [opções de recursos.](how-to-use-automated-ml-for-ml-models.md#featurization) 

O principal objetivo dos modelos de classificação é prever em que categorias novos dados se basearão nas aprendizagens a partir dos seus dados de formação. Exemplos comuns de classificação incluem deteção de fraudes, reconhecimento de caligrafia e deteção de objetos.  Saiba mais e veja um exemplo de [classificação com machine learning automatizado.](tutorial-train-models-with-aml.md)

Diferente da classificação onde os valores de saída previstos são categóricos, os modelos de regressão preveem valores de saída numéricos baseados em preditores independentes. Em regressão, o objetivo é ajudar a estabelecer a relação entre essas variáveis de previsão independente, estimando como uma variável impacta as outras. Por exemplo, o preço do automóvel com base em características como, quilometragem de gás, classificação de segurança, etc. Saiba mais e veja um exemplo de [regressão com aprendizagem automática de máquinas.](tutorial-auto-train-models.md)

## <a name="time-series-forecasting"></a>Previsão de séries temporais

As previsões de construção são parte integrante de qualquer negócio, seja receita, inventário, vendas ou procura de clientes. Pode utilizar ml automatizado para combinar técnicas e abordagens e obter uma previsão recomendada e de alta qualidade da série de tempo.

Uma experiência automatizada em séries temporais é tratada como um problema de regressão multivariada. Os valores das séries de tempo passadas são "apostados" para se tornarem dimensões adicionais para o regresso, juntamente com outros preditores. Esta abordagem, ao contrário dos métodos clássicos da série de tempo, tem uma vantagem de incorporar naturalmente múltiplas variáveis contextuais e sua relação entre si durante o treino. O ML automatizado aprende um modelo único, mas muitas vezes ramificado internamente para todos os itens nos horizontes de dataset e previsão. Mais dados estão assim disponíveis para estimar os parâmetros dos modelos e a generalização para séries invisíveis torna-se possível.

Saiba mais e veja um exemplo de [aprendizagem automática de máquinas para previsão de séries temporais.](how-to-auto-train-forecast.md) Ou, consulte o caderno de procura de [energia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) para exemplos de código detalhados de configuração de previsão avançada, incluindo:

* deteção e caracterização de férias
* séries temporais e alunos do DNN (Auto-ARIMA, Profeta, ForecastTCN)
* muitos modelos suportam através do agrupamento
* validação cruzada de origem rolante
* lags configuráveis
* características agregados de janela sonantes

## <a name="ensemble"></a>Modelos conjunto

O machine learning automatizado suporta modelos conjuntos, que são ativados por padrão. A aprendizagem do conjunto melhora os resultados do machine learning e o desempenho preditivo, combinando vários modelos em oposição à utilização de modelos individuais. As iterações do conjunto aparecem como as iterações finais da sua corrida. O machine learning automatizado utiliza métodos de conjunto de votação e empilhamento para combinar modelos:

* **Votação**: prevê com base na média ponderada das probabilidades de classe previstas (para tarefas de classificação) ou nos objetivos de regressão previstos (para tarefas de regressão).
* **Empilhamento**: empilhamento combina modelos heterogéneos e treina um meta-modelo com base na saída dos modelos individuais. Os atuais meta-modelos padrão são LogisticRegression para tarefas de classificação e ElasticNet para tarefas de regressão/previsão.

O algoritmo de [seleção do conjunto Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) com inicialização de conjunto siolado é usado para decidir quais os modelos a usar dentro do conjunto. A um nível elevado, este algoritmo inicializa o conjunto com até cinco modelos com as melhores pontuações individuais, e verifica que estes modelos estão dentro do limiar de 5% da melhor pontuação para evitar um conjunto inicial pobre. Em seguida, para cada iteração do conjunto, um novo modelo é adicionado ao conjunto existente e a pontuação resultante é calculada. Se um novo modelo melhorou a pontuação do conjunto existente, o conjunto é atualizado para incluir o novo modelo.

Consulte o ["como fazer"](how-to-configure-auto-train.md#ensemble) para alterar as definições padrão do conjunto na aprendizagem automática de máquinas.

## <a name="imbalance"></a>Dados desequilibrados

Os dados desequilibrados são geralmente encontrados em dados para cenários de classificação de aprendizagem automática, e refere-se a dados que contêm uma relação desproporcional de observações em cada classe. Este desequilíbrio pode levar a um efeito positivo falsamente percebido da precisão de um modelo, porque os dados de entrada têm tendência para uma classe, o que resulta no modelo treinado para imitar esse enviesamento. 

Como parte do seu objetivo de simplificar o fluxo de trabalho de aprendizagem automática, o ML automatizado construiu capacidades para ajudar a lidar com dados desequilibrados como, tais como, 

- Uma coluna de **peso**: ml automatizado suporta uma coluna ponderada como entrada, fazendo com que as linhas nos dados sejam ponderadas para cima ou para baixo, o que pode tornar uma classe mais ou menos "importante".

- Os algoritmos utilizados por ML automatizado podem lidar adequadamente com o desequilíbrio de até 20:1, o que significa que a classe mais comum pode ter 20 vezes mais linhas nos dados do que a classe menos comum.

### <a name="identify-models-with-imbalanced-data"></a>Identificar modelos com dados desequilibrados

Como os algoritmos de classificação são geralmente avaliados pela precisão, verificar a pontuação de precisão de um modelo é uma boa maneira de identificar se foi impactado por dados desequilibrados. Tinha uma precisão muito alta ou uma precisão muito baixa para certas classes?

Além disso, as execuções automáticas de ML geram automaticamente os seguintes gráficos, o que pode ajudá-lo a entender a correção das classificações do seu modelo, e identificar modelos potencialmente impactados por dados desequilibrados.

Gráfico| Descrição
---|---
[Matriz de confusão](how-to-understand-automated-ml.md#confusion-matrix)| Avalia as etiquetas corretamente classificadas com os rótulos reais dos dados. 
[Recolha de precisão](how-to-understand-automated-ml.md#precision-recall-chart)| Avalia a relação dos rótulos corretos com o rácio de instâncias de etiquetas encontradas dos dados 
[Curvas ROC](how-to-understand-automated-ml.md#roc)| Avalia a relação dos rótulos corretos com o rácio de etiquetas falsas positivas.

### <a name="handle-imbalanced-data"></a>Lidar com dados desequilibrados 

As seguintes técnicas são opções adicionais para lidar com dados desequilibrados fora do ML automatizado. 

- Reprovando até mesmo o desequilíbrio de classes, quer através da amostragem das classes mais pequenas, quer da amostragem das classes maiores. Estes métodos requerem conhecimentos especializados para processar e analisar.

- Use uma métrica de desempenho que lida melhor com dados desequilibrados. Por exemplo, a pontuação de F1 é uma média ponderada de precisão e recordação. A precisão mede a exacticidade de um classificador, a baixa precisão indica um elevado número de falsos positivos--, enquanto a recordação mede a completude de um classificador- a baixa recordação indica um elevado número de falsos negativos. 

## <a name="use-with-onnx-in-c-apps"></a>Utilizar com ONNX em C# apps

Com o Azure Machine Learning, pode utilizar ml automatizado para construir um modelo Python e convertê-lo para o formato ONNX. O tempo de funcionação C#ONNX suporta, para que possa C# utilizar o modelo construído automaticamente nas suas apps sem necessidade de recodificação ou qualquer das lestabeleceções de rede que os pontos finais rest introduzem. Experimente um exemplo deste fluxo [neste caderno jupyter.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)

## <a name="automated-ml-in-azure-machine-learning"></a>ML automatizado em Aprendizagem automática de máquinas azure

Azure Machine Learning oferece duas experiências para trabalhar com ML automatizado

* Para clientes experientes em código, [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* Para clientes com experiência limitada/sem código, o estúdio Azure Machine Learning na [https://ml.azure.com](https://ml.azure.com/)  

O seguinte resume as capacidades ml automatizadas de alto nível suportadas em cada experiência.

<a name="parity"></a>

### <a name="experiment-settings"></a>Configurações de experiências 

As seguintes definições permitem configurar a sua experiência ml automatizada. 

| | SDK Python| estúdio
----|:----:|:----:
Dados divididos em conjuntos de comboio/validação| ✓|✓
Suporta tarefas ML: classificação, regressão e previsão| ✓| ✓
Otimiza com base na métrica primária| ✓| ✓
Suporta a computação AML como alvo de cálculo | ✓|✓
Configure horizonte de previsão, lags de destino e janela de rolamento|✓|✓
Definir critérios de saída |✓|✓ 
Definir iterações simultâneas| ✓|✓
Colunas de gotete| ✓|✓
Algoritmos de bloco|✓|✓
Validação cruzada |✓|✓
Apoia a formação em clusters de Databricks Azure| ✓|
Ver nomes de funcionalidades projetados|✓|
Resumo da caracterização| ✓|
Característica de férias|✓|
Nível de verbosidade para ficheiros de registo| ✓|

### <a name="model-settings"></a>Definições do modelo

Estas definições podem ser aplicadas ao melhor modelo como resultado da sua experiência automatizada em ML.

||SDK Python|estúdio
----|:----:|:----:
Melhor registo de modelo| ✓|✓
Melhor implementação de modelos| ✓| ✓
Melhor explicabilidade do modelo| ✓|✓
Ativar modelos de conjunto de votação e stack| ✓|✓
Mostrar melhor modelo com base em métricanão primária|✓|Ativar/desativar a compatibilidade do modelo ONNX|✓|
Testar o modelo | ✓| |

### <a name="run-control-settings"></a>Executar definições de controlo

Estas definições permitem-lhe rever e controlar as suas experiências e a sua criança corre. 

||SDK Python| estúdio
----|:----:|:----:
Executar mesa de resumo| ✓|✓
Cancelar corrida| ✓|✓
Cancelar a corrida infantil| ✓| ✓
Pegue guarda-costas| ✓|✓
Pausa corrida| ✓| 
Retomar a execução| ✓| 

## <a name="next-steps"></a>Passos Seguintes

Veja exemplos e aprenda a construir modelos usando machine learning automatizado:

+ Siga o [Tutorial: Treine automaticamente um modelo de regressão com Azure Machine Learning](tutorial-auto-train-models.md)

+ Configure as definições para a experiência de treino automático:
  + No estúdio Azure Machine Learning, [utilize estes passos.](how-to-use-automated-ml-for-ml-models.md)
  + Com o Python SDK, [use estes passos.](how-to-configure-auto-train.md)

+ Aprenda a treinar automaticamente utilizando dados da série time, [use estes passos](how-to-auto-train-forecast.md).

+ Experimente [amostras de Caderno Jupyter para aprendizagem automática de máquinas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Ml automatizado também está disponível em outras soluções da Microsoft tais como, [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) e [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
