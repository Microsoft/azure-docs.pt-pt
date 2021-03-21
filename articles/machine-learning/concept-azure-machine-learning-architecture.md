---
title: Arquitetura & conceitos-chave
titleSuffix: Azure Machine Learning
description: Este artigo dá-lhe uma compreensão de alto nível da arquitetura, termos e conceitos que compõem a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: dc1954c97da0d7f40deaf0f4efa7ca99793107bb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503696"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como funciona a Azure Machine Learning: Arquitetura e conceitos

Conheça a arquitetura e os conceitos para [Azure Machine Learning.](overview-what-is-azure-ml.md)  Este artigo dá-lhe uma compreensão de alto nível dos componentes e como eles trabalham em conjunto para ajudar no processo de construção, implantação e manutenção de modelos de aprendizagem automática.

## <a name="workspace"></a><a name="workspace"></a> Espaço de trabalho

Um espaço de trabalho de [aprendizagem automática](concept-workspace.md) é o recurso de alto nível para a Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagrama: Azure Machine Learning arquitetura de um espaço de trabalho e seus componentes":::

O espaço de trabalho é o lugar centralizado para:

* Gerir os recursos que utiliza para a formação e implementação de modelos, como [computas](#compute-instance)
* Armazenar os ativos que cria quando utiliza a Azure Machine Learning, incluindo:
  * [Ambientes](#environments)
  * [Experiências](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Conjuntos de dados](#datasets-and-datastores)
  * [Modelos](#models)
  * [Pontos Finais](#endpoints)

Um espaço de trabalho inclui outros recursos Azure que são usados pelo espaço de trabalho:

+ [Registo do contentor Azure (ACR)](https://azure.microsoft.com/services/container-registry/): Regista os recipientes de estivadores que utiliza durante o treino e quando implementa um modelo. Para minimizar os custos, o ACR só é criado quando as imagens de implantação são criadas.
+ [Conta de Armazenamento Azure](https://azure.microsoft.com/services/storage/): É utilizada como a datastore predefinida para o espaço de trabalho.  Os cadernos Jupyter que são usados com os seus casos de cálculo de Aprendizagem de Máquinas Azure também estão armazenados aqui.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Armazena informações de monitorização sobre os seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Armazena segredos que são usados por alvos computacional e outras informações sensíveis que são necessárias pelo espaço de trabalho.

Podes partilhar um espaço de trabalho com os outros.

## <a name="computes"></a>Cálculos

<a name="compute-targets"></a> Um [alvo de cálculo](concept-compute-target.md) é qualquer máquina ou conjunto de máquinas que utilize para executar o seu script de treino ou hospedar a sua implementação de serviço. Pode utilizar a sua máquina local ou um recurso de computação remota como alvo de computação.  Com alvos de cálculo, pode começar a treinar na sua máquina local e depois escalar para a nuvem sem alterar o seu script de treino.

A Azure Machine Learning introduz duas máquinas virtuais totalmente geridas (VM) totalmente geridas que estão configuradas para tarefas de aprendizagem automática:

* <a name="compute-instance"></a>**Instância de cálculo**: Uma instância computacional é um VM que inclui múltiplas ferramentas e ambientes instalados para machine learning. O uso primário de um exemplo de computação é para o seu posto de trabalho de desenvolvimento.  Pode começar a executar cadernos de amostra sem necessidade de configuração. Uma instância computacional também pode ser usada como um alvo de computação para a formação e inferenção de empregos.

* **Clusters computativo**: Os clusters computacional são um aglomerado de VMs com capacidades de escala de vários nós. Os clusters computacional são mais adequados para metas de computação para grandes empregos e produção.  O cluster aumenta automaticamente quando um trabalho é submetido.  Utilizar como alvo de computação de treino ou para implantação dev/teste.

Para obter mais informações sobre os alvos de computação de formação, consulte [os alvos do computação de formação](concept-compute-target.md#train).  Para obter mais informações sobre os alvos de computação de implantação, consulte [os alvos de implementação](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Conjuntos de dados e datastores

[**Os conjuntos de dados de aprendizagem de máquinas Azure**](concept-data.md#datasets)  facilitam o acesso e o trabalho com os seus dados. Ao criar um conjunto de dados, cria uma referência à localização da fonte de dados juntamente com uma cópia dos seus metadados. Como os dados permanecem na sua localização existente, não incorre nenhum custo extra de armazenamento e não arrisca a integridade das suas fontes de dados.

Para obter mais informações, consulte [Criar e registar conjuntos de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md).  Para obter mais exemplos utilizando conjuntos de dados, consulte os [cadernos de amostras.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)

Os conjuntos de dados utilizam [as datas-tores](concept-data.md#datastores) para se conectarem de forma segura aos seus serviços de armazenamento Azure. As lojas de dados armazenam informações de conexão sem colocar em risco as suas credenciais de autenticação e a integridade da sua fonte de dados original. Armazenam informações de ligação, como o ID de subscrição e a autorização simbólica no cofre-chave associado ao espaço de trabalho, para que possa aceder de forma segura ao seu armazenamento sem ter de os codificar no seu script.

## <a name="environments"></a>Ambientes

[Espaço de](#workspace)  >  trabalho **Ambientes**

Um [ambiente](concept-environments.md) é a encapsulação do ambiente onde o treino ou a pontuação do seu modelo de aprendizagem automática acontecem. O ambiente especifica os pacotes Python, variáveis ambientais e configurações de software em torno dos seus scripts de treino e pontuação.  

Para obter amostras de código, consulte a secção "Gerir ambientes" de [como utilizar ambientes](how-to-use-environments.md#manage-environments).

## <a name="experiments"></a>Experimentações

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

Uma configuração de execução define como um script deve ser executado em um alvo de computação especificado. Utiliza a configuração para especificar o script, o alvo do cálculo e o ambiente Azure ML para executar, quaisquer configurações específicas do trabalho distribuídas e algumas propriedades adicionais. Para obter mais informações sobre o conjunto completo de opções configuráveis para execuções, consulte [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).

Uma configuração de execução pode ser persistido num ficheiro dentro do diretório que contém o seu script de treino.   Ou pode ser construído como um objeto na memória e usado para submeter uma corrida.

Por exemplo, executar configurações, ver [Configurar uma execução de treino](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Instantâneos

[Espaço de](#workspace)  >  trabalho [Experiências](#experiments)  >  [Correr](#runs)  >  **Instantâneo**

Quando submete uma execução, o Azure Machine Learning comprime o diretório que contém o script como ficheiro zip e envia-o para o alvo do cálculo. O ficheiro zip é então extraído, e o script é executado lá. A Azure Machine Learning também armazena o ficheiro zip como um instantâneo como parte do registo de execução. Qualquer pessoa com acesso ao espaço de trabalho pode navegar num registo de execução e descarregar o instantâneo.

### <a name="logging"></a>Registo

A Azure Machine Learning regista automaticamente as métricas padrão de execução para si. No entanto, também pode [utilizar o Python SDK para registar métricas arbitrárias.](how-to-track-experiments.md)

Existem várias formas de visualizar os seus registos: monitorizar o estado da execução em tempo real ou visualizar resultados após a conclusão. Para obter mais informações, consulte [Monitor e veja registos de execução ML](how-to-monitor-view-training-logs.md).


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Isto funciona com runs submetidos usando uma configuração de execução de script ou pipeline ML. Também funciona para as execuções submetidas a partir do SDK ou machine learning CLI.

Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

### <a name="training-workflow"></a>Fluxo de trabalho de formação

Quando se faz uma experiência para treinar um modelo, os seguintes passos acontecem. Estes são ilustrados no diagrama de trabalho de formação abaixo:

* A Azure Machine Learning é chamada com o ID instantâneo para a imagem de código guardada na secção anterior.
* O Azure Machine Learning cria um ID de execução (opcional) e um token de serviço de machine learning, que é mais tarde utilizado por alvos computacionais como Machine Learning Compute/VMs para comunicar com o serviço de Machine Learning.
* Você pode escolher um alvo de computação gerido (como Machine Learning Compute) ou um alvo de computação não gerido (como VMs) para executar trabalhos de formação. Aqui estão os fluxos de dados para ambos os cenários:
   * VMs/HDInsight, acedido por credenciais SSH num cofre chave na subscrição da Microsoft. A Azure Machine Learning executa o código de gestão no alvo do cálculo que:

   1. Prepara o ambiente. (Docker é uma opção para VMs e computadores locais. Consulte os seguintes passos para machine learning compute para entender como funciona as experiências em contentores Docker.)
   1. Descarrega o código.
   1. Configura variáveis e configurações ambientais.
   1. Executa scripts de utilizador (a imagem de código mencionada na secção anterior).

   * Machine Learning Compute, acedido através de uma identidade gerida pelo espaço de trabalho.
Como o Machine Learning Compute é um alvo de computação gerido (isto é, é gerido pela Microsoft) funciona sob a subscrição da Microsoft.

   1. A construção remota do Docker é iniciada, se necessário.
   1. O código de gestão é escrito para a partilha de Ficheiros Azure do utilizador.
   1. O recipiente é iniciado com um comando inicial. Ou seja, código de gestão, tal como descrito no passo anterior.

* Depois de terminar a execução, pode consultar corridas e métricas. No diagrama de fluxo abaixo, este passo ocorre quando o alvo do computação de treino escreve as métricas de corrida de volta para Azure Machine Learning a partir do armazenamento na base de dados do Cosmos DB. Os clientes podem ligar para a Azure Machine Learning. A Machine Learning, por sua vez, irá retirar métricas da base de dados da Cosmos DB e devolvê-las ao cliente.

[![Fluxo de trabalho de formação](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modelos

No seu mais simples, um modelo é um pedaço de código que pega numa entrada e produz a saída. A criação de um modelo de aprendizagem automática envolve selecionar um algoritmo, fornecer-lhe dados e [sintonizar hiperparímetros.](how-to-tune-hyperparameters.md) A formação é um processo iterativo que produz um modelo treinado, que encapsula o que o modelo aprendeu durante o processo de treino.

Pode trazer um modelo treinado fora do Azure Machine Learning. Ou pode treinar um modelo submetendo uma [série](#runs) de [experiências](#experiments) a um [alvo de computação](#compute-targets) em Azure Machine Learning. Uma vez que tenha um modelo, [registe o modelo](#register-model) no espaço de trabalho.

Azure Machine Learning é agnóstico enquadrante. Quando cria um modelo, pode utilizar qualquer estrutura popular de aprendizagem automática, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para um exemplo de formação de um modelo que usa Scikit-learn, consulte [Tutorial: Treine um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Registo de modelos

[Espaço de](#workspace)  >  trabalho **Modelos**

O **registo de modelos** permite-lhe acompanhar todos os modelos do seu espaço de trabalho Azure Machine Learning.

Os modelos são identificados pelo nome e pela versão. Cada vez que regista um modelo com o mesmo nome que um existente, o registo assume que é uma nova versão. A versão é incrementada e o novo modelo está registado com o mesmo nome.

Quando regista o modelo, pode fornecer etiquetas de metadados adicionais e, em seguida, utilizar as etiquetas quando procura por modelos.

> [!TIP]
> Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registo, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que foram registados.

Não é possível eliminar um modelo registado que esteja a ser utilizado por uma implementação ativa.

Para um exemplo de registo de um modelo, consulte [Train um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

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

Pode ativar a telemetria do Application Insights ou a telemetria do modelo para monitorizar o seu serviço web. Os dados de telemetria são acessíveis apenas a si.  Está armazenado nas suas informações de aplicação e casos de conta de armazenamento. Se tiver ativado o dimensionamento automático, o Azure escala automaticamente a sua implantação.

O diagrama a seguir mostra o fluxo de trabalho de inferência para um modelo implantado como ponto final de serviço web:

Aqui estão os detalhes:

* O utilizador regista um modelo utilizando um cliente como o Azure Machine Learning SDK.
* O utilizador cria uma imagem utilizando um modelo, um ficheiro de pontuação e outras dependências de modelos.
* A imagem docker é criada e armazenada no Registo do Contentor Azure.
* O serviço web é implantado para o alvo de computação (Instâncias de Contentores/AKS) utilizando a imagem criada no passo anterior.
* Os detalhes do pedido de pontuação são armazenados no Application Insights, que está na subscrição do utilizador.
* A telemetria também é empurrada para a subscrição Microsoft/Azure.

[![Fluxo de trabalho de inferência](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Para um exemplo de implantação de um modelo como serviço web, consulte [implementar um modelo de classificação de imagem em Instâncias de Contentores Azure](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Pontos finais em tempo real

Quando se implanta um modelo treinado no designer, [pode-se implementar o modelo como um ponto final em tempo real](tutorial-designer-automobile-price-deploy.md). Um ponto final em tempo real geralmente recebe um único pedido através do ponto final REST e devolve uma previsão em tempo real. Isto contrasta com o processamento de lotes, que processa vários valores ao mesmo tempo e guarda os resultados após a conclusão de uma datastore.

#### <a name="pipeline-endpoints"></a>Pontos finais do gasoduto

Os pontos finais do pipeline permitem-lhe ligar para os [seus Gasodutos ML](#ml-pipelines) programaticamente através de um ponto final REST. Os pontos finais do gasoduto permitem automatizar os fluxos de trabalho do gasoduto.

Um ponto final de gasoduto é uma coleção de oleodutos publicados. Esta organização lógica permite-lhe gerir e chamar vários oleodutos usando o mesmo ponto final. Cada gasoduto publicado num ponto final de gasoduto é vertado. Pode selecionar um pipeline predefinido para o ponto final ou especificar uma versão na chamada REST.
 

#### <a name="iot-module-endpoints"></a>Pontos finais do módulo IoT

Um ponto final de módulo IoT implantado é um recipiente Docker que inclui o seu modelo e script ou aplicação associados e quaisquer dependências adicionais. Você implementa estes módulos utilizando Azure IoT Edge em dispositivos de borda.

Se tiver ativado a monitorização, o Azure recolhe dados de telemetria do modelo dentro do módulo Azure IoT Edge. Os dados de telemetria são acessíveis apenas a si, e são armazenados na sua conta de armazenamento.

O Azure IoT Edge garante que o seu módulo está em funcionamento e monitoriza o dispositivo que o acolhe. 
## <a name="automation"></a>Automatização

### <a name="azure-machine-learning-cli"></a>CLI do Azure Machine Learning 

O [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) é uma extensão do Azure CLI, uma interface de linha de comando transversal para a plataforma Azure. Esta extensão fornece comandos para automatizar as suas atividades de aprendizagem automática.

### <a name="ml-pipelines"></a>Gasodutos ML

Você usa [oleodutos de aprendizagem automática](concept-ml-pipelines.md) para criar e gerir fluxos de trabalho que cosem fases de aprendizagem automática. Por exemplo, um gasoduto pode incluir preparação de dados, formação de modelos, implantação de modelos e fases de inferência/pontuação. Cada fase pode abranger vários passos, cada um dos quais pode correr sem vigilância em vários alvos de computação. 

Os passos do gasoduto são reutilizáveis e podem ser executados sem repetir os passos anteriores se a saída desses passos não tiver mudado. Por exemplo, pode reforçá-lo um modelo sem repetir passos de preparação de dados dispendiosos se os dados não mudarem. Os oleodutos também permitem que os cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizagem automática.

## <a name="monitoring-and-logging"></a>Monitorização e registos

A Azure Machine Learning fornece as seguintes capacidades de monitorização e registo:

* Para __cientistas de dados,__ pode monitorizar as suas experiências e registar informações a partir das suas pistas de treino. Para obter mais informações, veja os seguintes artigos:
   * [Iniciar, monitorizar e cancelar treinos](how-to-manage-runs.md)
   * [Métricas de registo para execuções de preparações](how-to-track-experiments.md)
   * [Controlar experiências com o MLflow](how-to-use-mlflow.md)
   * [Visualizar execuções com TensorBoard](how-to-monitor-tensorboard.md)
* Para __os Administradores,__ pode monitorizar informações sobre o espaço de trabalho, recursos Azure relacionados, e eventos como a criação e eliminação de recursos através do Azure Monitor. Para obter mais informações, consulte [Como monitorizar o Azure Machine Learning](monitor-azure-machine-learning.md).
* Para __DevOps__ ou __MLOps,__ pode monitorizar as informações geradas por modelos implementados como serviços web ou módulos IoT Edge para identificar problemas com as implementações e recolher dados submetidos ao serviço. Para obter mais informações, consulte [recolher dados do modelo](how-to-enable-data-collection.md) e monitorizar com insights de [aplicação.](how-to-enable-app-insights.md)

## <a name="interacting-with-your-workspace"></a>Interagindo com o seu espaço de trabalho

### <a name="studio"></a>Studio

[O estúdio Azure Machine Learning](overview-what-is-machine-learning-studio.md) oferece uma visão web de todos os artefactos do seu espaço de trabalho.  Pode ver resultados e detalhes dos seus conjuntos de dados, experiências, oleodutos, modelos e pontos finais.  Também pode gerir recursos de computação e datastores no estúdio.

O estúdio é também onde você acede às ferramentas interativas que fazem parte do Azure Machine Learning:

+ [Azure Machine Learning designer](concept-designer.md) para executar etapas de fluxo de trabalho sem código de escrita
+ Experiência web para [aprendizagem automática de máquinas](concept-automated-ml.md)
+ [Azure Machine Learning para](how-to-run-jupyter-notebooks.md) escrever e executar o seu próprio código em servidores de cadernos Jupyter integrados.
+ [Projetos de rotulagem de dados](how-to-create-labeling-projects.md) para criar, gerir e monitorizar projetos para rotular os seus dados

### <a name="programming-tools"></a>Ferramentas de programação

> [!IMPORTANT]
> As ferramentas marcadas (pré-visualização) abaixo estão atualmente em visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interaja com o serviço em qualquer ambiente Python com o [Azure Machine Learning SDK para Python.](/python/api/overview/azure/ml/intro)
+ Interaja com o serviço em qualquer ambiente R com o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (pré-visualização).
+ Utilize [o designer de aprendizagem automática Azure](concept-designer.md) para executar os passos de fluxo de trabalho sem escrever código. 
+ Utilize [o CLI de aprendizagem automática Azure](./reference-azure-machine-learning-cli.md) para automatização.
+ O [Acelerador de Soluções de Muitos Modelos](https://aka.ms/many-models) (pré-visualização) baseia-se no Azure Machine Learning e permite-lhe treinar, operar e gerir centenas ou mesmo milhares de modelos de machine learning.

## <a name="next-steps"></a>Passos seguintes

Para começar com a Azure Machine Learning, consulte:

* [O que é o Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar uma área de trabalho do Azure Machine Learning](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treine um modelo](tutorial-train-models-with-aml.md)