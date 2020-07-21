---
title: Arquitetura & conceitos-chave
titleSuffix: Azure Machine Learning
description: Conheça a arquitetura, termos, conceitos e fluxos de trabalho que compõem a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/13/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 749a2366438bd1abfef4ca0cf2a195f23529d6a5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536305"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como funciona a Azure Machine Learning: Arquitetura e conceitos

Conheça a arquitetura, conceitos e fluxo de trabalho para Azure Machine Learning. Os principais componentes do serviço e o fluxo de trabalho geral para a utilização do serviço são apresentados no seguinte diagrama:

![Azure Machine Learning arquitetura e fluxo de trabalho](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho do modelo de aprendizagem automática geralmente segue esta sequência:

1. **Preparar**
    + Desenvolver scripts de treinamento de machine learning em **Python,** **R,** ou com o designer visual.
    + Criar e configurar um **alvo de cálculo**.
    + **Submeta os scripts** a um alvo de computação configurado para executar nesse ambiente. Durante o treino, os scripts podem ler ou escrever para **datastores.** Os registos e a produção produzidos durante o treino são guardados como **corridas** no **espaço de trabalho** e agrupados em **experiências**.

1. **Pacote** - Após uma execução satisfatória, registe o modelo persistido no **registo do modelo**.

1. **Validar**  -  **Consultar a experiência** para métricas registadas a partir das correntes e do passado. Se as métricas não indicarem um resultado desejado, volte ao passo 1 e itere nos seus scripts.

1. **Implementar** - Desenvolver um script de pontuação que utilize o modelo e **implemente o modelo** como um **serviço web** em Azure, ou para um **dispositivo IoT Edge**.

1. **Monitor** - Monitor para **a deriva de dados** entre o conjunto de dados de formação e os dados de inferência de um modelo implantado. Quando necessário, volte ao passo 1 para voltar a treinar o modelo com novos dados de treino.

## <a name="tools-for-azure-machine-learning"></a>Ferramentas para aprendizagem automática Azure

Utilize estas ferramentas para aprendizagem automática Azure:

> [!IMPORTANT]
> As ferramentas marcadas (pré-visualização) abaixo estão atualmente em visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interaja com o serviço em qualquer ambiente Python com o [Azure Machine Learning SDK para Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Interaja com o serviço em qualquer ambiente R com o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (pré-visualização).
+ Automatize as suas atividades de machine learning com o [CLI de Aprendizagem automática Azure.](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ Utilize [o designer de aprendizagem automática Azure (pré-visualização)](concept-designer.md) para executar os passos de fluxo de trabalho sem escrever código. (Um [espaço de trabalho da Enterprise](concept-workspace.md#upgrade)) é necessário para usar designer.)
+ O [Acelerador de Soluções de Muitos Modelos](https://aka.ms/many-models) (pré-visualização) baseia-se no Azure Machine Learning e permite-lhe treinar, operar e gerir centenas ou mesmo milhares de modelos de machine learning.

> [!NOTE]
> Embora este artigo defina termos e conceitos usados pela Azure Machine Learning, não define termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma Azure, consulte o glossário microsoft [Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossário

* [Atividade](#activities)
* [Espaço de trabalho](#workspaces)
    * [Experiências](#experiments)
        * [Executar](#runs) 
            * [Configuração de execução](#run-configurations)
            * [Instantâneo](#snapshots)
            * [Rastreio de Git](#github-tracking-and-integration)
            * [Registo de madeira](#logging)
    * [Pipelines de ML](#ml-pipelines)
    * [Modelos](#models)
        * [Ambientes](#environments)
        * [Roteiro de treino](#training-scripts)
        * [Estimadores](#estimators)
    * [Pontos Finais](#endpoints)
        * [Serviço Web](#web-service-endpoint)
        * [Módulos IoT](#iot-module-endpoints)
    * [Dataset & datastores](#datasets-and-datastores)
    * [Os destinos de computação](#compute-targets)

### <a name="activities"></a>Atividades

Uma atividade representa uma operação de longa duração. As seguintes operações são exemplos de atividades:

* Criar ou eliminar um alvo de computação
* Executar um script em um alvo de computação

As atividades podem fornecer notificações através do SDK ou da UI web para que possa monitorizar facilmente o progresso destas operações.

### <a name="workspaces"></a>Áreas de trabalho

[O espaço de trabalho](concept-workspace.md) é o recurso de alto nível para a Azure Machine Learning. Ele fornece um lugar centralizado para trabalhar com todos os artefactos que você cria quando você usa Azure Machine Learning. Podes partilhar um espaço de trabalho com os outros. Para uma descrição detalhada dos espaços de trabalho, veja o que é um espaço de [trabalho de aprendizagem automática Azure?](concept-workspace.md)

### <a name="experiments"></a>Experimentações

Uma experiência é um agrupamento de muitas corridas a partir de um script especificado. Pertence sempre a um espaço de trabalho. Quando submete uma corrida, fornece um nome de experiência. A informação para a execução é armazenada no âmbito dessa experiência. Se submeter uma execução e especificar um nome de experiência que não existe, uma nova experiência com esse nome recentemente especificado é automaticamente criada.

Para um exemplo de utilização de uma experiência, consulte [Tutorial: Treine o seu primeiro modelo.](tutorial-1st-experiment-sdk-train.md)

### <a name="runs"></a>Execuções

Uma corrida é uma única execução de um roteiro de treino. Uma experiência normalmente conterá várias corridas.

A Azure Machine Learning regista todas as execuções e armazena as seguintes informações na experiência:

* Metadados sobre a execução (timetamp, duração, e assim por diante)
* Métricas que são registadas pelo seu script
* Ficheiros de saída que são automaticamente recolhidos pela experiência ou enviados explicitamente por si
* Uma imagem do diretório que contém os seus scripts, antes da execução

Produz-se uma corrida quando se envia um guião para treinar um modelo. Uma corrida pode ter zero ou mais corridas de crianças. Por exemplo, a corrida de alto nível pode ter duas corridas de crianças, cada uma das quais pode ter a sua própria criança gerida.

### <a name="run-configurations"></a>Executar configurações

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um alvo de computação especificado. A configuração inclui um vasto conjunto de definições de comportamento, tais como se usar um ambiente python existente ou usar um ambiente Conda que é construído a partir de uma especificação.

Uma configuração de execução pode ser persistido num ficheiro dentro do diretório que contém o seu script de treino, ou pode ser construído como um objeto na memória e usado para submeter uma execução.

Por exemplo, executar configurações, ver [Selecione e use um alvo de computação para treinar o seu modelo.](how-to-set-up-training-targets.md)

### <a name="snapshots"></a>Instantâneos

Quando submete uma execução, o Azure Machine Learning comprime o diretório que contém o script como ficheiro zip e envia-o para o alvo do cálculo. O ficheiro zip é então extraído, e o script é executado lá. A Azure Machine Learning também armazena o ficheiro zip como um instantâneo como parte do registo de execução. Qualquer pessoa com acesso ao espaço de trabalho pode navegar num registo de execução e descarregar o instantâneo.

> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="github-tracking-and-integration"></a>Rastreio e integração do GitHub

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Isto funciona com runs submetidos usando um estimador, pipeline ML ou script run. Também funciona para as execuções submetidas a partir do SDK ou machine learning CLI.

Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

### <a name="logging"></a>Registo

Quando desenvolver a sua solução, use o Azure Machine Learning Python SDK no seu script Python para registar métricas arbitrárias. Após a execução, questione as métricas para determinar se o funcionante produziu o modelo que pretende implementar.

### <a name="ml-pipelines"></a>Gasodutos ML

Você usa oleodutos de aprendizagem automática para criar e gerir fluxos de trabalho que cosem fases de aprendizagem automática. Por exemplo, um gasoduto pode incluir preparação de dados, formação de modelos, implantação de modelos e fases de inferência/pontuação. Cada fase pode abranger vários passos, cada um dos quais pode correr sem vigilância em vários alvos de computação. 

Os passos do gasoduto são reutilizáveis e podem ser executados sem repetir os passos anteriores se a saída desses passos não tiver mudado. Por exemplo, pode reforçá-lo um modelo sem repetir passos de preparação de dados dispendiosos se os dados não mudarem. Os oleodutos também permitem que os cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizagem automática.

Para obter mais informações sobre os oleodutos de aprendizagem automática com este serviço, consulte [Pipelines e Azure Machine Learning.](concept-ml-pipelines.md)

### <a name="models"></a>Modelos

No seu mais simples, um modelo é um pedaço de código que pega numa entrada e produz a saída. Criar um modelo de aprendizagem automática envolve selecionar um algoritmo, fornecer-lhe dados e sintonizar hiperparímetros. A formação é um processo iterativo que produz um modelo treinado, que encapsula o que o modelo aprendeu durante o processo de treino.

Um modelo é produzido por uma corrida em Azure Machine Learning. Também pode usar um modelo treinado fora do Azure Machine Learning. Pode registar um modelo num espaço de trabalho Azure Machine Learning.

Azure Machine Learning é agnóstico enquadrante. Quando cria um modelo, pode utilizar qualquer estrutura popular de aprendizagem automática, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para um exemplo de formação de um modelo utilizando o Scikit-learn e um estimador, consulte [Tutorial: Treine um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

O **registo de modelos** acompanha todos os modelos do seu espaço de trabalho Azure Machine Learning.

Os modelos são identificados pelo nome e pela versão. Cada vez que regista um modelo com o mesmo nome que um existente, o registo assume que é uma nova versão. A versão é incrementada e o novo modelo está registado com o mesmo nome.

Quando regista o modelo, pode fornecer etiquetas de metadados adicionais e, em seguida, utilizar as etiquetas quando procura por modelos.

> [!TIP]
> Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registo, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que foram registados.

Não é possível eliminar um modelo registado que esteja a ser utilizado por uma implementação ativa.

Para um exemplo de registo de um modelo, consulte [Train um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Ambientes

Azure ML Ambientes são usados para especificar a configuração (Docker / Python / Spark / etc.) usada para criar um ambiente reprodutível para a preparação de dados, formação de modelos e serviço de modelos. São entidades geridas e versadas dentro do seu espaço de trabalho Azure Machine Learning que permitem fluxos de trabalho de aprendizagem automática reprodutível, auditáveis e portáteis em diferentes alvos de computação.

Você pode usar um objeto ambiental no seu computo local para desenvolver o seu script de treino, reutilizar esse mesmo ambiente no Azure Machine Learning Compute para formação de modelos em escala, e até mesmo implementar o seu modelo com esse mesmo ambiente. 

Aprenda [a criar e gerir um ambiente ML reutilizável](how-to-use-environments.md) para treino e inferência.

### <a name="training-scripts"></a>Scripts de treino

Para treinar um modelo, especifica o diretório que contém o script de treino e os ficheiros associados. Também especifica um nome de experiência, que é usado para armazenar informações recolhidas durante o treino. Durante o treino, todo o diretório é copiado para o ambiente de treino (alvo de computação), e o script especificado pela configuração de execução é iniciado. Uma foto do diretório também é armazenada sob a experiência no espaço de trabalho.

Por exemplo, consulte [Tutorial: Treine um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Estimadores

Para facilitar a formação de modelos com quadros populares, a classe de estimador permite-lhe construir facilmente configurações de execução. Pode criar e utilizar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para submeter scripts de formação que utilizem qualquer estrutura de aprendizagem que escolher (como o scikit-learn).

Para tarefas PyTorch, TensorFlow e Chainer, a Azure Machine Learning também fornece os respetivos estimadores [de PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar usando estes quadros.

Para obter mais informações, veja os seguintes artigos:

* [Treinar modelos ML com estimadores.](how-to-train-ml-models.md)
* [Treine pytorch modelos de aprendizagem profunda em escala com Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registe os modelos TensorFlow em escala com a Azure Machine Learning](how-to-train-tensorflow.md).
* [Treine e registe os modelos Chainer em escala com a Azure Machine Learning](how-to-train-ml-models.md).

### <a name="endpoints"></a>Pontos Finais

Um ponto final é uma instantânea do seu modelo num serviço web que pode ser hospedado na nuvem ou num módulo IoT para implementações integradas de dispositivos.

#### <a name="web-service-endpoint"></a>Ponto final do serviço web

Ao implementar um modelo como serviço web, o ponto final pode ser implantado em Instâncias de Contentores Azure, Serviço Azure Kubernetes ou FPGAs. Cria o serviço a partir do seu modelo, script e ficheiros associados. Estes são colocados numa imagem de recipiente base que contém o ambiente de execução do modelo. A imagem tem um ponto final HTTP equilibrado em carga que recebe pedidos de pontuação que são enviados para o serviço web.

O Azure ajuda-o a monitorizar o seu serviço web recolhendo telemetria de Insights de Aplicação ou telemetria de modelo, se tiver optado por ativar esta funcionalidade. Os dados de telemetria são acessíveis apenas a si, e são armazenados nas suas informações de aplicação e casos de conta de armazenamento.

Se tiver ativado o dimensionamento automático, o Azure escala automaticamente a sua implantação.

Para um exemplo de implantação de um modelo como serviço web, consulte [implementar um modelo de classificação de imagem em Instâncias de Contentores Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Pontos finais do módulo IoT

Um ponto final de módulo IoT implantado é um recipiente Docker que inclui o seu modelo e script ou aplicação associados e quaisquer dependências adicionais. Você implementa estes módulos utilizando Azure IoT Edge em dispositivos de borda.

Se tiver ativado a monitorização, o Azure recolhe dados de telemetria do modelo dentro do módulo Azure IoT Edge. Os dados de telemetria são acessíveis apenas a si, e são armazenados na sua conta de armazenamento.

O Azure IoT Edge garante que o seu módulo está em funcionamento e monitoriza o dispositivo que o acolhe.


### <a name="compute-instance"></a><a name="compute-instance"></a>Instância de computação

Um **caso de computação Azure Machine Learning** (anteriormente Notebook VM) é uma estação de trabalho totalmente gerida baseada na nuvem que inclui múltiplas ferramentas e ambientes instalados para machine learning. Os casos de cálculo podem ser usados como um alvo de computação para a formação e inferenção de empregos. Para grandes tarefas, [os clusters de cálculo Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com capacidades de escala de múltiplos nós são uma melhor escolha de destino de computação.

Saiba mais sobre [casos de computação.](concept-compute-instance.md)

### <a name="datasets-and-datastores"></a>Conjuntos de dados e datastores

**Os conjuntos de dados de aprendizagem de máquinas Azure** (pré-visualização) facilitam o acesso e o trabalho com os seus dados. Os conjuntos de dados gerem dados em vários cenários, como a formação de modelos e a criação de gasodutos. Utilizando o Azure Machine Learning SDK, pode aceder ao armazenamento subjacente, explorar dados e gerir o ciclo de vida de diferentes definições do Dataset.

Os conjuntos de dados fornecem métodos para trabalhar com dados em formatos populares, tais como a utilização `from_delimited_files()` ou `to_pandas_dataframe()` .

Para obter mais informações, consulte [Criar e registar conjuntos de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md).  Para obter mais exemplos utilizando conjuntos de dados, consulte os [cadernos de amostras.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)

Uma **datastore** é uma abstração de armazenamento sobre uma conta de armazenamento Azure. A loja de dados pode usar um recipiente de bolhas Azure ou uma partilha de ficheiros Azure como armazenamento de back-end. Cada espaço de trabalho tem uma datastore predefinido, e pode registar datastores adicionais. Utilize o Python SDK API ou o Azure Machine Learning CLI para armazenar e recuperar ficheiros da datastore.

### <a name="compute-targets"></a>Os destinos de computação

Um [alvo de cálculo](concept-compute-target.md) permite especificar o recurso computacional onde executa o seu script de treino ou hospeda a sua implementação de serviço. Esta localização pode ser a sua máquina local ou um recurso computacional baseado na nuvem.

Saiba mais sobre os [alvos de computação disponíveis para treino e implantação.](concept-compute-target.md)

### <a name="next-steps"></a>Próximos passos

Para começar com a Azure Machine Learning, consulte:

* [O que é o Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar um espaço de trabalho para aprendizagem de máquinas Azure](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treine um modelo](tutorial-train-models-with-aml.md)
