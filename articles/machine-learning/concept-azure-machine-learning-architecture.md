---
title: Arquitetura & conceitos-chave
titleSuffix: Azure Machine Learning
description: Conheça a arquitetura, termos, conceitos e fluxos de trabalho que compõem o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 9f1d23f11cf73680a8861c9f1ac6cbd40ad497a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257339"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como funciona o Azure Machine Learning: Arquitetura e conceitos

Conheça a arquitetura, conceitos e fluxo de trabalho para azure machine learning. Os principais componentes do serviço e o fluxo de trabalho geral para a utilização do serviço são mostrados no seguinte diagrama:

![Arquitetura e fluxo de trabalho de aprendizagem automática azure](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho do modelo de aprendizagem automática geralmente segue esta sequência:

1. **Preparar**
    + Desenvolver scripts de treinamento de machine learning em **Python,** **R,** ou com o designer visual.
    + Criar e configurar um **alvo de cálculo.**
    + **Envie os scripts** para um alvo de cálculo configurado para executar nesse ambiente. Durante o treino, os scripts podem ler ou escrever para **as datastores.** Os registos e a saída produzidos durante o treino são guardados como **corre** no espaço de **trabalho** e agrupados sob **experiências**.

1. **Pacote** - Depois de encontrar uma execução satisfatória, registe o modelo persistente no registo do **modelo**.

1. **Validar** - **a experiência** para métricas registadas a partir das correntes e passadas. Se as métricas não indicarem um resultado desejado, volte ao passo 1 e iterará-lo nos seus scripts.

1. **Implementar** - Desenvolver um script de pontuação que utilize o modelo e **implemente o modelo** como **um serviço web** em Azure, ou para um dispositivo **IoT Edge**.

1. **Monitor** - Monitor para **a deriva** de dados entre o conjunto de dados de formação e os dados de inferência de um modelo implantado. Quando necessário, volte ao passo 1 para retreinar o modelo com novos dados de formação.

## <a name="tools-for-azure-machine-learning"></a>Ferramentas para aprendizagem automática azure

Utilize estas ferramentas para aprendizagem automática azure:

+  Interaja com o serviço em qualquer ambiente Python com o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interaja com o serviço em qualquer ambiente R com o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatizar as suas atividades de aprendizagem automática com o [AZURE Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Utilize o [designer de machine learning Azure (pré-visualização)](concept-designer.md) para executar os passos de fluxo de trabalho sem código de escrita.


> [!NOTE]
> Embora este artigo defina termos e conceitos utilizados pelo Azure Machine Learning, não define termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma Azure, consulte o [glossário do Microsoft Azure.](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)

## <a name="glossary"></a>Glossário

* [Atividade](#activities)
* [Área de trabalho](#workspaces)
    * [Experiências](#experiments)
        * [Executar](#runs) 
            * [Configuração de execução](#run-configurations)
            * [Instantâneo](#snapshots)
            * [Rastreio de Git](#github-tracking-and-integration)
            * [Registo](#logging)
    * [Pipelines de ML](#ml-pipelines)
    * [Modelos](#models)
        * [Ambientes](#environments)
        * [Roteiro de treino](#training-scripts)
        * [Estimadores](#estimators)
    * [Pontos Finais](#endpoints)
        * [Serviço web](#web-service-endpoint)
        * [Módulos IoT](#iot-module-endpoints)
    * [Dataset & datastores](#datasets-and-datastores)
    * [Os destinos de computação](#compute-targets)

### <a name="activities"></a>Atividades

Uma atividade representa uma operação de longa duração. As seguintes operações são exemplos de atividades:

* Criar ou eliminar um alvo de computação
* Executando um script em um alvo de computação

As atividades podem fornecer notificações através do SDK ou da Web UI para que possa monitorizar facilmente o progresso destas operações.

### <a name="workspaces"></a>Áreas de Trabalho

[O espaço de trabalho](concept-workspace.md) é o recurso de alto nível para o Azure Machine Learning. Fornece um lugar centralizado para trabalhar com todos os artefactos que cria quando utiliza o Azure Machine Learning. Podes partilhar um espaço de trabalho com os outros. Para uma descrição detalhada dos espaços de trabalho, veja o que é um espaço de [trabalho Azure Machine Learning?](concept-workspace.md)

### <a name="experiments"></a>Experimentações

Uma experiência é um agrupamento de muitas execuções a partir de um script especificado. Pertence sempre a um espaço de trabalho. Quando submete uma corrida, fornece um nome de experiência. A informação para a execução é armazenada sob esta experiência. Se submeter uma corrida e especificar um nome de experiência que não existe, uma nova experiência com esse nome recentemente especificado é automaticamente criada.

Para um exemplo de utilização de uma experiência, consulte [Tutorial: Treine o seu primeiro modelo](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Execuções

Uma corrida é uma execução única de um roteiro de treino. Uma experiência normalmente conterá várias corridas.

O Azure Machine Learning regista todas as execuções e lojas as seguintes informações na experiência:

* Metadados sobre a execução (carimbo de tempo, duração, e assim por diante)
* Métricas que são registadas pelo seu script
* Ficheiros de saída que são recolhidos automaticamente pela experiência ou explicitamente carregados por si
* Uma foto do diretório que contém os seus scripts, antes da execução

Produz-se uma corrida quando se submete um guião para treinar um modelo. Uma corrida pode ter zero ou mais corridas de crianças. Por exemplo, a corrida de alto nível pode ter duas corridas de crianças, cada uma das quais pode ter o seu próprio filho executado.

### <a name="run-configurations"></a>Configurações de execução

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um alvo de cálculo especificado. A configuração inclui um vasto conjunto de definições de comportamento, tais como usar um ambiente Python existente ou usar um ambiente Conda que é construído a partir de uma especificação.

Uma configuração de execução pode ser persistida num ficheiro dentro do diretório que contém o seu script de treino, ou pode ser construído como um objeto de memória e usado para submeter uma execução.

Por exemplo, executar configurações, consulte [Select e use um alvo de cálculo para treinar o seu modelo](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Instantâneos

Quando submete uma execução, o Azure Machine Learning comprime o diretório que contém o script como ficheiro zip e envia-o para o alvo da computação. O ficheiro zip é então extraído, e o script é executado lá. O Azure Machine Learning também armazena o ficheiro zip como instantâneo como parte do registo de execução. Qualquer pessoa com acesso ao espaço de trabalho pode navegar num registo de execução e descarregar o instantâneo.

> [!NOTE]
> Para evitar que ficheiros desnecessários sejam incluídos no instantâneo, faça um ficheiro de ignorar (.gitignore ou .amlignore). Coloque este ficheiro no diretório Snapshot e adicione os nomes de ficheiros para ignorar nele. O ficheiro .amlignore utiliza a mesma [sintaxe e padrões que o ficheiro .gitignore](https://git-scm.com/docs/gitignore). Se ambos os ficheiros existirem, o ficheiro .amlignore tem precedência.

### <a name="github-tracking-and-integration"></a>Rastreio e integração gitHub

Quando se inicia uma corrida de formação onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Isto funciona com execuções submetidas com um estimador, um gasoduto ML ou uma execução de script. Também funciona para executagens submetidas a partir do SDK ou Machine Learning CLI.

Para mais informações, consulte a [integração de Git para Azure Machine Learning.](concept-train-model-git-integration.md)

### <a name="logging"></a>Registo

Quando desenvolver a sua solução, utilize o SDK Python de Aprendizagem automática Azure no seu script Python para registar métricas arbitrárias. Após a execução, consultar as métricas para determinar se a execução produziu o modelo que pretende implementar.

### <a name="ml-pipelines"></a>Gasodutos ML

Utiliza-se gasodutos de aprendizagem automática para criar e gerir fluxos de trabalho que cosem fases de aprendizagem automática. Por exemplo, um gasoduto pode incluir preparação de dados, formação de modelos, implantação de modelos e fases de inferência/pontuação. Cada fase pode abranger vários passos, cada um dos quais pode ser executado sem vigilância em vários alvos de computação. 

Os passos do gasoduto são reutilizáveis e podem ser executados sem reexecutar os passos anteriores se a saída desses passos não tiver mudado. Por exemplo, pode retreinar um modelo sem reexecutar passos de preparação de dados dispendiosos se os dados não mudarem. Os oleodutos também permitem que os cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizagem automática.

Para obter mais informações sobre os gasodutos de aprendizagem automática com este serviço, consulte [Pipelines e Azure Machine Learning.](concept-ml-pipelines.md)

### <a name="models"></a>Modelos

No seu mais simples, um modelo é um pedaço de código que pega numa entrada e produz saída. Criar um modelo de machine learning envolve selecionar um algoritmo, fornecer-lhe dados e afinar hiperparâmetros. A formação é um processo iterativo que produz um modelo treinado, que engloba o que o modelo aprendeu durante o processo de treino.

Um modelo é produzido por uma corrida em Azure Machine Learning. Também pode usar um modelo treinado fora do Azure Machine Learning. Pode registar um modelo num espaço de trabalho azure machine learning.

Azure Machine Learning é agnóstico-quadro. Ao criar um modelo, pode utilizar qualquer quadro popular de aprendizagem automática, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para um exemplo de formação de um modelo usando scikit-learn e um estimador, consulte [Tutorial: Treine um modelo](tutorial-train-models-with-aml.md)de classificação de imagem com Azure Machine Learning .

O registo de **modelos** acompanha todos os modelos do seu espaço de trabalho Azure Machine Learning.

Os modelos são identificados pelo nome e pela versão. Cada vez que regista um modelo com o mesmo nome que um existente, o registo assume que é uma nova versão. A versão é incrementada e o novo modelo está registado com o mesmo nome.

Ao registar o modelo, pode fornecer etiquetas de metadados adicionais e, em seguida, utilizar as etiquetas quando procura modelos.

> [!TIP]
> Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que está armazenado em vários ficheiros, pode registá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registo, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros registados.

Não é possível eliminar um modelo registado que esteja a ser utilizado por uma implementação ativa.

Para um exemplo de registo de um modelo, consulte Treinar um modelo de classificação de [imagem com O Machine Learning Azure](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Ambientes

Os ambientes Azure ML são utilizados para especificar a configuração (Docker / Python / Spark / etc.) utilizada para criar um ambiente reprodutível para a preparação de dados, formação de modelos e serviço de modelos. São entidades geridas e versonizadas dentro do seu espaço de trabalho Azure Machine Learning que permitem fluxos de trabalho reprodutíveis, auditáveis e portáteis de aprendizagem automática em diferentes alvos de computação.

Você pode usar um objeto ambiental na sua computação local para desenvolver o seu script de treino, reutilizar esse mesmo ambiente na Azure Machine Learning Compute para formação de modelos em escala, e até mesmo implementar o seu modelo com esse mesmo ambiente. 

Aprenda [a criar e gerir um ambiente ML reutilizável](how-to-use-environments.md) para treino e inferência.

### <a name="training-scripts"></a>Scripts de treinamento

Para treinar um modelo, especifice o diretório que contém o script de treino e os ficheiros associados. Também especifica um nome de experiência, que é usado para armazenar informações recolhidas durante o treino. Durante o treino, todo o diretório é copiado para o ambiente de treino (alvo computacional), e o script especificado pela configuração de execução é iniciado. Uma imagem do diretório também é armazenada sob a experiência no espaço de trabalho.

Por exemplo, consulte [Tutorial: Treine um modelo](tutorial-train-models-with-aml.md)de classificação de imagem com Azure Machine Learning .

### <a name="estimators"></a>Estimadores

Para facilitar a formação de modelos com quadros populares, a classe estimador permite-lhe construir facilmente configurações de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para submeter scripts de formação que usam qualquer quadro de aprendizagem que você escolher (como scikit-learn).

Para as tarefas PyTorch, TensorFlow e Chainer, o Azure Machine Learning também fornece os respetivos estimativas [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar a utilização destas estruturas.

Para obter mais informações, veja os artigos seguintes:

* [Treine modelos ML com estimadores.](how-to-train-ml-models.md)
* [Treine modelos de aprendizagem profunda pytorch em escala com Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registe os modelos TensorFlow em escala com o Azure Machine Learning.](how-to-train-tensorflow.md)
* [Treine e registe os modelos Chainer em escala com o Azure Machine Learning.](how-to-train-ml-models.md)

### <a name="endpoints"></a>Pontos Finais

Um ponto final é uma instantiação do seu modelo num serviço web que pode ser hospedado na nuvem ou num módulo IoT para implementações integradas de dispositivos.

#### <a name="web-service-endpoint"></a>Ponto final do serviço web

Ao implantar um modelo como serviço web, o ponto final pode ser implantado em Instâncias de Contentores Azure, Serviço Azure Kubernetes ou FPGAs. Cria o serviço a partir do seu modelo, script e ficheiros associados. Estes são colocados numa imagem de recipiente de base que contém o ambiente de execução do modelo. A imagem tem um ponto final http equilibrado em carga que recebe pedidos de pontuação que são enviados para o serviço web.

O Azure ajuda-o a monitorizar o seu serviço web através da recolha de telemetria de Application Insights ou telemetria de modelos, se tiver optado por ativar esta funcionalidade. Os dados de telemetria são acessíveis apenas a si, e estão armazenados nas instâncias da sua conta de aplicação insights e armazenamento.

Se tiver ativado a escala automática, o Azure escala automaticamente a sua implantação.

Para um exemplo de implantação de um modelo como serviço web, consulte Implementar um modelo de classificação de [imagem em Instâncias de Contentores Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Pontos finais do módulo IoT

Um ponto final do módulo IoT implantado é um recipiente Docker que inclui o seu modelo e script ou aplicação associado e quaisquer dependências adicionais. Implementa estes módulos utilizando o Azure IoT Edge em dispositivos de borda.

Se tiver ativado a monitorização, o Azure recolhe dados de telemetria do modelo dentro do módulo Azure IoT Edge. Os dados de telemetria são acessíveis apenas a si, e estão armazenados na sua conta de armazenamento.

O Azure IoT Edge garante que o seu módulo está em funcionamento e monitoriza o dispositivo que o está a hospedar.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Instância computacional (pré-visualização)

Um caso de **computação azure machine learning** (anteriormente Notebook VM) é uma estação de trabalho totalmente gerida em nuvem que inclui múltiplas ferramentas e ambientes instalados para aprendizagem automática. Os casos de cálculo podem ser usados como um alvo de computação para trabalhos de formação e inferência. Para grandes tarefas, [os clusters computacionais de Aprendizagem automática Azure](how-to-set-up-training-targets.md#amlcompute) com capacidades de escala de vários nós é uma melhor escolha de alvo computacional.

Saiba mais sobre [os casos de computação.](concept-compute-instance.md)

### <a name="datasets-and-datastores"></a>Conjuntos de dados e datastores

Os Conjuntos de Dados de **Aprendizagem Automática Azure** (pré-visualização) facilitam o acesso e o trabalho com os seus dados. Os conjuntos de dados gerem dados em vários cenários, tais como formação de modelos e criação de gasodutos. Utilizando o Azure Machine Learning SDK, pode aceder ao armazenamento subjacente, explorar dados e gerir o ciclo de vida de diferentes definições de Dataset.

Os conjuntos de dados fornecem métodos para trabalhar `from_delimited_files()` `to_pandas_dataframe()`com dados em formatos populares, tais como a utilização ou .

Para mais informações, consulte Criar e registar Conjuntos de Dados de [Aprendizagem Automática Azure](how-to-create-register-datasets.md).  Para mais exemplos utilizando conjuntos de dados, consulte os [cadernos de amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Uma loja de **dados** é uma abstração de armazenamento sobre uma conta de armazenamento Azure. A loja de dados pode utilizar um recipiente de blob Azure ou uma partilha de ficheiros Azure como armazenamento final. Cada espaço de trabalho tem uma loja de dados padrão, e pode registar reservas adicionais de dados. Utilize o Python SDK API ou o Azure Machine Learning CLI para armazenar e recuperar ficheiros da loja de dados.

### <a name="compute-targets"></a>Os destinos de computação

Um [alvo computacional](concept-compute-target.md) permite especificar o recurso computacional onde executa o seu script de treino ou hospedar a sua implementação de serviço. Esta localização pode ser a sua máquina local ou um recurso computacional baseado em nuvem.

Saiba mais sobre os [objetivos de computação disponíveis para a formação e implementação.](concept-compute-target.md)

### <a name="next-steps"></a>Passos seguintes

Para começar com o Azure Machine Learning, veja:

* [O que é o Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar um espaço de trabalho azure machine learning](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treinar um modelo](tutorial-train-models-with-aml.md)
