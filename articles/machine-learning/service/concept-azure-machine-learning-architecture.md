---
title: 'Aprendizagem automática na cloud: Termos e arquitetura'
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a arquitetura, terminologia e conceitos que constituem o serviço Azure Machine Learning. Também Saiba mais sobre o fluxo de trabalho geral de usar o serviço e os serviços do Azure que são utilizados pelo serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1640b1cdb9410f33f6556667f36aafcfe575a082
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080327"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Como funciona o serviço Azure Machine Learning: Conceitos e arquitetura

Este artigo descreve a arquitetura e conceitos do serviço Azure Machine Learning. Os principais componentes do serviço e o fluxo de trabalho geral para utilizar o serviço são apresentados no diagrama seguinte:

[![Arquitetura de serviço do Azure Machine Learning e o fluxo de trabalho](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

O fluxo de trabalho em geral, segue essa seqüência:

1. Desenvolver a aprendizagem, treinamento scripts no **Python**.
1. Criar e configurar uma **destino de computação**.
1. **Submeter os scripts** para o destino de computação configurada para ser executado nesse ambiente. Durante o treinamento, os scripts podem ler ou escrever **arquivo de dados**. E os registos de execução são guardados como **executa** no **área de trabalho** e agrupados sob **experimentações**.
1. **Consultar a experimentação** para as métricas registadas de execuções a atuais e anteriores. Se as métricas não indicam um resultado desejado, fazer um loop novamente para o passo 1 e iterar em seus scripts.
1. Após encontra uma execução satisfatória, registe o modelo persistente na **registo de modelo**.
1. Desenvolva um script de classificação.
1. **Criar uma imagem** e registe-na **registo de imagem**.
1. **Implementar a imagem** como uma **serviço web** no Azure.


> [!NOTE]
> Embora este artigo define os termos e conceitos utilizados pelo serviço Azure Machine Learning, não definir termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma do Azure, consulte a [Glossário do Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Área de trabalho

A área de trabalho é o recurso de nível superior para o serviço Azure Machine Learning. Ele fornece um local centralizado para trabalhar com todos os artefactos que criar quando utilizar o serviço Azure Machine Learning.

A área de trabalho mantém uma lista de destinos de computação que pode utilizar para preparar o seu modelo. Além disso, mantém um histórico de execuções de preparação, incluindo registos, métricas, saída e um instantâneo dos seus scripts. Utilize estas informações para determinar qual execução de treinamento produz o melhor modelo.

Registe-se de modelos com a área de trabalho. Utilize um modelo registado e scripts de classificação para criar uma imagem. Pode, em seguida, implementar a imagem no Azure Container Instances, Azure Kubernetes Service, ou a uma matriz de porta de campos programáveis (FPGA) como um ponto de final HTTP baseado em REST. Também pode implementar a imagem para um dispositivo Azure IoT Edge como um módulo.

Pode criar várias áreas de trabalho, e cada área de trabalho pode ser partilhada por várias pessoas. Quando partilha uma área de trabalho, pode controlar o acesso ao mesmo ao atribuir as seguintes funções para os utilizadores:

* Proprietário
* Contribuinte
* Leitor

Quando cria uma nova área de trabalho, este cria automaticamente vários recursos do Azure que são utilizados pela área de trabalho:

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Regista os contentores do docker que utilizar durante o treinamento e ao implementar um modelo.
* [Conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/): É utilizado como o arquivo de dados predefinido para a área de trabalho.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Arquivos de informações sobre os modelos de monitorização.
* [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/): Os segredos de arquivos que são utilizados por computação destinos e outras informações confidenciais que é necessário pela área de trabalho.

> [!NOTE]
> Além de criar novas versões, também pode utilizar os serviços do Azure existentes.

Uma taxonomia da área de trabalho é ilustrada no diagrama seguinte:

[![Taxonomia da área de trabalho](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="experiment"></a>Experimentação

Uma experimentação é um agrupamento de várias execuções de um script especificado. Sempre pertence a uma área de trabalho. Quando submete uma execução, fornece um nome de experimentação. Informações para a execução são armazenadas desse experimento. Se submeter uma execução e especifique um nome de experimentação que não existe, é automaticamente criada uma nova experimentação com esse nome recentemente especificado.

Para obter um exemplo da utilização de uma experimentação, consulte [início rápido: Introdução ao serviço Azure Machine Learning](quickstart-get-started.md).

## <a name="model"></a>Modelo

Em sua forma mais simples, um modelo é um trecho de código que usa entrada e produz um resultado. A criação de um modelo de aprendizagem automática envolve selecionar um algoritmo, fornecendo dados e ajuste hiperparâmetros. O treinamento é um processo iterativo que produz um modelo preparado, que encapsula o que o modelo aprendidas durante o processo de treinamento.

Um modelo é produzido por uma execução no Azure Machine Learning. Também pode utilizar um modelo que está preparado fora do Azure Machine Learning. Pode registrar um modelo numa área de trabalho do serviço do Azure Machine Learning.

O serviço do Azure Machine Learning é agnóstico quanto a estrutura. Quando cria um modelo, pode utilizar qualquer estrutura de aprendizado de máquina populares, como Scikit-saiba, XGBoost, PyTorch, TensorFlow, Chainer e Microsoft Cognitive Toolkit (anteriormente designado CNTK).

Para obter um exemplo de preparar um modelo, consulte [início rápido: Criar uma área de trabalho do serviço de Machine Learning](quickstart-get-started.md).

### <a name="model-registry"></a>Registo de modelo

O registo de modelo mantém um registo de todos os modelos na sua área de trabalho do serviço do Azure Machine Learning.

Modelos são identificados pelo nome e versão. Sempre que registar um modelo com o mesmo nome que um já existente, o registro parte do princípio de que é uma nova versão. A versão é incrementada e o novo modelo está registado com o mesmo nome.

Quando registra o modelo, pode fornecer marcas de metadados adicionais e, em seguida, utilizar as etiquetas quando procurar modelos.

Não é possível eliminar modelos que estão a ser utilizados por uma imagem.

Para obter um exemplo de um modelo de registo, consulte [preparar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run-configuration"></a>Configuração de execução

Uma configuração de execução é um conjunto de instruções que define como executar um script num destino de computação especificado. A configuração inclui uma grande variedade de definições de comportamento, por exemplo, se utilizar um ambiente de Python existente ou utilizar um ambiente de Conda que é criado a partir de uma especificação.

Uma configuração de execução pode ser mantida num arquivo dentro do diretório que contém o script de treinamento, ou pode ser construído como um objeto na memória e utilizado para submeter uma execução.

Por exemplo, executar configurações, consulte [selecionar e utilizar um destino de computação para preparar o seu modelo](how-to-set-up-training-targets.md).

## <a name="datastore"></a>Arquivo de dados

Um arquivo de dados é uma abstração de armazenamento através de uma conta de armazenamento do Azure. O arquivo de dados pode utilizar um contentor de Blobs do Azure ou uma partilha de ficheiros do Azure, como o armazenamento de back-end. Cada área de trabalho tem um arquivo de dados padrão e pode registar os arquivos de dados adicionais.

Utilize a API do SDK de Python ou a CLI do Azure Machine Learning para armazenar e recuperar ficheiros a partir do arquivo de dados.

## <a name="compute-target"></a>Destino de computação

Um destino de computação é o recurso de computação que utiliza para executar o script de treinamento ou a implementação do serviço de anfitrião. Os destinos de computação suportados são:

| Destino de computação | Formação | Implementação |
| ---- |:----:|:----:|
| Seu computador local | ✓ | &nbsp; |
| Computação do Machine Learning do Azure | ✓ | &nbsp; |
| Uma VM do Linux no Azure</br>(por exemplo, a Máquina Virtual de ciência de dados) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; |
| Azure Data Lake Analytics | ✓ | &nbsp; |
| Apache Spark para HDInsight | ✓ | &nbsp; |
| Azure Container Instances | &nbsp; | ✓ |
| Serviço Kubernetes do Azure | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(Matriz de porta de campos programáveis) | &nbsp; | ✓ |

Destinos de computação são anexados a uma área de trabalho. Destinos que não seja o computador local são partilhados por utilizadores da área de trabalho de computação.

### <a name="managed-and-unmanaged-compute-targets"></a>Destinos de computação geridos e não geridos

* **Gerido**: Computação destinos que são criados e geridos pelo serviço Azure Machine Learning. Estes computação destinos estão otimizados para cargas de trabalho do machine learning. Computação do Machine Learning do Azure é o único destino de computação gerida a partir de 4 de Dezembro de 2018. Destinos de computação geridos adicionais podem ser adicionados no futuro.

    Pode criar machine learning instâncias diretamente através da área de trabalho de computação com o portal do Azure, o SDK do Azure Machine Learning ou a CLI do Azure. Todos os outros destinos de computação tem de ser criados fora da área de trabalho e, em seguida, ligados ao mesmo.

* **Não gerido**: Destinos de computação que são *não* gerido pelo serviço Azure Machine Learning. Poderá ter de criá-los fora do Azure Machine Learning e, em seguida, anexe-os à área de trabalho antes do uso. Destinos de computação não gerenciado podem exigem passos adicionais para si para manter ou melhorar o desempenho para cargas de trabalho do machine learning.

Para obter informações sobre como selecionar um destino de computação para a formação, consulte [selecionar e utilizar um destino de computação para preparar o seu modelo](how-to-set-up-training-targets.md).

Para obter informações sobre como selecionar um destino de computação para a implementação, consulte a [implementar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="training-script"></a>Script de treinamento

Para preparar um modelo, especifique o diretório que contém o script de formação e os ficheiros associados. Também especificar um nome de experimentação, o que é utilizado para armazenar informações que são recolhidas durante o treinamento. Durante o treinamento, o diretório é copiado para o ambiente de treinamento (destino de computação) e o script que é especificado pela configuração de execução é iniciado. Um instantâneo do diretório também é armazenado abaixo a experimentação na área de trabalho.

Por exemplo, veja [criar uma área de trabalho com o Python](quickstart-get-started.md).

## <a name="run"></a>Executar

Uma execução é um registo que contém as seguintes informações:

* Metadados sobre a execução (timestamp, a duração e assim por diante)
* Métricas que são registadas pelo seu script
* Ficheiros de saída que estão autocollected pela experimentação ou explicitamente carregados por si
* Um instantâneo do diretório que contém os scripts, antes da execução

Produzir uma execução ao submeter um script para preparar um modelo. Uma execução pode ter zero ou mais execuções de subordinados. Por exemplo, a execução de nível superior pode ter duas execuções de subordinados, cada um dos quais pode ter seu próprio filho em execução.

Para obter um exemplo de execuções que são produzidas por preparar um modelo de exibição, consulte [início rápido: Introdução ao serviço Azure Machine Learning](quickstart-get-started.md).

## <a name="snapshot"></a>Instantâneo

Quando submete uma execução, o Azure Machine Learning compacta o diretório que contém o script como um ficheiro zip e envia-os para o destino de computação. O ficheiro zip, em seguida, é extraído e o script é executado lá. O Azure Machine Learning também armazena o ficheiro zip como um instantâneo como parte do registo de execução. Qualquer pessoa com acesso à área de trabalho pode procurar um registo de execução e transferir o instantâneo.

## <a name="activity"></a>Atividade

Uma atividade representa uma operação de longa execução. As seguintes operações são exemplos de atividades:

* Criar ou eliminar um destino de computação
* Executar um script num destino de computação

Atividades podem fornecer notificações através do SDK ou a IU da web, de modo a que pode monitorizar facilmente o progresso de uma destas operações.

## <a name="image"></a>Imagem

Imagens fornecem uma forma de forma fiável implementar um modelo, juntamente com todos os componentes que tem de utilizar o modelo. Uma imagem contém os seguintes itens:

* Um modelo.
* Um script de classificação ou um aplicativo. Utilize o script para passar a entrada para o modelo e devolver o resultado do modelo.
* As dependências necessários para o modelo ou classificação script ou aplicativo. Por exemplo, pode incluir um ficheiro de ambiente de Conda que lista as dependências de pacote do Python.

O Azure Machine Learning pode criar dois tipos de imagens:

* **Imagem FPGA**: Utilizado quando implementa numa matriz de porta de campos programáveis no Azure.
* **Imagem do docker**: Usado ao implementar em destinos que não seja FPGA de computação. Os exemplos são o Azure Container Instances e o Azure Kubernetes Service.

Para obter um exemplo de criação de uma imagem, veja [implementar um modelo de classificação de imagem no Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registo de imagem

O registo de imagem mantém um registo de imagens que são criadas a partir de seus modelos. Pode fornecer marcas de metadados adicionais ao criar a imagem. Marcas de metadados são armazenadas pelo registo de imagem, e pode consultá-los para encontrar a sua imagem.

## <a name="deployment"></a>Implementação

Uma implementação é uma instanciação de sua imagem em qualquer um de um serviço da web que pode ser alojado na cloud ou um módulo de IoT para Implantações de dispositivos integrada.

### <a name="web-service"></a>Serviço Web

Um serviço web implementado pode utilizar o Azure Container Instances, Azure Kubernetes Service ou FPGAs. Criar o serviço a partir de uma imagem que encapsula o seu modelo, com scripts e ficheiros associados. A imagem tem um ponto final do HTTP com balanceamento de carga, recebe pedidos de classificação que são enviados para o serviço web.

Azure ajuda-o a monitorizar a implementação do serviço web através da recolha de telemetria do Application Insights ou telemetria de modelo, se tiver escolhido para ativar esta funcionalidade. Os dados de telemetria estão acessíveis apenas para si e é armazenado no Application Insights e instâncias de conta de armazenamento.

Se tiver habilitado o dimensionamento automático, o Azure dimensiona automaticamente a implementação.

Para obter um exemplo de implementação de um modelo como um serviço web, consulte [implementar um modelo de classificação de imagem no Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Módulo de IoT

Um módulo de IoT implementado é um contentor de Docker que inclui o seu modelo e script associado ou aplicação e dependências adicionais. Implementar esses módulos, utilizando o Azure IoT Edge em dispositivos periféricos.

Se ativar a monitorização, o Azure recolhe dados de telemetria do modelo dentro do módulo do Azure IoT Edge. Os dados de telemetria estão acessíveis apenas para si e é armazenado na sua instância de conta de armazenamento.

O Azure IoT Edge garante que seu módulo está a ser executado e que monitoriza o dispositivo que está hospedando.

## <a name="pipeline"></a>Pipeline

Utilizar o machine learning pipelines para criar e gerir fluxos de trabalho que reunir do machine learning fases. Por exemplo, um pipeline pode incluir preparação de dados, preparação de modelos, implementação do modelo e fases de inferência. Cada fase pode incluir vários passos, cada um dos quais pode ser executado automaticamente em vários destinos de computação.

Para obter mais informações sobre o machine learning pipelines com este serviço, consulte [Pipelines e o Azure Machine Learning](concept-ml-pipelines.md).

## <a name="logging"></a>Registo

Quando desenvolver a sua solução, utilize o SDK de Python do Azure Machine Learning no seu script de Python para registar o métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução tiver produzido o modelo que pretende implementar.

## <a name="next-steps"></a>Passos Seguintes

Para começar a utilizar com o serviço Azure Machine Learning, veja:

* [O que é o serviço Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Quickstart: Criar uma área de trabalho com Python](quickstart-get-started.md)
* [Tutorial: Preparar um modelo](tutorial-train-models-with-aml.md)
* [Criar uma área de trabalho com um modelo do resource manager](how-to-create-workspace-template.md)