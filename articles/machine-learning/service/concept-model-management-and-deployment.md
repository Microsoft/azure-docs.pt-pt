---
title: 'MLOps: gerenciamento de modelos de ML'
titleSuffix: Azure Machine Learning
description: 'Saiba mais sobre o gerenciamento de modelos com o Azure Machine Learning (MLOps). Implante, gerencie e monitore seus modelos para melhorá-los continuamente. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: a56096ffa760bcddd6bd423d1262e57dc1db3e3c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973565"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: gerenciamento de modelos, implantação e monitoramento com Azure Machine Learning

Neste artigo, saiba mais sobre como usar Azure Machine Learning para gerenciar o ciclo de vida de seus modelos. Azure Machine Learning usa uma abordagem de MLOps (operações de Machine Learning). O MLOps melhora a qualidade e a consistência de suas soluções de aprendizado de máquina. 

Azure Machine Learning fornece os seguintes recursos de MLOps:

- **Crie pipelines ml reproduzíveis**. Os pipelines permitem que você defina etapas reproduzíveis e reutilizáveis para seus processos de preparação, treinamento e pontuação de dados.
- **Registre, empacote e implante modelos de qualquer lugar** e acompanhe os metadados associados necessários para usar o modelo.
- **Capture os dados de governança necessários para capturar o ciclo de vida de ml de ponta a ponta**, incluindo quem está publicando modelos, por que as alterações estão sendo feitas e quando os modelos foram implantados ou usados na produção.
- **Notifique e alerte eventos no ciclo de vida do ml** , como a conclusão do experimento, o registro do modelo, a implantação do modelo e a detecção de descompasso de dados.
- **Monitore aplicativos ml para problemas operacionais e relacionados ao ml**. Compare as entradas de modelo entre treinamento e inferência, explore métricas específicas de modelo e forneça monitoramento e alertas em sua infraestrutura de ML.
- **Automatize o ciclo de vida de ml de ponta a ponta com Azure Machine Learning e o Azure DevOps** para atualizar modelos com frequência, testar novos modelos e distribuir continuamente novos modelos de ml junto com seus outros aplicativos e serviços.

## <a name="create-reproducible-ml-pipelines"></a>Criar pipelines ML reproduzíveis

Use pipelines ML do Azure Machine Learning para unir todas as etapas envolvidas no processo de treinamento do modelo.

Um pipeline ML pode conter etapas de preparação de dados para extração de recursos para ajuste de hiperparâmetro para avaliação de modelo. Para obter mais informações, consulte [pipelines do ml](concept-ml-pipelines.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registre, empacote e implante modelos de qualquer lugar

### <a name="register-and-track-ml-models"></a>Registrar e acompanhar modelos de ML

O registro de modelo permite armazenar e fazer a versão de seus modelos na nuvem do Azure, no seu espaço de trabalho. O registro de modelo torna mais fácil organizar e controlar seus modelos treinados.

> [!TIP]
> Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo em seu espaço de trabalho do Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que foram registrados.

Os modelos registrados são identificados por nome e versão. Sempre que registar um modelo com o mesmo nome que um já existente, o registro incrementa a versão. Marcas de metadados adicionais podem ser fornecidas durante o registro. Essas marcas são usadas durante a pesquisa de um modelo. Azure Machine Learning dá suporte a qualquer modelo que possa ser carregado usando Python 3.5.2 ou superior.

> [!TIP]
> Você também pode registrar modelos treinados fora do Azure Machine Learning.

Você não pode excluir um modelo registrado que está sendo usado em uma implantação ativa.
Para obter mais informações, consulte a seção registrar modelo de [implantar modelos](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Modelos de pacote e depuração

Antes de implantar um modelo na produção, ele é empacotado em uma imagem do Docker. Na maioria dos casos, a criação de imagem ocorre automaticamente em segundo plano durante a implantação. Você pode especificar a imagem manualmente.

Se você tiver problemas com a implantação, poderá implantar em seu ambiente de desenvolvimento local para solução de problemas e depuração.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md#registermodel) e [solucionar problemas de implantações](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Validar e criar perfil de modelos

Azure Machine Learning pode usar a criação de perfil para determinar as configurações de CPU e memória ideais a serem usadas ao implantar seu modelo. A validação de modelo ocorre como parte desse processo, usando dados que você fornece para o processo de criação de perfil.

### <a name="convert-and-optimize-models"></a>Converter e otimizar modelos

A conversão do modelo para [abrir a rede neural](https://onnx.ai) (ONNX) pode melhorar o desempenho. Em média, a conversão para ONNX pode gerar um aumento de desempenho de 2x.

Para obter mais informações sobre o ONNX com Azure Machine Learning, consulte o artigo [criar e acelerar modelos de ml](concept-onnx.md) .

### <a name="use-models"></a>Usar modelos

Os modelos de aprendizado de máquina treinados são implantados como serviços Web na nuvem ou localmente. Você também pode implantar modelos para Azure IoT Edge dispositivos. As implantações usam CPU, GPU ou FPGA (matrizes de portão programável por campo) para inferência. Você também pode usar modelos do Power BI.

Ao usar um modelo como um serviço Web ou IoT Edge dispositivo, você fornece os seguintes itens:

* Os modelos que são usados para pontuar os dados enviados para o serviço/dispositivo.
* Um script de entrada. Esse script aceita solicitações, usa os modelos para pontuar os dados e retornar uma resposta.
* Um arquivo de ambiente Conda que descreve as dependências exigidas pelos modelos e o script de entrada.
* Quaisquer ativos adicionais, como texto, dados etc., que são necessários para o (s) modelos e o script de entrada.

Você também fornece a configuração da plataforma de implantação de destino. Por exemplo, o tipo de família de VMs, a memória disponível e o número de núcleos durante a implantação no serviço kubernetes do Azure.

Quando a imagem é criada, os componentes exigidos pelo Azure Machine Learning também são adicionados. Por exemplo, os ativos necessários para executar o serviço Web e interagir com IoT Edge.

#### <a name="batch-scoring"></a>Pontuação em lote
A pontuação de lote tem suporte por meio de pipelines de ML. Para obter mais informações, consulte [previsões de lote em Big data](how-to-run-batch-predictions.md).

#### <a name="real-time-web-services"></a>Serviços Web em tempo real

Você pode usar seus modelos em **Serviços Web** com os seguintes destinos de computação:

* Instância de Contentor do Azure
* Serviço Kubernetes do Azure
* Ambiente de desenvolvimento local

Para implantar o modelo como um serviço Web, você deve fornecer os seguintes itens:

* O modelo ou Ensemble de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita solicitações e invoca o modelo, dependências Conda, etc.
* Configuração de implantação que descreve como e onde implantar o modelo.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Dispositivos IoT Edge

Você pode usar modelos com dispositivos IoT por meio de **módulos Azure IOT Edge**. IoT Edge módulos são implantados em um dispositivo de hardware, o que permite a inferência, ou a Pontuação do modelo, no dispositivo.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md).

### <a name="analytics"></a>Análise

O Microsoft Power BI dá suporte ao uso de modelos de aprendizado de máquina para análise de dados. Para obter mais informações, consulte [integração de Azure Machine Learning no Power bi (versão prévia)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Capturar os dados de governança necessários para capturar o ciclo de vida de ML de ponta a ponta

O Azure ML oferece a capacidade de acompanhar a trilha de auditoria de ponta a ponta de todos os seus ativos de ML. Especificamente:

- O Azure ML [integra-se com o Git](how-to-set-up-training-targets.md#gitintegration) para rastrear informações sobre a qual repositório/Branch/confirmação seu código veio.
- Os conjuntos de dados [do Azure ml](how-to-create-register-datasets.md) ajudam a acompanhar, criar o perfil e a versão. 
- O histórico de execuções do Azure ML armazena um instantâneo do código, dos dados e da computação usados para treinar um modelo.
- O registro de modelo do Azure ML captura todos os metadados associados ao seu modelo (que experimento o treina, onde ele está sendo implantado, se suas implantações estiverem íntegras).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Notificar, automatizar e alertar sobre eventos no ciclo de vida am
O Azure ML publica eventos-chave no Azure EventGrid, que pode ser usado para notificar e automatizar eventos no ciclo de vida do ML. Para obter mais informações, consulte [este documento](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitorar problemas operacionais de & ML

O monitoramento permite que você entenda quais dados estão sendo enviados para seu modelo e as previsões que ele retorna.

Essas informações ajudam a entender como seu modelo está sendo usado. Os dados de entrada coletados também podem ser úteis para treinar versões futuras do modelo.

Para obter mais informações, consulte [como habilitar a coleta de dados de modelo](how-to-enable-data-collection.md).

## <a name="automate-the-ml-lifecycle"></a>Automatizar o ciclo de vida do ML 

Você pode usar o GitHub e o Azure Pipelines para criar um processo de integração contínua que treina um modelo. Em um cenário típico, quando um cientista de dados verifica uma alteração no repositório Git para um projeto, o pipeline do Azure iniciará uma execução de treinamento. Os resultados da execução podem ser inspecionados para ver as características de desempenho do modelo treinado. Você também pode criar um pipeline que implanta o modelo como um serviço Web.

A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) torna mais fácil trabalhar com Azure pipelines. Ele fornece os seguintes aprimoramentos para Azure Pipelines:

* Habilita a seleção de espaço de trabalho ao definir uma conexão de serviço.
* Permite que os pipelines de versão sejam disparados por modelos treinados criados em um pipeline de treinamento.

Para obter mais informações sobre como usar Azure Pipelines com Azure Machine Learning, consulte a [integração e a implantação contínuas dos modelos de ml com Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning) artigo e o repositório [Azure Machine Learning MLOps](https://aka.ms/mlops) .

## <a name="next-steps"></a>Passos seguintes

Saiba mais lendo e explorando os seguintes recursos:

+ [Como & onde implantar modelos](how-to-deploy-and-where.md) com Azure Machine Learning

+ [Tutorial: implantar um modelo de classificação de imagem em ACI](tutorial-deploy-models-with-aml.md).

+ [Repositório de exemplos de MLOps de ponta a ponta](https://github.com/microsoft/MLOps)

+ [CI/CD de modelos de ML com Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Criar clientes que [consomem um modelo implantado](how-to-consume-web-service.md)

+ [Aprendizagem automática à escala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Arquiteturas de referência do AI do Azure & representante de práticas recomendadas](https://github.com/microsoft/AI)
