---
title: 'MLOps: gestão de modelos ML'
titleSuffix: Azure Machine Learning
description: 'Conheça a gestão de modelos com o Azure Machine Learning (MLOps). Desloque, gerencie e monitorize os seus modelos para os melhorar continuamente. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: f5aaf8adf33d27f8ebb99c8ca3a873d958632a4f
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616836"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Gestão de modelos, implantação e monitorização com Aprendizagem automática Azure

Neste artigo, saiba como usar o Azure Machine Learning para gerir o ciclo de vida dos seus modelos. O Azure Machine Learning utiliza uma abordagem de Operações de Aprendizagem automática (MLOps). MlOps melhora a qualidade e consistência das suas soluções de aprendizagem automática. 

## <a name="what-is-mlops"></a>O que é o MLOps?

As Operações de Aprendizagem Automática (MLOps) baseiam-se em princípios e [práticas de DevOps](https://azure.microsoft.com/overview/what-is-devops/) que aumentam a eficiência dos fluxos de trabalho. Por exemplo, integração contínua, entrega e implantação. MlOps aplica estes princípios ao processo de aprendizagem automática, com o objetivo de:

* Experimentação e desenvolvimento mais rápidos de modelos
* Implantação mais rápida de modelos em produção
* Controlo de qualidade

O Azure Machine Learning fornece as seguintes capacidades de MLOps:

- **Criar gasodutos ML reprodutíveis.** Os gasodutos de Machine Learning permitem definir passos reutilizáveis e reutilizáveis para os seus processos de preparação, treino e pontuação de dados.
- **Criar ambientes de software reutilizáveis** para treinar e implementar modelos.
- **Registe, empreitada e implemente modelos de qualquer lugar**. Também pode rastrear metadados associados necessários para usar o modelo.
- **Capture os dados de governação para o ciclo de vida ml final.** As informações registadas podem incluir quem está a publicar modelos, por que foram feitas alterações e quando os modelos foram implantados ou utilizados na produção.
- **Notifique e alerte sobre os acontecimentos no ciclo de vida ML**. Por exemplo, a conclusão da experiência, o registo do modelo, a implementação do modelo e a deteção de dados à deriva.
- **Monitorizar as aplicações ML para questões operacionais e relacionadas com o ML.** Compare as inputs do modelo entre formação e inferência, explore métricas específicas do modelo e forneça monitorização e alertas na sua infraestrutura ML.
- **Automatizar o ciclo de vida ml de ponta a ponta com o Azure Machine Learning e os Gasodutos Azure**. A utilização de pipelines permite-lhe atualizar frequentemente os modelos, testar novos modelos e lançar continuamente novos modelos ML ao lado das outras aplicações e serviços.

## <a name="create-reproducible-ml-pipelines"></a>Criar gasodutos ML reprodutíveis

Utilize os gasodutos ML da Azure Machine Learning para coser todos os passos envolvidos no seu processo de treino de modelos.

Um gasoduto ML pode conter passos da preparação de dados para a extração de recursos até à sintonização do hiperparâmetro até à avaliação do modelo. Para mais informações, consulte [os gasodutos ML](concept-ml-pipelines.md).

Se utilizar o [Designer](concept-designer.md) para criar os seus oleodutos ML, pode clicar no **"..."** no canto superior direito da página Designer e, em seguida, selecionar **Clone**. A clonagem do seu pipeline permite-lhe iterar o seu design de pipeline sem perder as suas versões antigas.  

## <a name="create-reusable-software-environments"></a>Criar ambientes de software reutilizáveis

Os ambientes de Aprendizagem automática Azure permitem-lhe acompanhar e reproduzir as dependências de software dos seus projetos à medida que evoluem. Os ambientes permitem-lhe garantir que as construções são reprodutíveis sem configurações de software manual.

Os ambientes descrevem as dependências pip e Conda para os seus projetos, e podem ser usados tanto para a formação como para a implantação de modelos. Para mais informações, consulte os ambientes de [Aprendizagem automática Azure.](concept-environments.md)

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registar, embalar e implementar modelos de qualquer lugar

### <a name="register-and-track-ml-models"></a>Registar e rastrear modelos ML

O registo do modelo permite-lhe armazenar e verizar os seus modelos na nuvem Azure, no seu espaço de trabalho. O registo de modelos facilita a organização e o acompanhamento dos seus modelos treinados.

> [!TIP]
> Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que está armazenado em vários ficheiros, pode registá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registo, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros registados.

Os modelos registados são identificados por nome e versão. Sempre que registar um modelo com o mesmo nome de um modelo já existente, o registo aumenta a versão. Podem ser fornecidas etiquetas adicionais de metadados durante o registo. Estas etiquetas são então usadas quando se procura um modelo. O Azure Machine Learning suporta qualquer modelo que possa ser carregado com python 3.5.2 ou superior.

> [!TIP]
> Também pode registar modelos treinados fora do Azure Machine Learning.

Não é possível eliminar um modelo registado que esteja a ser utilizado numa implementação ativa.
Para mais informações, consulte a secção do modelo de registo dos [modelos Deploy](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Modelos de perfil

O Azure Machine Learning pode ajudá-lo a compreender os requisitos de CPU e memória do serviço que será criado quando implementar o seu modelo. O perfil testa o serviço que executa o seu modelo e devolve informações como o uso do CPU, o uso da memória e a latência de resposta. Também fornece uma CPU e uma recomendação de memória com base no uso do recurso.
Para mais informações, consulte a secção de perfis dos [modelos Deploy](how-to-deploy-and-where.md#profilemodel).

### <a name="package-and-debug-models"></a>Modelos de pacote e depuração

Antes de implantar um modelo em produção, é embalado numa imagem do Docker. Na maioria dos casos, a criação de imagem ocorre automaticamente em segundo plano durante a implantação. Pode especificar manualmente a imagem.

Se tiver problemas com a implementação, pode implantar-se no seu ambiente de desenvolvimento local para resolução de problemas e depuração.

Para mais informações, consulte [os modelos de implementação](how-to-deploy-and-where.md#registermodel) e implementações de [resolução de problemas.](how-to-troubleshoot-deployment.md)

### <a name="convert-and-optimize-models"></a>Converter e otimizar modelos

A conversão do seu modelo para [Open Neural Network Exchange](https://onnx.ai) (ONNX) pode melhorar o desempenho. Em média, a conversão para ONNX pode gerar um aumento de desempenho de 2x.

Para obter mais informações sobre onNX com Azure Machine Learning, consulte o artigo [Create e accelerate ML models.](concept-onnx.md)

### <a name="use-models"></a>Utilizar modelos

Os modelos de aprendizagem automática treinados são implantados como serviços web na nuvem ou localmente. Também pode implementar modelos para dispositivos Azure IoT Edge. As implementações utilizam cpu, GPU ou matrizes de porta programáveis em campo (FPGA) para inferência. Também pode utilizar modelos do Power BI.

Ao utilizar um modelo como serviço web ou dispositivo IoT Edge, fornece os seguintes itens:

* Os modelos que são utilizados para obter dados submetidos ao serviço/dispositivo.
* Um script de entrada. Este script aceita pedidos, utiliza o(s) modelo(s) para marcar os dados e devolver uma resposta.
* Um ambiente azure machine learning que descreve as dependências pip e Conda exigidas pelo modelo(s) e script de entrada.
* Quaisquer ativos adicionais, tais como texto, dados, etc. que sejam exigidos pelo modelo ou pelo script de entrada.

Também fornece a configuração da plataforma de implementação do alvo. Por exemplo, o tipo de família VM, memória disponível e número de núcleos ao implantar para o Serviço Azure Kubernetes.

Quando a imagem é criada, são também adicionados componentes exigidos pelo Azure Machine Learning. Por exemplo, os ativos necessários para executar o serviço web e interagir com o IoT Edge.

#### <a name="batch-scoring"></a>Pontuação em lote
A pontuação do lote é suportada através de gasodutos ML. Para mais informações, consulte [as previsões do Lote sobre big data](how-to-use-parallel-run-step.md).

#### <a name="real-time-web-services"></a>Serviços web em tempo real

Pode utilizar os seus modelos em **serviços web** com os seguintes alvos de cálculo:

* Instância de Contentor do Azure
* Azure Kubernetes Service
* Ambiente de desenvolvimento local

Para implementar o modelo como um serviço web, deve fornecer os seguintes itens:

* O modelo ou conjunto de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita pedidos e invoca o modelo, dependências de conda, etc.
* Configuração de implementação que descreve como e onde implementar o modelo.

Para mais informações, consulte [os modelos Deploy](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Dispositivos IoT Edge

Pode utilizar modelos com dispositivos IoT através de **módulos Azure IoT Edge**. Os módulos IoT Edge são implantados num dispositivo de hardware, que permite inferência, ou pontuação de modelo, no dispositivo.

Para mais informações, consulte [os modelos Deploy](how-to-deploy-and-where.md).

### <a name="analytics"></a>Análise

O Microsoft Power BI suporta a utilização de modelos de machine learning para análise de dados. Para mais informações, consulte a [integração do Azure Machine Learning no Power BI (pré-visualização)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Capturar os dados de governação necessários para capturar o ciclo de vida ml final

O Azure ML dá-lhe a capacidade de rastrear o rasto de auditoria de ponta a ponta de todos os seus ativos ml. Mais concretamente:

- O Azure ML [integra-se com](how-to-set-up-training-targets.md#gitintegration) a Git para rastrear informações sobre as quais veio o repositório/ramo/ comprometer o seu código.
- [Os Conjuntos](how-to-create-register-datasets.md) de Dados Azure ML ajudam-no a rastrear, perfil e versão dados. 
- O histórico azure ML Run armazena uma imagem instantânea do código, dados e cálculos usados para treinar um modelo.
- O Registo do Modelo Azure ML captura todos os metadados associados ao seu modelo (que experiência o treinou, onde está a ser implantado, se as suas implementações forem saudáveis).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Notificar, automatizar e alertar sobre os acontecimentos no ciclo de vida ml
O Azure ML publica eventos-chave para a Azure EventGrid, que pode ser usada para notificar e automatizar eventos no ciclo de vida ml. Para mais informações, por favor leia as informações sobre [este documento.](how-to-use-event-grid.md)


## <a name="monitor-for-operational--ml-issues"></a>Monitor para questões de & operacional ml

A monitorização permite-lhe compreender quais os dados que estão a ser enviados para o seu modelo e as previsões que devolve.

Esta informação ajuda-o a entender como o seu modelo está a ser utilizado. Os dados de entrada recolhidos também podem ser úteis na formação de futuras versões do modelo.

Para mais informações, consulte Como ativar a recolha de dados do [modelo.](how-to-enable-data-collection.md)

## <a name="retrain-your-model-on-new-data"></a>Retrete o seu modelo em novos dados

Muitas vezes, vai querer atualizar o seu modelo, ou até mesmo retreiná-lo do zero, uma vez que recebe novas informações. Às vezes, receber novos dados é uma parte esperada do domínio. Outras vezes, como discutido no [Detect data drift (pré-visualização) em conjuntos](how-to-monitor-datasets.md)de dados, o desempenho do modelo pode degradar-se face a coisas como alterações a um determinado sensor, alterações de dados naturais, tais como efeitos sazonais, ou funcionalidades que mudam na sua relação com outras funcionalidades. 

Não há resposta universal para "Como sei se devo voltar a treinar?" mas o evento Azure ML e as ferramentas de monitorização anteriormente discutidas são bons pontos de partida para a automação. Uma vez que tenha decidido voltar a treinar, deve: 

- Pré-processe os seus dados usando um processo repetível e automatizado
- Treine o seu novo modelo
- Compare as saídas do seu novo modelo com as do seu antigo modelo
- Utilize critérios predefinidos para escolher se substitui o seu antigo modelo 

Um tema dos passos acima é que a sua reconversão deve ser automatizada, não ad hoc. Os [oleodutos Azure Machine Learning](concept-ml-pipelines.md) são uma boa resposta para a criação de fluxos de trabalho relacionados com a preparação de dados, formação, validação e implantação. Leia [os modelos Retrain com o designer de Machine Learning Azure (pré-visualização)](how-to-retrain-designer.md) para ver como os oleodutos e o designer de Machine Learning Azure se encaixam num cenário de reconversão. 

## <a name="automate-the-ml-lifecycle"></a>Automatizar o ciclo de vida ml 

Pode utilizar os gasodutos GitHub e Azure para criar um processo de integração contínua que treine um modelo. Num cenário típico, quando um Cientista de Dados verifica uma mudança no repo Git para um projeto, o Pipeline Azure iniciará uma corrida de formação. Os resultados da corrida podem então ser inspecionados para ver as características de desempenho do modelo treinado. Também pode criar um pipeline que implemente o modelo como um serviço web.

A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) facilita o trabalho com os Gasodutos Azure. Fornece as seguintes melhorias aos gasodutos Azure:

* Permite a seleção do espaço de trabalho ao definir uma ligação de serviço.
* Permite que os gasodutos de libertação sejam acionados por modelos treinados criados num gasoduto de treino.

Para obter mais informações sobre a utilização de Pipelines Azure com Aprendizagem automática Azure, consulte as seguintes ligações:

* [Integração contínua e implantação de modelos ML com Pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Repositório de MlOps de Aprendizagem automática Azure.](https://aka.ms/mlops)
* [Cesitório de Aprendizagem automática Azure MLOpsPython.](https://github.com/Microsoft/MLOpspython)

Também pode utilizar a Azure Data Factory para criar um pipeline de ingestão de dados que prepare dados para uso com formação. Para mais informações, consulte o pipeline de [ingestão](how-to-cicd-data-ingestion.md)de dados .

## <a name="next-steps"></a>Passos seguintes

Saiba mais lendo e explorando os seguintes recursos:

+ [Como & onde implementar modelos](how-to-deploy-and-where.md) com Azure Machine Learning

+ [Tutorial: Implemente um modelo de classificação de imagem em ACI](tutorial-deploy-models-with-aml.md).

+ [MlOps de ponta a ponta exemplos repo](https://github.com/microsoft/MLOps)

+ [CI/CD dos modelos ML com Pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning)

+ Criar clientes que [consomem um modelo implantado](how-to-consume-web-service.md)

+ [Aprendizagem automática à escala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Arquiteturas de referência azure AI & representante das boas práticas](https://github.com/microsoft/AI)
