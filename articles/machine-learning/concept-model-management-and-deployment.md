---
title: 'MLOps: Gestão de modelos ML'
titleSuffix: Azure Machine Learning
description: 'Saiba mais sobre gestão de modelos com Azure Machine Learning (MLOps). Implemente, gerencie e monitorize os seus modelos para os melhorar continuamente. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 6f03a1e44fdb62570b693753f5e01c7ab0f53e78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302422"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Gestão de modelos, implantação e monitorização com Azure Machine Learning

Neste artigo, aprenda sobre como usar a Azure Machine Learning para gerir o ciclo de vida dos seus modelos. A Azure Machine Learning utiliza uma abordagem de Machine Learning Operations (MLOps). O MLOps melhora a qualidade e consistência das suas soluções de aprendizagem automática. 

## <a name="what-is-mlops"></a>O que é o MLOps?

As Operações de Machine Learning (MLOps) baseiam-se em princípios e práticas [de DevOps](https://azure.microsoft.com/overview/what-is-devops/) que aumentam a eficiência dos fluxos de trabalho. Por exemplo, integração contínua, entrega e implantação. O MLOps aplica estes princípios ao processo de aprendizagem automática, com o objetivo de:

* Experimentação e desenvolvimento mais rápidos de modelos
* Implantação mais rápida de modelos na produção
* Garantia de qualidade

A Azure Machine Learning fornece as seguintes capacidades MLOps:

- **Criar gasodutos ML reprodutíveis**. Os oleodutos machine learning permitem-lhe definir passos repressíveis e reutilizáveis para os seus processos de preparação, treino e pontuação de dados.
- **Criar ambientes de software reutilizáveis** para modelos de treino e implantação.
- **Registe,pacote e implemente modelos de qualquer lugar**. Também pode rastrear metadados associados necessários para utilizar o modelo.
- **Capture os dados de governação para o ciclo de vida ml de ponta a ponta**. As informações registadas podem incluir quem está a publicar modelos, por que foram feitas alterações e quando os modelos foram implementados ou utilizados na produção.
- **Notifique e alerte sobre os acontecimentos no ciclo de vida da ML**. Por exemplo, conclusão de experiências, registo de modelos, implantação de modelos e deteção de deriva de dados.
- **Monitorar aplicações ML para questões operacionais e relacionadas com ml**. Compare as entradas de modelos entre o treino e a inferência, explore métricas específicas do modelo e forneça monitorização e alertas na sua infraestrutura ML.
- **Automatizar o ciclo de vida ML de ponta a ponta com a Azure Machine Learning e os Gasodutos Azure**. A utilização de pipelines permite-lhe atualizar frequentemente modelos, testar novos modelos e lançar continuamente novos modelos ML ao lado das outras aplicações e serviços.

## <a name="create-reproducible-ml-pipelines"></a>Criar gasodutos ML reprodutíveis

Utilize os oleodutos ML da Azure Machine Learning para coser todos os passos envolvidos no seu processo de treino de modelo.

Um gasoduto ML pode conter passos desde a preparação de dados até à extração de características até à sintonização hiperparítnica para a avaliação do modelo. Para mais informações, consulte [os gasodutos ML](concept-ml-pipelines.md).

Se utilizar o [Designer](concept-designer.md) para criar os seus oleodutos ML, pode, a qualquer momento, clicar no **"..."** no topo-direito da página Designer e, em seguida, selecionar **Clone**. A clonagem do seu oleoduto permite-lhe iterar o seu design de pipeline sem perder as suas versões antigas.  

## <a name="create-reusable-software-environments"></a>Criar ambientes de software reutilizáveis

Os ambientes de Aprendizagem automática Azure permitem-lhe rastrear e reproduzir as dependências de software dos seus projetos à medida que evoluem. Os ambientes permitem-lhe garantir que as construções são reprodutíveis sem configurações de software manual.

Os ambientes descrevem as dependências de pip e Conda para os seus projetos, e podem ser usados tanto para a formação como para a implementação de modelos. Para mais informações, consulte [os ambientes de aprendizagem automática Azure.](concept-environments.md)

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registar, embalar e implementar modelos de qualquer lugar

### <a name="register-and-track-ml-models"></a>Registar e rastrear modelos ML

O registo de modelos permite-lhe armazenar e ver versão os seus modelos na nuvem Azure, no seu espaço de trabalho. O registo de modelos facilita a organização e o acompanhamento dos seus modelos treinados.

> [!TIP]
> Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registo, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que foram registados.

Os modelos registados são identificados por nome e versão. Sempre que registar um modelo com o mesmo nome de um modelo já existente, o registo aumenta a versão. Podem ser fornecidas etiquetas de metadados adicionais durante o registo. Estas etiquetas são então utilizadas quando procuram um modelo. A Azure Machine Learning suporta qualquer modelo que possa ser carregado usando Python 3.5.2 ou superior.

> [!TIP]
> Também pode registar modelos treinados fora do Azure Machine Learning.

Não é possível eliminar um modelo registado que esteja a ser utilizado numa implementação ativa.
Para obter mais informações, consulte a secção de modelos de registo dos [modelos Implementar.](how-to-deploy-and-where.md#registermodel)

### <a name="profile-models"></a>Modelos de perfil

O Azure Machine Learning pode ajudá-lo a compreender os requisitos de CPU e memória do serviço que será criado quando implementar o seu modelo. O perfil testa o serviço que executa o seu modelo e devolve informações como o uso do CPU, o uso da memória e a latência da resposta. Também fornece uma recomendação de CPU e memória com base na utilização do recurso.
Para obter mais informações, consulte a secção de perfis dos [modelos Implementar.](how-to-deploy-profile-model.md)

### <a name="package-and-debug-models"></a>Pacotes e modelos de depuração

Antes de colocar um modelo em produção, é embalado numa imagem docker. Na maioria dos casos, a criação de imagem ocorre automaticamente em segundo plano durante a implementação. Pode especificar manualmente a imagem.

Se tiver problemas com a implantação, pode implantar-se no seu ambiente de desenvolvimento local para resolução de problemas e depuração.

Para obter mais informações, consulte [os modelos implementar](how-to-deploy-and-where.md#registermodel) e [implementar resolução de problemas](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Converter e otimizar modelos

Converter o seu modelo para [Open Neural Network Exchange](https://onnx.ai) (ONNX) pode melhorar o desempenho. Em média, a conversão para ONNX pode gerar um aumento de desempenho de 2x.

Para obter mais informações sobre ONNX com Azure Machine Learning, consulte o artigo [de modelos Create e accelerate ML.](concept-onnx.md)

### <a name="use-models"></a>Use modelos

Os modelos de aprendizagem de máquinas treinados são implementados como serviços web na nuvem ou localmente. Também pode implementar modelos para dispositivos Azure IoT Edge. As implementações utilizam cpu, GPU ou matrizes de portão programáveis de campo (FPGA) para inferentecção. Também pode utilizar modelos da Power BI.

Ao utilizar um modelo como um serviço Web ou dispositivo IoT Edge, fornece os seguintes itens:

* Os modelos que são utilizados para marcar dados submetidos ao serviço/dispositivo.
* Um script de entrada. Este script aceita pedidos, usa o(s) modelo(s) para marcar os dados e devolver uma resposta.
* Um ambiente de aprendizagem automática Azure que descreve as dependências de pip e Conda exigidas pelo(s) modelo(s) e script de entrada.
* Quaisquer ativos adicionais, tais como texto, dados, etc. que sejam exigidos pelo(s) modelo(s) e script de entrada.

Também apresenta a configuração da plataforma de implementação de destino. Por exemplo, o tipo de família VM, memória disponível e número de núcleos ao ser implantado no Serviço Azure Kubernetes.

Quando a imagem é criada, também são adicionados os componentes exigidos pelo Azure Machine Learning. Por exemplo, os recursos necessários para executar o serviço Web e interagir com o IoT Edge.

#### <a name="batch-scoring"></a>Pontuação em lote
A pontuação do lote é suportada através de gasodutos ML. Para obter mais informações, consulte [as previsões do Lote sobre os big data.](how-to-use-parallel-run-step.md)

#### <a name="real-time-web-services"></a>Serviços web em tempo real

Pode utilizar os seus modelos em **serviços web** com os seguintes alvos de computação:

* Instância de Contentor do Azure
* Azure Kubernetes Service
* Ambiente de desenvolvimento local

Para implementar o modelo como um serviço web, deve fornecer os seguintes itens:

* O modelo ou conjunto de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita pedidos e invoca o modelo, dependências conda, etc.
* Configuração de implementação que descreve como e onde implementar o modelo.

Para obter mais informações, consulte [os modelos Implementar.](how-to-deploy-and-where.md)

#### <a name="controlled-rollout"></a>Rollout controlado

Ao implementar no Serviço Azure Kubernetes, pode utilizar o roll-out controlado para ativar os seguintes cenários:

* Criar várias versões de um ponto final para uma implantação
* Realizar testes A/B encaminhando o tráfego para diferentes versões do ponto final.
* Altere entre as versões de ponto final atualizando a percentagem de tráfego na configuração do ponto final.

Para obter mais informações, consulte [o lançamento controlado dos modelos ML](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

#### <a name="iot-edge-devices"></a>Dispositivos IoT Edge

Pode utilizar modelos com dispositivos IoT através **de módulos Azure IoT Edge**. Os módulos IoT Edge são implantados num dispositivo de hardware, que permite inferência, ou pontuação de modelos, no dispositivo.

Para obter mais informações, consulte [os modelos Implementar.](how-to-deploy-and-where.md)

### <a name="analytics"></a>Análise

O Microsoft Power BI suporta a utilização de modelos de aprendizagem automática para análise de dados. Para obter mais informações, consulte [a integração Azure Machine Learning no Power BI (pré-visualização)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Capturar os dados de governação necessários para capturar o ciclo de vida ML de ponta a ponta

O Azure ML dá-lhe a capacidade de rastrear o rasto de auditoria de ponta a ponta de todos os seus ativos ML utilizando metadados.

- Azure ML [integra-se com Git](how-to-set-up-training-targets.md#gitintegration) para rastrear informações sobre de que repositório /ramo /cometer o seu código veio.
- [Os conjuntos de dados Azure ML](how-to-create-register-datasets.md) ajudam-no a rastrear, perfil e dados de versão.
- [A interpretação](how-to-machine-learning-interpretability.md) permite-lhe explicar os seus modelos, cumprir a conformidade regulamentar e compreender como os modelos chegam a um resultado para uma dada entrada.
- O histórico do Azure ML Run armazena uma imagem do código, dados e cálculos usados para treinar um modelo.
- O Registo do Modelo Azure ML captura todos os metadados associados ao seu modelo (que experiência o treinou, onde está a ser implantado, se as suas implementações forem saudáveis).
- [A integração com o Azure](how-to-use-event-grid.md)  permite-lhe atuar em eventos no ciclo de vida do ML. Por exemplo, registo de modelos, implantação, deriva de dados e eventos de formação (run).

> [!TIP]
> Embora algumas informações sobre modelos e conjuntos de dados são automaticamente capturadas, pode adicionar informações adicionais utilizando __tags__. Ao procurar modelos e conjuntos de dados registados no seu espaço de trabalho, pode utilizar tags como filtro.
>
> Associar um conjunto de dados com um modelo registado é um passo opcional. Para obter informações sobre a referência a um conjunto de dados ao registar um modelo, consulte a referência da classe [Modelo.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true)


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Notifique, automatize e alerte sobre os eventos no ciclo de vida do ML
A Azure ML publica eventos-chave para a Azure EventGrid, que pode ser usada para notificar e automatizar eventos no ciclo de vida ML. Para mais informações, consulte [este documento.](how-to-use-event-grid.md)


## <a name="monitor-for-operational--ml-issues"></a>Monitor para problemas operacionais de ML &

A monitorização permite-lhe compreender que dados estão a ser enviados para o seu modelo e as previsões que devolve.

Esta informação ajuda-o a perceber como o seu modelo está a ser usado. Os dados de entrada recolhidos também podem ser úteis na formação de futuras versões do modelo.

Para obter mais informações, consulte [Como permitir a recolha de dados do modelo.](how-to-enable-data-collection.md)

## <a name="retrain-your-model-on-new-data"></a>Retreine o seu modelo em novos dados

Muitas vezes, vai querer validar o seu modelo, atualizá-lo ou até reforçá-lo do zero, à medida que recebe novas informações. Às vezes, receber novos dados é uma parte esperada do domínio. Outras vezes, como discutido no [Detect data drift (pré-visualização) em conjuntos de dados,](how-to-monitor-datasets.md)o desempenho do modelo pode degradar-se face a coisas como alterações num determinado sensor, alterações naturais de dados, como efeitos sazonais, ou funcionalidades que mudam na sua relação com outras funcionalidades. 

Não há resposta universal para "Como sei se devo voltar a treinar?" mas o evento Azure ML e as ferramentas de monitorização previamente discutidas são bons pontos de partida para a automação. Uma vez que tenha decidido voltar a treinar, deve: 

- Pré-processar os seus dados usando um processo repetível e automatizado
- Treine o seu novo modelo
- Compare as saídas do seu novo modelo com as do seu modelo antigo
- Utilize critérios predefinidos para escolher se deve substituir o seu modelo antigo 

Um tema dos passos acima é que a sua reconversão deve ser automatizada, não ad hoc. [Os gasodutos Azure Machine Learning](concept-ml-pipelines.md) são uma boa resposta para a criação de fluxos de trabalho relacionados com a preparação de dados, formação, validação e implantação. Leia [os modelos retrain com o designer Azure Machine Learning](how-to-retrain-designer.md) para ver como os oleodutos e o designer de Machine Learning Azure se encaixam num cenário de reconversão. 

## <a name="automate-the-ml-lifecycle"></a>Automatizar o ciclo de vida do ML 

Você pode usar GitHub e Azure Pipelines para criar um processo de integração contínua que treina um modelo. Num cenário típico, quando um Cientista de Dados verifica uma mudança no git repo para um projeto, o Gasoduto Azure iniciará uma corrida de treino. Os resultados da execução podem então ser inspecionados para ver as características de desempenho do modelo treinado. Também pode criar um pipeline que implementa o modelo como um serviço web.

[A extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) facilita o trabalho com os Gasodutos Azure. Fornece os seguintes melhoramentos aos gasodutos Azure:

* Permite a seleção do espaço de trabalho ao definir uma ligação de serviço.
* Permite que os gasodutos de libertação sejam acionados por modelos treinados criados num gasoduto de treino.

Para obter mais informações sobre a utilização de Gasodutos Azure com Azure Machine Learning, consulte os seguintes links:

* [Integração contínua e implantação de modelos ML com Gasodutos Azure](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Repositório de MLOps de Aprendizagem de Máquinas Azure.](https://aka.ms/mlops)
* [Repositório de Aprendizagem de Máquinas Azure MLOpsPython.](https://github.com/Microsoft/MLOpspython)

Também pode utilizar a Azure Data Factory para criar um pipeline de ingestão de dados que prepara dados para uso com formação. Para obter mais informações, consulte [o pipeline data ingestion.](how-to-cicd-data-ingestion.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais lendo e explorando os seguintes recursos:

+ [Como & onde implementar modelos](how-to-deploy-and-where.md) com Azure Machine Learning

+ [Tutorial: Implementar um modelo de classificação de imagem no ACI](tutorial-deploy-models-with-aml.md).

+ [Exemplos de MLOps de ponta a ponta repo](https://github.com/microsoft/MLOps)

+ [CI/CD de modelos ML com Gasodutos Azure](/azure/devops/pipelines/targets/azure-machine-learning)

+ Criar clientes que [consomem um modelo implantado](how-to-consume-web-service.md)

+ [Aprendizagem automática à escala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI arquiteturas de referência & as melhores práticas](https://github.com/microsoft/AI)
