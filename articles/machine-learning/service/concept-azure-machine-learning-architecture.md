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
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: 706f76c00022c5f5661ea261a5bb35eedc13d5ba
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756037"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como funciona a Azure Machine Learning: arquitetura e conceitos

Saiba mais sobre a arquitetura, os conceitos e o fluxo de trabalho para Azure Machine Learning. Os principais componentes do serviço e o fluxo de trabalho geral para usar o serviço são mostrados no diagrama a seguir:

![Arquitetura e fluxo de trabalho de Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Fluxo de Trabalho

O fluxo de trabalho do modelo de aprendizado de máquina geralmente segue esta sequência:

1. **Trem**
    + Desenvolva scripts de treinamento do Machine Learning no **Python** ou com a interface visual.
    + Criar e configurar um **destino de computação**.
    + **Envie os scripts** para o destino de computação configurado para execução nesse ambiente. Durante o treinamento, os scripts podem ler ou gravar no **repositório de armazenamento**. E os registros de execução são salvos como são **executados** no **espaço de trabalho** e agrupados em **experimentos**.

1. **Pacote** -após uma execução satisfatória ser encontrada, registre o modelo persistente no registro de **modelo**.

1. **Validar**  - **consultar o experimento** quanto a métricas registradas das execuções atuais e anteriores. Se as métricas não indicarem um resultado desejado, execute o loop novamente para a etapa 1 e itere em seus scripts.

1. **Implantar** – desenvolver um script de pontuação que usa o modelo e **implantar o modelo** como um **serviço Web** no Azure ou em um **dispositivo IOT Edge**.

1. **Monitore** -monitor para **descompasso de dados** entre o conjunto de dados de treinamento e de inferência de um modelo implantado. Quando necessário, execute o loop novamente para a etapa 1 para treinar novamente o modelo com os novos dados de treinamento.

## <a name="tools-for-azure-machine-learning"></a>Ferramentas para Azure Machine Learning

Use estas ferramentas para Azure Machine Learning:

+  Interaja com o serviço em qualquer ambiente do Python com o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatize suas atividades de aprendizado de máquina com a [CLI do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Gravar código em Visual Studio Code com [Azure Machine Learning extensão de vs Code](how-to-vscode-tools.md)
+ Use a [interface visual (visualização) para Azure Machine Learning](ui-concept-visual-interface.md) executar as etapas do fluxo de trabalho sem escrever código.

> [!NOTE]
> Embora este artigo defina os termos e conceitos usados pelo Azure Machine Learning, ele não define os termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma Azure, consulte o [Glossário de Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossário
+ <a href="#activities">Atividade</a>
+ <a href="#compute-targets">Destinos de computação</a>
+ <a href="#datasets-and-datastores">Repositórios de & de conjunto de armazenamento</a>
+ <a href="#deployment">Implementação</a>
+ <a href="#environments">Sistemas</a>
+ [Estimativas](#estimators)
+ <a href="#experiments">Experiências</a>
+ <a href="#github-tracking-and-integration">Rastreamento de git</a>
+ <a href="#iot-module-deployments">Módulos IoT</a>
+ <a href="#logging">Logout</a>
+ <a href="#ml-pipelines">Pipelines de ML</a>
+ <a href="#models">Modelos</a>
+ <a href="#runs">Funcionam</a>
+ <a href="#run-configurations">Configuração de execução</a>
+ <a href="#snapshots">Instantânea</a>
+ <a href="#training-scripts">Script de treinamento</a>
+ <a href="#web-service-deployments">Serviços Web</a>
+ <a href="#workspaces">Espaço</a>

### <a name="activities"></a>Atividades

Uma atividade representa uma operação de execução longa. As seguintes operações são exemplos de atividades:

* Criando ou excluindo um destino de computação
* Executando um script em um destino de computação

As atividades podem fornecer notificações por meio do SDK ou da interface do usuário da Web para que você possa monitorar facilmente o progresso dessas operações.

### <a name="compute-targets"></a>Destinos de computação

Um [destino de computação](concept-compute-target.md) permite especificar o recurso de computação em que você executa o script de treinamento ou hospeda sua implantação de serviço. Esse local pode ser seu computador local ou um recurso de computação baseado em nuvem. Os destinos de computação facilitam a alteração do ambiente de computação sem alterar seu código.

Saiba mais sobre os [destinos de computação disponíveis para treinamento e implantação](concept-compute-target.md).

### <a name="datasets-and-datastores"></a>Conjuntos de e armazenamentos de

Os conjuntos de dados **Azure Machine Learning** (versão prévia) facilitam o acesso e o trabalho com eles. DataSets gerenciam dados em vários cenários, como treinamento de modelo e criação de pipeline. Usando o SDK do Azure Machine Learning, você pode acessar o armazenamento subjacente, explorar dados e gerenciar o ciclo de vida de diferentes definições do conjunto de dados.

DataSets fornecem métodos para trabalhar com dados em formatos populares, como usar `from_delimited_files()` ou `to_pandas_dataframe()`.

Para obter mais informações, consulte [criar e registrar conjuntos de dados Azure Machine Learning](how-to-create-register-datasets.md).  Para obter mais exemplos usando conjuntos de informações, consulte os [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Um **datastore** é uma abstração de armazenamento em uma conta de armazenamento do Azure. O repositório de armazenamento pode usar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como o armazenamento de back-end. Cada espaço de trabalho tem um repositório de armazenamento padrão e você pode registrar repositórios de armazenamento adicionais. Use a API do SDK do Python ou a CLI do Azure Machine Learning para armazenar e recuperar arquivos do repositório de armazenamento.

### <a name="deployment"></a>Implementação

Uma implantação é uma instanciação do seu modelo em um serviço Web que pode ser hospedado na nuvem ou um módulo IoT para implantações de dispositivo integradas.

#### <a name="web-service-deployments"></a>Implantações de serviço Web

Um serviço Web implantado pode usar as instâncias de contêiner do Azure, o serviço kubernetes do Azure ou o FPGAs. Você cria o serviço de seu modelo, script e arquivos associados. Eles são encapsulados em uma imagem, que fornece o ambiente de tempo de execução para o serviço Web. A imagem tem um ponto de extremidade HTTP com balanceamento de carga que recebe solicitações de Pontuação enviadas para o serviço Web.

O Azure ajuda a monitorar a implantação do serviço Web coletando Application Insights telemetria ou telemetria de modelo, se você tiver optado por habilitar esse recurso. Os dados de telemetria só podem ser acessados por você e armazenados em suas instâncias de Application Insights e de conta de armazenamento.

Se você tiver habilitado o dimensionamento automático, o Azure dimensionará automaticamente a implantação.

Para obter um exemplo de implantação de um modelo como um serviço Web, consulte [implantar um modelo de classificação de imagem em instâncias de contêiner do Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>Implantações de módulo IoT

Um módulo IoT implantado é um contêiner do Docker que inclui seu modelo e o script ou aplicativo associado e quaisquer dependências adicionais. Você implanta esses módulos usando Azure IoT Edge em dispositivos de borda.

Se você habilitou o monitoramento, o Azure coletará dados de telemetria do modelo dentro do módulo Azure IoT Edge. Os dados de telemetria só podem ser acessados por você e armazenados em sua instância de conta de armazenamento.

Azure IoT Edge garante que o módulo está em execução e monitora o dispositivo que o hospeda.

### <a name="environments"></a>Ambientes

Os ambientes do Azure ML são usados para especificar a configuração (Docker/Python/Spark/etc.) usada para criar um ambiente reproduzível para preparação de dados, treinamento de modelo e serviço de modelo. Eles são entidades gerenciadas e com controle de versão em seu espaço de trabalho de Azure Machine Learning que permitem fluxos de trabalho de aprendizado de máquina reproduzíveis, auditáveis e portáteis entre diferentes destinos de computação.

Você pode usar um objeto de ambiente em sua computação local para desenvolver seu script de treinamento, reutilizar o mesmo ambiente em Azure Machine Learning computação para treinamento de modelo em escala e até mesmo implantar seu modelo com o mesmo ambiente. 

Saiba [como criar e gerenciar um ambiente de ml reutilizável](how-to-use-environments.md) para treinamento e inferência.

### <a name="estimators"></a>Estimativas

Para facilitar o treinamento de modelo com estruturas populares, a classe Estimate permite que você construa facilmente configurações de execução. Você pode criar e usar um [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar scripts de treinamento que usam qualquer estrutura de aprendizado que você escolher (como scikit-learn).

Para as tarefas PyTorch, TensorFlow e Chainer, Azure Machine Learning também fornece os respectivos estimadores [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

Para obter mais informações, veja os artigos seguintes:

* [Treinar modelos ml com estimações](how-to-train-ml-models.md).
* [Treine os modelos de aprendizado profundo do Pytorch em escala com Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registre os modelos de TensorFlow em escala com Azure Machine Learning](how-to-train-tensorflow.md).
* [Treine e registre modelos de encadeamento em escala com Azure Machine Learning](how-to-train-chainer.md).

### <a name="experiments"></a>Experimentações

Um experimento é um agrupamento de muitas execuções de um script especificado. Ele sempre pertence a um espaço de trabalho. Ao enviar uma execução, você fornece um nome de experimento. As informações para a execução são armazenadas nesse experimento. Se você enviar uma execução e especificar um nome de experimento que não existe, um novo experimento com esse nome especificado recentemente será criado automaticamente.

Para obter um exemplo de como usar um experimento, consulte [tutorial: treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md).


### <a name="github-tracking-and-integration"></a>Acompanhamento e integração do GitHub

Quando você inicia uma execução de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execuções. Isso funciona com execuções enviadas usando um estimador, pipeline de ML ou execução de script. Ele também funciona para execuções enviadas do SDK ou da CLI do Machine Learning.

Para obter mais informações, consulte [integração do git para Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Registo

Ao desenvolver sua solução, use o SDK do Azure Machine Learning Python em seu script Python para registrar métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução produziu o modelo que você deseja implantar.

### <a name="ml-pipelines"></a>Pipelines de ML

Você usa pipelines de Machine Learning para criar e gerenciar fluxos de trabalho que unem as fases de aprendizado de máquina. Por exemplo, um pipeline pode incluir preparação de dados, treinamento de modelo, implantação de modelo e fases de inferência/Pontuação. Cada fase pode abranger várias etapas, cada uma delas pode ser executada de forma autônoma em vários destinos de computação. 

As etapas de pipeline são reutilizáveis e podem ser executadas sem executar novamente as etapas subsequentes se a saída dessa etapa não tiver sido alterada. Por exemplo, você pode treinar novamente um modelo sem executar novamente as etapas dispendiosas de preparação de dados se os dados não forem alterados. Os pipelines também permitem que os cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizado de máquina.

Para obter mais informações sobre pipelines de Machine Learning com esse serviço, consulte [pipelines e Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modelos

Em sua forma mais simples, um modelo é um trecho de código que usa uma entrada e produz a saída. A criação de um modelo de aprendizado de máquina envolve a seleção de um algoritmo, o fornecimento de dados e o ajuste de hiperparâmetros. O treinamento é um processo iterativo que produz um modelo treinado, que encapsula o que o modelo aprendeu durante o processo de treinamento.

Um modelo é produzido por uma execução em Azure Machine Learning. Você também pode usar um modelo treinado fora do Azure Machine Learning. Você pode registrar um modelo em um espaço de trabalho Azure Machine Learning.

A Azure Machine Learning é independente da estrutura. Ao criar um modelo, você pode usar qualquer estrutura de aprendizado de máquina popular, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para obter um exemplo de treinamento de um modelo usando Scikit-Learn e um estimador, consulte [tutorial: treinar um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

O **registro de modelo** mantém o controle de todos os modelos em seu espaço de trabalho Azure Machine Learning.

Os modelos são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um existente, o registro pressupõe que ele é uma nova versão. A versão é incrementada e o novo modelo é registrado com o mesmo nome.

Ao registrar o modelo, você pode fornecer marcas de metadados adicionais e, em seguida, usar as marcas ao pesquisar modelos.

> [!TIP]
> Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo em seu espaço de trabalho do Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que foram registrados.

Você não pode excluir um modelo registrado que está sendo usado por uma implantação ativa.

Para obter um exemplo de como registrar um modelo, consulte [treinar um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="runs"></a>Execuções

Uma execução é uma única execução de um script de treinamento. Azure Machine Learning registra todas as execuções e armazena as seguintes informações:

* Metadados sobre a execução (carimbo de data/hora, duração e assim por diante)
* Métricas registradas por seu script
* Arquivos de saída que são coletados pelo experimento ou carregados explicitamente por você
* Um instantâneo do diretório que contém seus scripts, antes da execução

Você produz uma execução quando envia um script para treinar um modelo. Uma execução pode ter zero ou mais execuções filhas. Por exemplo, a execução de nível superior pode ter duas execuções filhas, cada uma das quais pode ter sua própria execução filho.

### <a name="run-configurations"></a>Configurações de execução

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um destino de computação especificado. A configuração inclui um amplo conjunto de definições de comportamento, como se usar um ambiente Python existente ou usar um ambiente Conda criado a partir de uma especificação.

Uma configuração de execução pode ser persistida em um arquivo dentro do diretório que contém o script de treinamento ou pode ser construída como um objeto na memória e usado para enviar uma execução.

Por exemplo, execute as configurações, consulte [selecionar e usar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md).
### <a name="snapshots"></a>Instantâneos

Quando você envia uma execução, Azure Machine Learning compacta o diretório que contém o script como um arquivo zip e o envia para o destino de computação. O arquivo zip é então extraído e o script é executado lá. Azure Machine Learning também armazena o arquivo zip como um instantâneo como parte do registro de execução. Qualquer pessoa com acesso ao espaço de trabalho pode procurar um registro de execução e baixar o instantâneo.

> [!NOTE]
> Para impedir que arquivos desnecessários sejam incluídos no instantâneo, faça um arquivo ignorado (. gitignore ou. amlignore). Coloque esse arquivo no diretório de instantâneo e adicione os nomes de arquivos a serem ignorados nele. O arquivo. amlignore usa a mesma [sintaxe e padrões que o arquivo. gitignore](https://git-scm.com/docs/gitignore). Se ambos os arquivos existirem, o arquivo. amlignore terá precedência.

### <a name="training-scripts"></a>Scripts de treinamento

Para treinar um modelo, você especifica o diretório que contém o script de treinamento e os arquivos associados. Você também especifica um nome de experimento, que é usado para armazenar informações coletadas durante o treinamento. Durante o treinamento, o diretório inteiro é copiado para o ambiente de treinamento (destino de computação) e o script especificado pela configuração de execução é iniciado. Um instantâneo do diretório também é armazenado sob o experimento no espaço de trabalho.

Para obter um exemplo, consulte [tutorial: treinar um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="workspaces"></a>Áreas de trabalho

[O espaço de trabalho](concept-workspace.md) é o recurso de nível superior para Azure Machine Learning. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar ao usar o Azure Machine Learning. Você pode compartilhar um espaço de trabalho com outras pessoas. Para obter uma descrição detalhada dos espaços de trabalho, consulte [o que é um espaço de trabalho Azure Machine Learning?](concept-workspace.md).


### <a name="next-steps"></a>Passos seguintes

Para começar a usar o Azure Machine Learning, consulte:

* [O que é o Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md)
* [Tutorial (parte 1): treinar um modelo](tutorial-train-models-with-aml.md)
