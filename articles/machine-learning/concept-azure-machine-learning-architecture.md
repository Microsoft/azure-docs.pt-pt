---
title: Arquitetura & conceitos-chave
titleSuffix: Azure Machine Learning
description: Conheça a arquitetura, termos e conceitos que compõem a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: d7bad24510f74a7fadd74328e24ea22855e6fe02
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750854"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como funciona a Azure Machine Learning: Arquitetura e conceitos

Conheça a arquitetura e os conceitos para Azure Machine Learning.

> [!NOTE]
> Embora este artigo defina termos e conceitos usados pela Azure Machine Learning, não define termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma Azure, consulte o glossário microsoft [Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a><a name="workspace"></a> Espaço de trabalho

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Arquitetura de aprendizagem automática Azure":::

Um espaço de trabalho de [aprendizagem automática](concept-workspace.md) é o recurso de alto nível para a Azure Machine Learning.  O espaço de trabalho é o lugar centralizado para:
* Gerir os recursos que utiliza para a formação e implementação de modelos, como [computas](#compute-instance)
* Armazenar os ativos que cria quando utiliza a Azure Machine Learning, incluindo:
  * [Ambientes](#environments)
  * [Execuções](#runs)
  * [Pipelines](#ml-pipelines)
  * [Conjuntos de dados](#datasets-and-datastores)
  * [Modelos](#models)
  * [Pontos finais](#endpoints)

Um espaço de trabalho inclui outros recursos Azure que são usados pelo espaço de trabalho:

+ [Registo do contentor Azure](https://azure.microsoft.com/services/container-registry/): Regista os recipientes de estivadores que utiliza durante o treino e quando implementa um modelo. Para minimizar os custos, o ACR é **preguiçoso até** que as imagens de implantação sejam criadas.
+ [Conta de Armazenamento Azure](https://azure.microsoft.com/services/storage/): É utilizada como a datastore predefinida para o espaço de trabalho.  Os cadernos Jupyter que são usados com os seus casos de cálculo de Aprendizagem de Máquinas Azure também estão armazenados aqui.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Armazena informações de monitorização sobre os seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Armazena segredos que são usados por alvos computacional e outras informações sensíveis que são necessárias pelo espaço de trabalho.

Podes partilhar um espaço de trabalho com os outros.

## <a name="studio"></a>Studio

[O estúdio Azure Machine Learning](https://ml.azure.com) oferece uma visão web de todos os artefactos do seu espaço de trabalho.  Este portal é também onde você acede às ferramentas interativas que fazem parte do Azure Machine Learning:

+ [Azure Machine Learning designer (pré-visualização)](concept-designer.md) para executar etapas de fluxo de trabalho sem código de escrita
+ Experiência web para [aprendizagem automática de máquinas](concept-automated-ml.md)
+ [Projetos de rotulagem de dados](how-to-create-labeling-projects.md) para criar, gerir e monitorizar projetos para rotular os seus dados

##  <a name="computes"></a>Cálculos

<a name="compute-targets"></a> Um [alvo de cálculo](concept-compute-target.md) é a máquina ou conjunto de máquinas onde executa o seu script de treino ou hospeda a sua implementação de serviço. Esta localização pode ser a sua máquina local ou um recurso de computação remota.

A Azure Machine Learning introduz dois recursos de computação totalmente geridos baseados na nuvem que são configurados para tarefas de aprendizagem automática:

* <a name="compute-instance"></a>**Instância** [computacional (computeinstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance?view=azure-ml-py)): Uma instância computacional é uma máquina virtual (VM) que inclui múltiplas ferramentas e ambientes instalados para machine learning. Use uma instância de cálculo como a sua estação de trabalho de desenvolvimento para começar a executar cadernos de amostra sem necessidade de configuração. Também pode ser usado como um alvo de computação para a formação e inferenundo empregos.
* **Clusters computativo** [(amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)): Cluster de VMs com capacidades de escalonamento de vários nós. Aumenta automaticamente quando um trabalho é submetido. Mais adequado para metas de computação para grandes empregos e produção. Utilizar como alvo de computação de treino ou para implantação dev/teste.

Para obter mais informações sobre os alvos de computação de formação, consulte [os alvos do computação de formação](concept-compute-target.md#train).  Para obter mais informações sobre os alvos de computação de implantação, consulte [os alvos de implementação](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Conjuntos de dados e datastores

[**Os conjuntos de dados de aprendizagem de máquinas Azure**](concept-data.md#datasets)  facilitam o acesso e o trabalho com os seus dados. Os conjuntos de dados gerem dados em vários cenários, como a formação de modelos e a criação de gasodutos. Utilizando o Azure Machine Learning SDK, pode aceder ao armazenamento subjacente, explorar dados e gerir o ciclo de vida de diferentes definições do Dataset.

Os conjuntos de dados fornecem métodos para trabalhar com dados em formatos populares, tais como a utilização `from_delimited_files()` ou `to_pandas_dataframe()` .

Para obter mais informações, consulte [Criar e registar conjuntos de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md).  Para obter mais exemplos utilizando conjuntos de dados, consulte os [cadernos de amostras.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)

Uma [**datastore**](concept-data.md#datastores) é uma abstração de armazenamento sobre uma conta de armazenamento Azure. Cada espaço de trabalho tem uma datastore predefinido, e pode registar datastores adicionais. Utilize o Python SDK API ou o Azure Machine Learning CLI para armazenar e recuperar ficheiros da datastore. 

## <a name="models"></a>Modelos

No seu mais simples, um modelo é um pedaço de código que pega numa entrada e produz a saída. A criação de um modelo de aprendizagem automática envolve selecionar um algoritmo, fornecer-lhe dados e [sintonizar hiperparímetros.](how-to-tune-hyperparameters.md) A formação é um processo iterativo que produz um modelo treinado, que encapsula o que o modelo aprendeu durante o processo de treino.

Um modelo é produzido por uma [série](#runs) de [experiências](#experiments) em Azure Machine Learning. Também pode usar um modelo treinado fora do Azure Machine Learning. Depois [regista-se o modelo](#register-model) no espaço de trabalho.

Azure Machine Learning é agnóstico enquadrante. Quando cria um modelo, pode utilizar qualquer estrutura popular de aprendizagem automática, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para um exemplo de formação de um modelo que usa Scikit-learn, consulte [Tutorial: Treine um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a><a name="register-model"></a> Registo de modelos
[Espaço de](#workspace)  >  trabalho **Registo de modelos**

O **registo de modelos** permite-lhe acompanhar todos os modelos do seu espaço de trabalho Azure Machine Learning.

Os modelos são identificados pelo nome e pela versão. Cada vez que regista um modelo com o mesmo nome que um existente, o registo assume que é uma nova versão. A versão é incrementada e o novo modelo está registado com o mesmo nome.

Quando regista o modelo, pode fornecer etiquetas de metadados adicionais e, em seguida, utilizar as etiquetas quando procura por modelos.

> [!TIP]
> Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registo, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que foram registados.

Não é possível eliminar um modelo registado que esteja a ser utilizado por uma implementação ativa.

Para um exemplo de registo de um modelo, consulte [Train um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="environments"></a>Ambientes

[Espaço de](#workspace)  >  trabalho **Ambientes**

Um [ambiente](concept-environments.md) é a encapsulação do ambiente onde o treino ou a pontuação do seu modelo de aprendizagem automática acontecem. O ambiente especifica os pacotes Python, variáveis ambientais e configurações de software em torno dos seus scripts de treino e pontuação.

Para obter amostras de código, consulte a secção "Gerir ambientes" de [como utilizar ambientes](how-to-use-environments.md#manage-environments).

### <a name="experiments"></a>Experimentações

[Espaço de](#workspace)  >  trabalho **Experiências**

Uma experiência é um agrupamento de muitas corridas a partir de um script especificado. Pertence sempre a um espaço de trabalho. Quando submete uma corrida, fornece um nome de experiência. A informação para a execução é armazenada no âmbito dessa experiência. Se o nome não existir quando se submete uma experiência, uma nova experiência é criada automaticamente.

Para um exemplo de utilização de uma experiência, consulte [Tutorial: Treine o seu primeiro modelo.](tutorial-1st-experiment-sdk-train.md)

### <a name="runs"></a>Execuções

[Espaço de](#workspace)  >  trabalho [Experiências](#experiments)  >  **Correr**

Uma corrida é uma única execução de um roteiro de treino. Uma experiência normalmente conterá várias corridas.

A Azure Machine Learning regista todas as execuções e armazena as seguintes informações na experiência:

* Metadados sobre a execução (timetamp, duração, e assim por diante)
* Métricas que são registadas pelo seu script
* Ficheiros de saída que são automaticamente recolhidos pela experiência ou enviados explicitamente por si
* Uma imagem do diretório que contém os seus scripts, antes da execução

Produz-se uma corrida quando se envia um guião para treinar um modelo. Uma corrida pode ter zero ou mais corridas de crianças. Por exemplo, a corrida de alto nível pode ter duas corridas de crianças, cada uma das quais pode ter a sua própria criança gerida.

### <a name="run-configurations"></a>Executar configurações

[Espaço de](#workspace)  >  trabalho [Experiências](#experiments)  >  [Correr](#runs)  >  **Configuração de execução**

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um alvo de computação especificado. A configuração inclui um vasto conjunto de definições de comportamento, tais como se usar um ambiente python existente ou usar um ambiente Conda que é construído a partir de uma especificação.

Uma configuração de execução pode ser persistido num ficheiro dentro do diretório que contém o seu script de treino.   Ou pode ser construído como um objeto na memória e usado para submeter uma corrida.

Por exemplo, executar configurações, ver [Selecione e use um alvo de computação para treinar o seu modelo.](how-to-set-up-training-targets.md)

### <a name="estimators"></a>Estimadores

Para facilitar a formação de modelos com quadros populares, a classe de estimador permite-lhe construir facilmente configurações de execução. Pode criar e utilizar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para submeter scripts de formação que utilizem qualquer estrutura de aprendizagem que escolher (como o scikit-learn).

Para tarefas PyTorch, TensorFlow e Chainer, a Azure Machine Learning também fornece os respetivos estimadores [de PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar usando estes quadros.

Para obter mais informações, veja os seguintes artigos:

* [Treinar modelos ML com estimadores.](how-to-train-ml-models.md)
* [Treine pytorch modelos de aprendizagem profunda em escala com Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registe os modelos TensorFlow em escala com a Azure Machine Learning](how-to-train-tensorflow.md).
* [Treine e registe os modelos Chainer em escala com a Azure Machine Learning](how-to-train-ml-models.md).

### <a name="snapshots"></a>Instantâneos

[Espaço de](#workspace)  >  trabalho [Experiências](#experiments)  >  [Correr](#runs)  >  **Instantâneo**

Quando submete uma execução, o Azure Machine Learning comprime o diretório que contém o script como ficheiro zip e envia-o para o alvo do cálculo. O ficheiro zip é então extraído, e o script é executado lá. A Azure Machine Learning também armazena o ficheiro zip como um instantâneo como parte do registo de execução. Qualquer pessoa com acesso ao espaço de trabalho pode navegar num registo de execução e descarregar o instantâneo.


### <a name="logging"></a>Registo

Quando desenvolver a sua solução, use o Azure Machine Learning Python SDK no seu script Python para registar métricas arbitrárias. Após a execução, questione as métricas para determinar se o funcionante produziu o modelo que pretende implementar.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Isto funciona com runs submetidos usando um estimador, pipeline ML ou script run. Também funciona para as execuções submetidas a partir do SDK ou machine learning CLI.

Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="deployment"></a>Implementação

Implementa um [modelo registado](#register-model) como ponto final de serviço. Precisa dos seguintes componentes:

* **Ambiente**. Este ambiente engloba as dependências necessárias para executar o seu modelo para inferência.
* **Código de pontuação.** Este script aceita pedidos, pontua os pedidos utilizando o modelo e devolve os resultados.
* **Configuração de inferência**. A configuração da inferência especifica o ambiente, o script de entrada e outros componentes necessários para executar o modelo como um serviço.

Para obter mais informações sobre estes componentes, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Pontos Finais

[Espaço de](#workspace)  >  trabalho **Pontos finais**

Um ponto final é uma instantânea do seu modelo num serviço web que pode ser hospedado na nuvem ou num módulo IoT para implementações integradas de dispositivos.

#### <a name="web-service-endpoint"></a>Ponto final do serviço web

Ao implementar um modelo como serviço web, o ponto final pode ser implantado em Instâncias de Contentores Azure, Serviço Azure Kubernetes ou FPGAs. Cria o serviço a partir do seu modelo, script e ficheiros associados. Estes são colocados numa imagem de recipiente base, que contém o ambiente de execução do modelo. A imagem tem um ponto final HTTP equilibrado em carga que recebe pedidos de pontuação que são enviados para o serviço web.

Pode ativar a telemetria do Application Insights ou a telemetria do modelo para monitorizar o seu serviço web. Os dados de telemetria são acessíveis apenas a si.  Está armazenado nas suas informações de aplicação e casos de conta de armazenamento.

Se tiver ativado o dimensionamento automático, o Azure escala automaticamente a sua implantação.

Para um exemplo de implantação de um modelo como serviço web, consulte [implementar um modelo de classificação de imagem em Instâncias de Contentores Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Pontos finais do módulo IoT

Um ponto final de módulo IoT implantado é um recipiente Docker que inclui o seu modelo e script ou aplicação associados e quaisquer dependências adicionais. Você implementa estes módulos utilizando Azure IoT Edge em dispositivos de borda.

Se tiver ativado a monitorização, o Azure recolhe dados de telemetria do modelo dentro do módulo Azure IoT Edge. Os dados de telemetria são acessíveis apenas a si, e são armazenados na sua conta de armazenamento.

O Azure IoT Edge garante que o seu módulo está em funcionamento e monitoriza o dispositivo que o acolhe.
. 
## <a name="automation"></a>Automatização

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

O [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) é uma extensão do Azure CLI, uma interface de linha de comando transversal para a plataforma Azure. Esta extensão fornece comandos para automatizar as suas atividades de aprendizagem automática.

### <a name="ml-pipelines"></a>Gasodutos ML

Você usa [oleodutos de aprendizagem automática](concept-ml-pipelines.md) para criar e gerir fluxos de trabalho que cosem fases de aprendizagem automática. Por exemplo, um gasoduto pode incluir preparação de dados, formação de modelos, implantação de modelos e fases de inferência/pontuação. Cada fase pode abranger vários passos, cada um dos quais pode correr sem vigilância em vários alvos de computação. 

Os passos do gasoduto são reutilizáveis e podem ser executados sem repetir os passos anteriores se a saída desses passos não tiver mudado. Por exemplo, pode reforçá-lo um modelo sem repetir passos de preparação de dados dispendiosos se os dados não mudarem. Os oleodutos também permitem que os cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizagem automática.

## <a name="interacting-with-machine-learning"></a>Interagindo com a aprendizagem automática

> [!IMPORTANT]
> As ferramentas marcadas (pré-visualização) abaixo estão atualmente em visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interaja com o serviço em qualquer ambiente Python com o [Azure Machine Learning SDK para Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Interaja com o serviço em qualquer ambiente R com o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (pré-visualização).
+ Utilize [o designer de aprendizagem automática Azure (pré-visualização)](concept-designer.md) para executar os passos de fluxo de trabalho sem escrever código. (Um [espaço de trabalho da Enterprise](concept-workspace.md#upgrade)) é necessário para usar designer.)
+ Utilize [o CLI de aprendizagem automática Azure](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) para automatização.
+ O [Acelerador de Soluções de Muitos Modelos](https://aka.ms/many-models) (pré-visualização) baseia-se no Azure Machine Learning e permite-lhe treinar, operar e gerir centenas ou mesmo milhares de modelos de machine learning.

## <a name="next-steps"></a>Passos seguintes

Para começar com a Azure Machine Learning, consulte:

* [O que é o Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar um espaço de trabalho para aprendizagem de máquinas Azure](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treine um modelo](tutorial-train-models-with-aml.md)
