---
title: Arquitetura e conceitos-chave
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a arquitetura, termos, conceitos e fluxo de trabalho que formam o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: 2196e375db582202997b838d05c902db95b3a3ad
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461471"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Como funciona o serviço Azure Machine Learning: Conceitos e arquitetura

Saiba mais sobre a arquitetura, conceitos e fluxo de trabalho para o serviço Azure Machine Learning. Os principais componentes do serviço e o fluxo de trabalho geral para utilizar o serviço são apresentados no diagrama seguinte:

![Arquitetura de serviço do Azure Machine Learning e o fluxo de trabalho](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Fluxo de trabalho

O machine learning em geral, o fluxo de trabalho do modelo segue essa seqüência:

1. **Train**
    + Desenvolver a aprendizagem, treinamento scripts no **Python** ou com a interface visual.
    + Criar e configurar uma **destino de computação**.
    + **Submeter os scripts** para o destino de computação configurada para ser executado nesse ambiente. Durante o treinamento, os scripts podem ler ou escrever **arquivo de dados**. E os registos de execução são guardados como **executa** no **área de trabalho** e agrupados sob **experimentações**.

1. **Pacote** - depois de uma execução satisfatória for encontrada, registe o modelo persistente na **registo de modelo**.

1. **Validar** - **consultar a experimentação** para as métricas registadas de execuções a atuais e anteriores. Se as métricas não indicam um resultado desejado, fazer um loop novamente para o passo 1 e iterar em seus scripts.

1. **Deploy** -desenvolver um script de classificação que utiliza o modelo e **implementar o modelo** como uma **serviço web** no Azure ou para um **dispositivo IoT Edge**.

1. **Monitor** -monitorizar **descompassos dados** entre os dados de conjunto de dados e inferência de tipos de treinamento de um modelo implementado. Quando for necessário, fazer um loop novamente para o passo 1 para voltar a preparar o modelo com novos dados de treinamento.

## <a name="tools-for-azure-machine-learning"></a>Ferramentas para o Azure Machine Learning 

Utilize estas ferramentas para o Azure Machine Learning:

+  Interagir com o serviço em qualquer ambiente de Python com o [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatizar a sua aprendizagem automática atividades com o [do Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Escrever código no Visual Studio Code com [extensão do Azure Machine Learning VS Code](how-to-vscode-tools.md) 
+ Utilize o [interface visual (pré-visualização) para o serviço Azure Machine Learning](ui-concept-visual-interface.md) para efetuar os passos de fluxo de trabalho sem escrever código.

## <a name="glossary-of-concepts"></a>Glossário dos conceitos

+ <a href="#workspaces">Área de trabalho</a>
+ <a href="#experiments">Experiências</a>
+ <a href="#models">modelos</a>
+ <a href="#run-configurations">Configuração de execução</a>
+ <a href="#datasets-and-datastores">Arquivos de dados e o conjunto de dados</a>
+ <a href="#compute-targets">Destinos de computação</a>
+ <a href="#training-scripts">Script de treinamento</a>
+ <a href="#runs">Executar</a>
+ <a href="#github-tracking-and-integration">Controlo de Git</a>
+ <a href="#snapshots">instantâneo</a>
+ <a href="#activities">Atividade</a>
+ <a href="#images">Imagem</a>
+ <a href="#deployment">Implementação</a>
+ <a href="#web-service-deployments">Serviços Web</a>
+ <a href="#iot-module-deployments">Módulos do IoT</a>
+ <a href="#ml-pipelines">Pipelines de ML</a>
+ <a href="#logging">Logging</a>

> [!NOTE]
> Embora este artigo define os termos e conceitos utilizados pelo serviço Azure Machine Learning, não definir termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma do Azure, consulte a [Glossário do Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).


### <a name="workspaces"></a>Áreas de Trabalho

[A área de trabalho](concept-workspace.md) é o recurso de nível superior para o serviço Azure Machine Learning. Ele fornece um local centralizado para trabalhar com todos os artefactos que criar quando utilizar o serviço Azure Machine Learning.

Uma taxonomia da área de trabalho é ilustrada no diagrama seguinte:

[![Taxonomia da área de trabalho](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Para obter mais informações sobre áreas de trabalho, consulte [o que é uma área de trabalho do Azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Experimentações

Uma experimentação é um agrupamento de várias execuções de um script especificado. Sempre pertence a uma área de trabalho. Quando submete uma execução, fornece um nome de experimentação. Informações para a execução são armazenadas desse experimento. Se submeter uma execução e especifique um nome de experimentação que não existe, é automaticamente criada uma nova experimentação com esse nome recentemente especificado.

Para obter um exemplo da utilização de uma experimentação, consulte [início rápido: Introdução ao serviço Azure Machine Learning](quickstart-run-cloud-notebook.md).

### <a name="models"></a>Modelos

Em sua forma mais simples, um modelo é um trecho de código que usa entrada e produz um resultado. A criação de um modelo de aprendizagem automática envolve selecionar um algoritmo, fornecendo dados e ajuste hiperparâmetros. O treinamento é um processo iterativo que produz um modelo preparado, que encapsula o que o modelo aprendidas durante o processo de treinamento.

Um modelo é produzido por uma execução no Azure Machine Learning. Também pode utilizar um modelo que está preparado fora do Azure Machine Learning. Pode registrar um modelo numa área de trabalho do serviço do Azure Machine Learning.

O serviço do Azure Machine Learning é agnóstico quanto a estrutura. Quando cria um modelo, pode utilizar qualquer estrutura de aprendizado de máquina populares, como Scikit-saiba, XGBoost, PyTorch, TensorFlow e Chainer.

Para obter um exemplo de preparar um modelo, consulte [Tutorial: Preparar um modelo de classificação de imagem com o serviço Azure Machine Learning](tutorial-train-models-with-aml.md).

O **registo de modelo** mantém um registro de todos os modelos na sua área de trabalho do serviço do Azure Machine Learning.

Modelos são identificados pelo nome e versão. Sempre que registar um modelo com o mesmo nome que um já existente, o registro parte do princípio de que é uma nova versão. A versão é incrementada e o novo modelo está registado com o mesmo nome.

Quando registra o modelo, pode fornecer marcas de metadados adicionais e, em seguida, utilizar as etiquetas quando procurar modelos.

> [!TIP]
> Um modelo registado é um contentor lógico para um ou mais arquivos que formam o seu modelo. Por exemplo, se tiver um modelo que está armazenado em vários arquivos, pode registá-los como um modelo único na sua área de trabalho do Azure Machine Learning. Depois do Registro, pode, em seguida, transferir ou implementar o modelo registado e receber todos os ficheiros que foram registados.

Não é possível eliminar um modelo registado que está a ser utilizado por uma implementação ativa.

Para obter um exemplo de um modelo de registo, consulte [preparar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Configurações de execução

Uma configuração de execução é um conjunto de instruções que define como executar um script num destino de computação especificado. A configuração inclui uma grande variedade de definições de comportamento, por exemplo, se utilizar um ambiente de Python existente ou utilizar um ambiente de Conda que é criado a partir de uma especificação.

Uma configuração de execução pode ser mantida num arquivo dentro do diretório que contém o script de treinamento, ou pode ser construído como um objeto na memória e utilizado para submeter uma execução.

Por exemplo, executar configurações, consulte [selecionar e utilizar um destino de computação para preparar o seu modelo](how-to-set-up-training-targets.md).

### <a name="datasets-and-datastores"></a>Conjuntos de dados e arquivos de dados

**Conjuntos de dados do Azure Machine Learning** (pré-visualização) que seja mais fácil acesso e trabalhar com os seus dados. Conjuntos de dados de gerir os dados em vários cenários, tais como a preparação de modelos e criação de pipelines. Com o SDK do Azure Machine Learning, pode aceder ao armazenamento subjacente, explorar e preparar dados, gerir o ciclo de vida de definições do conjunto de dados diferentes e comparar entre conjuntos de dados utilizados no treinamento e produção.

Conjuntos de dados fornece métodos para trabalhar com dados em formatos populares, como o uso `from_delimited_files()` ou `to_pandas_dataframe()`.

Para obter mais informações, consulte [criar e registar conjuntos de dados do Azure Machine Learning](how-to-create-register-datasets.md).  Para obter mais exemplos utilizando conjuntos de dados, consulte a [blocos de notas de exemplo](https://aka.ms/dataset-tutorial).

R **arquivo de dados** é uma abstração de armazenamento através de uma conta de armazenamento do Azure. O arquivo de dados pode utilizar um contentor de Blobs do Azure ou uma partilha de ficheiros do Azure, como o armazenamento de back-end. Cada área de trabalho tem um arquivo de dados padrão e pode registar os arquivos de dados adicionais. Utilize a API do SDK de Python ou a CLI do Azure Machine Learning para armazenar e recuperar ficheiros a partir do arquivo de dados.

### <a name="compute-targets"></a>Destinos de computação

R [destino de computação](concept-compute-target.md) permite-lhe especificar o recurso de computação em que executou o script de treinamento ou anfitrião a implementação do serviço. Esta localização pode ser seu computador local ou um recurso de computação com base na cloud. Destinos de computação tornam mais fácil alterar o seu ambiente de computação sem alterar o seu código. 

Saiba mais sobre o [destinos de computação disponíveis para implantação e treinamento](concept-compute-target.md). 

### <a name="training-scripts"></a>Scripts de preparação

Para preparar um modelo, especifique o diretório que contém o script de formação e os ficheiros associados. Também especificar um nome de experimentação, o que é utilizado para armazenar informações que são recolhidas durante o treinamento. Durante o treinamento, o diretório é copiado para o ambiente de treinamento (destino de computação) e o script que é especificado pela configuração de execução é iniciado. Um instantâneo do diretório também é armazenado abaixo a experimentação na área de trabalho.

Por exemplo, veja [Tutorial: Preparar um modelo de classificação de imagem com o serviço Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Execuções

Uma execução é um registo que contém as seguintes informações:

* Metadados sobre a execução (timestamp, a duração e assim por diante)
* Métricas que são registadas pelo seu script
* Ficheiros de saída que estão autocollected pela experimentação ou explicitamente carregados por si
* Um instantâneo do diretório que contém os scripts, antes da execução

Produzir uma execução ao submeter um script para preparar um modelo. Uma execução pode ter zero ou mais execuções de subordinados. Por exemplo, a execução de nível superior pode ter duas execuções de subordinados, cada um dos quais pode ter seu próprio filho em execução.

Para obter um exemplo de execuções que são produzidas por preparar um modelo de exibição, consulte [início rápido: Introdução ao serviço Azure Machine Learning](quickstart-run-cloud-notebook.md).

### <a name="github-tracking-and-integration"></a>Controlo de GitHub e integração

Quando inicia um treinamento execute onde o diretório de origem é um repositório de Git local, informações sobre o repositório são armazenadas no histórico de execuções. Por exemplo, o ID de consolidação atual para o repositório é registado como parte da história. Isso funciona com execuções enviadas através de uma calculadora, pipeline de ML ou execução do script. Ele também funciona para execuções enviadas a partir do SDK ou da CLI do Machine Learning.

### <a name="snapshots"></a>Instantâneos

Quando submete uma execução, o Azure Machine Learning compacta o diretório que contém o script como um ficheiro zip e envia-os para o destino de computação. O ficheiro zip, em seguida, é extraído e o script é executado lá. O Azure Machine Learning também armazena o ficheiro zip como um instantâneo como parte do registo de execução. Qualquer pessoa com acesso à área de trabalho pode procurar um registo de execução e transferir o instantâneo.

> [!NOTE]
> Para impedir a ser incluído no instantâneo de arquivos desnecessários, criar um ficheiro de ignorar (. gitignore ou .amlignore). Coloque este ficheiro no diretório instantâneo e adicione os nomes de arquivo para ignorar nela. O ficheiro de .amlignore utiliza as mesmas [sintaxe e padrões como o ficheiro. gitignore](https://git-scm.com/docs/gitignore). Se existirem ambos os ficheiros, o ficheiro de .amlignore tem precedência.

### <a name="activities"></a>Actividades

Uma atividade representa uma operação de longa execução. As seguintes operações são exemplos de atividades:

* Criar ou eliminar um destino de computação
* Executar um script num destino de computação

Atividades podem fornecer notificações através do SDK ou a IU da web, de modo a que pode monitorizar facilmente o progresso de uma destas operações.

### <a name="images"></a>Imagens

Imagens fornecem uma forma de forma fiável implementar um modelo, juntamente com todos os componentes que tem de utilizar o modelo. Uma imagem contém os seguintes itens:

* Um modelo.
* Um script de classificação ou um aplicativo. Utilize o script para passar a entrada para o modelo e devolver o resultado do modelo.
* As dependências necessários para o modelo ou classificação script ou aplicativo. Por exemplo, pode incluir um ficheiro de ambiente de Conda que lista as dependências de pacote do Python.

O Azure Machine Learning pode criar dois tipos de imagens:

* **Imagem FPGA**: Utilizado quando implementa numa matriz de porta de campos programáveis no Azure.
* **Imagem do docker**: Usado ao implementar em destinos que não seja FPGA de computação. Os exemplos são o Azure Container Instances e o Azure Kubernetes Service.

O serviço Azure Machine Learning fornece uma imagem base, o que é utilizada por predefinição. Também pode fornecer suas próprias imagens personalizadas.

### <a name="image-registry"></a>Registo de imagem

Imagens são cataloged no **registo de imagem** na sua área de trabalho. Pode fornecer marcas de metadados adicionais ao criar a imagem, para que pode consultá-los para encontrar a imagem mais tarde.

Para obter um exemplo de criação de uma imagem, veja [implementar um modelo de classificação de imagem no Azure Container Instances](tutorial-deploy-models-with-aml.md).

Para obter um exemplo de implementação de um modelo com uma imagem personalizada, consulte [como implementar um modelo com uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="deployment"></a>Implementação

Uma implementação é uma instanciação do seu modelo em qualquer um de um serviço da web que pode ser alojado na cloud ou um módulo de IoT para Implantações de dispositivos integrada.

#### <a name="web-service-deployments"></a>Implementações de serviços Web

Um serviço web implementado pode utilizar o Azure Container Instances, Azure Kubernetes Service ou FPGAs. Criar o serviço a partir do seu modelo, com scripts e ficheiros associados. Eles são encapsulados numa imagem, que fornece o ambiente de tempo de execução para o serviço web. A imagem tem um ponto final do HTTP com balanceamento de carga, recebe pedidos de classificação que são enviados para o serviço web.

Azure ajuda-o a monitorizar a implementação do serviço web através da recolha de telemetria do Application Insights ou telemetria de modelo, se tiver escolhido para ativar esta funcionalidade. Os dados de telemetria estão acessíveis apenas para si e é armazenado no Application Insights e instâncias de conta de armazenamento.

Se tiver habilitado o dimensionamento automático, o Azure dimensiona automaticamente a implementação.

Para obter um exemplo de implementação de um modelo como um serviço web, consulte [implementar um modelo de classificação de imagem no Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>Implementações de módulo de IoT

Um módulo de IoT implementado é um contentor de Docker que inclui o seu modelo e script associado ou aplicação e dependências adicionais. Implementar esses módulos, utilizando o Azure IoT Edge em dispositivos periféricos.

Se ativar a monitorização, o Azure recolhe dados de telemetria do modelo dentro do módulo do Azure IoT Edge. Os dados de telemetria estão acessíveis apenas para si e é armazenado na sua instância de conta de armazenamento.

O Azure IoT Edge garante que seu módulo está a ser executado e que monitoriza o dispositivo que está hospedando.

### <a name="ml-pipelines"></a>Pipelines de ML

Utilizar o machine learning pipelines para criar e gerir fluxos de trabalho que reunir do machine learning fases. Por exemplo, um pipeline pode incluir preparação de dados, preparação de modelos, implementação do modelo e inferência de tipos de classificação/fases. Cada fase pode incluir vários passos, cada um dos quais pode ser executado automaticamente em vários destinos de computação.

Para obter mais informações sobre o machine learning pipelines com este serviço, consulte [Pipelines e o Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Registo

Quando desenvolver a sua solução, utilize o SDK de Python do Azure Machine Learning no seu script de Python para registar o métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução tiver produzido o modelo que pretende implementar.

### <a name="next-steps"></a>Passos Seguintes

Para começar a utilizar com o serviço Azure Machine Learning, veja:

* [O que é o serviço Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar uma área de trabalho do serviço do Azure Machine Learning](setup-create-workspace.md)
* [Tutorial (parte 1): Preparar um modelo](tutorial-train-models-with-aml.md)
