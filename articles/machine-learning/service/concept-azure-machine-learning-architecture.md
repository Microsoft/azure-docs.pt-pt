---
title: Arquitetura & conceitos principais
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a arquitetura, os termos, os conceitos e os fluxos de trabalho que compõem o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: ea5e476680b07a6a7ba2b57e94f1f0b99cc10987
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990090"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Como Azure Machine Learning serviço funciona: Arquitetura e conceitos

Saiba mais sobre a arquitetura, os conceitos e o fluxo de trabalho para Azure Machine Learning serviço. Os principais componentes do serviço e o fluxo de trabalho geral para usar o serviço são mostrados no diagrama a seguir:

![Arquitetura do serviço de Azure Machine Learning e fluxo de trabalho](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho do modelo de aprendizado de máquina geralmente segue esta sequência:

1. **Trem**
    + Desenvolva scripts de treinamento do Machine Learning no **Python** ou com a interface visual.
    + Criar e configurar uma **destino de computação**.
    + **Submeter os scripts** para o destino de computação configurada para ser executado nesse ambiente. Durante o treinamento, os scripts podem ler ou gravar no **repositório de armazenamento**. E os registros de execução são salvos como são **executados** no **espaço de trabalho** e agrupados em **experimentos**.

1. **Pacote** -após uma execução satisfatória ser encontrada, registre o modelo persistente no registro de **modelo**.

1. Validar - **consulta o experimento** de métricas registradas das execuções atuais e anteriores. Se as métricas não indicarem um resultado desejado, execute o loop novamente para a etapa 1 e itere em seus scripts.

1. **Implantar** – desenvolver um script de pontuação que usa o modelo e **implantar o modelo** como um **serviço Web** no Azure ou em um **dispositivo IOT Edge**.

1. **Monitore** -monitor para descompasso de **dados** entre o conjunto de dados de treinamento e de inferência de um modelo implantado. Quando necessário, execute o loop novamente para a etapa 1 para treinar novamente o modelo com os novos dados de treinamento.

## <a name="tools-for-azure-machine-learning"></a>Ferramentas para Azure Machine Learning

Use estas ferramentas para Azure Machine Learning:

+  Interaja com o serviço em qualquer ambiente do Python com o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatize suas atividades de aprendizado de máquina com a [CLI do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Gravar código em Visual Studio Code com [Azure Machine Learning extensão de vs Code](how-to-vscode-tools.md)
+ Use a [interface visual (visualização) para Azure Machine Learning serviço](ui-concept-visual-interface.md) para executar as etapas do fluxo de trabalho sem escrever código.

> [!NOTE]
> Embora este artigo defina os termos e conceitos usados pelo serviço Azure Machine Learning, ele não define os termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma Azure, consulte o [Glossário de Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossário

+ <a href="#workspaces">Espaço</a>
+ <a href="#experiments">Experiências</a>
+ <a href="#models">Modelos</a>
+ <a href="#run-configurations">Configuração de execução</a>
+ [Estimativas](#estimators)
+ <a href="#datasets-and-datastores">Repositórios de & de conjunto de armazenamento</a>
+ <a href="#compute-targets">Destinos de computação</a>
+ <a href="#training-scripts">Script de treinamento</a>
+ <a href="#runs">Executar</a>
+ <a href="#github-tracking-and-integration">Rastreamento de git</a>
+ <a href="#snapshots">Instantânea</a>
+ <a href="#activities">Atividade</a>
+ <a href="#images">Imagem</a>
+ <a href="#deployment">Implementação</a>
+ <a href="#web-service-deployments">Serviços Web</a>
+ <a href="#iot-module-deployments">Módulos IoT</a>
+ <a href="#ml-pipelines">Pipelines de ML</a>
+ <a href="#logging">Logging</a>

### <a name="workspaces"></a>Áreas de trabalho

[O espaço de trabalho](concept-workspace.md) é o recurso de nível superior para Azure Machine Learning serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos criados quando você usa o serviço Azure Machine Learning. Você pode compartilhar um espaço de trabalho com outras pessoas. Para obter uma descrição detalhada dos espaços de trabalho, consulte [o que é um espaço de trabalho Azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Experimentações

Um experimento é um agrupamento de muitas execuções de um script especificado. Sempre pertence a uma área de trabalho. Quando submete uma execução, fornece um nome de experimentação. Informações para a execução são armazenadas desse experimento. Se você enviar uma execução e especificar um nome de experimento que não existe, um novo experimento com esse nome especificado recentemente será criado automaticamente.

Para obter um exemplo de como usar um experimento [, consulte o tutorial: Treine seu primeiro modelo](tutorial-1st-experiment-sdk-train.md).

### <a name="models"></a>Modelos

Em sua forma mais simples, um modelo é um trecho de código que usa entrada e produz um resultado. A criação de um modelo de aprendizagem automática envolve selecionar um algoritmo, fornecendo dados e ajuste hiperparâmetros. O treinamento é um processo iterativo que produz um modelo preparado, que encapsula o que o modelo aprendidas durante o processo de treinamento.

Um modelo é produzido por uma execução no Azure Machine Learning. Você também pode usar um modelo treinado fora do Azure Machine Learning. Você pode registrar um modelo em um espaço de trabalho do Azure Machine Learning Service.

O serviço de Azure Machine Learning é independente da estrutura. Ao criar um modelo, você pode usar qualquer estrutura de aprendizado de máquina popular, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para obter um exemplo de treinamento de um modelo usando Scikit-Learn e um estimador, consulte [o tutorial: Treine um modelo de classificação de imagem com](tutorial-train-models-with-aml.md)o serviço Azure Machine Learning.

O **registro de modelo** mantém o controle de todos os modelos em seu espaço de trabalho do Azure Machine Learning Service.

Modelos são identificados pelo nome e versão. Cada vez que você registra um modelo com o mesmo nome de um existente, o registro pressupõe que ele é uma nova versão. A versão é incrementada e o novo modelo é registrado com o mesmo nome.

Ao registrar o modelo, você pode fornecer marcas de metadados adicionais e, em seguida, usar as marcas ao pesquisar modelos.

> [!TIP]
> Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo em seu espaço de trabalho do Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que foram registrados.

Você não pode excluir um modelo registrado que está sendo usado por uma implantação ativa.

Para obter um exemplo de como registrar um modelo, consulte [treinar um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Configurações de execução

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um destino de computação especificado. A configuração inclui um amplo conjunto de definições de comportamento, como se usar um ambiente Python existente ou usar um ambiente Conda criado a partir de uma especificação.

Uma configuração de execução pode ser persistida em um arquivo dentro do diretório que contém o script de treinamento ou pode ser construída como um objeto na memória e usado para enviar uma execução.

Por exemplo, execute as configurações, consulte [selecionar e usar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Estimativas

Para facilitar o treinamento de modelo com estruturas populares, a classe Estimate permite que você construa facilmente configurações de execução. Você pode criar e usar um [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar scripts de treinamento que usam qualquer estrutura de aprendizado que você escolher (como scikit-learn).

Para as tarefas PyTorch, TensorFlow e Chainer, Azure Machine Learning também fornece os respectivos estimadores [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

Para obter mais informações, veja os artigos seguintes:

* [Treinar modelos ml com estimações](how-to-train-ml-models.md).
* [Treine os modelos de aprendizado profundo do Pytorch em escala com Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registre os modelos de TensorFlow em escala com o serviço Azure Machine Learning](how-to-train-tensorflow.md).
* [Treine e registre modelos de encadeamento em escala com o serviço Azure Machine Learning](how-to-train-chainer.md).

### <a name="datasets-and-datastores"></a>Conjuntos de e armazenamentos de

**Conjuntos de Azure Machine Learning** (versão prévia) facilite o acesso e o trabalho com seus dados. DataSets gerenciam dados em vários cenários, como treinamento de modelo e criação de pipeline. Usando o SDK do Azure Machine Learning, você pode acessar o armazenamento subjacente, explorar e preparar dados, gerenciar o ciclo de vida de definições de conjunto de dados diferentes e comparar entre os DataSets usados no treinamento e na produção.

DataSets fornecem métodos para trabalhar com dados em formatos populares, como usar `from_delimited_files()` o ou `to_pandas_dataframe()`o.

Para obter mais informações, consulte [criar e registrar conjuntos de dados Azure Machine Learning](how-to-create-register-datasets.md).  Para obter mais exemplos usando conjuntos de informações, consulte os [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/work-with-data/datasets).

Um **datastore** é uma abstração de armazenamento em uma conta de armazenamento do Azure. O repositório de armazenamento pode usar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como o armazenamento de back-end. Cada espaço de trabalho tem um repositório de armazenamento padrão e você pode registrar repositórios de armazenamento adicionais. Use a API do SDK do Python ou a CLI do Azure Machine Learning para armazenar e recuperar arquivos do repositório de armazenamento.

### <a name="compute-targets"></a>Destinos de computação

Um [destino de computação](concept-compute-target.md) permite especificar o recurso de computação em que você executa o script de treinamento ou hospeda sua implantação de serviço. Esse local pode ser seu computador local ou um recurso de computação baseado em nuvem. Os destinos de computação facilitam a alteração do ambiente de computação sem alterar seu código.

Saiba mais sobre os [destinos de computação disponíveis para treinamento e implantação](concept-compute-target.md).

### <a name="training-scripts"></a>Scripts de preparação

Para preparar um modelo, especifique o diretório que contém o script de formação e os ficheiros associados. Você também especifica um nome de experimento, que é usado para armazenar informações coletadas durante o treinamento. Durante o treinamento, o diretório inteiro é copiado para o ambiente de treinamento (destino de computação) e o script especificado pela configuração de execução é iniciado. Um instantâneo do diretório também é armazenado abaixo a experimentação na área de trabalho.

Para obter um exemplo, [consulte o tutorial: Treine um modelo de classificação de imagem com](tutorial-train-models-with-aml.md)o serviço Azure Machine Learning.

### <a name="runs"></a>Execuções

Uma execução é um registo que contém as seguintes informações:

* Metadados sobre a execução (carimbo de data/hora, duração e assim por diante)
* Métricas registradas por seu script
* Arquivos de saída que são coletados pelo experimento ou carregados explicitamente por você
* Um instantâneo do diretório que contém os scripts, antes da execução

Você produz uma execução quando envia um script para treinar um modelo. Uma execução pode ter zero ou mais execuções de subordinados. Por exemplo, a execução de nível superior pode ter duas execuções filhas, cada uma das quais pode ter sua própria execução filho.

### <a name="github-tracking-and-integration"></a>Acompanhamento e integração do GitHub

Quando você inicia uma execução de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execuções. Por exemplo, a ID de confirmação atual para o repositório é registrada como parte do histórico. Isso funciona com execuções enviadas usando um estimador, pipeline de ML ou execução de script. Ele também funciona para execuções enviadas do SDK ou da CLI do Machine Learning.

### <a name="snapshots"></a>Instantâneos

Quando você envia uma execução, Azure Machine Learning compacta o diretório que contém o script como um arquivo zip e o envia para o destino de computação. O arquivo zip é então extraído e o script é executado lá. O Azure Machine Learning também armazena o ficheiro zip como um instantâneo como parte do registo de execução. Qualquer pessoa com acesso à área de trabalho pode procurar um registo de execução e transferir o instantâneo.

> [!NOTE]
> Para impedir que arquivos desnecessários sejam incluídos no instantâneo, faça um arquivo ignorado (. gitignore ou. amlignore). Coloque esse arquivo no diretório de instantâneo e adicione os nomes de arquivos a serem ignorados nele. O arquivo. amlignore usa a mesma [sintaxe e padrões que o arquivo. gitignore](https://git-scm.com/docs/gitignore). Se ambos os arquivos existirem, o arquivo. amlignore terá precedência.

### <a name="activities"></a>Atividades

Uma atividade representa uma operação de longa execução. As seguintes operações são exemplos de atividades:

* Criar ou eliminar um destino de computação
* Executar um script num destino de computação

As atividades podem fornecer notificações por meio do SDK ou da interface do usuário da Web para que você possa monitorar facilmente o progresso dessas operações.

### <a name="images"></a>Imagens

As imagens fornecem uma maneira de implantar um modelo de forma confiável, juntamente com todos os componentes de que você precisa para usar o modelo. Uma imagem contém os seguintes itens:

* Um modelo.
* Um script de classificação ou um aplicativo. Você usa o script para passar a entrada para o modelo e retornar a saída do modelo.
* As dependências que são necessárias para o modelo ou script de pontuação ou aplicativo. Por exemplo, pode incluir um ficheiro de ambiente de Conda que lista as dependências de pacote do Python.

Azure Machine Learning pode criar dois tipos de imagens:

* **Imagem de FPGA**: Usado quando você implanta em uma matriz de portão programável por campo no Azure.
* **Imagem**do Docker: Usado quando você implanta em destinos de computação diferentes de FPGA. Os exemplos são as instâncias de contêiner do Azure e o serviço kubernetes do Azure.

O serviço de Azure Machine Learning fornece uma imagem base, que é usada por padrão. Você também pode fornecer suas próprias imagens personalizadas.

### <a name="image-registry"></a>Registo de imagem

As imagens são catalogadas no **registro de imagem** em seu espaço de trabalho. Você pode fornecer marcas de metadados adicionais ao criar a imagem, para que você possa consultá-las para localizar a imagem mais tarde.

Para obter um exemplo de criação de uma imagem, consulte [implantar um modelo de classificação de imagem em instâncias de contêiner do Azure](tutorial-deploy-models-with-aml.md).

Para obter um exemplo de implantação de um modelo usando uma imagem personalizada, consulte [como implantar um modelo usando uma imagem personalizada](how-to-deploy-custom-docker-image.md)do Docker.

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

### <a name="ml-pipelines"></a>Pipelines de ML

Você usa pipelines de Machine Learning para criar e gerenciar fluxos de trabalho que unem as fases de aprendizado de máquina. Por exemplo, um pipeline pode incluir preparação de dados, treinamento de modelo, implantação de modelo e fases de inferência/Pontuação. Cada fase pode incluir vários passos, cada um dos quais pode ser executado automaticamente em vários destinos de computação. 

As etapas de pipeline são reutilizáveis e podem ser executadas sem executar novamente as etapas subsequentes se a saída dessa etapa não tiver sido alterada. Por exemplo, você pode treinar novamente um modelo sem executar novamente as etapas dispendiosas de preparação de dados se os dados não forem alterados. Os pipelines também permitem que os cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizado de máquina.

Para obter mais informações sobre pipelines de Machine Learning com esse serviço, consulte [pipelines e Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Registo

Ao desenvolver sua solução, use o SDK do Azure Machine Learning Python em seu script Python para registrar métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução produziu o modelo que você deseja implantar.

### <a name="next-steps"></a>Passos Seguintes

Para começar a usar o Azure Machine Learning Service, consulte:

* [O que é o serviço Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar um espaço de trabalho de serviço do Azure Machine Learning](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treinar um modelo](tutorial-train-models-with-aml.md)
