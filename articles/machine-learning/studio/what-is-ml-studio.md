---
title: O que é
titleSuffix: Azure Machine Learning Studio
description: O Azure Machine Learning Studio é uma ferramenta de arrastar e largar para rapidamente criar modelos de uma biblioteca de prontos a utilizar de algoritmos e módulos.
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: e454420c014460fbf44c01cb9f620d0f3273c602
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586023"
---
# <a name="what-is-azure-machine-learning-studio"></a>O que é o Azure Machine Learning Studio?
O Microsoft Azure Machine Learning Studio é uma ferramenta de colaboração, de arrastar e largar que pode utilizar para criar, testar e implementar soluções de análise preditiva nos seus dados. Machine Learning Studio publica modelos como serviços web que podem facilmente ser consumidos por aplicações personalizadas ou ferramentas de BI como o Excel.

Machine Learning Studio é onde se reúnem a ciência de dados, a análise preditiva, os recursos de nuvem e os dados.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>A área de trabalho interativa do Machine Learning Studio
Para desenvolver um modelo de análise preditiva, utiliza normalmente dados a partir de uma ou mais origens, transforma e analisa esses dados através da manipulação de vários dados e funções estatísticas e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que modifica nas várias funções e os respetivos parâmetros, os seus resultados convergem até achar que tem um modelo preparado e eficaz.

O **Azure Machine Learning Studio** fornece uma área de trabalho interativa e visual para facilmente criar, testar e iterar um modelo de análise preditiva. Pode arrastar e largar ***conjuntos de dados*** e ***módulos*** de análise em telas interativas, ligá-las entre si para formar uma ***experimentação***, que executa no Machine Learning Studio. Para iterar o design do modelo, edite a experimentação, guarde uma cópia se assim pretender e execute-a novamente. Quando estiver pronto, pode converter a ***experimentação de preparação*** numa ***experimentação preditiva*** e, em seguida, publicá-la como um ***serviço Web*** para que o modelo possa ser acedido por outras pessoas.

Não existe nenhuma programação necessária, basta ligar visualmente os conjuntos de dados e módulos para construir o seu modelo de análise preditiva.

![Diagrama de studio do Azure Machine Learning: Criar experimentações, ler dados de várias origens, escrever dados classificados, escrever modelos.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="get-started-with-machine-learning-studio"></a>Introdução ao Machine Learning Studio
Quando entrar pela primeira vez no[Machine Learning Studio](https://studio.azureml.net), verá a página **inicial**. A partir daqui, pode ver a documentação, vídeos, webinars e localizar outros recursos importantes.

Clique no menu do canto superior esquerdo ![Menu](./media/what-is-ml-studio/menu.png) e verá várias opções.

### <a name="cortana-intelligence"></a>Cortana Intelligence
Clique em **Cortana Intelligence** e será direcionado para a home page do [Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite). O Cortana Intelligence Suite é um conjunto de aplicações totalmente geridas de macrodados e de análises avançadas para transformar os seus dados em ações inteligentes. Consulte a home page do Suite para ver a documentação completa, incluindo histórias dos clientes.

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
Existem duas opções aqui, **Página Principal**, a página de início, e **Studio**.

Clique em **Studio** e será direcionado para o **Azure Machine Learning Studio**. Em primeiro lugar, ser-lhe-á pedido para iniciar sessão com a sua conta Microsoft ou a sua conta escolar ou profissional. Depois de iniciar sessão, irá ver os seguintes separadores no lado esquerdo:

* **PROJETOS** - Coleções de experimentações, conjuntos de dados, blocos de notas e outros recursos que representam um único projeto
* **EXPERIMENTAÇÕES** - As experimentações que foram criadas ou guardadas como rascunhos
* **SERVIÇOS WEB** - Os serviços web que foram implementados a partir das suas experimentações
* **BLOCOS DE NOTAS** - Blocos de notas Jupyter que criou
* **CONJUNTOS DE DADOS** - Conjuntos de dados que carregou para o Studio
* **MODELOS DE FORMAÇÃO** - Modelos que preparado nas experimentações e guardou no Studio
* **DEFINIÇÕES** - Uma coleção de definições que pode utilizar para configurar a sua conta e recursos.

### <a name="gallery"></a>Galeria
Clique em **Galeria** e será direcionado para a **[Galeria de IA do Azure](http://gallery.azure.ai/)**. A Galeria é um local onde uma comunidade de cientistas de dados e programadores partilham as soluções criadas com componentes do Cortana Intelligence Suite.

Para mais informações sobre a Galeria, veja [Partilhar e detetar soluções na galeria de IA do Azure](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Componentes de uma experimentação
Uma experimentação é constituída por conjuntos de dados que fornecem dados aos módulos analíticos, que ligar em conjunto para construir um modelo de análise preditiva. Especificamente, uma experimentação válida tem estas características:

* A experimentação tem, pelo menos, um conjunto de dados e um módulo
* Os conjuntos de dados só podem ser ligados aos módulos
* Os módulos poderão estar ligados a conjuntos de dados ou a outros módulos
* Todas as portas de entrada de módulos têm de ter alguma ligação ao fluxo de dados
* Devem ser definidos todos os parâmetros necessários para cada módulo

Pode criar uma experimentação de raiz, ou pode utilizar uma experimentação de exemplo existente como um modelo. Para obter mais informações, veja [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Copiar experimentações de exemplo para criar novas experimentações de aprendizagem automática).

Para obter um exemplo de criação de uma experimentação simples, consulte o artigo [Criar uma experimentação simples no Azure Machine Learning Studio](create-experiment.md).

Para obter instruções mais completas de criação de uma solução de Análise Preditiva, veja [desenvolver uma solução Preditiva com o Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Conjuntos de dados
Um conjunto de dados que foi carregado no Machine Learning Studio para que possa ser utilizado no processo de modelação. Um número de conjuntos de dados de exemplo está incluído no Machine Learning Studio para experimentação e pode carregar mais conjuntos de dados conforme seja necessário. Seguem-se alguns exemplos de conjuntos de dados incluídos:

* **Dados MPG para vários automóveis** - quilómetros por litro (MPG) valores para automóveis identificados pelo número de cilindros, potência de cavalos, etc.
* **Dados sobre cancro da mama** - Dados de diagnóstico sobre cancro da mama.
* **Dados sobre incêndios florestais** - tamanhos dos incêndios florestais no nordeste de Portugal.

À medida que cria uma experimentação, pode escolher a partir de uma lista de conjuntos de dados disponível à esquerda da tela.

Para obter uma lista de conjuntos de dados de exemplo incluídos no Machine Learning Srudio, consulte o artigo [Utilizar os conjuntos de dados de exemplo no Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Um módulo é um algoritmo que pode utilizar nos seus dados. O Machine Learning Studio tem um número de módulos que se situa entre funções de entrada de dados para formação, classificação e processos de validação. Seguem-se alguns exemplos de módulos incluídos:

* [Converter em ARFF][convert-to-arff] - converte um conjunto de dados .NET em série para o formato de ficheiro de relação de atributo (ARFF).
* [Calcular estatísticas básicas][elementary-statistics] - calcula estatísticas básicas como a média, o desvio padrão, etc.
* [Regressão linear][linear-regression] - cria um modelo de regressão linear online descendente.
* [Modelo de pontuação][score-model] - Pontua um modelo de classificação ou regressão preparado.

À medida que cria uma experimentação, pode escolher a partir de uma lista de módulos disponíveis à esquerda da tela.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando seleciona um módulo na tela, os parâmetros do módulo são apresentados no painel **Propriedades** à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo.

Para obter ajuda na navegação da extensa biblioteca de algoritmos de machine learning disponíveis, consulte [como escolher algoritmos para o Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementar um serviço web de análise preditiva
Assim que o modelo de análise preditiva estiver pronto, pode implementá-lo como um serviço web a partir do Machine Learning Studio. Consulte o artigo [Implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md) para obter mais detalhes.


## <a name="key-machine-learning-terms-and-concepts"></a>Principais termos e conceitos de machine learning
Os termos de machine learning podem ser confusos. Seguem-se as definições dos principais termos para ajudá-lo. Utilize os comentários que se seguem para nos informar sobre que outros termos gostaria de ter uma definição.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Exploração de dados, análise descritiva e análise preditiva

**Exploração de dados** é o processo de recolha de informações sobre um conjunto de dados de grande volume e, muitas vezes, não estruturado para encontrar características para uma análise focada.

**Extração de dados** refere-se a exploração de dados automática.

**Análise descritiva** é o processo de analisar um conjunto de dados para resumir o que aconteceu. A maioria das análises do negócio - como os relatórios de vendas, métricas da web e análise de redes sociais – são descritivas.

**Análise preditiva** é o processo de criação de modelos a partir dos dados históricos ou atuais para prever futuros resultados.

### <a name="supervised-and-unsupervised-learning"></a>Aprendizagem supervisionada e não supervisionada
 **Aprendizagem supervisionada** os algoritmos são preparados com dados com nome - por outras palavras, dados compostos por exemplos de respostas pretendidas. Por exemplo, um modelo que identifica a utilização fraudulenta de cartões de crédito seria preparado a partir de um conjunto de dados etiquetados de custos fraudulentos e válidos conhecidos. A maior parte do Machine Learning é supervisionada.

 **Learning não supervisionado** é utilizado em dados sem identificação e o objetivo é encontrar relações entre os dados. Por exemplo, pode querer localizar agrupamentos de dados demográficos do cliente com hábitos de aquisição semelhantes.

### <a name="model-training-and-evaluation"></a>Modelo de formação e avaliação
Um modelo do Machine Learning é uma abstração da pergunta a que está a tentar responder ou o resultado que pretende prever. Os modelos são ensinados e avaliados a partir dos dados existentes.

#### <a name="training-data"></a>Dados de preparação
Quando preparar um modelo a partir de dados, pode utilizar um conjunto de dados conhecidos e fazer ajustes para o modelo com base nas características de dados para obter a resposta mais exata. No Azure Machine Learning Studio, é criado um modelo a partir de um módulo de algoritmo que processa dados de treinamento e módulos funcionais, por exemplo, um módulo de pontuação.

Na aprendizagem supervisionada, se estiver a dar formação sobre um modelo de deteção de fraudes, utiliza um conjunto de transações que estão identificadas como fraudulentas ou válidas. Divide aleatoriamente o seu conjunto de dados e utiliza parte do mesmo para preparar o modelo e outra parte para testar ou avaliar o modelo.

#### <a name="evaluation-data"></a>Dados de avaliação
Depois de ter um modelo de preparação, avalie o modelo utilizando os restantes dados de teste. Utilize dados dos quais já conhece os resultados para saber se o seu modelo prevê com precisão.

## <a name="other-common-machine-learning-terms"></a>Outros termos comuns do Machine Learning
* **algorithm**: Um conjunto de regras utilizadas para resolver problemas através do processamento de dados, matemático ou raciocínio automatizado.
* **Deteção de anomalias**: Um modelo que sinaliza eventos ou valores invulgares e ajuda-o a detetar problemas. Por exemplo, a deteção de fraudes de cartões de crédito procura compras invulgares.
* **dados categóricos**: Dados que estão organizado por categorias e que podem ser divididos em grupos. Por exemplo, um conjunto de dados categórico para automóveis pode especificar o ano, a marca, o modelo e o preço.
* **Classificação**: Um modelo para organizar os pontos de dados em categorias com base num conjunto de dados para a qual categoria agrupamentos são já conhecidos.
* **"Feature Engineering"**: O processo de extração ou seleção das funcionalidades relacionadas com um conjunto de dados para melhorar o conjunto de dados e melhorar os resultados. Por exemplo, podem ser melhorados os dados das tarifas aéreas por dias da semana e feriados. Ver [seleção da funcionalidade e engenharia no Azure Machine Learning Studio](../team-data-science-process/create-features.md).
* **módulo**: Uma parte funcional num modelo de Machine Learning Studio, como o módulo de introdução de dados que lhe permite introduzir e editar pequenos conjuntos de dados. Um algoritmo também é um tipo de módulo no Machine Learning Studio.
* **modelo**: Um modelo de aprendizagem supervisionado é o produto de uma experimentação do machine learning composta por dados de treinamento, um módulo de algoritmo e módulos funcionais, por exemplo, um módulo de modelo de pontuação.
* **dados numéricos**: Dados que representam medidas (dados contínuos) ou contagem (dados discretos). Também referidos como *dados quantitativos*.
* **partition**: O método pelo qual dividir os dados em amostras. Consulte o artigo [Partição e amostras](https://msdn.microsoft.com/library/azure/dn905960.aspx) para obter mais informações.
* **predição**: Uma predição é uma previsão de um valor ou valores de um modelo de aprendizagem automática. Também pode ver o termo "classificação preditiva". No entanto, as classificações preditivas não são o resultado final de um modelo. Uma avaliação do modelo está de acordo com a pontuação.
* **regression**: Um modelo para prever um valor com base nas variáveis independentes, por exemplo, prever o preço de um automóvel com base no respetivo ano e fazer.
* **pontuação**: Um valor previsto gerado a partir de uma classificação de preparação ou modelo de regressão, utilizando o [módulo do modelo de pontuação](https://msdn.microsoft.com/library/azure/dn905995.aspx) no Machine Learning Studio. Os modelos de classificação também devolvem uma pontuação para indicar a probabilidade do valor previsto. Assim que tiver gerado pontuações de um modelo, pode avaliar exatidão o modelo utilizando o [módulo do modelo de avaliação](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **sample**: Uma parte de um conjunto de dados que se destina a ser representativo da totalidade. Exemplos podem ser selecionados aleatoriamente ou com base nas funcionalidades específicas do conjunto de dados.

## <a name="next-steps"></a>Passos Seguintes
Pode aprender as noções básicas de Análise Preditiva e machine learning utilizando um [guia de introdução passo a passo](create-experiment.md) e por [tendo amostras](sample-experiments.md).


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
