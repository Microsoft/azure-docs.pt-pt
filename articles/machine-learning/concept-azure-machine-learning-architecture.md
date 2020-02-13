---
title: Arquitetura & conceitos principais
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a arquitetura, os termos, os conceitos e os fluxos de trabalho que compõem Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 5f5522201534a54f5d132257553469eed5addab3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169878"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como funciona a Azure Machine Learning: arquitetura e conceitos

Saiba mais sobre a arquitetura, os conceitos e o fluxo de trabalho para Azure Machine Learning. Os principais componentes do serviço e o fluxo de trabalho geral para usar o serviço são mostrados no diagrama a seguir:

![Arquitetura e fluxo de trabalho de Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho do modelo de aprendizado de máquina geralmente segue esta sequência:

1. **Comboio**
    + Desenvolver scripts de treinamento de machine learning em **Python** ou com o designer visual.
    + Criar e configurar um **alvo de cálculo.**
    + **Envie os scripts** para o alvo de cálculo configurado para executar nesse ambiente. Durante o treino, os scripts podem ler ou escrever para **datastore**. E os registos de execução são salvos à medida que **corre** no **espaço** de trabalho e agrupados sob **experiências.**

1. **Pacote** - Depois de encontrar uma execução satisfatória, registe o modelo persistente no registo do **modelo**.

1. **Valide** - **Consultar a experiência** para métricas registadas a partir das correntes e do passado. Se as métricas não indicarem um resultado desejado, execute o loop novamente para a etapa 1 e itere em seus scripts.

1. **Implementar** - Desenvolver um script de pontuação que utilize o modelo e **implemente o modelo** como **um serviço web** em Azure, ou para um dispositivo **IoT Edge**.

1. **Monitor** - Monitor para **a deriva** de dados entre o conjunto de dados de formação e os dados de inferência de um modelo implantado. Quando necessário, execute o loop novamente para a etapa 1 para treinar novamente o modelo com os novos dados de treinamento.

## <a name="tools-for-azure-machine-learning"></a>Ferramentas para Azure Machine Learning

Use estas ferramentas para Azure Machine Learning:

+  Interaja com o serviço em qualquer ambiente Python com o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interaja com o serviço em qualquer ambiente R com o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatizar as suas atividades de aprendizagem automática com o [AZURE Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Utilize o [designer de machine learning Azure (pré-visualização)](concept-designer.md) para executar os passos de fluxo de trabalho sem código de escrita.


> [!NOTE]
> Embora este artigo defina os termos e conceitos usados pelo Azure Machine Learning, ele não define os termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma Azure, consulte o [glossário do Microsoft Azure.](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)

## <a name="glossary"></a>Glossário
+ <a href="#activities">Atividade</a>
+ <a href="#compute-targets">Alvos computacionais</a>
+ <a href="#datasets-and-datastores">Dataset e datastores</a>
+ <a href="#endpoints">Pontos finais</a>
+ <a href="#environments">Ambientes</a>
+ [Estimadores](#estimators)
+ <a href="#experiments">Experiências</a>
+ <a href="#github-tracking-and-integration">Rastreio de Git</a>
+ <a href="#iot-module-endpoints">Módulos IoT</a>
+ <a href="#logging">Exploração madeireira</a>
+ <a href="#ml-pipelines">Oleodutos ML</a>
+ <a href="#models">Modelos</a>
+ <a href="#runs">Correr</a>
+ <a href="#run-configurations">Configuração de execução</a>
+ <a href="#snapshots">Instantâneo</a>
+ <a href="#training-scripts">Roteiro de treino</a>
+ <a href="#web-service-endpoint">Serviços web</a>
+ <a href="#workspaces">Espaço de trabalho</a>

### <a name="activities"></a>Atividades

Uma atividade representa uma operação de longa execução. As seguintes operações são exemplos de atividades:

* Criar ou eliminar um destino de computação
* Executar um script num destino de computação

As atividades podem fornecer notificações por meio do SDK ou da interface do usuário da Web para que você possa monitorar facilmente o progresso dessas operações.

### <a name="compute-instance"></a>Instância computacional (pré-visualização)


Um caso de **computação azure machine learning** (anteriormente Notebook VM) é uma estação de trabalho totalmente gerida em nuvem que inclui múltiplas ferramentas e ambientes instalados para aprendizagem automática. Os casos de cálculo podem ser usados como um alvo de computação para trabalhos de formação e inferência. Para grandes tarefas, [os clusters computacionais de Aprendizagem automática Azure](how-to-set-up-training-targets.md#amlcompute) com capacidades de escala de vários nós é uma melhor escolha de alvo computacional.

Saiba mais sobre [os casos de computação.](concept-compute-instance.md)

### <a name="compute-targets"></a>Os destinos de computação

Um [alvo computacional](concept-compute-target.md) permite especificar o recurso computacional onde executa o seu script de treino ou hospedar a sua implementação de serviço. Esta localização pode ser a sua máquina local ou um recurso computacional baseado em nuvem.

Saiba mais sobre os [objetivos de computação disponíveis para a formação e implementação.](concept-compute-target.md)

### <a name="datasets-and-datastores"></a>Conjuntos de e armazenamentos de

Os Conjuntos de Dados de **Aprendizagem Automática Azure** (pré-visualização) facilitam o acesso e o trabalho com os seus dados. DataSets gerenciam dados em vários cenários, como treinamento de modelo e criação de pipeline. Usando o SDK do Azure Machine Learning, você pode acessar o armazenamento subjacente, explorar dados e gerenciar o ciclo de vida de diferentes definições do conjunto de dados.

Os conjuntos de dados fornecem métodos para trabalhar com dados em formatos populares, tais como a utilização de `from_delimited_files()` ou `to_pandas_dataframe()`.

Para mais informações, consulte Criar e registar Conjuntos de Dados de [Aprendizagem Automática Azure](how-to-create-register-datasets.md).  Para mais exemplos utilizando conjuntos de dados, consulte os [cadernos de amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Uma loja de **dados** é uma abstração de armazenamento sobre uma conta de armazenamento Azure. O repositório de armazenamento pode usar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como o armazenamento de back-end. Cada espaço de trabalho tem um repositório de armazenamento padrão e você pode registrar repositórios de armazenamento adicionais. Use a API do SDK do Python ou a CLI do Azure Machine Learning para armazenar e recuperar arquivos do repositório de armazenamento.

### <a name="endpoints"></a>Pontos Finais

Um ponto de extremidade é uma instanciação do seu modelo em um serviço Web que pode ser hospedado na nuvem ou um módulo IoT para implantações de dispositivo integradas.

#### <a name="web-service-endpoint"></a>Ponto de extremidade de serviço Web

Ao implantar um modelo como um serviço Web, o ponto de extremidade pode ser implantado em instâncias de contêiner do Azure, serviço kubernetes do Azure ou FPGAs. Você cria o serviço de seu modelo, script e arquivos associados. Eles são colocados em uma imagem de contêiner de base que contém o ambiente de execução do modelo. A imagem tem um ponto de extremidade HTTP com balanceamento de carga que recebe solicitações de Pontuação enviadas para o serviço Web.

O Azure ajuda você a monitorar seu serviço Web coletando Application Insights telemetria ou telemetria de modelo, se você tiver optado por habilitar esse recurso. Os dados de telemetria só podem ser acessados por você e armazenados em suas instâncias de Application Insights e de conta de armazenamento.

Se você tiver habilitado o dimensionamento automático, o Azure dimensionará automaticamente a implantação.

Para um exemplo de implantação de um modelo como serviço web, consulte Implementar um modelo de classificação de [imagem em Instâncias de Contentores Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Pontos de extremidade do módulo IoT

Um ponto de extremidade do módulo IoT implantado é um contêiner do Docker que inclui seu modelo e o script ou aplicativo associado e quaisquer dependências adicionais. Você implanta esses módulos usando Azure IoT Edge em dispositivos de borda.

Se você habilitou o monitoramento, o Azure coletará dados de telemetria do modelo dentro do módulo Azure IoT Edge. Os dados de telemetria só podem ser acessados por você e armazenados em sua instância de conta de armazenamento.

Azure IoT Edge garante que o módulo está em execução e monitora o dispositivo que o hospeda.

### <a name="environments"></a>Ambientes

Os ambientes do Azure ML são usados para especificar a configuração (Docker/Python/Spark/etc.) usada para criar um ambiente reproduzível para preparação de dados, treinamento de modelo e serviço de modelo. São entidades geridas e versonizadas dentro do seu espaço de trabalho Azure Machine Learning que permitem fluxos de trabalho reprodutíveis, auditáveis e portáteis de aprendizagem automática em diferentes alvos de computação.

Você pode usar um objeto ambiental na sua computação local para desenvolver o seu script de treino, reutilizar esse mesmo ambiente na Azure Machine Learning Compute para formação de modelos em escala, e até mesmo implementar o seu modelo com esse mesmo ambiente. 

Aprenda [a criar e gerir um ambiente ML reutilizável](how-to-use-environments.md) para treino e inferência.

### <a name="estimators"></a>Estimativas

Para facilitar o treinamento de modelo com estruturas populares, a classe Estimate permite que você construa facilmente configurações de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para submeter scripts de formação que usam qualquer quadro de aprendizagem que você escolher (como scikit-learn).

Para as tarefas PyTorch, TensorFlow e Chainer, o Azure Machine Learning também fornece os respetivos estimativas [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar a utilização destas estruturas.

Para obter mais informações, veja os artigos seguintes:

* [Treine modelos ML com estimadores.](how-to-train-ml-models.md)
* [Treine modelos de aprendizagem profunda pytorch em escala com Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registe os modelos TensorFlow em escala com o Azure Machine Learning.](how-to-train-tensorflow.md)
* [Treine e registe os modelos Chainer em escala com o Azure Machine Learning.](how-to-train-chainer.md)

### <a name="experiments"></a>Experimentações

Um experimento é um agrupamento de muitas execuções de um script especificado. Sempre pertence a uma área de trabalho. Quando submete uma execução, fornece um nome de experimentação. Informações para a execução são armazenadas desse experimento. Se você enviar uma execução e especificar um nome de experimento que não existe, um novo experimento com esse nome especificado recentemente será criado automaticamente.

Para um exemplo de utilização de uma experiência, consulte [Tutorial: Treine o seu primeiro modelo](tutorial-1st-experiment-sdk-train.md).


### <a name="github-tracking-and-integration"></a>Rastreio e integração gitHub

Quando se inicia uma corrida de formação onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Isso funciona com execuções enviadas usando um estimador, pipeline de ML ou execução de script. Ele também funciona para execuções enviadas do SDK ou da CLI do Machine Learning.

Para mais informações, consulte a [integração de Git para Azure Machine Learning.](concept-train-model-git-integration.md)

### <a name="logging"></a>Registo

Ao desenvolver sua solução, use o SDK do Azure Machine Learning Python em seu script Python para registrar métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução produziu o modelo que você deseja implantar.

### <a name="ml-pipelines"></a>Pipelines de ML

Você usa pipelines de Machine Learning para criar e gerenciar fluxos de trabalho que unem as fases de aprendizado de máquina. Por exemplo, um pipeline pode incluir preparação de dados, treinamento de modelo, implantação de modelo e fases de inferência/Pontuação. Cada fase pode incluir vários passos, cada um dos quais pode ser executado automaticamente em vários destinos de computação. 

As etapas de pipeline são reutilizáveis e podem ser executadas sem executar novamente as etapas subsequentes se a saída dessa etapa não tiver sido alterada. Por exemplo, você pode treinar novamente um modelo sem executar novamente as etapas dispendiosas de preparação de dados se os dados não forem alterados. Os pipelines também permitem que os cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizado de máquina.

Para obter mais informações sobre os gasodutos de aprendizagem automática com este serviço, consulte [Pipelines e Azure Machine Learning.](concept-ml-pipelines.md)

### <a name="models"></a>Modelos

Em sua forma mais simples, um modelo é um trecho de código que usa entrada e produz um resultado. A criação de um modelo de aprendizagem automática envolve selecionar um algoritmo, fornecendo dados e ajuste hiperparâmetros. O treinamento é um processo iterativo que produz um modelo preparado, que encapsula o que o modelo aprendidas durante o processo de treinamento.

Um modelo é produzido por uma execução no Azure Machine Learning. Você também pode usar um modelo treinado fora do Azure Machine Learning. Você pode registrar um modelo em um espaço de trabalho Azure Machine Learning.

A Azure Machine Learning é independente da estrutura. Ao criar um modelo, você pode usar qualquer estrutura de aprendizado de máquina popular, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para um exemplo de formação de um modelo usando scikit-learn e um estimador, consulte [Tutorial: Treine um modelo](tutorial-train-models-with-aml.md)de classificação de imagem com Azure Machine Learning .

O registo de **modelos** acompanha todos os modelos do seu espaço de trabalho Azure Machine Learning.

Modelos são identificados pelo nome e versão. Cada vez que você registra um modelo com o mesmo nome de um existente, o registro pressupõe que ele é uma nova versão. A versão é incrementada e o novo modelo é registrado com o mesmo nome.

Ao registrar o modelo, você pode fornecer marcas de metadados adicionais e, em seguida, usar as marcas ao pesquisar modelos.

> [!TIP]
> Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que está armazenado em vários ficheiros, pode registá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registo, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros registados.

Você não pode excluir um modelo registrado que está sendo usado por uma implantação ativa.

Para um exemplo de registo de um modelo, consulte Treinar um modelo de classificação de [imagem com O Machine Learning Azure](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Corre

Uma execução é uma única execução de um script de treinamento. Azure Machine Learning registra todas as execuções e armazena as seguintes informações:

* Metadados sobre a execução (carimbo de data/hora, duração e assim por diante)
* Métricas registradas por seu script
* Arquivos de saída que são coletados pelo experimento ou carregados explicitamente por você
* Um instantâneo do diretório que contém os scripts, antes da execução

Você produz uma execução quando envia um script para treinar um modelo. Uma execução pode ter zero ou mais execuções de subordinados. Por exemplo, a execução de nível superior pode ter duas execuções filhas, cada uma das quais pode ter sua própria execução filho.

### <a name="run-configurations"></a>Configurações de execução

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um destino de computação especificado. A configuração inclui um vasto conjunto de definições de comportamento, tais como usar um ambiente Python existente ou usar um ambiente Conda que é construído a partir de uma especificação.

Uma configuração de execução pode ser persistida em um arquivo dentro do diretório que contém o script de treinamento ou pode ser construída como um objeto na memória e usado para enviar uma execução.

Por exemplo, executar configurações, consulte [Select e use um alvo de cálculo para treinar o seu modelo](how-to-set-up-training-targets.md).
### <a name="snapshots"></a>Instantâneos

Quando você envia uma execução, Azure Machine Learning compacta o diretório que contém o script como um arquivo zip e o envia para o destino de computação. O arquivo zip é então extraído e o script é executado lá. O Azure Machine Learning também armazena o ficheiro zip como um instantâneo como parte do registo de execução. Qualquer pessoa com acesso à área de trabalho pode procurar um registo de execução e transferir o instantâneo.

> [!NOTE]
> Para impedir que arquivos desnecessários sejam incluídos no instantâneo, faça um arquivo ignorado (. gitignore ou. amlignore). Coloque esse arquivo no diretório de instantâneo e adicione os nomes de arquivos a serem ignorados nele. O ficheiro .amlignore utiliza a mesma [sintaxe e padrões que o ficheiro .gitignore](https://git-scm.com/docs/gitignore). Se ambos os arquivos existirem, o arquivo. amlignore terá precedência.

### <a name="training-scripts"></a>Scripts de preparação

Para preparar um modelo, especifique o diretório que contém o script de formação e os ficheiros associados. Você também especifica um nome de experimento, que é usado para armazenar informações coletadas durante o treinamento. Durante o treinamento, o diretório inteiro é copiado para o ambiente de treinamento (destino de computação) e o script especificado pela configuração de execução é iniciado. Um instantâneo do diretório também é armazenado abaixo a experimentação na área de trabalho.

Por exemplo, consulte [Tutorial: Treine um modelo](tutorial-train-models-with-aml.md)de classificação de imagem com Azure Machine Learning .

### <a name="workspaces"></a>Áreas de Trabalho

[O espaço de trabalho](concept-workspace.md) é o recurso de alto nível para o Azure Machine Learning. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar ao usar o Azure Machine Learning. Você pode compartilhar um espaço de trabalho com outras pessoas. Para uma descrição detalhada dos espaços de trabalho, veja o que é um espaço de [trabalho Azure Machine Learning?](concept-workspace.md)

### <a name="next-steps"></a>Passos seguintes

Para começar com o Azure Machine Learning, veja:

* [O que é o Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar um espaço de trabalho azure machine learning](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treinar um modelo](tutorial-train-models-with-aml.md)
